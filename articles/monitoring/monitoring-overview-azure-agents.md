---
title: Panoramica degli agenti di monitoraggio di Azure | Microsoft Docs
description: Questo articolo offre una panoramica dettagliata degli agenti di Azure disponibili che supportano il monitoraggio delle macchine virtuali di Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: magoedte
ms.openlocfilehash: a399c3968e5ee1e2d1f6d623a68dbb1e15cef212
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37088455"
---
# <a name="overview-of-the-azure-agents-to-monitor-azure-virtual-machines"></a>Panoramica degli agenti di Azure per monitorare macchine virtuali di Azure
In Microsoft Azure sono disponibili più modi per raccogliere tipi diversi di dati dalle macchine virtuali ospitate in Azure o in altri provider cloud che eseguono Microsoft Windows e Linux.  Questo articolo offre una descrizione delle differenze e delle funzionalità disponibili per ogni agente affinché sia possibile determinare quale agente supporterà la gestione del servizio o i requisiti generali di monitoraggio.  

## <a name="comparing-azure-diagnostic-and-log-analytics-agent"></a>Confronto tra l'agente Diagnostica di Azure e l'agente di Log Analytics
Attualmente Azure offre due tipi di agente per monitorare una macchina virtuale di Azure: l'estensione Diagnostica di Azure e l'agente di Log Analytics per Linux e Windows.  Questi agenti sono fondamentalmente progettati per raccogliere metriche e log da inoltrare a un repository. Qui però finiscono le similitudini.  

L'[estensione Diagnostica di Azure](../monitoring-and-diagnostics/azure-diagnostics.md), introdotta per Servizi cloud di Azure dopo essere divenuta disponibile a livello generale nel 2010, è un agente che raccoglie semplicemente i dati di diagnostica da una risorsa IaaS di Azure, ad esempio una macchina virtuale, e li salva in modo permanente nell'archiviazione di Azure.  Dopo averli archiviati, i dati possono essere visualizzati usando uno dei numerosi strumenti disponibili, ad esempio [Esplora Server in Visual Studio](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md) e [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

È possibile scegliere di raccogliere gli elementi seguenti:

* Un set predefinito di contatori delle prestazioni del sistema operativo e di log eventi oppure è possibile specificare che cosa raccogliere. 
* Tutte le richieste e/o le richieste non riuscite a un server Web IIS
* Log di output di traccia dell'app .NET
* Traccia di eventi per Event Tracing for Windows (ETW) 
* Eventi del log da syslog  
* Dump di arresto anomalo del sistema 

In alternativa, i dati possono essere inoltrati ad [Application Insights](../application-insights/app-insights-cloudservices.md), [Log Analytics](../log-analytics/log-analytics-overview.md) o a servizi non Azure tramite l'[hub eventi](../event-hubs/event-hubs-what-is-event-hubs.md). 

Per un monitoraggio avanzato in cui non è sufficiente raccogliere metriche e un sottoinsieme di log, è necessario l'agente di Log Analytics per Windows e Linux.  Questo agente consente di usare i servizi di Azure, ad esempio Automazione e Log Analytics (incluso il set completo delle funzionalità offerte) per gestire in modo completo le macchine virtuali di Azure durante il loro ciclo di vita. Sono inclusi:

* [Gestione aggiornamenti di Automazione di Azure](../automation/automation-update-management.md) degli aggiornamenti del sistema operativo
* [Desired State Configuration di Automazione di Azure](../automation/automation-dsc-overview.md) per mantenere lo stato di configurazione coerente
* [Rilevamento modifiche e inventario di Automazione di Azure](../automation/automation-change-tracking.md) per tenere traccia delle modifiche di configurazione
* Raccolta di log personalizzati dal sistema operativo e da applicazioni ospitate, ad esempio [FluentD](../log-analytics/log-analytics-data-sources-json.md), [log personalizzati](../log-analytics/log-analytics-data-sources-custom-logs.md), [MySQL e Apache](../log-analytics/log-analytics-data-sources-linux-applications.md), usando Log Analytics
* I servizi di Azure come [Application Insights](https://docs.microsoft.com/azure/application-insights/) e il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/) archiviano i relativi dati direttamente in Log Analytics in modo nativo.  

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Raccogliere dati dai computer nell'ambiente con Log Analytics](../log-analytics/log-analytics-concept-hybrid.md) per rivedere i requisiti e i metodi disponibili per distribuire l'agente ai computer nel data center o in un altro ambiente cloud.
- Vedere [Raccogliere dati sulle macchine virtuali di Azure](../log-analytics/log-analytics-quick-collect-azurevm.md) per configurare la raccolta di dati dalle macchine virtuali di Azure. 