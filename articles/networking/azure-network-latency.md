---
title: Statistiche sulla latenza di round trip di rete di Azure | Microsoft Docs
description: Informazioni sulle statistiche di latenza di round trip tra le aree di Azure.
services: networking
documentationcenter: na
author: nayak-mahesh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/04/2019
ms.author: mnayak
ms.openlocfilehash: 500676983233f943fdc9638d75758645dee65564
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587589"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistiche sulla latenza di round trip di rete di Azure

Azure monitora costantemente la latenza (velocità) delle aree principali della rete usando gli strumenti di monitoraggio interni e le misurazioni raccolte da [ThousandEyes](https://thousandeyes.com), un servizio di monitoraggio sintetico di terze parti.

## <a name="how-are-the-measurements-collected"></a>In che modo vengono raccolte le misurazioni?

Le misurazioni di latenza vengono raccolte dagli agenti ThousandEyes ospitati nelle aree del cloud di Azure in tutto il mondo, che inviano continuamente sonde di rete tra loro, in intervalli di 1 minuto. Le statistiche relative alla latenza mensile derivano dalla media degli esempi raccolti per il mese.

## <a name="october-2019-latency-figures"></a>Cifre latenza ottobre 2019

Per i 31 giorni che terminano il 31 ottobre 2019, i tempi di latenza minima e massima del round trip mensili nelle aree aggregate sono:

- da **5 MS** a **72 ms** per round trip all'interno di **America del Nord** aree.
- da **3 ms** a **28 ms** per i round trip nelle aree dell' **Europa** .
- da **4 ms** a **134 MS** per round trip in **Asia** regions.

Le misurazioni di latenza tra aree seguenti sono basate su [ThousandEyes](https://thousandeyes.com). L'unità di misura nella tabella seguente è in millisecondi (MS).

![Statistiche sulla latenza tra aree di Azure](media/azure-network-latency/azure-inter-region-latency.png)


## <a name="next-steps"></a>Passaggi successivi
- Informazioni sulle [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/).