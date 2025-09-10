---
layout: page
title: "IDOR Lab Report"
description: "Report on IDOR lab activity"
---

# IDOR Vulnerabilities

In this report, the OWASP Juice Shop webapp is used to showcase the exploitation of two IDOR vulnerabilities.

![WEBSITE](https://alerenda.github.io/assets/reports/IDOR/images/burp.png)

## #1. View Basket
> <cite>Description: View another user’s shopping basket</cite>

Of course, this is a mistake in the code of the webapp, i.e., a vulnerability: a user must not be able to see data private of another user.

### Tools
- OWASP Juice Shop (running from Docker image)
- BURP Suite Proxy

### Preliminary steps
1. Create two accounts, e.g.:
- `a@gmail.com`
- `b@gmail.com`

2. Login as `a@gmail.com`
3. Add a product to the basket. 
4. Visualize the content of the basket by clicking on the cart icon:
![BASKET](https://alerenda.github.io/assets/reports/IDOR/images/basketa.png)

5. Logout and then login as the other user, i.e., `b@gmail.com`


### Discovery
1. Add a product to the basket.
2. Set `Intercept On`.
3. Visualize the content of the basket by clicking on the cart icon.
BURP will intercept, among others, a `GET` requests to the endpoint `/rest/basket/7`
![basketrequest](https://alerenda.github.io/assets/reports/IDOR/images/basketrequest.png) 
Notice that, as requests are stalling, the content of the basket is not showing up.
![basketrequest](https://alerenda.github.io/assets/reports/IDOR/images/interceptbasket.png) 
3. Set `Intercept Off` and make sure the shopping basket has been fully displayed. Under the hood, the client-side JavaScript has processed the server response and has dynamically updated the DOM to display the basket content. 
![basketrequest](https://alerenda.github.io/assets/reports/IDOR/images/basketb.png) 
4. Go to the `HTTP History` tab and inspect the response to the request `GET` `/rest/basket/7`. The resource in the body of the response is represented in JSON:
```JSON
{
    "status":"success",
    "data":{
        "id":7,
        "coupon":null,
        "UserId":24,
        "createdAt":"2025-09-10T14:37:23.218Z",
        "updatedAt":"2025-09-10T14:37:23.218Z",
        "Products":[
            {
                "id":6,
                "name":"Banana Juice (1000ml)",
                "description":"Monkeys love it the most.",
                "price":1.99,
                "deluxePrice":1.99,
                "image":"banana_juice.jpg",
                "createdAt":"2025-09-10T14:05:01.949Z",
                "updatedAt":"2025-09-10T14:05:01.949Z",
                "deletedAt":null,
                "BasketItem":{
                    "ProductId":6,
                    "BasketId":7,
                    "id":10,
                    "quantity":1,
                    "createdAt":"2025-09-10T14:37:57.462Z",
                    "updatedAt":"2025-09-10T14:37:57.462Z"
                }
            }
        ]
    }
}
```


Observations: 

> The `GET` to `/rest/basket/7` may be suggestive of an **IDOR vulnerability**: the parameter in the URL path represents a direct object reference, which identifies the shopping basket of this user. 

> The value of the parameter in the URL path (i.e., `7`) is a small integer and, as such, it is enumerable and highly predictable. *Security through obscurity* is not implemented. 

> Ultimately, the presence of an IDOR vulnerability depends on whether the application enforces proper authorization checks. 

The discovery procedure is outlined in the following:

1. Go to the `HTTP History` tab and select the request `GET` `/rest/basket/7`.
2. Right click on it and select `Send to Repeater`
3. Move to the `Repeater` tab. Modify the request by incrementing or decrementing the url parameter.
4. Click on send and check the response.
![alt text](https://alerenda.github.io/assets/reports/IDOR/images/repeater.png)
5. Note that the basket represented in JSON within the response is the one related to the user `a@gmail.com`, whereas the current user, who sent the request, is `b@gmail.com`.
6. Check the browser. A *vulnerability found* banner will show up.
![banner](https://alerenda.github.io/assets/reports/IDOR/images/banner.png)

> The web application is vulnerable to IDOR: due to missing authorization checks, a user can access the shopping basket of another user.

### Exploiting in practice
Decrementing the parameter was just an educated guess (the account `a@gmail.com` was manually created few seconds before `b@gmail.com`). 

In a practical setting the attacker would not know how many users are logged in and what their corresponding "small integer" values are. 
The attacker would thus have to repeatedly send `GET` `/rest/basket/basket-id`, with many different values for `basket-id`.

This procedure can be automated in BURP with the Intruder module, as follows. 

1. Go to the `HTTP History` tab and select the request `GET` `/rest/basket/7` (as before, logged in as `b@gmail.com`)
2. Right click on it and select `Send to Intruder`
3. Move to the `Intruder` tab.  
4. Specify which part of the request has to be modified at each iteration: select the parameter value `7` in the request and click on `Add §`. The request will appear as follows: `GET /rest/basket/§7§`
![alt text](https://alerenda.github.io/assets/reports/IDOR/images/intruder.png)
5. Specify how to modify the selected part of the URL at each iteration: in the `Payloads tab` set `Payload Type: Numbers` and set a reasonable range (e.g., `Sequential` from 1 to 10 with step 1).
6. Specify which part of the response will be extracted and shown in the results window: on the side Menu on the right, click on the `Settings` tab; in the `Grep - Extract` section, click on `Add`. Selecting a portion of text in the response will create a suitable regex automatically. Click `Ok`.
![alt text](https://alerenda.github.io/assets/reports/IDOR/images/grepmatch.png)
7. Click on `Start attack`.
8. A window related to the attack will appear. The column *"success"* (the name derives from the regex) shows the content of the extracted text for each response. Note that the payloads 8, 9, 10 probably do not represent valid values of `basket-id`, as the extracted value of `data` is null. In the bottom part of the window, each request and response can be inspected.
![alt text](https://alerenda.github.io/assets/reports/IDOR/images/intruderresults.png)


## Manipulate Basket
> <cite>Put an additional product into another user’s shopping basket.</cite>

The preliminary steps of the previous challenge can be leveraged.


### Discovery
1. Set `Intercept On`.
2. In the browser, add a product to the basket.
3. BURP will intercept a `GET /rest/basket/7`. Forward it.
4. BURP will then intercept, among others, a `POST` requests to the endpoint `/api/BasketItems` with the following body 
`{"ProductId":42,"BasketId":"7","quantity":1}`
![additem](https://alerenda.github.io/assets/reports/IDOR/images/additem.png)
4. Set `Intercept Off` and make sure the shopping basket has been updated. 


Observations: 

> The request `POST` `/api/BasketItems` may be suggestive of an **IDOR vulnerability**: the parameter `BasketId`in the request body represents a direct object reference, which identifies the shopping basket to be updated, associated with the current user. 

The discovery procedure is outlined in the following:

1. Go to the `HTTP History` tab and select the request `POST` `/api/BasketItems`.
2. Right click on it and select `Send to Repeater`
3. Move to the `Repeater` tab. Modify the request as before (e.g., decrementing the id)
4. Click on send and check the response.
5. Note that the response code and status indicate `401 Unauthorized` with body `{'error': 'Invalid BasketId'}`
![alt text](https://alerenda.github.io/assets/reports/IDOR/images/correctauth.png)



> At first glance, an authorization mechanism appears to be in place. However, the presence of such a check does not necessarily mean that the endpoint is free of flaws: it may still be vulnerable to IDOR (*and indeed it is, since this challenge exists*). Further hints provided by the [companion website](https://pwning.owasp-juice.shop/companion-guide/latest/part2/broken-access-control.html#_put_an_additional_product_into_another_users_shopping_basket)  may help identify where the authorization logic fails and how the vulnerability can be exploited.

**Try to complete the challenge!**