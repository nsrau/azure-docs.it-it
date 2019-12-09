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
ms.openlocfilehash: ef61139056ad194d89e2a67cb4ac1b1414a3380b
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74829146"
---
In questo argomento di avvio rapido si userà l'[API REST di trascrizione in batch](../../../batch-transcription.md) per riconoscere la voce archiviata in un [BLOB SAS](https://aka.ms/ignite2019/speech/placeholder). Dopo aver soddisfatto alcuni prerequisiti, il riconoscimento vocale tramite un'API REST richiede solo alcuni passaggi:
> [!div class="checklist"]
> * Inviare la richiesta JSON al servizio Voce per iniziare a la trascrizione della voce.
> * Verificare lo stato della trascrizione.
> * Al termine, scaricare i risultati della trascrizione.