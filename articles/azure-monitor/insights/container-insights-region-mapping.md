---
title: Monitoraggio di Azure per i mapping delle aree dei contenitori
description: Questo articolo descrive i mapping dell'area supportati tra monitoraggio di Azure per contenitori, Log Analytics area di lavoro e metriche personalizzate.
services: azure-monitor
ms.service: azure-monitor
ms.workload: infrastructure-services
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ca77277c8d8d405b8fe81f612a8d7600d3c937ab
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388194"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapping dell'area supportati da monitoraggio di Azure per i contenitori

 Quando si Abilita monitoraggio di Azure per i contenitori, sono supportate solo alcune aree per il collegamento di un'area di lavoro Log Analytics e un cluster AKS e la raccolta di metriche personalizzate inviate a monitoraggio di Azure.

## <a name="log-analytics-workspace-supported-mappings"></a>Mapping supportati dall'area di lavoro Log Analytics

Le risorse del cluster AKS o Log Analytics area di lavoro possono risiedere in altre aree e la tabella seguente illustra i mapping.

|**Area del cluster AKS** | **Area dell'area di lavoro Log Analytics** |
|-----------------------|------------------------------------|
|**Africa** | |
|SouthAfricaNorth |Europa occidentale |
|SouthAfricaWest |Europa occidentale |
|**Australia** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**Asia Pacifico** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**Brasile** | |
|BrazilSouth | SouthCentralUS |
|**Canada** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**Europa** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|NorthEurope |NorthEurope |
|UKSouth |UKSouth |
|UKWest |UKSouth |
|Europa occidentale |Europa occidentale |
|**India** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Giappone** | |
|JapanEast |JapanEast |
|JapanWest |JapanEast |
|**Corea del Sud** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**Stati Uniti** | |
|CentralUS |CentralUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|Eastus<sup>1</sup>|
|US Gov Virginia |US Gov Virginia |

<sup>1</sup> a causa dei vincoli di capacità, l'area non è disponibile quando si creano nuove risorse. Sono incluse un'area di lavoro Log Analytics. Tuttavia, le risorse collegate preesistenti nell'area dovrebbero continuare a funzionare.

## <a name="custom-metrics-supported-regions"></a>Aree supportate per le metriche personalizzate

Per la pubblicazione come metrica personalizzata solo nelle [aree di Azure](../platform/metrics-custom-overview.md#supported-regions)seguenti sono supportate le metriche dei nodi e dei cluster di Azure Kubernetes Services (AKS).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare il monitoraggio del cluster AKS, vedere [come abilitare il monitoraggio di Azure per i contenitori](container-insights-onboard.md) per comprendere i requisiti e i metodi disponibili per abilitare il monitoraggio.  
