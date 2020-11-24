---
title: File di inclusione
description: File di inclusione
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 24a07109fc8f4d6ebd283dee7ee00107f0eb49b7
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556093"
---
È possibile usare l'API REST per [ottenere l'integrità dell'endpoint](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) per ottenere lo stato di integrità degli endpoint. Si consiglia di usare le [metriche di routing dell'hub](../articles/iot-hub/monitor-iot-hub-reference.md#routing-metrics) Internet in relazione alla latenza dei messaggi di routing per identificare ed eseguire il debug degli errori quando l'integrità dell'endpoint è inattiva o non integra, perché si prevede che la latenza sia superiore quando l'endpoint si trova in uno di questi Stati. Per altre informazioni sull'uso delle metriche dell'hub Internet, vedere [monitorare l'hub](../articles/iot-hub/monitor-iot-hub.md).

|Stato integrità|Descrizione|
|---|---|
|healthy|L'endpoint accetta messaggi come previsto.|
|non integro|L'endpoint non accetta messaggi e l'hub Internet sta tentando di inviare messaggi a questo endpoint.|
|unknown|L'hub Internet non ha tentato di recapitare i messaggi a questo endpoint.|
|degradato|L'endpoint accetta messaggi più lenti del previsto o è in stato di recupero da uno stato non integro.|
|morto|L'hub Internet delle cose non recapita più messaggi a questo endpoint. Tentativi di invio di messaggi a questo endpoint non riusciti.|