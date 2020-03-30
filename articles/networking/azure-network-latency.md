---
title: Statistiche sulla latenza di andata e ritorno della rete di Azure Documenti Microsoft
description: Informazioni sulle statistiche di latenza di andata e ritorno tra le aree di Azure.Learn about round trip latency statistics between Azure regions.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: 3469daa8b81b20d5d0052a23ce3236fc4fde75de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082940"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistiche sulla latenza di andata e ritorno della rete di AzureAzure network round trip latency statistics

Azure monitora continuamente la latenza (velocità) delle aree principali della rete utilizzando gli strumenti di monitoraggio interni e le misurazioni raccolte da [ThousandEyes](https://thousandeyes.com), un servizio di monitoraggio sintetico di terze parti.

## <a name="how-are-the-measurements-collected"></a>Come vengono raccolte le misurazioni?

Le misurazioni della latenza vengono raccolte dagli agenti ThousandEyes, ospitati nelle aree cloud di Azure in tutto il mondo, che inviano continuamente probe di rete tra di loro a intervalli di 1 minuto. Le statistiche di latenza mensile derivano dalla media dei campioni raccolti per il mese.

## <a name="february-2020-round-trip-latency-figures"></a>Dati di latenza andata e ritorno di febbraio 2020

I tempi di andata e ritorno medi mensili tra le aree di Azure negli ultimi 29 giorni (che terminano il 29 febbraio 2020) sono illustrati di seguito. Le seguenti misurazioni sono alimentate da [ThousandEyes](https://thousandeyes.com).

[![Statistiche di latenza tra aree di AzureAzure inter-region latency statistics](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [aree](https://azure.microsoft.com/global-infrastructure/regions/)di Azure .
