---
title: Panoramica degli agenti di monitoraggio di Azure | Microsoft Docs
description: Questo articolo offre una panoramica dettagliata degli agenti disponibili di Azure che supportano il monitoraggio delle macchine virtuali ospitate in Azure o in un ambiente ibrido.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: magoedte
ms.openlocfilehash: 89e73a4578134493a25ec3cbd2385433a2b36156
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775881"
---
# <a name="overview-of-the-azure-monitoring-agents"></a>Panoramica degli agenti di monitoraggio di Azure 
In Microsoft Azure sono disponibili più modi per raccogliere tipi diversi di dati dalle macchine virtuali che eseguono Microsoft Windows e Linux ospitate in Azure, nel data center o in altri provider cloud. I tipi di agenti disponibili per monitorare una macchina virtuale sono tre:

* Estensioni Diagnostica di Azure
* Agente di Log Analytics per Linux e Windows
* Dependency Agent

Questo articolo illustra le differenze tra gli agenti e le relative funzionalità per consentire di determinare quale agente supporterà la gestione del servizio IT o i requisiti generali di monitoraggio.  

## <a name="azure-diagnostic-extension"></a>Estensione Diagnostica di Azure
L'[estensione Diagnostica di Azure](../../azure-monitor/platform/diagnostics-extension-overview.md), definita comunemente Diagnostica di Microsoft Azure (WAD) or Linux Azure Diagnostic (LAD) e introdotta per Servizi cloud di Azure dopo essere divenuta disponibile a livello generale nel 2010, è un agente che raccoglie semplicemente i dati di diagnostica da una risorsa di calcolo di Azure, ad esempio una macchina virtuale, e li salva in modo permanente nell'archiviazione di Azure. Dopo averli archiviati, i dati possono essere visualizzati usando uno dei numerosi strumenti disponibili, ad esempio [Esplora Server in Visual Studio](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) e [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

È possibile scegliere di raccogliere gli elementi seguenti:

* Un set predefinito di contatori delle prestazioni del sistema operativo e di log eventi oppure è possibile specificare che cosa raccogliere. 
* Tutte le richieste e/o le richieste non riuscite a un server Web IIS
* Log di output di traccia dell'app .NET
* Traccia di eventi per Event Tracing for Windows (ETW) 
* Eventi del log da syslog  
* Dump di arresto anomalo del sistema 

L'agente di Diagnostica di Azure deve essere usato quando si vuole:

* Archiviare log e metriche in archiviazione di Azure.
* Integrare i dati di monitoraggio con strumenti di terze parti. Questi strumenti usano un'ampia gamma di metodi, tra cui l'esecuzione di query sull'account di archiviazione con l'inoltro ad [Hub eventi](../../event-hubs/event-hubs-about.md) o l'esecuzione di query con l'[API REST Monitoraggio di Azure](../../azure-monitor/platform/rest-api-walkthrough.md).
* Caricare i dati in Monitoraggio di Azure per creare grafici delle metriche nel portale di Azure o [avvisi delle metriche](../../azure-monitor/platform/alerts-metric-overview.md) near real-time. 
* Ridimensionare automaticamente i set di scalabilità di macchine virtuali e la versione classica di Servizi cloud in base alle metriche del sistema operativo guest.
* Esaminare i problemi di avvio delle macchine virtuali con [Diagnostica di avvio](../../virtual-machines/troubleshooting/boot-diagnostics.md).
* Comprendere quali sono le prestazioni fornite dalle applicazioni e identificare in modo proattivo i problemi di tali applicazioni con [Application Insights](../../azure-monitor/overview.md).
* Configurare monitoraggio di Azure per importare le metriche e registrare i dati raccolti dai servizi Cloud, macchine virtuali classiche, e i nodi di Service Fabric archiviati in un account di archiviazione di Azure.

## <a name="log-analytics-agent"></a>Agente di Log Analytics
Per il monitoraggio avanzato in cui è necessario raccogliere più di un subset dei log e metriche, è necessario l'agente di Log Analitica per Linux e Windows (noto anche come Microsoft Monitoring Agent (MMA)). L'agente di Log Analytics è stato sviluppato per la gestione completa di tutti i server fisici e le macchine virtuali locali, dei computer monitorati da System Center Operations Manager e delle macchine virtuali ospitate in altri cloud. Gli agenti Windows e Linux connettono a un'area di lavoro di Log Analitica in Monitoraggio di Azure per raccogliere i dati basati su soluzioni di monitoraggio nonché a origini dati personalizzate che configuri.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

L'agente di Log Analytics deve essere usato quando si vuole:

* Raccogliere dati da un'ampia gamma di origini disponibili all'interno di Azure, presso altri provider di servizi cloud e in risorse locali. 
* Usare una delle soluzioni di Monitoraggio di Azure, ad esempio [Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md), [Monitoraggio di Azure per i contenitori](../insights/container-insights-overview.md) e così via.  
* Usare uno degli altri servizi di gestione di Azure, ad esempio [Centro sicurezza di Azure](../../security-center/security-center-intro.md), [Automazione di Azure](../../automation/automation-intro.md) e così via.

In precedenza, alcuni servizi di Azure sono stati aggregati in *Operations Management Suite* e, di conseguenza, l'agente di Log Analytics è condiviso tra diversi servizi, tra cui Centro sicurezza di Azure e Automazione di Azure.  Questa integrazione include il set completo delle funzionalità offerte, per una gestione completa delle macchine virtuali di Azure per l'intero ciclo di vita.  Di seguito sono riportati alcuni esempi:

* [Gestione aggiornamenti di Automazione di Azure](../../automation/automation-update-management.md) per gli aggiornamenti del sistema operativo.
* [Desired State Configuration di Automazione di Azure](../../automation/automation-dsc-overview.md) per mantenere lo stato di coerenza della configurazione.
* [Rilevamento modifiche e inventario di Automazione di Azure](../../automation/automation-change-tracking.md) per tenere traccia delle modifiche di configurazione.
* I servizi di Azure, ad esempio [Application Insights](https://docs.microsoft.com/azure/application-insights/) e [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/), che archiviano i dati direttamente in Log Analytics in modo nativo.  

## <a name="dependency-agent"></a>Dependency Agent
Dependency agent è stata sviluppata come parte della soluzione Service Map, che non è stato originariamente sviluppata da Microsoft. [Service Map](../insights/service-map.md) e [monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md) richiede un agente di dipendenza in Windows e Linux macchine virtuali e si integra con l'agente di Log Analitica per raccogliere i dati individuati sui processi in esecuzione nel disco computer e le dipendenze di processo esterno. Archivia questi dati in un'area di lavoro di Log Analitica e visualizza i componenti interconnessi individuati.

Potrebbe essere necessaria una combinazione di questi agenti per monitorare la macchina virtuale. Gli agenti possono essere installati side-by-side come estensioni di Azure. Tuttavia, in Linux l'agente di Log Analytics *deve* essere installato per primo, altrimenti l'installazione avrà esito negativo. 

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Overview of the Log Analytics agent](../../azure-monitor/platform/log-analytics-agent.md) (Panoramica dell'agente di Log Analytics) per esaminare i requisiti e i metodi supportati per distribuire l'agente nei computer ospitati in Azure, nel data center o in un altro ambiente cloud.

