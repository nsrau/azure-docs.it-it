---
title: Il metodo Analyzers - API Analisi linguistica
titlesuffix: Azure Cognitive Services
description: L'API REST degli analizzatori fornisce un elenco di analizzatori attualmente supportati dall'API Analisi linguistica.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 06/30/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 1b33b60f674eebb15fdc6112e1d630b93b98494b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60404720"
---
# <a name="analyzers-method"></a>Metodo Analyzers

> [!IMPORTANT]
> L'anteprima di Analisi linguistica è stata ritirata il 9 agosto 2018. Per l'elaborazione e l'analisi del testo è consigliabile usare i [moduli di analisi del testo di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics).

L'API REST **Analyzers** fornisce un elenco di analizzatori attualmente supportati dal servizio.
Le risposta include i [nomi](Analyzer-Names.md) e le lingue supportate da ciascuno (ad esempio, "en" per inglese).

## <a name="request-parameters"></a>Parametri della richiesta
Nessuna

<br>

## <a name="response-parameters"></a>Parametri di risposta

Name | Type | DESCRIZIONE
-----|------|--------------
lingue | elenco di stringhe | elenco di codici di lingua ISO di due lettere per cui può essere usato l'analizzatore.
id   | stringa | ID univoco per l'analizzatore
kind | stringa | tipo ampio di analizzatore
specifica | stringa | nome della specifica usata per l'analizzatore
implementazione | stringa | descrizione del modello e/o dell'algoritmo alla base di questo analizzatore

<br>

## <a name="example"></a>Esempio
GET /analyzers

Risposta: JSON
```json
[
    {
        "id": "22A6B758-420F-4745-8A3C-46835A67C0D2",
        "languages": ["en"],
        "kind": "Constituency_Tree",  
        "specification": "PennTreebank3",
        "implementation": "SplitMerge"
    },
    {
        "id" : "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
        "languages": ["en"],
        "kind": "POS_Tags",
        "specification": "PennTreebank3",
        "implementation": "cmm"
    },
    {
        "id" : "08EA174B-BFDB-4E64-987E-602F85DA7F72",
        "languages": ["en"],
        "kind": "Tokens",
        "specification":"PennTreebank3",
        "implementation": "regexes"
    }
]
```
