---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 9280790f6692096a0b3909c9d1dfab2e94a8c0d7
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2018
ms.locfileid: "48904553"
---
Con l'[API Ricerca di anomalie](https://labs.cognitive.microsoft.com/en-us/project-anomaly-finder) è possibile caricare dati di serie temporali in formato JSON sull'endpoint API e quindi leggere il risultato dalla risposta dell'API. È possibile caricare i dati delle serie temporali, includendo per ogni punto dati:  
* Timestamp: timestamp del punto dati. Assicurarsi che usi una stringa di data e ora UTC, ad esempio "2017-08-01T00:00:00Z".
* Value: la misurazione del punto dati

I risultati sono costituiti da:
* Period: periodicità usata dall'API per rilevare le anomalie
* WarningText: eventuali informazioni di avviso
* ExpectedValue: valore previsto dal modello basato sull'apprendimento
* IsAnomaly: risultato che indica se i punti dati sono anomalie o meno in entrambe le direzioni (picchi o flessioni)
* IsAnomaly_Neg: risultato che indica se i punti dati sono anomalie in direzione negativa (flessioni)
* IsAnomaly_Pos: risultato che indica se i punti dati sono anomalie in direzione positiva (picchi)
* UpperMargin: la somma di ExpectedValue e UpperMargin determina il limite superiore entro il quale il punto dati viene ancora considerato normale.
* LowerMargin: la differenza tra ExpectedValue e LowerMargin determina il limite inferiore entro il quale il punto dati viene ancora considerato normale.

I dettagli del contratto di dati sono disponibili [qui](../apiref.md).

