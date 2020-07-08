---
title: includere il file
description: includere file
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a0d1de622eefad4ae5e55a427f8b0b1bf4360c0a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84792091"
---
È possibile usare l'API REST per [ottenere l'integrità dell'endpoint](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) per ottenere lo stato di integrità degli endpoint. Si consiglia di usare le [metriche dell'hub](../articles/iot-hub/iot-hub-metrics.md) delle cose correlate alla latenza dei messaggi di routing per identificare ed eseguire il debug degli errori quando l'integrità dell'endpoint è inattiva o non integra, perché si prevede che la latenza sia superiore quando l'endpoint si trova in uno di questi Stati.


|Stato integrità|Descrizione|
|---|---|
|healthy|L'endpoint accetta messaggi come previsto.|
|non integro|L'endpoint non accetta messaggi e l'hub Internet sta tentando di inviare messaggi a questo endpoint.|
|unknown|L'hub Internet non ha tentato di recapitare i messaggi a questo endpoint.|
|degradato|L'endpoint accetta messaggi più lenti del previsto o è in stato di recupero da uno stato non integro.|
|morto|L'hub Internet delle cose non recapita più messaggi a questo endpoint. Tentativi di invio di messaggi a questo endpoint non riusciti.|
