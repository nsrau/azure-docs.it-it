---
title: Usare l'archiviazione BLOB per IIS e l'archiviazione tabelle per gli eventi in Monitoraggio di Azure. Documenti Microsoft
description: Monitoraggio di Azure può leggere i log per i servizi di Azure che scrivono la diagnostica nell'archiviazione tabelle o nei log di IIS scritti nell'archiviazione BLOB.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 44368ab90abd189c6a8a0792494828c87142eb20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672396"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Raccogliere dati dall'estensione di diagnostica di Azure ai log di Monitoraggio di AzureCollect data from Azure diagnostics extension to Azure Monitor Logs
L'estensione di diagnostica di Azure è un [agente in Monitoraggio di Azure](agents-overview.md) che raccoglie i dati di monitoraggio dal sistema operativo guest delle risorse di calcolo di Azure, incluse le macchine virtuali. Questo articolo descrive come raccogliere i dati raccolti dall'estensione di diagnostica da Archiviazione di Azure ai log di Monitoraggio di Azure.This article describes how to collect data collected by the diagnostics extension from Azure Storage to Azure Monitor Logs.

> [!NOTE]
> The Log Analytics agent in Azure Monitor is typically the preferred method to collect data from the guest operating system into Azure Monitor Logs. Per un confronto dettagliato degli agenti, vedere [Panoramica degli agenti di Monitoraggio di Azure.See Overview of the Azure Monitor agents](agents-overview.md) for a detailed comparison of the agents.

## <a name="supported-data-types"></a>Tipi di dati supportati
L'estensione di diagnostica di Azure archivia i dati in un account di archiviazione di Azure.Azure diagnostics extension stores data in an Azure Storage account. Affinché i log di Monitoraggio di Azure raccolgano questi dati, questi devono trovarsi nelle posizioni seguenti:For Azure Monitor Logs to collect this data, it must be in the following locations:

| Tipo di log | Tipo di risorsa | Location |
| --- | --- | --- |
| Log di IIS |Macchine virtuali <br> Ruoli Web <br> Ruoli di lavoro |wad-iis-logfiles (archivio BLOB) |
| syslog |Macchine virtuali |LinuxsyslogVer2v0 (archivio tabelle) |
| Eventi operativi di Service Fabric |Nodi di Service Fabric |WADServiceFabricSystemEventTable |
| Eventi di Reliable Actor di Service Fabric |Nodi di Service Fabric |WADServiceFabricReliableActorEventTable |
| Eventi di Reliable Service di Service Fabric |Nodi di Service Fabric |WADServiceFabricReliableServiceEventTable |
| Log eventi di Windows |Nodi di Service Fabric <br> Macchine virtuali <br> Ruoli Web <br> Ruoli di lavoro |WADWindowsEventLogsTable (archivio tabelle) |
| Log di Windows ETW |Nodi di Service Fabric <br> Macchine virtuali <br> Ruoli Web <br> Ruoli di lavoro |WADETWEventTable (archivio tabelle) |

## <a name="data-types-not-supported"></a>Tipi di dati non supportati

- Dati sulle prestazioni del sistema operativo guest
- Log Di IIS da siti Web di AzureIIS logs from Azure websites


## <a name="enable-azure-diagnostics-extension"></a>Abilitare l'estensione di diagnostica di AzureEnable Azure diagnostics extension
Per informazioni dettagliate sull'installazione e la configurazione dell'estensione di diagnostica, vedere [Installare e configurare l'estensione](diagnostics-extension-windows-install.md) di diagnostica di Windows Azure o [Usare l'estensione di diagnostica Linux per monitorare le metriche e i log.](../../virtual-machines/extensions/diagnostics-linux.md) In questo modo sarà possibile specificare l'account di archiviazione e configurare la raccolta dei dati che si desidera inoltrare ai log di Monitoraggio di Azure.This will alow you will alow you to specify the storage account and to configure collection of the data that you want to forward to Azure Monitor Logs.


## <a name="collect-logs-from-azure-storage"></a>Raccogliere log da Archiviazione di AzureCollect logs from Azure Storage
Usare la procedura seguente per abilitare la raccolta dei dati di estensione di diagnostica da un account di archiviazione di Azure:Use the following procedure to enable collection of diagnostics extension data from an Azure Storage account:

1. Nel portale di Azure passare a **Aree** di lavoro di Log Analytics e selezionare l'area di lavoro.
1. Fare clic su **Registri account di archiviazione** nella sezione Origini dati area di **lavoro** del menu.
2. Fare clic su **Aggiungi**.
3. Selezionare **l'account di archiviazione** che contiene i dati da raccogliere.
4. Selezionare il **tipo di dati** che si desidera raccogliere.
5. Il valore per Source viene popolato automaticamente in base al tipo di dati.
6. Fare clic su **OK** per salvare la configurazione.
7. Ripetere l'operazione per altri tipi di dati.

In circa 30 minuti, è possibile visualizzare i dati dell'account di archiviazione nell'area di lavoro di Log Analytics.In approximately 30 minutes, you can see data from the storage account in the Log Analytics workspace. Verranno visualizzati solo i dati scritti nell'archivio dopo l'applicazione della configurazione. L'area di lavoro non legge i dati preesistenti dall'account di archiviazione.

> [!NOTE]
> Il portale non convalida l'esito dell'origine nell'account di archiviazione o se vengono scritti nuovi dati.



## <a name="next-steps"></a>Passaggi successivi

* [Raccogliere i log e le metriche per i servizi di Azure](collect-azure-metrics-logs.md) per i servizi supportati di Azure.
* [Abilitare soluzioni](../../azure-monitor/insights/solutions.md) per fornire informazioni dettagliate sui dati.
* [Usare query di ricerca](../../azure-monitor/log-query/log-query-overview.md) per analizzare i dati.
