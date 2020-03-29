---
title: Limiti e quote - Servizio visione artificiale personalizzato
titleSuffix: Azure Cognitive Services
description: In questo articolo vengono illustrati i diversi tipi di codici di licenza e i limiti e le quote per il servizio di visione personalizzata.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 5f481ebf219eea8ae31e9802144bbf771a24e8b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79081778"
---
# <a name="limits-and-quotas"></a>Limiti e quote

Esistono due livelli di chiavi per il servizio di visione personalizzata. È possibile registrarsi per una sottoscrizione F0 (gratuita) o S0 (standard) tramite il portale di Azure. Per informazioni dettagliate su prezzi e transazioni, vedere la pagina corrispondente di [Prezzi di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/).

Si prevede che nel tempo il numero di immagini di training per progetto di tag per progetto aumenterà per i progetti S0.

||**F0**|**S0**|
|-----|-----|-----|
|Progetti|2|100|
|Immagini di training per progetto |5.000|100,000|
|Previsioni / mese|10,000 |Nessuna limitazione|
|Tag / progetto|50|500|
|Iterazioni |10|10|
|Immagini con etichetta minimo per tag, classificazione (più di 50 immagini consigliate) |5|5|
|Immagini con etichetta minimo per tag, rilevamento di oggetti (più di 50 immagini consigliate)|15|15|
|Durata dell'archiviazione immagini stimate|30 giorni|30 giorni|
|Operazioni di [stima](https://go.microsoft.com/fwlink/?linkid=865445) con archiviazione (transazioni al secondo)|2|10|
|Operazioni di [stima](https://go.microsoft.com/fwlink/?linkid=865445) senza archiviazione (transazioni al secondo)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (chiamate API al secondo)|2|10|
|[Altre chiamate API](https://go.microsoft.com/fwlink/?linkid=865446) (transazioni al secondo)|10|10|
|Tipi di immagine accettati|jpg, png, bmp, gif|jpg, png, bmp, gif|
|Minima altezza/larghezza dell'immagine in pixel|256 (vedi nota)|256 (vedi nota)|
|Altezza/larghezza massima dell'immagine in pixel|senza limiti|senza limiti|
|Dimensioni max dell'immagine (caricamento dell'immagine di training) |6 MB|6 MB|
|Dimensioni max dell'immagine (stima)|4 MB|4 MB|
|Immagine di training Numero massimo di aree per rilevamento di oggettiMax regions per object detection training image|300|300|
|Numero massimo di tag per immagine di classificazione|100|100|

> [!NOTE]
> Le immagini di dimensioni inferiori a 256 pixel verranno accettate ma aumentate.
