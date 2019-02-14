---
title: Metodo dell'interpretazione - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Usare il metodo Interpret per restituire interpretazioni formattate delle stringhe di query degli utenti in base ai dati di Academic Graph e alla grammatica Academic in Servizi cognitivi Microsoft.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 6db9a5b65fc2723af2eae006ad81716e23e52133
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860525"
---
# <a name="interpret-method"></a>Metodo Interpret

L'API REST **interpret** accetta una stringa di query dell'utente finale (ad esempio, una query immessa dall'utente dell'applicazione) e restituisce interpretazioni formattate delle finalità dell'utente in base ai dati di Academic Graph e alla grammatica Academic.

Per fornire un'esperienza interattiva, è possibile chiamare ripetutamente questo metodo dopo ogni carattere immesso dall'utente. In tal caso, è necessario impostare il parametro **complete** su 1 per abilitare i suggerimenti di completamento automatico. Se l'applicazione non necessita del completamento automatico, è necessario impostare il parametro **complete** su 0.

**Endpoint REST:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>Parametri della richiesta

NOME     | Valore | Obbligatorio?  | DESCRIZIONE
---------|---------|---------|---------
**query**    | Stringa di testo | Sì | Query immessa dall'utente.  Se complete è impostato su 1, la query verrà interpretata come un prefisso per la generazione dei suggerimenti di completamento automatico della query.        
**model**    | Stringa di testo | No   | Nome del modello su cui si vuole eseguire la query.  Attualmente il valore predefinito è *latest*.        
**complete** | 0 o 1 | No <br>valore predefinito: 0  | 1 indica che i suggerimenti di completamento automatico vengono generati in base alla grammatica e ai dati di Graph.         
**count**    | Number | No <br>valore predefinito: 10 | Numero massimo di interpretazioni da restituire.         
**offset**   | Number | No <br>valore predefinito: 0  | Indice della prima interpretazione da restituire. Ad esempio, *count=2&offset=0* restituisce le interpretazioni 0 e 1. *count=2&offset=2* restituisce le interpretazioni 2 e 3.       
**timeout**  | Number | No <br>valore predefinito: 1000 | Timeout in millisecondi. Vengono restituite solo le interpretazioni trovate prima del timeout.
<br>
  
## <a name="response-json"></a>Risposta (JSON)
NOME     | DESCRIZIONE
---------|---------
**query** |Parametro *query* della richiesta.
**interpretations** |Matrice di 0 o più metodi diversi di confronto dell'input utente rispetto alla grammatica.
**interpretations[x].logprob**  |Probabilità logaritmica naturale relativa dell'interpretazione. Valori più elevati indicano una maggiore probabilità.
**interpretations[x].parse**  |Stringa XML che illustra come è stata interpretata ogni parte della query.
**interpretations[x].rules**  |Matrice di 1 o più regole definite nella grammatica che sono state richiamate durante l'interpretazione. Per l'API Academic Knowledge, sarà sempre presente una sola regola.
**interpretations[x].rules[y].name**  |Nome della regola.
**interpretations[x].rules[y].output**  |Output della regola.
**interpretations[x].rules[y].output.type** |Tipo di dati dell'output della regola.  Per l'API Academic Knowledge, sarà sempre "query".
**interpretations[x].rules[y].output.value**  |Output della regola. Per l'API Academic Knowledge, si tratta di una stringa di espressione di query che può essere passata ai metodi evaluate e calchistogram.
**aborted** | True se si è verificato il timeout della richiesta.

<br>
#### <a name="example"></a>Esempio:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>La risposta riportata di seguito contiene le prime due (a causa del parametro *count=2*) interpretazioni più probabili che completano l'input dell'utente parziale *papers by jaime*: *papers by jaime teevan* e *papers by jaime green*.  Il servizio ha generato i completamenti delle query invece di prendere in considerazione solo le corrispondenze esatte per l'autore *jaime* perché nella richiesta è stato specificato *complete=1*. Si noti la corrispondenza del valore canonico *j l green* tramite il sinonimo *jamie green*, come indicato nell'analisi.


```JSON
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -12.728,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -12.774,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='j l green')"
          }
        }
      ]
    }
  ]
}
```  
<br>Per recuperare i risultati delle entità per un'interpretazione, usare *output.value* dall'API **interpret** e passare tale valore nell'API **evaluate** tramite il parametro *expr*. In questo esempio, la query per la prima interpretazione è: 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 
