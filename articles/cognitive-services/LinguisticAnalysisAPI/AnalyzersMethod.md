---
title: Il metodo Analyzers - API Analisi linguistica
titlesuffix: Azure Cognitive Services
description: L'API REST degli analizzatori fornisce un elenco di analizzatori attualmente supportati dall'API Analisi linguistica.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: conceptual
ms.date: 06/30/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 762ebf50999a88251dcd05824f2ed450cec97f04
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237399"
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
NOME | type | DESCRIZIONE
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
