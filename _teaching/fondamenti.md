---
layout: distill
title: "[013IN] Fondamenti di Informatica"
description: Corso di Studi in Ingegneria Industriale
tags: distill formatting
giscus_comments: true
featured: true
mermaid:
  enabled: true
  zoomable: true
code_diff: true
map: true
chart:
  chartjs: true
  echarts: true
  vega_lite: true
tikzjax: true
typograms: true
permalink: /teaching/fondamenti/
toc:
  - name: Informazioni Generali
  - name: Informazioni Esame
  - name: Prove d'esame passate
---


## Informazioni Generali

|  |                            |
|----------:|-----------------------------------------------|
| **Anno Accademico**      | 2024-2025       |
| **Corso di laurea**       | Ingegneria Industriale       |
| **Codice**       | 013IN                          |
| **Docente**      | Alessandro Renda                         |
| **CFU**      | 9 |
| **Durata**    | 72 ore (48 Lezione, 24 Laboratorio)        |
| **Periodo**     | Secondo semestre              |

## Informazioni Esame


<div class="callout-note">
  <p>È <strong>obbligatoria l’iscrizione all'esame</strong> tramite la piattaforma esse3.</p>
  <p>Si noti che le iscrizioni chiudono alcuni giorni prima (almeno 5) rispetto alla data dell’esame.</p>
</div>


<div class="callout-note">
  <p><strong>Studenti di anni accademici precedenti</strong></p>
  <p>È possibile sostenere l’esame con le modalità ed il programma previsti per l'A.A. in cui si è seguito il corso. In questo caso è obbligatorio:</p>
  <ul>
    <li>Comunicare al docente via mail, almeno due settimane prima rispetto alla data dell’esame, l’intenzione di sostenere l’esame secondo le modalità di un A.A. specifico, diverso da quello corrente</li>
    <li>Comunicare l’intenzione di sostenere l’esame secondo le modalità di un A.A. specifico, diverso da quello corrente, al momento dell’iscrizione all’esame su esse3</li>
  </ul>
</div>


|  |                            |
|----------:|-----------------------------------------------|
|  2024-2025       |
| **Corso di laurea**       | Ingegneria Industriale       |
| **Codice**       | 013IN                          |
| **Docente**      | Alessandro Renda                         |
| **CFU**      | 9 |
| **Durata**    | 72 ore (48 Lezione, 24 Laboratorio)        |
| **Periodo**     | Secondo semestre              |

## Prove d'esame passate

Si riportano i testi delle prove d'esame passate per quanto riguarda la parte di programmazione in Python
{% assign fondinf_2627 = site.static_files | where_exp:"f", "f.path contains '/assets/fondinf2627/'" %}
{% assign fondinf_2526 = site.static_files | where_exp:"f", "f.path contains '/assets/fondinf2526/'" %}
{% assign fondinfsim_files = site.static_files | where_exp:"f", "f.path contains '/assets/fondinfsim/'" %}


<table style="width: 100%; table-layout: fixed; border-collapse: collapse; border: none;">
  <tbody>
    <!-- RIGA 25-26 -->
    <tr>
      <td style="font-weight: bold; border: none;">25-26</td>
      {% assign col_count = 0 %}
      {% for file in fondinf_2526 %}
        <td style="text-align: center; border: none;"><a href="{{ file.path }}">{{ file.name | replace: '.pdf', '' }}</a></td>
        {% assign col_count = col_count | plus: 1 %}
      {% endfor %}
      {% assign remaining = 7 | minus: col_count %}
      {% for i in (1..remaining) %}
        <td style="border: none;"></td>
      {% endfor %}
    </tr>
    <!-- RIGA Sim -->
    <tr>
      <td style="font-weight: bold; border: none;">Sim</td>
      {% assign sim_count = 0 %}
      {% for file in fondinfsim_files %}
        <td style="text-align: center; border: none;"><a href="{{ file.path }}">{{ file.name | replace: '.pdf', '' }}</a></td>
        {% assign sim_count = sim_count | plus: 1 %}
      {% endfor %}
      {% assign empty_cells = 7 | minus: sim_count %}
      {% for i in (1..empty_cells) %}
        <td style="border: none;"></td>
      {% endfor %}
    </tr>
  </tbody>
</table>
