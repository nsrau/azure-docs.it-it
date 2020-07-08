---
title: Statistiche di latenza round trip rete di Azure | Microsoft Docs
description: Informazioni sulle statistiche di latenza round trip tra le aree di Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 05/26/2020
ms.author: kumud
ms.openlocfilehash: 8cf5d07bb071217a5ecafca8f351c94590291603
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83994009"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistiche di latenza round trip rete di Azure

Azure monitora costantemente la latenza (velocità) delle aree principali della rete usando gli strumenti di monitoraggio interni e le misurazioni raccolte da [ThousandEyes](https://thousandeyes.com), un servizio di monitoraggio sintetico di terze parti.

## <a name="how-are-the-measurements-collected"></a>In che modo vengono raccolte le misurazioni?

Le misurazioni di latenza vengono raccolte dagli agenti ThousandEyes, ospitate in aree del cloud di Azure in tutto il mondo, che inviano continuamente sonde di rete tra loro in intervalli di 1 minuto. Le statistiche relative alla latenza mensile derivano dalla media degli esempi raccolti per il mese.

## <a name="april-2020-round-trip-latency-figures"></a>round trip di aprile 2020 cifre latenza

I tempi di round trip della media mensile tra le aree di Azure per gli ultimi 30 giorni (che terminano il 30 aprile 2020) sono riportati di seguito. Le misurazioni seguenti sono basate su [ThousandEyes](https://thousandeyes.com).

[![Statistiche sulla latenza tra aree di Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/).
