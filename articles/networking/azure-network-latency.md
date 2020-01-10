---
title: Statistiche sulla latenza di round trip di rete di Azure | Microsoft Docs
description: Informazioni sulle statistiche di latenza di round trip tra le aree di Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 01/08/2020
ms.author: mnayak
ms.openlocfilehash: 91b528cc6900a3ec91ff7189f58f941226b8acd5
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779723"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistiche sulla latenza di round trip di rete di Azure

Azure monitora costantemente la latenza (velocità) delle aree principali della rete usando gli strumenti di monitoraggio interni e le misurazioni raccolte da [ThousandEyes](https://thousandeyes.com), un servizio di monitoraggio sintetico di terze parti.

## <a name="how-are-the-measurements-collected"></a>In che modo vengono raccolte le misurazioni?

Le misurazioni di latenza vengono raccolte dagli agenti ThousandEyes, ospitate in aree del cloud di Azure in tutto il mondo, che inviano continuamente sonde di rete tra loro in intervalli di 1 minuto. Le statistiche relative alla latenza mensile derivano dalla media degli esempi raccolti per il mese.

## <a name="december-2019-latency-figures"></a>Cifre latenza 2019 dicembre

I tempi di round trip della media mensile tra le aree di Azure per gli ultimi 30 giorni (che terminano il 31 dicembre 2019) sono riportati di seguito. Le misurazioni seguenti sono basate su [ThousandEyes](https://thousandeyes.com).

[Statistiche sulla latenza tra aree di Azure ![](media/azure-network-latency/december.jpg)](media/azure-network-latency/december.jpg#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/).
