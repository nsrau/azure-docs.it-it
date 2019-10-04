---
title: Monitoraggio di Azure per mapping delle aree di contenitori
description: Questo articolo descrive il mapping delle aree supportate da monitoraggio di Azure per contenitori, dell'area di lavoro di Log Analitica e metriche personalizzate.
services: azure-monitor
ms.service: azure-monitor
ms.workload: infrastructure-services
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 481a2a400be4e983e0a2337a200324061494efa1
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518079"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapping delle aree supportate da monitoraggio di Azure per contenitori

 Quando si abilita il monitoraggio di Azure per contenitori, sono supportati solo determinate aree geografiche per il collegamento a un'area di lavoro di Log Analitica e un cluster AKS, e la raccolta di metriche personalizzate inviate al monitoraggio di Azure.

## <a name="log-analytics-workspace-supported-mappings"></a>Area di lavoro di log Analitica supportati mapping

Le risorse del cluster servizio contenitore di AZURE o l'area di lavoro di Log Analitica può trovarsi in altre aree e la tabella seguente vengono illustrati i mapping.

|**Area geografica del Cluster servizio contenitore di AZURE** | **Area dell'area di lavoro di Analitica di log** |
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
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|

<sup>1</sup> a causa di limiti di capacità, l'area non è disponibile durante la creazione di nuove risorse. Si tratta di un'area di lavoro di Log Analitica. Tuttavia, le risorse collegate preesistente nell'area continueranno a funzionare.

## <a name="custom-metrics-supported-regions"></a>Aree supportate di metriche personalizzate

Raccogliere le metriche da servizi Kubernetes di Azure (AKS) nodi dei cluster e per la pubblicazione come metriche personalizzate solo nelle risorse seguenti sono supportati i POD [aree di Azure](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare il monitoraggio del cluster AKS, esaminare [come abilitare il monitoraggio di Azure per contenitori](container-insights-onboard.md) per comprendere i requisiti e i metodi disponibili per abilitare il monitoraggio.  