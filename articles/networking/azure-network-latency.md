---
title: Statistiche sulla latenza di round trip di rete di Azure | Microsoft Docs
description: Informazioni sulle statistiche di latenza di round trip tra le aree di Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 07/07/2020
ms.author: kumud
ms.openlocfilehash: f0266205ea5ce618793022523ce167cbc6a615c0
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86114287"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistiche sulla latenza di round trip di rete di Azure

Azure monitora costantemente la latenza (velocità) delle aree principali della rete usando gli strumenti di monitoraggio interni e le misurazioni raccolte da [ThousandEyes](https://thousandeyes.com), un servizio di monitoraggio sintetico di terze parti.

## <a name="how-are-the-measurements-collected"></a>In che modo vengono raccolte le misurazioni?

Le misurazioni di latenza vengono raccolte dagli agenti ThousandEyes, ospitate in aree del cloud di Azure in tutto il mondo, che inviano continuamente sonde di rete tra loro in intervalli di 1 minuto. Le statistiche relative alla latenza mensile derivano dalla media degli esempi raccolti per il mese.

## <a name="june-2020-round-trip-latency-figures"></a>Cifre della latenza di round trip di giugno 2020

I tempi di round trip della media mensile tra le aree di Azure per gli ultimi 30 giorni (che terminano il 30 giugno 2020) sono riportati di seguito. Le misurazioni seguenti sono basate su [ThousandEyes](https://thousandeyes.com).

[![Statistiche sulla latenza tra aree di Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/).
