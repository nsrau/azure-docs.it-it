---
title: Prezzi e limiti - Servizio visione artificiale personalizzato
titlesuffix: Azure Cognitive Services
description: Informazioni sui limiti e le quote per il Servizio visione artificiale personalizzato.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: anroth
ms.openlocfilehash: d0a6b7be62afcd8e9f51b6a9387c9aa33f62d9a9
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430069"
---
# <a name="pricing-and-limits"></a>Prezzi e limiti

Esistono tre livelli di chiavi per il Servizio visione artificiale personalizzato. Le risorse per il progetto delle versioni di valutazione limitate sono associate all'account di accesso del Servizio visione artificiale personalizzato, ovvero un account Azure Active Directory o un account Microsoft. Sono destinate all'uso per brevi versioni di valutazione del servizio. È possibile registrarsi per una sottoscrizione F0 (gratuita) o S0 (standard) tramite il portale di Azure. Per informazioni dettagliate su prezzi e transazioni, vedere la pagina corrispondente di [Prezzi di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/).

Gli account creati durante l'anteprima gratuita, prima dell'introduzione delle anteprime di Azure (1° marzo 2018), manterranno le rispettive quote precedenti per le versioni di valutazione limitate. 

Si prevede che nel tempo il numero di immagini di training per progetto di tag per progetto aumenterà per i progetti S0.

||**Versione di valutazione limitata**|**F0**|**S0**|
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


