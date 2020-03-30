---
title: Inventario delle soluzioni di monitoraggio in Azure Documenti Microsoft
description: Le soluzioni di monitoraggio in Monitoraggio di Azure sono una raccolta di regole logiche, di visualizzazione e di acquisizione dei dati che forniscono metriche relative a un'area problematica specifica.  In questo articolo viene fornito un elenco di soluzioni di monitoraggio disponibili da Microsoft e i dettagli sul metodo e la frequenza di raccolta dei dati.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: 7b88d957bce45bf518fc77584f1691de8010459a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663131"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Dettagli dell'inventario e della raccolta dati per il monitoraggio delle soluzioni in Azure
[Le soluzioni](solutions.md) di monitoraggio sfruttano i servizi in Azure per fornire informazioni aggiuntive sul funzionamento di una determinata applicazione o servizio. Le soluzioni di monitoraggio raccolgono in genere log di dati e offrono query e visualizzazioni per l'analisi dei dati raccolti. È possibile aggiungere soluzioni di monitoraggio a Monitoraggio di Azure per qualsiasi applicazione e servizio usati. Sono in genere disponibili gratuitamente, ma la raccolta dati potrebbe implicare addebiti per l'utilizzo.

Questo articolo include un elenco di [soluzioni di montaggio](solutions.md) disponibili da Microsoft con collegamenti alla relativa documentazione dettagliata.  Sono inoltre disponibili informazioni sul metodo e la frequenza della raccolta dati in Monitoraggio di Azure.  È possibile utilizzare le informazioni contenute in questo articolo per identificare le diverse soluzioni disponibili e per comprendere i requisiti di connessione e flusso di dati per le diverse soluzioni di monitoraggio.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Elenco delle soluzioni di monitoraggio

Nella tabella seguente sono elencate le [soluzioni](solutions.md) di monitoraggio in Azure fornite da Microsoft.The following table lists the monitoring solutions in Azure provided by Microsoft. Un punto nella colonna indica che la soluzione raccoglie i dati in Monitoraggio di Azure usando il metodo corrispondente.  Se per una soluzione non è selezionata alcuna colonna, questo indica che scrive direttamente in Monitoraggio di Azure da un altro servizio di Azure. Fare clic sul collegamento per ognuna per visualizzare altre informazioni nella relativa documentazione dettagliata.

L'elenco seguente include le spiegazioni per le colonne:

- **Agente** di monitoraggio Microsoft - Agente usato in Windows e Linux per eseguire management pack da SCOM e soluzioni di monitoraggio da Azure.Microsoft monitoring agent - Agent used on Windows and Linux to run managements pack from SCOM and monitoring solutions from Azure. In questa configurazione, l'agente è connesso direttamente a Monitoraggio di Azure senza essere connesso a un gruppo di gestione di Operations Manager. 
- **Operations Manager** - Stesso agente di Microsoft Monitoring Agent. In questa configurazione è [connesso a un gruppo di gestione di Operations Manager](../platform/om-agents.md) a sua volta connesso a Monitoraggio di Azure. 
-  **Archiviazione di Azure** - La soluzione raccoglie i dati da un account di Archiviazione di Azure. 
- **È necessario Operations Manager?** - Un gruppo di gestione connesso di Operations Manager è necessario per la raccolta dei dati dalla soluzione di monitoraggio. 
- **Dati dell'agente di Operations Manager inviati tramite il gruppo di gestione** - Se l'agente è [connesso a un gruppo di gestione SCOM](../platform/om-agents.md), i dati vengono inviati a Monitoraggio di Azure dal server di gestione. In questo caso, l'agente non deve connettersi direttamente a Monitoraggio di Azure. Se questa casella non è selezionata, i dati vengono inviati dall'agente direttamente a Monitoraggio di Azure anche se l'agente è connesso a un gruppo di gestione di SCOM. Dovrà essere in grado di comunicare con Monitoraggio di Azure tramite il [gateway di Log Analytics](../platform/gateway.md).
- **Frequenza di raccolta:** specifica la frequenza con cui i dati vengono raccolti dalla soluzione di monitoraggio. 



