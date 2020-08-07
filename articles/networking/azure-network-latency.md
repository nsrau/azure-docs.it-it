---
title: Statistiche sulla latenza di round trip di rete di Azure | Microsoft Docs
description: Informazioni sulle statistiche di latenza di round trip tra le aree di Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 08/05/2020
ms.author: kumud
ms.openlocfilehash: 1898bcf619f1fc9aaf19384968225e35c9589688
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87847440"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistiche sulla latenza di round trip di rete di Azure

Azure monitora costantemente la latenza (velocità) delle aree principali della rete usando gli strumenti di monitoraggio interni e le misurazioni raccolte da [ThousandEyes](https://thousandeyes.com), un servizio di monitoraggio sintetico di terze parti.

## <a name="how-are-the-measurements-collected"></a>In che modo vengono raccolte le misurazioni?

Le misurazioni di latenza vengono raccolte dagli agenti ThousandEyes, ospitate in aree del cloud di Azure in tutto il mondo, che inviano continuamente sonde di rete tra loro in intervalli di 1 minuto. Le statistiche relative alla latenza mensile derivano dalla media degli esempi raccolti per il mese.

## <a name="july-2020-round-trip-latency-figures"></a>Le cifre della latenza di round trip di luglio 2020

I tempi di round trip della media mensile tra le aree di Azure per gli ultimi 31 giorni (che termina il 31 luglio 2020) sono riportati di seguito. Le misurazioni seguenti sono basate su [ThousandEyes](https://thousandeyes.com).

[![Statistiche sulla latenza tra aree di Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/).
