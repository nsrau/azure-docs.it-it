---
title: Competenza cognitiva Shaper
titleSuffix: Azure Cognitive Search
description: Estrarre metadati e informazioni strutturate da dati non strutturati e modellarli come un tipo complesso in una pipeline di arricchimento ai dati in Ricerca cognitiva di Azure.Extract metadata and structured information from unstructured data and shape it as a complex type in an AI enrichment pipeline in Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 384b79037bb30656934c5e4b596dac2b776593b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754126"
---
# <a name="shaper-cognitive-skill"></a>Competenza cognitiva Shaper

La competenza **Shaper** consolida diversi input in un [tipo complesso](search-howto-complex-data-types.md) a cui è possibile fare riferimento in un secondo momento nella pipeline di arricchimento. La competenza **Shaper** consente essenzialmente di creare una struttura, definire il nome dei membri di tale struttura e assegnare valori a ogni membro. Esempi di campi consolidati utili negli scenari di ricerca includono la combinazione di nome e cognome in un'unica struttura, città e stato in un'unica struttura o nome e data di nascita in un'unica struttura per stabilire un'identità univoca.

Inoltre, la competenza **Shaper** illustrata nello [scenario 3](#nested-complex-types) aggiunge una proprietà *sourceContext* facoltativa all'input. Le proprietà *source* e *sourceContext* si escludono a vicenda. Se l'input si trova nel contesto della competenza, è sufficiente utilizzare *source*. Se l'input si trova in un contesto *diverso* da quello della competenza, usare *sourceContext*. *Il sourceContext* richiede di definire un input annidato con l'elemento specifico indirizzato come origine. 

Il nome di output è sempre "output". Internamente, la pipeline può eseguire il mapping di un nome diverso, ad esempio "analyzedText", come illustrato negli esempi seguenti, ma la competenza **Shaper** stessa restituisce "output" nella risposta. Questo potrebbe essere importante se si esegue il debug di documenti approfonditi e si nota la discrepanza nella denominazione oppure se si compila una competenza personalizzata e si esegue la struttura della risposta manualmente.

> [!NOTE]
> L'abilità **Shaper** non è associata a un'API di Servizi cognitivi e non viene addebitata alcuna operazione. È tuttavia necessario [collegare una risorsa di Servizi cognitivi](cognitive-search-attach-cognitive-services.md) per eseguire l'override dell'opzione di risorsa **Gratuito**, che consente solo un numero ridotto di arricchimenti al giorno.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Scenario 1: tipi complessiScenario 1: complex types

Si consideri uno scenario in cui si desidera creare una struttura denominata *analyzedText* dotata di due membri: rispettivamente *testo* e *valutazione*. In un indice, un campo ricercabile in più parti è denominato *tipo complesso* e viene spesso creato quando i dati di origine hanno una struttura complessa corrispondente che esegue il mapping ad esso.

Tuttavia, un altro approccio per la creazione di tipi complessi è tramite l'abilità **Shaper.However,** another approach for creating complex types is through the Shaper skill. Includendo questa competenza in un set di competenze, le operazioni in memoria durante l'elaborazione del set di competenze possono produrre forme di dati con strutture annidate, che possono quindi essere mappate a un tipo complesso nell'indice. 

La definizione di competenza di esempio seguente fornisce i nomi dei membri come input. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>Indice di esempio

Un set di competenze viene richiamato da un indicizzatore e un indicizzatore richiede un indice. Una rappresentazione di campo complessa nell'indice potrebbe essere simile all'esempio seguente. 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>Ingresso abilità

Un documento JSON in entrata che fornisce input utilizzabile per questa abilità Shaper potrebbe essere:An incoming JSON document providing usable input for this **Shaper** skill could be:

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>Uscita competenze

La abilità **Shaper** genera un nuovo elemento chiamato *analyzedText* con gli elementi combinati di *testo* e *sentimento*. Questo output è conforme allo schema dell'indice. Verrà importato e indicizzato in un indice di Ricerca cognitiva di Azure.It will be imported and indexed in an Azure Cognitive Search index.

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>Scenario 2: consolidamento dell'input

In un altro esempio, si supponga che in diverse fasi di elaborazione della pipeline siano stati estratti il titolo di un libro e i titoli dei capitoli in diverse pagine del libro. È ora possibile creare una singola struttura composta da questi input diversi.

La definizione di competenza Shaper per questo scenario potrebbe essere simile all'esempio seguente:The **Shaper** skill definition for this scenario might look like the following example:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Uscita competenze
In questo caso, **Shaper** appiattisce tutti i titoli dei capitoli per creare una singola matrice. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Scenario 3: consolidamento dell'input da contesti annidatiScenario 3: input consolidation from nested contexts

Immagina di avere il titolo, i capitoli e i contenuti di un libro e di aver eseguito il riconoscimento delle entità e le frasi chiave sui contenuti e ora devi aggregare i risultati delle diverse competenze in un'unica forma con il nome del capitolo, le entità e le frasi chiave.

La definizione di competenza Shaper per questo scenario potrebbe essere simile all'esempio seguente:The **Shaper** skill definition for this scenario might look like the following example:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Uscita competenze
In questo caso, lo **Shaper** crea un tipo complesso. Questa struttura esiste in memoria. Se si desidera salvarlo in un [knowledge store](knowledge-store-concept-intro.md), è necessario creare una proiezione nel set di competenze che definisca le caratteristiche di archiviazione.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Vedere anche

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Come utilizzare tipi complessi](search-howto-complex-data-types.md)
+ [Che cos'è il knowledge store in Ricerca di Azure?](knowledge-store-concept-intro.md)
+ [Creare un archivio conoscenze in REST](knowledge-store-create-rest.md)