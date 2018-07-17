---
title: Metodo Interpret nell'API Knowledge Exploration Service | Microsoft Docs
description: Informazioni su come usare il metodo Interpret nell'API Knowledge Exploration Service (KES) in Servizi cognitivi.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: ef68d98dacf393abf8d030b9312217ea380947d2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373180"
---
# <a name="interpret-method"></a>Metodo interpret
Il metodo *interpret* accetta una stringa di query in linguaggio naturale e restituisce le interpretazioni formattate delle finalità dell'utente in base alla grammatica e ai dati di indice.  Per fornire un'esperienza di ricerca interattiva, questo metodo può essere chiamato dopo ogni carattere immesso dall'utente con il parametro *complete* impostato su 1 per abilitare i suggerimenti di completamento automatico.

## <a name="request"></a>Richiesta
`http://<host>/interpret?query=<query>[&<options>]`

NOME|Valore| DESCRIZIONE
----|----|----
query    | Stringa di testo | Query immessa dall'utente.  Se complete è impostato su 1, la query verrà interpretata come un prefisso per la generazione dei suggerimenti di completamento automatico della query.        
complete | 0 (valore predefinito) o 1 | 1 indica che i suggerimenti di completamento automatico vengono generati in base alla grammatica e ai dati di indice.         
count    | Numero (valore predefinito=10) | Numero massimo di interpretazioni da restituire.         
offset   | Numero (valore predefinito=0) | Indice della prima interpretazione da restituire.  Ad esempio, *count=2&offset=0* restituisce le interpretazioni 0 e 1. *count=2&offset=2* restituisce le interpretazioni 2 e 3.       
timeout  | Numero (valore predefinito=1000) | Timeout in millisecondi. Vengono restituite solo le interpretazioni trovate prima del timeout.

Usando i parametri *count* e *offset*, un numero elevato di risultati può essere ottenuto in modo incrementale attraverso più richieste.

## <a name="response-json"></a>Risposta (JSON)
JSONPath     | DESCRIZIONE
---------|---------
$.query |Parametro *query* della richiesta.
$.interpretations   |Matrice di 0 o più metodi di confronto della query di input rispetto alla grammatica.
$.interpretations[\*].logprob   |Probabilità logaritmica relativa dell'interpretazione (<= 0).  Valori più elevati indicano una maggiore probabilità.
$.interpretations[\*].parse |Stringa XML che illustra come è stata interpretata ogni parte della query.
$.interpretations[\*].rules |Matrice di 1 o più regole definite nella grammatica richiamate durante l'interpretazione.
$.interpretations[\*].rules[\*].name    |Nome della regola.
$.interpretations[\*].rules[\*].output  |Output semantico della regola.
$.interpretations[\*].rules[\*].output.type |Tipo di dati dell'output semantico.
$.interpretations[\*].rules[\*].output.value|Valore dell'output semantico.  
$.aborted | True se si è verificato il timeout della richiesta.

### <a name="parse-xml"></a>Codice XML di analisi
Il codice XML di analisi annota la query (completata) con informazioni sulla relativa corrispondenza alle regole della grammatica e agli attributi dell'indice.  Di seguito è riportato un esempio relativo al dominio delle pubblicazioni accademiche:

```xml
<rule name="#GetPapers">
  papers by 
  <attr name="academic#Author.Name" canonical="heungyeung shum">harry shum</attr>
  <rule name="#GetPaperYear">
    written in
    <attr name="academic#Year">2000</attr>
  </rule>
</rule>
```

L'elemento `<rule>` delimita l'intervallo nella query che corrisponde alla regola specificata dal relativo attributo `name`.  Può essere annidato quando l'analisi include riferimenti a regole nella grammatica.

L'elemento `<attr>` delimita l'intervallo nella query che corrisponde all'attributo di indice specificato dal relativo attributo `name`.  Quando la corrispondenza include un sinonimo nella query di input, l'attributo `canonical` conterrà il valore canonico che corrisponde al sinonimo nell'indice.

## <a name="example"></a>Esempio
Nell'esempio relativo alle pubblicazioni accademiche, la richiesta seguente restituisce fino a 2 suggerimenti di completamento automatico per la query di prefisso "papers by jaime":

`http://<host>/interpret?query=papers by jaime&complete=1&count=2`

La risposta contiene le prime due ("count=2") interpretazioni più probabili che completano la query parziale "papers by jaime": "papers by jaime teevan" e "papers by jaime green".  Il servizio ha generato i completamenti delle query invece di prendere in considerazione solo le corrispondenze esatte per l'autore "jaime" perché nella richiesta è stato specificato "complete=1". Si noti la corrispondenza del valore canonico "j l green" tramite il sinonimo "jamie green", come indicato nell'analisi.


```json
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -5.615,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -5.849,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='j l green')"
          }
        }
      ]
    }
  ]
}
```  

Quando il tipo di output semantico è "query", come in questo esempio, gli oggetti corrispondenti possono essere recuperati passando *output.value* all'API [*evaluate*](evaluateMethod.md) tramite il parametro *expr*.

`http://<host>/evaluate?expr=Composite(AA.AuN=='jaime teevan')`
  
