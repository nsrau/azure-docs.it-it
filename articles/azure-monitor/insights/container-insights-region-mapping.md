---
title: Monitoraggio di Azure per i mapping delle aree dei contenitori
description: Descrive i mapping di area supportati tra monitoraggio di Azure per contenitori, Log Analytics area di lavoro e metriche personalizzate.
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 9e3b8635c70dfdf33b0a062be80c948cd77923cc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91272906"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapping dell'area supportati da monitoraggio di Azure per i contenitori

 Quando si Abilita monitoraggio di Azure per i contenitori, sono supportate solo alcune aree per il collegamento di un'area di lavoro Log Analytics e un cluster AKS e la raccolta di metriche personalizzate inviate a monitoraggio di Azure.

## <a name="log-analytics-workspace-supported-mappings"></a>Mapping supportati dall'area di lavoro Log Analytics

Le aree AKS supportate sono elencate in [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service). L'area di lavoro Log Analytics deve trovarsi nella stessa area, ad eccezione delle aree elencate nella tabella seguente. Vedere le [Note sulla versione di AKS](https://github.com/Azure/AKS/releases) per gli aggiornamenti.


|**Area del cluster AKS** | **Area dell'area di lavoro Log Analytics** |
|-----------------------|------------------------------------|
|**Africa** | |
|SouthAfricaNorth |Europa occidentale |
|SouthAfricaWest |Europa occidentale |
|**Australia** | |
|AustraliaCentral2 |AustraliaCentral |
|**Brasile** | |
|BrazilSouth | SouthCentralUS |
|**Canada** ||
|CanadaEast |CanadaCentral |
|**Europa** | |
|FranceSouth |FranceCentral |
|UKWest |UKSouth |
|**India** | |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Giappone** | |
|JapanWest |JapanEast |
|**Corea del Sud** | |
|KoreaSouth |KoreaCentral |
|**Stati Uniti** | |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|


<sup>1</sup> a causa dei vincoli di capacità, l'area non è disponibile quando si creano nuove risorse. Sono incluse un'area di lavoro Log Analytics. Tuttavia, le risorse collegate preesistenti nell'area dovrebbero continuare a funzionare.

## <a name="custom-metrics-supported-regions"></a>Aree supportate per le metriche personalizzate

Per la pubblicazione come metrica personalizzata solo nelle [aree di Azure](../platform/metrics-custom-overview.md#supported-regions)seguenti sono supportate le metriche dei nodi e dei cluster di Azure Kubernetes Services (AKS).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare il monitoraggio del cluster AKS, vedere [come abilitare il monitoraggio di Azure per i contenitori](container-insights-onboard.md) per comprendere i requisiti e i metodi disponibili per abilitare il monitoraggio.  
