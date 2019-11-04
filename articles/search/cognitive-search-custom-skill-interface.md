---
title: Definizione dell'interfaccia per le competenze personalizzate
titleSuffix: Azure Cognitive Search
description: Interfaccia di estrazione dei dati personalizzata per le competenze personalizzate dell'API Web in una pipeline di arricchimento di intelligenza artificiale in Azure ricerca cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e0b54ca89511063e1a76753793846deaf8e8b9b4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466782"
---
# <a name="how-to-add-a-custom-skill-to-an-azure-cognitive-search-enrichment-pipeline"></a>Come aggiungere un'abilità personalizzata a una pipeline di arricchimento ricerca cognitiva di Azure

Una [pipeline di arricchimento](cognitive-search-concept-intro.md) in Azure ricerca cognitiva può essere assemblata da [competenze cognitive predefinite](cognitive-search-predefined-skills.md) , oltre che da [competenze personalizzate](cognitive-search-custom-skill-web-api.md) che vengono create e aggiunte personalmente alla pipeline. In questo articolo viene illustrato come creare un'abilità personalizzata che espone un'interfaccia per consentirne l'inclusione in una pipeline di arricchimento dell'intelligenza artificiale. 

Compilare un'esperienza personalizzata permette di inserire trasformazioni esclusive nel proprio contenuto. Una competenza personalizzata viene eseguita in modo indipendente, applicando qualsiasi passaggio di arricchimento desiderato. Ad esempio, è possibile definire entità personalizzate specifiche per campo, compilare modelli di classificazione personalizzati per distinguere i contratti e documenti commerciali da quelli finanziari oppure aggiungere una competenza di riconoscimento vocale per analizzare in modo più approfondito i file audio allo scopo di individuare il contenuto pertinente. Per un esempio dettagliato, vedere [esempio: creazione di un'abilità personalizzata per l'arricchimento di intelligenza artificiale](cognitive-search-create-custom-skill-example.md).

 Qualunque sia la capacità personalizzata necessaria, è disponibile un'interfaccia semplice e chiara per la connessione di una competenza personalizzata al resto della pipeline di arricchimento. L'unico requisito per l'inclusione in un [set di competenze](cognitive-search-defining-skillset.md) è la capacità di accettare input e generare output in modi che possano essere usati all'interno del set di competenze nel suo complesso. Questo articolo è incentrato sui formati di input e output richiesti dalla pipeline di arricchimento.

## <a name="web-api-custom-skill-interface"></a>Interfaccia della competenza personalizzata API Web

Sugli endpoint di competenza WebAPI personalizzati viene eseguito il timeout per impostazione predefinita se non restituiscono una risposta all'interno di una finestra di 30 secondi. La pipeline di indicizzazione è sincrona e l'indicizzazione genera un errore di timeout se in tale periodo non viene ricevuta alcuna risposta.  È possibile configurare il timeout per un massimo di 230 secondi, impostando il parametro timeout:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 230 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT230S",
```

Attualmente, l'unico meccanismo per interagire con una competenza è tramite un'interfaccia API Web. L'API Web deve soddisfare i requisiti descritti in questa sezione.

### <a name="1--web-api-input-format"></a>1. formato di input dell'API Web

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

### <a name="2-web-api-output-format"></a>2. formato di output dell'API Web

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

Questo articolo ha trattato i requisiti di interfaccia necessari per l'integrazione di un'abilità personalizzata in un skillt. Fare clic sui collegamenti seguenti per ulteriori informazioni sulle competenze personalizzate e sulla composizione di competenze.

+ [Power Skills: un repository di competenze personalizzate](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Esempio: creazione di un'abilità personalizzata per l'arricchimento di intelligenza artificiale](cognitive-search-create-custom-skill-example.md)
+ [Come definire un insieme di competenze](cognitive-search-defining-skillset.md)
+ [Creare un insieme di competenze (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Come eseguire il mapping dei campi arricchiti](cognitive-search-output-field-mapping.md)
