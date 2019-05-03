---
title: Definizione dell'interfaccia per le competenze personalizzate nella ricerca cognitiva - Ricerca di Azure
description: Interfaccia di estrazione dati personalizzata per una competenza personalizzata di tipo API Web nella pipeline di ricerca cognitiva di Ricerca di Azure.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 1bf42e5f418f99f5e5327d790c1adffe2357b84e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021928"
---
# <a name="how-to-add-a-custom-skill-to-a-cognitive-search-pipeline"></a>Come aggiungere una competenza personalizzata a una pipeline di ricerca cognitiva

Una [pipeline di indicizzazione di ricerca cognitiva](cognitive-search-concept-intro.md) in Ricerca di Azure può essere assemblata da [competenze predefinite](cognitive-search-predefined-skills.md), nonché da [competenze personalizzate](cognitive-search-custom-skill-web-api.md) create e aggiunte personalmente alla pipeline. Questo articolo offre informazioni su come creare una competenza personalizzata che esponga un'interfaccia che possa essere inclusa in una pipeline di ricerca cognitiva. 

Compilare un'esperienza personalizzata permette di inserire trasformazioni esclusive nel proprio contenuto. Una competenza personalizzata viene eseguita in modo indipendente, applicando qualsiasi passaggio di arricchimento desiderato. Ad esempio, è possibile definire entità personalizzate specifiche per campo, compilare modelli di classificazione personalizzati per distinguere i contratti e documenti commerciali da quelli finanziari oppure aggiungere una competenza di riconoscimento vocale per analizzare in modo più approfondito i file audio allo scopo di individuare il contenuto pertinente. Per un esempio dettagliato, vedere [Esempio: creare una competenza personalizzata](cognitive-search-create-custom-skill-example.md).

 Qualunque sia la capacità personalizzata necessaria, è disponibile un'interfaccia semplice e chiara per la connessione di una competenza personalizzata al resto della pipeline di arricchimento. L'unico requisito per l'inclusione in un [set di competenze](cognitive-search-defining-skillset.md) è la capacità di accettare input e generare output in modi che possano essere usati all'interno del set di competenze nel suo complesso. Questo articolo è incentrato sui formati di input e output richiesti dalla pipeline di arricchimento.

## <a name="web-api-custom-skill-interface"></a>Interfaccia della competenza personalizzata API Web

Sugli endpoint di competenza WebAPI personalizzati viene eseguito il timeout per impostazione predefinita se non restituiscono una risposta all'interno di una finestra di 30 secondi. La pipeline di indicizzazione è sincrona e l'indicizzazione genera un errore di timeout se in tale periodo non viene ricevuta alcuna risposta.  È possibile configurare il timeout fino a 90 secondi, impostando il parametro di timeout:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 90 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT90S",
```

Attualmente, l'unico meccanismo per interagire con una competenza è tramite un'interfaccia API Web. L'API Web deve soddisfare i requisiti descritti in questa sezione.

### <a name="1--web-api-input-format"></a>1.  Formato di input dell'API Web

L'API Web deve accettare una matrice di record da elaborare. Ogni record deve contenere un "contenitore delle proprietà", che corrisponde all'input fornito all'API Web. 

Si supponga di voler creare un enricher semplice che identifica la prima data menzionata nel testo di un contratto. In questo esempio, la competenza accetta un singolo input *contractText* come testo del contratto. La competenza ha anche un singolo output, ovvero la data del contratto. Per rendere l'enricher più interessante, restituire il valore *contractDate* sotto forma di un tipo complesso multiparte.

L'API Web deve essere pronta a ricevere un batch di record di input. Ogni membro della matrice dei *valori* rappresenta l'input per un record specifico. Ogni record deve avere gli elementi seguenti:

+ Un membro *recordId* che rappresenta l'identificatore univoco per un record specifico. Quando l'enricher restituisce i risultati, deve fornire il valore *recordId* per consentire al chiamante di abbinare i risultati dei record al relativo input.

+ Un membro *data*, che è essenzialmente un contenitore di campi di input per ogni record.

Per rendere il discorso più concreto, in base all'esempio precedente l'API Web deve essere in grado di ricevere richieste simili alla seguente:

```json
{
    "values": [
      {
        "recordId": "a1",
        "data":
           {
             "contractText": 
                "This is a contract that was issues on November 3, 2017 and that involves... "
           }
      },
      {
        "recordId": "b5",
        "data":
           {
             "contractText": 
                "In the City of Seattle, WA on February 5, 2018 there was a decision made..."
           }
      },
      {
        "recordId": "c3",
        "data":
           {
             "contractText": null
           }
      }
    ]
}
```
In realtà, il servizio può essere chiamato con centinaia o migliaia di record anziché solo con i tre mostrati qui.

### <a name="2-web-api-output-format"></a>2. Formato di output dell'API Web

Il formato dell'output è un set di record che contiene un *recordId* e un contenitore delle proprietà 

```json
{
  "values": 
  [
      {
        "recordId": "b5",
        "data" : 
        {
            "contractDate":  { "day" : 5, "month": 2, "year" : 2018 }
        }
      },
      {
        "recordId": "a1",
        "data" : {
            "contractDate": { "day" : 3, "month": 11, "year" : 2017 }                    
        }
      },
      {
        "recordId": "c3",
        "data" : 
        {
        },
        "errors": [ { "message": "contractText field required "}   ],  
        "warnings": [ {"message": "Date not found" }  ]
      }
    ]
}
```

Questo specifico esempio ha un solo output, ma è possibile restituire più proprietà. 

### <a name="errors-and-warning"></a>Errori e avvisi

Come illustrato nell'esempio precedente, si possono restituire messaggi di errore e avviso per ogni record.

## <a name="consuming-custom-skills-from-skillset"></a>Uso di competenze personalizzate dal set di competenze

Quando si crea un enricher API Web, è possibile descrivere intestazioni HTTP e parametri come parte della richiesta. Il frammento di codice seguente mostra come si possono descrivere parametri della richiesta e intestazioni HTTP come parte della definizione del set di competenze.

```json
{
    "skills": [
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill calls an Azure function, which in turn calls TA sentiment",
        "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/DateExtractor?language=en",
        "context": "/document",
        "httpHeaders": {
            "DateExtractor-Api-Key": "foo"
        },
        "inputs": [
          {
            "name": "contractText",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "contractDate",
            "targetName": "date"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

+ [Esempio: Creazione di una competenza personalizzata per l'API Traduzione testuale](cognitive-search-create-custom-skill-example.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Creare un set di competenze (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Come eseguire il mapping dei campi arricchiti](cognitive-search-output-field-mapping.md)
