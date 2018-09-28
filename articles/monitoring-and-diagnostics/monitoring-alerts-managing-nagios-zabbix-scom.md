---
title: Gestire gli avvisi di altri servizi di monitoraggio
description: Gestione degli avvisi di Nagios, Zabbix e SCOM in Monitoraggio di Azure
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 0a3e0f1ecc40213aca37e37e80c9ba35abedb3d6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961214"
---
# <a name="manage-alerts-from-other-monitoring-services"></a>Gestire gli avvisi di altri servizi di monitoraggio

È ora possibile visualizzare gli avvisi da Nagios, Zabbix e System Center Operations Manager nell'[esperienza di avvisi unificati](https://aka.ms/azure-alerts-overview). Questi avvisi provengono da integrazioni con i server Nagios o Zabbix o System Center Operations Manager in Log Analytics. 

## <a name="prerequisites"></a>Prerequisiti
Qualsiasi record nel repository di Log Analytics con un tipo di avviso verrà importato nell'esperienza di avvisi unificati, pertanto è necessario eseguire la configurazione necessaria per raccogliere questi record.
1. Per gli avvisi di **Nagios** e **Zabbix**, [configurare tali server](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-linux-agents) per inviare avvisi a Log Analytics.
1. Per gli avvisi **SCOM**, [connettere il gruppo di gestione Operations Manager all'area di lavoro Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-om-agents). Tutti gli avvisi creati in System Center Operations Manager vengono importati in Log Analytics.

## <a name="view-your-alert-instances"></a>Visualizzare le istanze di avviso
Dopo aver configurato l'importazione in Log Analytics, è possibile iniziare a visualizzare le istanze degli avvisi da questi servizi di monitoraggio nell'[esperienza di avvisi unificati](https://aka.ms/azure-alerts-overview). Dopo che sono presenti nell'esperienza di avvisi unificati, è possibile [gestire le istanze di avviso](https://aka.ms/managing-alert-instances), [gestire i gruppi intelligenti creati in questi avvisi](https://aka.ms/managing-smart-groups) e [modificare lo stato degli avvisi e dei gruppi intelligenti](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  Gli avvisi di Nagios nell'esperienza di avvisi unificati non sono con stato: ad esempio, la [condizione del monitoraggio](https://aka.ms/azure-alerts-overview) di un avviso non passa da "Attivato" a "Risolto". Al contrario, il "Attivato" e "Risolto" vengono visualizzati come istanze di avviso separate. 
