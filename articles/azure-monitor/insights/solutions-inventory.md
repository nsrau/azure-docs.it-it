---
title: Inventario delle soluzioni di monitoraggio in Azure | Microsoft Docs
description: Le soluzioni di monitoraggio in Monitoraggio di Azure sono una raccolta di regole logiche, di visualizzazione e di acquisizione dei dati che forniscono metriche relative a un'area problematica specifica.  Questo articolo fornisce un elenco delle soluzioni di monitoraggio disponibili in Microsoft e i dettagli relativi al metodo e alla frequenza della raccolta dei dati.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: b87179c79489bf781619b70b19ca8982f2e38dff
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509483"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Dettagli sull'inventario e sulla raccolta dei dati per le soluzioni di monitoraggio in Azure
Il [monitoraggio delle soluzioni](solutions.md) usa i servizi di Azure per acquisire familiarità con il funzionamento di un'applicazione o di un servizio specifico. Le soluzioni di monitoraggio raccolgono in genere log di dati e offrono query e visualizzazioni per l'analisi dei dati raccolti. È possibile aggiungere soluzioni di monitoraggio a Monitoraggio di Azure per qualsiasi applicazione e servizio usati. Sono in genere disponibili senza costi aggiuntivi, ma raccolgono dati che potrebbero richiamare costi di utilizzo.

Questo articolo include un elenco delle [soluzioni di monitoraggio](solutions.md) disponibili in Microsoft con i collegamenti alla relativa documentazione dettagliata.  Sono inoltre disponibili informazioni sul metodo e la frequenza della raccolta dati in Monitoraggio di Azure.  È possibile utilizzare le informazioni contenute in questo articolo per identificare le diverse soluzioni disponibili e per comprendere i requisiti del flusso di dati e della connessione per diverse soluzioni di monitoraggio.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Elenco delle soluzioni di monitoraggio

La tabella seguente elenca le [soluzioni di monitoraggio](solutions.md) in Azure fornite da Microsoft. Un punto nella colonna indica che la soluzione raccoglie i dati in Monitoraggio di Azure usando il metodo corrispondente.  Se per una soluzione non è selezionata alcuna colonna, questo indica che scrive direttamente in Monitoraggio di Azure da un altro servizio di Azure. Fare clic sul collegamento per ognuna per visualizzare altre informazioni nella relativa documentazione dettagliata.

L'elenco seguente include le spiegazioni per le colonne:

- **Microsoft Monitoring Agent** -Agent usato in Windows e Linux per eseguire Management Pack da Microsoft System Center-Operations Manager (OM) e soluzioni di monitoraggio da Azure. In questa configurazione, l'agente è connesso direttamente a Monitoraggio di Azure senza essere connesso a un gruppo di gestione di Operations Manager. 
- **Operations Manager** - Stesso agente di Microsoft Monitoring Agent. In questa configurazione è [connesso a un gruppo di gestione di Operations Manager](../platform/om-agents.md) a sua volta connesso a Monitoraggio di Azure. 
-  **Archiviazione di Azure** : la soluzione raccoglie i dati da un account di archiviazione di Azure. 
- **È necessario Operations Manager?** -Un gruppo di gestione Operations Manager connesso è necessario per la raccolta dei dati da parte della soluzione di monitoraggio. 
- **Operations Manager i dati dell'agente inviati tramite il gruppo di gestione** : se l'agente è [connesso a un gruppo di gestione OM](../platform/om-agents.md), i dati vengono inviati a monitoraggio di Azure dal server di gestione. In questo caso, l'agente non deve connettersi direttamente a Monitoraggio di Azure. Se questa casella non è selezionata, i dati vengono inviati direttamente dall'agente a monitoraggio di Azure anche se l'agente è connesso a un gruppo di gestione OM. Dovrà essere in grado di comunicare con Monitoraggio di Azure tramite il [gateway di Log Analytics](../platform/gateway.md).
- **Frequenza di raccolta** : specifica la frequenza con cui i dati vengono raccolti dalla soluzione di monitoraggio. 



