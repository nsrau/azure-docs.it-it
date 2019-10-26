---
title: Inventario delle soluzioni di monitoraggio in Azure | Microsoft Docs
description: Le soluzioni di monitoraggio in Monitoraggio di Azure sono una raccolta di regole logiche, di visualizzazione e di acquisizione dei dati che forniscono metriche relative a un'area problematica specifica.  Questo articolo fornisce un elenco delle soluzioni di monitoraggio disponibili in Microsoft e i dettagli relativi al metodo e alla frequenza della raccolta dei dati.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: article
author: MGoedtel
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: 1f93ada1a6fb6b87c79a3713e80ebfefae9fe56f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900643"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Dettagli sull'inventario e sulla raccolta dei dati per le soluzioni di monitoraggio in Azure
Le [soluzioni di monitoraggio](solutions.md) sfruttano i servizi in Azure per fornire informazioni aggiuntive sul funzionamento di un'applicazione o un servizio specifico. Le soluzioni di monitoraggio raccolgono in genere log di dati e offrono query e visualizzazioni per l'analisi dei dati raccolti. È possibile aggiungere soluzioni di monitoraggio a Monitoraggio di Azure per qualsiasi applicazione e servizio usati. Sono in genere disponibili gratuitamente, ma la raccolta dati potrebbe implicare addebiti per l'utilizzo.

Questo articolo include un elenco di [soluzioni Montioring](solutions.md) disponibili da Microsoft con collegamenti alla relativa documentazione dettagliata.  Sono inoltre disponibili informazioni sul metodo e la frequenza della raccolta dati in Monitoraggio di Azure.  È possibile utilizzare le informazioni contenute in questo articolo per identificare le diverse soluzioni disponibili e per comprendere i requisiti del flusso di dati e della connessione per diverse soluzioni di monitoraggio.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Elenco delle soluzioni di monitoraggio

La tabella seguente elenca le [soluzioni di monitoraggio](solutions.md) in Azure fornite da Microsoft. Un punto nella colonna indica che la soluzione raccoglie i dati in Monitoraggio di Azure usando il metodo corrispondente.  Se per una soluzione non è selezionata alcuna colonna, questo indica che scrive direttamente in Monitoraggio di Azure da un altro servizio di Azure. Fare clic sul collegamento per ognuna per visualizzare altre informazioni nella relativa documentazione dettagliata.

L'elenco seguente include le spiegazioni per le colonne:

- **Microsoft Monitoring Agent** -Agent usato in Windows e Linux per eseguire il Management Pack dalle soluzioni SCOM e di monitoraggio di Azure. In questa configurazione, l'agente è connesso direttamente a Monitoraggio di Azure senza essere connesso a un gruppo di gestione di Operations Manager. 
- **Operations Manager** - Stesso agente di Microsoft Monitoring Agent. In questa configurazione è [connesso a un gruppo di gestione di Operations Manager](../platform/om-agents.md) a sua volta connesso a Monitoraggio di Azure. 
-  **Archiviazione di Azure** - La soluzione raccoglie i dati da un account di Archiviazione di Azure. 
- **È necessario Operations Manager?** -Un gruppo di gestione Operations Manager connesso è necessario per la raccolta dei dati da parte della soluzione di monitoraggio. 
- **Dati dell'agente di Operations Manager inviati tramite il gruppo di gestione** - Se l'agente è [connesso a un gruppo di gestione SCOM](../platform/om-agents.md), i dati vengono inviati a Monitoraggio di Azure dal server di gestione. In questo caso, l'agente non deve connettersi direttamente a Monitoraggio di Azure. Se questa casella non è selezionata, i dati vengono inviati dall'agente direttamente a Monitoraggio di Azure anche se l'agente è connesso a un gruppo di gestione di SCOM. Dovrà essere in grado di comunicare con Monitoraggio di Azure tramite il [gateway di Log Analytics](../platform/gateway.md).
- **Frequenza di raccolta** : specifica la frequenza con cui i dati vengono raccolti dalla soluzione di monitoraggio. 



| **Soluzione di monitoraggio** | **Piattaforma** | **Microsoft Monitoring Agent** | **Agente di Operations Manager** | **Archiviazione di Azure** | **È necessario Operations Manager?** | **Dati dell'agente di Operations Manager inviati tramite il gruppo di gestione** | **Frequenza di raccolta** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Analisi log attività](../platform/activity-log-collect.md) | Azure | | | | | | su notifica |
| [Valutazione di AD](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 giorni |
| [Stato della replica di AD](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 giorni |
| [Integrità agente](solution-agenthealth.md) | Windows e Linux | &#8226; | &#8226; | | | &#8226; | 1 minuto |
| [Alert Management](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |All'arrivo |
| [Alert Management](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 minuto |
| [Gestione avvisi](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuti |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | N/D |
| [Connettore di Application Insights (Deprecato)](../platform/app-insights-connector.md) | Azure | | | |  |  | su notifica |
| [Ruolo di lavoro ibrido per runbook di Automazione](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | N/D |
| [Analisi dei gateway applicazione di Azure](azure-networking-analytics.md) | Azure |  |  |  |  |  | su notifica |
| **Soluzione di monitoraggio** | **Piattaforma** | **Microsoft Monitoring Agent** | **Agente di Operations Manager** | **Archiviazione di Azure** | **È necessario Operations Manager?** | **Dati dell'agente di Operations Manager inviati tramite il gruppo di gestione** | **Frequenza di raccolta** |
| [Analisi gruppo di sicurezza di rete di Azure](azure-networking-analytics.md) | Azure |  |  |  |  |  | su notifica |
| [Azure SQL Analytics (Anteprima)](azure-sql.md) | Windows | | | | | | 1 minuto |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | su notifica |
| [Capacità e prestazioni (anteprima)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |All'arrivo |
| [Rilevamento delle modifiche](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[varia](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Rilevamento delle modifiche](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[varia](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Contenitori](containers.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 3 minuti |
| [Analisi dell'insieme di credenziali delle chiavi](azure-key-vault.md) |Windows | | | | | |su notifica |
| [Valutazione di malware](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |Ogni ora |
| [Monitoraggio delle prestazioni di rete](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP esegue handshake ogni 5 secondi e i dati vengono inviati ogni 3 minuti |
| [Analisi Office 365 (anteprima)](solution-office-365.md) |Windows | | | | | |su notifica |
| **Soluzione di monitoraggio** | **Piattaforma** | **Microsoft Monitoring Agent** | **Agente di Operations Manager** | **Archiviazione di Azure** | **È necessario Operations Manager?** | **Dati dell'agente di Operations Manager inviati tramite il gruppo di gestione** | **Frequenza di raccolta** |
| [Analisi di Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minuti |
| [Elenco dei servizi](service-map.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 5 secondi |
| [SQL Assessment](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 giorni |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |All'arrivo |
| [Valutazione System Center Operations Manager (anteprima)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | 7 giorni |
| [Gestione degli aggiornamenti](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |almeno 2 volte al giorno e 15 minuti dopo l'installazione di un aggiornamento |
| [Preparazione dell'aggiornamento](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 giorni |
| [Monitoraggio VMware (deprecato)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuti |
| [Wire Data 2.0 (anteprima)](wire-data.md) |Windows (2012 R2/8.1 o versioni successive) |&#8226; |&#8226; | | | | 1 minuto |




## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [installare e usare le soluzioni di monitoraggio](solutions.md).
* Informazioni su come [creare query](../log-query/log-query-overview.md) per analizzare i dati raccolti dalle soluzioni di monitoraggio.
