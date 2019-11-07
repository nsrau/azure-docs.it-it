---
title: Limiti e quote - Servizio visione artificiale personalizzato
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra i diversi tipi di chiavi di licenza e i limiti e le quote per la Servizio visione artificiale personalizzato.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: e4fc3555d7d880dcbd4f76d8f507300e98c237db
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718359"
---
# <a name="limits-and-quotas"></a>Limiti e quote

Sono disponibili due livelli di chiavi per il servizio Visione personalizzata. È possibile registrarsi per una sottoscrizione F0 (gratuita) o S0 (standard) tramite il portale di Azure. Per informazioni dettagliate su prezzi e transazioni, vedere la pagina corrispondente di [Prezzi di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/).

Si prevede che nel tempo il numero di immagini di training per progetto di tag per progetto aumenterà per i progetti S0.

||**F0**|**S0**|
|-----|-----|-----|
|Progetti|2|100|
|Immagini di training per progetto |5\.000|100.000|
|Stime/mese|10.000 |Illimitato|
|Tag/progetto|50|500|
|Iterazioni |10|10|
|Immagini con etichetta min per tag, classificazione (50 + consigliata) |5|5|
|Immagini con etichetta min per tag, rilevamento di oggetti (50 + consigliato)|15|15|
|Durata dell'archiviazione immagini stimate|30 giorni|30 giorni|
|Operazioni di [stima](https://go.microsoft.com/fwlink/?linkid=865445) con archiviazione (transazioni al secondo)|2|10|
|Operazioni di [stima](https://go.microsoft.com/fwlink/?linkid=865445) senza archiviazione (transazioni al secondo)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (chiamate API al secondo)|2|10|
|[Altre chiamate API](https://go.microsoft.com/fwlink/?linkid=865446) (transazioni al secondo)|10|10|
|Tipi di immagine accettati|jpg, PNG, BMP, gif|jpg, PNG, BMP, gif|
|Altezza/larghezza minima immagine in pixel|256 (vedere la nota)|256 (vedere la nota)|
|Altezza/Larghezza immagine massima in pixel|senza limiti|senza limiti|
|Dimensioni max dell'immagine (caricamento dell'immagine di training) |6 MB|6 MB|
|Dimensioni max dell'immagine (stima)|4 MB|4 MB|
|Numero massimo di aree per l'immagine di training del rilevamento oggetti|200|200|
|Numero massimo di tag per ogni immagine di classificazione|30|30|

> [!NOTE]
> Le immagini più piccole di 256 pixel verranno accettate ma ridimensionate.
