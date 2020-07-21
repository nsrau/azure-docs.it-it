---
title: Gestire gli avvisi da System Center Operations Manager, Zabbix e Nagios in Monitoraggio di Azure
description: Gestire gli avvisi da System Center Operations Manager, Zabbix e Nagios in Monitoraggio di Azure
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 9fbad920bdfa26419e4b865db5d4e53dad97acce
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539449"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Gestire gli avvisi da System Center Operations Manager, Zabbix e Nagios in Monitoraggio di Azure

È ora possibile visualizzare gli avvisi da Nagios, Zabbix e System Center Operations Manager in [Monitoraggio di Azure](https://aka.ms/azure-alerts-overview). Questi avvisi provengono da integrazioni con i server Nagios o Zabbix o System Center Operations Manager in Log Analytics. 

## <a name="prerequisites"></a>Prerequisiti
Qualsiasi record nel repository di Log Analytics con un tipo di avviso verrà importato in Monitoraggio di Azure, pertanto è necessario eseguire la configurazione necessaria per raccogliere questi record.
1. Per gli avvisi di **Nagios** e **Zabbix** , [configurare questi server](../learn/quick-collect-linux-computer.md) per l' [invio di avvisi](./data-sources-custom-logs.md?toc=/azure/azure-monitor/toc.json) ai log Analytics.
1. Per gli avvisi **SCOM**, [connettere il gruppo di gestione Operations Manager all'area di lavoro Log Analytics](./om-agents.md). Successivamente, distribuire la soluzione [Gestione avvisi](./alert-management-solution.md) dal marketplace di soluzioni di Azure. Al termine, tutti gli avvisi creati in System Center Operations Manager vengono importati in Log Analytics.

## <a name="view-your-alert-instances"></a>Visualizzare le istanze di avviso
Dopo aver configurato l'importazione in Log Analytics, è possibile iniziare a visualizzare le istanze degli avvisi da questi servizi di monitoraggio in [Monitoraggio di Azure](https://aka.ms/azure-alerts-overview). Una volta presenti in Monitoraggio di Azure, è possibile [gestire le istanze di avviso](https://aka.ms/managing-alert-instances), [gestire i gruppi intelligenti creati in questi avvisi](https://aka.ms/managing-smart-groups) e [modificare lo stato degli avvisi e dei gruppi intelligenti](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. Questa soluzione consente solo di visualizzare le istanze di avviso System Center Operations Manager/Zabbix/Nagios generate in monitoraggio di Azure. La configurazione della regola di avviso può essere visualizzata o modificata solo nei rispettivi strumenti di monitoraggio. 
>  1. Tutte le istanze di avviso attivate saranno disponibili sia in Monitoraggio di Azure che in Azure Log Analytics. Attualmente, non è possibile scegliere tra le due opzioni o inserire solo specifici avvisi attivati.
>  1. Tutti gli avvisi di System Center Operations Manager, Zabbix e Nagios hanno il tipo di segnale "Unknown" poiché il tipo di telemetria sottostante non è disponibile.
>  1. Gli avvisi di Nagios sono senza stato: ad esempio, la [condizione del monitoraggio](https://aka.ms/azure-alerts-overview) di un avviso non passa da "Attivato" a "Risolto". Al contrario, il "Attivato" e "Risolto" vengono visualizzati come istanze di avviso separate. 
