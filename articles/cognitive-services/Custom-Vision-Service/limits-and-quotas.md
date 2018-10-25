---
title: Limiti e quote - Servizio visione artificiale personalizzato
titlesuffix: Azure Cognitive Services
description: Informazioni sui limiti e le quote per il Servizio visione artificiale personalizzato.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: anroth
ms.openlocfilehash: ce06effbce12abb6271e050829d3218f4fbbfbf4
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902860"
---
# <a name="limits-and-quotas"></a>Limiti e quote

Esistono tre livelli di chiavi per il Servizio visione artificiale personalizzato. Le risorse F0 e S0 vengono ottenute tramite il portale di Azure. Dettagli su definizioni di prezzi e transazioni sono disponibili alla [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/).  I progetti F0 possono essere aggiornati a progetti S0.

Le risorse per il progetto delle versioni di valutazione limitate sono allegate all'accesso al Servizio visione artificiale personalizzato (cioè un account AAD o un account Microsoft). Sono destinate all'uso per brevi versioni di valutazione del servizio.  Gli account creati durante l'anteprima gratuita, prima dell'introduzione delle anteprime di Azure (1° marzo 2018), manterranno le rispettive quote precedenti per le versioni di valutazione limitate. 

||**Versione di valutazione limitata**|**F0 (Azure)**|**S0 (Azure)**|
|-----|-----|-----|-----|
|Progetti|2|2|100|
|Immagini di training per progetto, classificazione|5.000|5.000|50.000|
|Immagini di training per progetto, rilevamento di oggetti|5.000|5.000|10.000|
|Previsioni/mese|10.000 |10.000|Illimitato|
|Tag/progetto|50|50|250|
|Iterazioni |10|10|10|
|Numero minimo di immagini con etichette per tag, classificazione (opzione consigliata: più di 50) |5|5|5|
|Numero minimo di immagini con etichette per tag, rilevamento oggetti (opzione consigliata: più di 50)|15|15|15|
|Durata dell'archiviazione immagini stimate|30 giorni|30 giorni|30 giorni|
|Operazioni di [stima](https://go.microsoft.com/fwlink/?linkid=865445) con archiviazione (transazioni al secondo)|2|2|10|
|Operazioni di [stima](https://go.microsoft.com/fwlink/?linkid=865445) senza archiviazione (transazioni al secondo)|2|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (chiamate API al secondo)|2|2|10|
|[Altre chiamate API](https://go.microsoft.com/fwlink/?linkid=865446) (transazioni al secondo)|10|10|10|
|Dimensioni max dell'immagine (caricamento dell'immagine di training) |6 MB|6 MB|6 MB|
|Dimensioni max dell'immagine (stima)|4 MB|4 MB|4 MB|

Si prevede che le limitazioni sul *numero di immagini di training per progetto* e sui *numeri di tag per progetto* aumenteranno nel tempo per i progetti S0. 
