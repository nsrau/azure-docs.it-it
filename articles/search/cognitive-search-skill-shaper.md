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
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: a1f5a698ee76ebd0561bd19ff1a23d0f04be0771
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2019
ms.locfileid: "54410116"
---
#   <a name="shaper-cognitive-skill"></a>Competenza cognitiva Shaper

La competenza **Shaper** crea un tipo complesso per supportare i campi compositi (noti anche come campi a più parti). Un campo di tipo complesso è costituito da più parti ma viene considerato come un singolo elemento in un indice di Ricerca di Azure. Esempi di campi consolidati utili negli scenari di ricerca comprendono la possibilità di combinare, all'interno di un singolo campo, nome e cognome, città e stato o nome e data di nascita, per stabilire un'identità univoca.

La competenza **Shaper** consente essenzialmente di creare una struttura, definire il nome dei membri di tale struttura e assegnare valori a ciascun membro.

Per impostazione predefinita, questa tecnica supporta gli oggetti che sono a un livello di profondità. Per oggetti più complessi, è possibile concatenare diversi passaggi **Shaper**.

Nella risposta, il nome di output è sempre "output". Internamente, la pipeline può eseguire il mapping di un nome diverso, ad esempio "analyzedText" negli esempi seguenti di "output", ma la stessa competenza **Shaper** restituisce "output" nella risposta. Questo potrebbe essere importante se si esegue il debug di documenti approfonditi e si nota la discrepanza nella denominazione oppure se si compila una competenza personalizzata e si esegue la struttura della risposta manualmente.

> [!NOTE]
> Questa competenza non è associata a un'API Servizi cognitivi e non vengono addebitati costi in caso di utilizzo. È tuttavia necessario [collegare una risorsa di Servizi cognitivi](cognitive-search-attach-cognitive-services.md) per eseguire l'override dell'opzione di risorsa **Gratuito**, che consente solo un numero ridotto di arricchimenti al giorno.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>Esempio 1: tipi complessi

Si consideri uno scenario in cui si desidera creare una struttura denominata *analyzedText* dotata di due membri: rispettivamente *testo* e *valutazione*. In Ricerca di Azure, i campo ricercabili a più parti vengono chiamati *tipi complessi* e non sono ancora supportati. In questa anteprima si può usare una competenza **Shaper** per generare campi di tipo complesso nell'indice. 

Nell'esempio seguente vengono forniti i nomi del membro come input. La struttura di output (il campo complesso in Ricerca di Azure) viene specificata tramite *targetName*. 


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
      "targetName": analyzedText"
    }
  ]
}
```

### <a name="sample-input"></a>Input di esempio
Un documento JSON che fornisce input utilizzabile per la competenza **Shaper** può essere:

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


### <a name="sample-output"></a>Output di esempio
La competenza **Shaper** genera un nuovo elemento denominato *analyzedText* con gli elementi combinati di *testo* e *valutazione*. 

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

## <a name="sample-2-input-consolidation"></a>Esempio 2: consolidamento di input

In un altro esempio, si supponga che in diverse fasi di elaborazione della pipeline siano stati estratti il titolo di un libro e i titoli dei capitoli in diverse pagine del libro. È ora possibile creare una singola struttura composta da questi input diversi.

La definizione della competenza Shaper per questo scenario potrebbe essere simile alla seguente:

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
            "source": "/document/content/pages/*/chapterTitles/*"
        }
    ],
    "outputs": [
        {
            "output": "titlesAndChapters",
            "targetName": "analyzedText"
        }
    ]
}
```

### <a name="sample-output"></a>Output di esempio
In questo caso, lo Shaper appiattisce tutti i titoli del capitolo per creare una matrice singola. 

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

## <a name="see-also"></a>Vedere anche 

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)

