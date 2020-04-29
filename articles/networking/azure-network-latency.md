---
title: Statistiche di latenza round trip rete di Azure | Microsoft Docs
description: Informazioni sulle statistiche di latenza round trip tra le aree di Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: d9cae04499f046749e504bcab89b893fcc31a81c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886951"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistiche di latenza round trip rete di Azure

Azure monitora costantemente la latenza (velocità) delle aree principali della rete usando gli strumenti di monitoraggio interni e le misurazioni raccolte da [ThousandEyes](https://thousandeyes.com), un servizio di monitoraggio sintetico di terze parti.

## <a name="how-are-the-measurements-collected"></a>In che modo vengono raccolte le misurazioni?

Le misurazioni di latenza vengono raccolte dagli agenti ThousandEyes, ospitate in aree del cloud di Azure in tutto il mondo, che inviano continuamente sonde di rete tra loro in intervalli di 1 minuto. Le statistiche relative alla latenza mensile derivano dalla media degli esempi raccolti per il mese.

## <a name="march-2020-round-trip-latency-figures"></a>2020 marzo round trip cifre latenza

I tempi di round trip della media mensile tra le aree di Azure per gli ultimi 31 giorni (termina il 31 marzo 2020) sono riportati di seguito. Le misurazioni seguenti sono basate su [ThousandEyes](https://thousandeyes.com).

[![Statistiche sulla latenza tra aree di Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/).