| **Soluzione di monitoraggio** | **Platform** | **Microsoft Monitoring Agent** | **Agente di Operations Manager** | **Archiviazione di Azure** | **È necessario Operations Manager?** | **Dati dell'agente Operations Manager inviati con il gruppo di gestione** | **Frequenza di raccolta** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Analisi log attività](../platform/activity-log-collect.md) | Azure | | | | | | su notifica |
| [AD Assessment](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 giorni |
| [Stato della replica di AD](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 giorni |
| [Integrità agente](solution-agenthealth.md) | Windows e Linux | &#8226; | &#8226; | | | &#8226; | 1 minuto |
| [Gestione avvisi](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |all'arrivo |
| [Gestione avvisi](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 minuto |
| [Gestione avvisi](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuti |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | n/d |
| [Connettore di Application Insights (Deprecato)](../platform/app-insights-connector.md) | Azure | | | |  |  | su notifica |
| [Ruolo di lavoro ibrido per runbook di Automazione](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | n/d |
| [Analisi dei gateway applicazione di Azure](azure-networking-analytics.md) | Azure |  |  |  |  |  | su notifica |
| **Soluzione di monitoraggio** | **Platform** | **Microsoft Monitoring Agent** | **Agente di Operations Manager** | **Archiviazione di Azure** | **È necessario Operations Manager?** | **Dati dell'agente Operations Manager inviati con il gruppo di gestione** | **Frequenza di raccolta** |
| [Analisi gruppo di sicurezza di rete di Azure](azure-networking-analytics.md) | Azure |  |  |  |  |  | su notifica |
| [Azure SQL Analytics (Anteprima)](azure-sql.md) | Windows | | | | | | 1 minuto |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | su notifica |
| [Capacità e prestazioni (anteprima)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |all'arrivo |
| [Rilevamento delle modifiche](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[varia](../../automation/change-tracking.md#change-tracking-and-inventory-data-collection) |
| [Rilevamento delle modifiche](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[varia](../../automation/change-tracking.md#change-tracking-and-inventory-data-collection) |
| [Contenitori](containers.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 3 minuti |
| [Analisi dell'insieme di credenziali delle chiavi](azure-key-vault.md) |Windows | | | | | |su notifica |
| [Malware Assessment](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |Ogni ora |
| [Monitoraggio delle prestazioni di rete](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP esegue handshake ogni 5 secondi e i dati vengono inviati ogni 3 minuti |
| [Office 365 Analytics (anteprima)](solution-office-365.md) |Windows | | | | | |su notifica |
| **Soluzione di monitoraggio** | **Platform** | **Microsoft Monitoring Agent** | **Agente di Operations Manager** | **Archiviazione di Azure** | **È necessario Operations Manager?** | **Dati dell'agente Operations Manager inviati con il gruppo di gestione** | **Frequenza di raccolta** |
| [Service Fabric Analytics](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minuti |
| [Mapping dei servizi](service-map.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 5 secondi |
| [SQL Assessment](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 giorni |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |all'arrivo |
| [Valutazione System Center Operations Manager (anteprima)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | 7 giorni |
| [Gestione degli aggiornamenti](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |almeno due volte al giorno e 15 minuti dopo l'installazione di un aggiornamento |
| [Preparazione dell'aggiornamento](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 giorni |
| [Monitoraggio VMware (deprecato)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuti |
| [Wire Data 2.0 (anteprima)](wire-data.md) |Windows (2012 R2/8.1 o versioni successive) |&#8226; |&#8226; | | | | 1 minuto |




## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [installare e usare le soluzioni di monitoraggio](solutions.md).
* Informazioni su come [creare query](../log-query/log-query-overview.md) per analizzare i dati raccolti dalle soluzioni di monitoraggio.
