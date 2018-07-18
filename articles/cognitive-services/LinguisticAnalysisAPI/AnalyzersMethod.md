---
title: Il metodo Analyzers nell'API Analisi linguistica | Microsoft Docs
description: L'API REST Analyzers fornisce un elenco di analizzatori attualmente supportati dal servizio in Servizi cognitivi Microsoft.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 06/30/2016
ms.author: lesun
ms.openlocfilehash: 3fc243a0da77c5bae9009929f2b82e1353347752
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372844"
---
# <a name="analyzers-method"></a>Metodo Analyzers

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
