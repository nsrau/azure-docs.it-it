---
title: 'Guida introduttiva: Riconoscimento della voce archiviata in archiviazione BLOB - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Da definire
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 195c4c94f7ab00a8740c9dd14aad0d0fd0daa9b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506100"
---
In questo argomento di avvio rapido si userà l'[API REST di trascrizione in batch](../../../batch-transcription.md) per riconoscere la voce archiviata in un [BLOB SAS](https://aka.ms/ignite2019/speech/placeholder). Dopo aver soddisfatto alcuni prerequisiti, il riconoscimento vocale tramite un'API REST richiede solo alcuni passaggi:
> [!div class="checklist"]
> * Inviare la richiesta JSON al servizio Voce per iniziare a la trascrizione della voce.
> * Verificare lo stato della trascrizione.
> * Al termine, scaricare i risultati della trascrizione.