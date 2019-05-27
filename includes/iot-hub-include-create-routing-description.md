---
title: File di inclusione
description: File di inclusione
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b640a3cb9382ad72bb48e06c6a7074c96409e2e4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162684"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

Si stanno per inviare messaggi a diverse risorse in base alle proprietà allegate al messaggio dal dispositivo simulato. I messaggi che non sono indirizzati in modo personalizzato vengono inviati all'endpoint predefinito (messaggi/eventi). Nella prossima esercitazione, si inviano messaggi all'hub IoT per poi visualizzarli indirizzati a destinazioni diverse.

|value |Risultato|
|------|------|
|level="storage" |Scrivere ad archiviazione di Azure|
|level="critical" |Scrivere a una coda del bus di servizio. Un'app per la logica recupera il messaggio dalla coda e usa Office 365 per inviare il messaggio con posta elettronica.|
|default |Visualizzare questi dati con Power BI.|

Il primo passaggio consiste nell'impostare l'endpoint a cui verranno instradati i dati. Il secondo passaggio consiste nel configurare la route di messaggi che usa tale endpoint. Dopo aver configurato il routing, è possibile visualizzare gli endpoint e le route di messaggi nel portale.