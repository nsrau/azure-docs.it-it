---
title: Monitoraggio di Azure per i mapping delle aree dei contenitoriAzure Monitor for containers region mappings
description: Questo articolo descrive i mapping di aree supportati tra Monitoraggio di Azure per i contenitori, L'area di lavoro di Log Analytics e le metriche personalizzate.
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: a058f9cac987bb5c7130019f50370c6a176b09ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403414"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapping di aree supportati da Monitoraggio di Azure per i contenitori

 Quando si abilita Monitoraggio di Azure per i contenitori, sono supportate solo determinate aree per il collegamento di un'area di lavoro di Log Analytics e un cluster AKS e la raccolta di metriche personalizzate inviate a Monitoraggio di Azure.When enabling Azure Monitor for containers, only certain regions are supported for linking a Log Analytics workspace and an AKS cluster, and collecting custom metrics submitted to Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mapping supportati dall'area di lavoro di Log AnalyticsLog Analytics workspace supported mappings

Le risorse cluster AKS o l'area di lavoro di Log Analytics possono risiedere in altre aree e nella tabella seguente vengono illustrati i mapping.

|**Area cluster AKS** | **Area di lavoro di Log AnalyticsLog Analytics Workspace region** |
|-----------------------|------------------------------------|
|**Africa** | |
|SudAfricaNord |Europa occidentale |
|SudafricaWest |Europa occidentale |
|**Australia** | |
|AustraliaEst |AustraliaEst |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEst |AustraliaEst |
|**Asia Pacifico** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**Brasile** | |
|BrasileSud | Stati Uniti centro-meridionali |
|**Canada** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**Europa** | |
|FranciaCentral |FranciaCentral |
|FranciaSud |FranciaCentral |
|NordEuropa |NordEuropa |
|UKSouth |UKSouth |
|Regno UnitoWest |UKSouth |
|Europa occidentale |Europa occidentale |
|**India** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Giappone** | |
|JapanEast |JapanEast |
|GiapponeOvest |JapanEast |
|**Corea** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**Noi** | |
|Stati Uniti centrali |Stati Uniti centrali|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|Stati Uniti occidentali 2 |Stati Uniti occidentali 2 |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|
|US Gov Virginia |US Gov Virginia |

<sup>1</sup> A causa dei vincoli di capacità, l'area non è disponibile durante la creazione di nuove risorse. Ciò include un'area di lavoro di Log Analytics.This includes a Log Analytics workspace. Tuttavia, le risorse collegate preesistenti nella regione dovrebbero continuare a funzionare.

## <a name="custom-metrics-supported-regions"></a>Aree geografiche supportate per le metriche personalizzate

La raccolta di metriche da nodi e pod di cluster di Servizi Kubernetes di Azure sono supportati per la pubblicazione come metriche personalizzate solo nelle [aree](../platform/metrics-custom-overview.md#supported-regions)di Azure seguenti.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a monitorare il cluster AKS, vedere [Come abilitare Monitoraggio di Azure per i contenitori](container-insights-onboard.md) per comprendere i requisiti e i metodi disponibili per abilitare il monitoraggio.  