| **Soluzione di monitoraggio** | **Piattaforma** | **Agente di monitoraggio Microsoft** | **Agente di Operations Manager** | **Archiviazione di AzureAzure storage** | **È necessario Operations Manager?** | **Dati dell'agente Operations Manager inviati con il gruppo di gestione** | **Frequenza di raccolta** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Analisi del log attività](../platform/activity-log-collect.md) | Azure | | | | | | su notifica |
| [AD Assessment](ad-assessment.md) |WINDOWS |&#8226; |&#8226; | | |&#8226; |7 giorni |
| [Stato della replica di AD](ad-replication-status.md) |WINDOWS |&#8226; |&#8226; | | |&#8226; |5 giorni |
| [Integrità agente](solution-agenthealth.md) | Windows e Linux | &#8226; | &#8226; | | | &#8226; | 1 minuto |
| [Gestione degli avvisi](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |all'arrivo |
| [Gestione degli avvisi](../platform/alert-management-solution.md) (zobbix) |Linux |&#8226; | | | | |1 minuto |
| [Gestione avvisi](../platform/alert-management-solution.md) (Operations Manager) |WINDOWS | |&#8226; | |&#8226; |&#8226; |3 minuti |
| [Ripristino del sito di AzureAzure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | n/d |
| [Connettore di Application Insights (Deprecato)](../platform/app-insights-connector.md) | Azure | | | |  |  | su notifica |
| [Ruolo di lavoro ibrido per runbook di Automazione](../../automation/automation-hybrid-runbook-worker.md) | WINDOWS | &#8226; | &#8226; |  |  |  | n/d |
| [Analisi dei gateway applicazione di Azure](azure-networking-analytics.md) | Azure |  |  |  |  |  | su notifica |
| **Soluzione di monitoraggio** | **Piattaforma** | **Agente di monitoraggio Microsoft** | **Agente di Operations Manager** | **Archiviazione di AzureAzure storage** | **È necessario Operations Manager?** | **Dati dell'agente Operations Manager inviati con il gruppo di gestione** | **Frequenza di raccolta** |
| [Analisi gruppo di sicurezza di rete di Azure](azure-networking-analytics.md) | Azure |  |  |  |  |  | su notifica |
| [Azure SQL Analytics (Anteprima)](azure-sql.md) | WINDOWS | | | | | | 1 minuto |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | su notifica |
| [Capacità e prestazioni (anteprima)](capacity-performance.md) |WINDOWS |&#8226; |&#8226; | | |&#8226; |all'arrivo |
| [Rilevamento delle modifiche](../../automation/change-tracking.md) |WINDOWS |&#8226; |&#8226; | | |&#8226; |[Varia](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Rilevamento delle modifiche](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[Varia](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Contenitori](containers.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 3 minuti |
| [Analisi dell'insieme di credenziali delle chiavi](azure-key-vault.md) |WINDOWS | | | | | |su notifica |
| [Malware Assessment](../../security-center/security-center-install-endpoint-protection.md) |WINDOWS |&#8226; |&#8226; | | |&#8226; |Ogni ora |
| [Monitoraggio prestazioni rete](network-performance-monitor.md) | WINDOWS | &#8226; | &#8226; |  |  |  | TCP esegue handshake ogni 5 secondi e i dati vengono inviati ogni 3 minuti |
| [Office 365 Analytics (anteprima)](solution-office-365.md) |WINDOWS | | | | | |su notifica |
| **Soluzione di monitoraggio** | **Piattaforma** | **Agente di monitoraggio Microsoft** | **Agente di Operations Manager** | **Archiviazione di AzureAzure storage** | **È necessario Operations Manager?** | **Dati dell'agente Operations Manager inviati con il gruppo di gestione** | **Frequenza di raccolta** |
| [Service Fabric Analytics](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |WINDOWS | | |&#8226; | | |5 minuti |
| [Mappa del servizio](service-map.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 5 secondi |
| [SQL Assessment](sql-assessment.md) |WINDOWS |&#8226; |&#8226; | | |&#8226; |7 giorni |
| [SurfaceHub](surface-hubs.md) |WINDOWS |&#8226; | | | | |all'arrivo |
| [Valutazione System Center Operations Manager (anteprima)](scom-assessment.md) | WINDOWS | &#8226; | &#8226; |  |  | &#8226; | 7 giorni |
| [Gestione degli aggiornamenti](../../automation/automation-update-management.md) | WINDOWS |&#8226; |&#8226; | | |&#8226; |almeno 2 volte al giorno e 15 minuti dopo l'installazione di un aggiornamento |
| [Preparazione dell'aggiornamento](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | WINDOWS | &#8226; |  |  |  |  | 2 giorni |
| [Monitoraggio VMware (deprecato)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuti |
| [Wire Data 2.0 (anteprima)](wire-data.md) |Windows (2012 R2/8.1 o versioni successive) |&#8226; |&#8226; | | | | 1 minuto |




## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [installare e utilizzare le soluzioni](solutions.md)di monitoraggio.
* Informazioni su come [creare query](../log-query/log-query-overview.md) per analizzare i dati raccolti dalle soluzioni di monitoraggio.
