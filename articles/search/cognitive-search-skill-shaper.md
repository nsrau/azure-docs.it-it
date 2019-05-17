---
title: Competenza Shaper della ricerca cognitiva - Ricerca di Azure
description: Estrarre metadati e informazioni strutturate da dati non strutturati e modellarli come tipo complesso in una pipeline di arricchimento di Ricerca di Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 058b6c979346d9dcce36940432d0e222e919dba9
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540837"
---
#   <a name="shaper-cognitive-skill"></a>Competenza cognitiva Shaper

Il **Shaper** competenza Consolida più input in un [tipo complesso](search-howto-complex-data-types.md) che è possibile fare riferimento in un secondo momento nella pipeline di arricchimento. La competenza **Shaper** consente essenzialmente di creare una struttura, definire il nome dei membri di tale struttura e assegnare valori a ciascun membro. Esempi di campi consolidati utili negli scenari di ricerca includono la combinazione di un nome e cognome in una singola struttura, città e stato in una singola struttura, o un nome e data di nascita in un'unica struttura per stabilire l'identità univoca.

La versione dell'API determina la profondità di data shaping è possibile ottenere. 

| Versione dell'API | Data shaping dei comportamenti | 
|-------------|-------------------|
| Versione 2019-05-06-preview dell'API REST (.NET SDK non è supportato) | Gli oggetti complessi, più livelli di profondità, in uno **Shaper** definizione competenze. |
| 2019-05-06 * * (disponibile a livello generale), 2017-11-11-Preview| Oggetti complessi, un livello di profondità. Una forma a più livelli richiede diversi passaggi shaper il concatenamento.|

Così come fornito da `api-version=2019-05-06-Preview`, il **Shaper** competenza illustrato [scenario 3](#nested-complex-types) aggiunge un nuovo facoltativo *sourceContext* proprietà all'input. Il *origine* e *sourceContext* proprietà si escludono a vicenda. Se l'input è in corrispondenza del contesto della competenza, usare semplicemente *origine*. Se l'input è in un *differente* contesto rispetto al contesto di competenza, utilizzare il *sourceContext*. Il *sourceContext* è necessario definire un input annidato con l'elemento specifico indirizzata come origine. 

Nella risposta, per tutte le versioni API, il nome di output è sempre "output". Internamente, la pipeline può eseguire il mapping di un nome diverso, ad esempio "analyzedText" come illustrato negli esempi seguenti, ma il **Shaper** competenza stesso restituisce "output" nella risposta. Questo potrebbe essere importante se si esegue il debug di documenti approfonditi e si nota la discrepanza nella denominazione oppure se si compila una competenza personalizzata e si esegue la struttura della risposta manualmente.

> [!NOTE]
> Il **Shaper** competenza non è associato a un'API servizi cognitivi e non vengono addebitati per il relativo utilizzo. È tuttavia necessario [collegare una risorsa di Servizi cognitivi](cognitive-search-attach-cognitive-services.md) per eseguire l'override dell'opzione di risorsa **Gratuito**, che consente solo un numero ridotto di arricchimenti al giorno.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Scenario 1: tipi complessi

Si consideri uno scenario in cui si desidera creare una struttura denominata *analyzedText* dotata di due membri: rispettivamente *testo* e *valutazione*. In un indice di ricerca di Azure, un campo ricercabile multiparte viene chiamato un *tipo complesso* e viene spesso creato quando i dati di origine hanno una struttura complessa corrispondente che esegue il mapping a esso.

Tuttavia, un altro approccio per la creazione di tipi complessi è tramite il **Shaper** competenza. Includendo questa esperienza in un insieme di competenze, le operazioni in memoria durante l'elaborazione di set di competenze possono restituire forme di data con strutture annidate, che possono quindi essere mappate a un tipo complesso nell'indice. 

La definizione di competenza di esempio seguente fornisce i nomi membro come input. 


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

### <a name="sample-index"></a>Indice degli esempi

Un insieme di competenze viene richiamato da un indicizzatore e un indicizzatore è necessario un indice. Una rappresentazione complessa campo nell'indice potrebbe essere simile al seguente. 

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

### <a name="skill-input"></a>Input di competenze

Un documento JSON in arrivo fornisce l'input utilizzabile per questo **Shaper** competenza potrebbe essere:

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


### <a name="skill-output"></a>Output di competenze

La competenza **Shaper** genera un nuovo elemento denominato *analyzedText* con gli elementi combinati di *testo* e *valutazione*. Questo output è conforme allo schema dell'indice. Verranno importato e indicizzato in un indice di ricerca di Azure.

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

## <a name="scenario-2-input-consolidation"></a>Scenario 2: input consolidamento

In un altro esempio, si supponga che in diverse fasi di elaborazione della pipeline siano stati estratti il titolo di un libro e i titoli dei capitoli in diverse pagine del libro. È ora possibile creare una singola struttura composta da questi input diversi.

Il **Shaper** definizione competenze per questo scenario potrebbe essere simile al seguente:

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

### <a name="skill-output"></a>Output di competenze
In questo caso, il **Shaper** rende flat tutti i titoli capitolo per creare una matrice. 

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

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Scenario 3: il consolidamento di input da contesti annidati

> [!NOTE]
> Annidata è supportate in strutture di [API REST versione 2019-05-06-Preview](search-api-preview.md) può essere usato in un [store knowledge](knowledge-store-concept-intro.md) o in un indice di ricerca di Azure.

Si supponga hanno il titolo, capitoli e contenuto di un libro e sono state eseguite le frasi chiave e riconoscimento di entità sul contenuto, è possibile per aggregare i risultati di competenze diverse in una singola forma con il nome capitolo, entità e le frasi chiave.

Il **Shaper** definizione competenze per questo scenario potrebbe essere simile al seguente:

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

### <a name="skill-output"></a>Output di competenze
In questo caso, il **Shaper** crea un tipo complesso. Questa struttura è presente in memoria. Se si desidera salvarlo in un archivio della Knowledge Base, è necessario creare una proiezione nelle tue competenze che definisce le caratteristiche di archiviazione.

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
+ [Come usare i tipi complessi](search-howto-complex-data-types.md)
+ [Panoramica di archivio della Knowledge Base](knowledge-store-concept-intro.md)
+ [Come iniziare a usare archivio della Knowledge Base](knowledge-store-howto.md)