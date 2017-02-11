---
title: Panoramica della raccolta dei dati di Archiviazione di Azure in Log Analytics | Documentazione Microsoft
description: "Le risorse di Azure possono scrivere log e metriche in un account di archiviazione di Azure, in genere usando Diagnostica di Azure. Log Analytics può indicizzare tali dati e renderli disponibili per la ricerca."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2a160030ab51799199fc6df08133f811d4987feb


---
# <a name="collecting-azure-storage-data-in-log-analytics-overview"></a>Panoramica della raccolta dei dati di Archiviazione di Azure in Log Analytics
Molte risorse di Azure possono scrivere log e metriche in un account di archiviazione di Azure. Log Analytics può utilizzare tali dati e facilitare il monitoraggio delle risorse di Azure.

Per scrivere in Archiviazione di Azure, una risorsa può usare Diagnostica di Azure oppure una propria modalità di scrittura dei dati. I dati possono essere scritti in vari formati in una delle posizioni seguenti:

* Tabella di Azure
* BLOB di Azure
* Hub eventi

Log Analytics supporta i servizi di Azure che scrivono dati usando [log di diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Log Analytics supporta inoltre altri servizi che restituiscono log e metriche in diversi formati e posizioni.  

> [!NOTE]
> Quando si inviano dati di diagnostica a un account di archiviazione e quando Log Analytics legge i dati da tale account, vengono addebitate le normali tariffe dati di Azure per l'archiviazione e le transazioni.
> 
> 

![Diagramma di Archiviazione di Azure](media/log-analytics-azure-storage/azure-storage-diagram.png)

## <a name="supported-azure-resources"></a>Risorse di Azure supportate
Log Analytics può raccogliere dati per le risorse di Azure seguenti:

| Tipo di risorsa | Log (categorie di diagnostica) | Soluzione di Log Analytics |
| --- | --- | --- |
| Application Insights |Disponibilità <br> Eventi personalizzati <br> Eccezioni <br> Requests <br> |Application Insights (anteprima) |
| Automazione <br> Microsoft.Automation/AutomationAccounts |JobLogs <br> JobStreams |AzureAutomation (anteprima) |
| Insieme di credenziali di chiave <br> Microsoft.KeyVault/Vaults |AuditEvent |KeyVault (anteprima) |
| gateway applicazione <br> Microsoft.Network/ApplicationGateways |ApplicationGatewayAccessLog <br> ApplicationGatewayPerformanceLog |AzureNetworking (anteprima) |
| Gruppo di sicurezza di rete <br> Microsoft.Network/NetworkSecurityGroups |NetworkSecurityGroupEvent <br> NetworkSecurityGroupRuleCounter |AzureNetworking (anteprima) |
| Service Fabric |ETWEvent <br> Evento operativo <br> Evento Reliable Actor <br> Evento Reliable Service |ServiceFabric (anteprima) |
| Macchine virtuali |Syslog Linux <br> Evento Windows <br> Log IIS <br> ETWEvent Windows |*nessuna* |
| Ruoli Web <br> Ruoli di lavoro |Syslog Linux <br> Evento Windows <br> Log IIS <br> ETWEvent Windows |*nessuna* |

> [!NOTE]
> Per monitorare le macchine virtuali di Azure (sia Linux che Windows), è consigliabile installare l'[estensione macchina virtuale di Log Analytics](log-analytics-azure-vm-extension.md). L'agente offre informazioni più dettagliate sulle macchine virtuali rispetto ai dati di diagnostica scritti in Archiviazione.
> 
> 

È possibile classificare in ordine di priorità i log da aggiungere a quelli supportati per l'analisi in OMS nella pagina dei [commenti e suggerimenti](http://feedback.azure.com/forums/267889-azure-log-analytics/category/88086-log-management-and-log-collection-policy).

* Vedere [Analizzare i log di diagnostica di Azure con Log Analytics](log-analytics-azure-storage-json.md) per altre informazioni sulle funzionalità di Log Analytics per la lettura dei log dei servizi di Azure che supportano i [log di diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md):
  * insieme di credenziali chiave
  * Automazione di Azure
  * gateway applicazione
  * Gruppi di sicurezza di rete
* Vedere [Use blob storage for IIS and table storage for events](log-analytics-azure-storage-iis-table.md) (Usare l'archiviazione BLOB per IIS e l'archiviazione tabelle per gli eventi) per informazioni sulle funzionalità di Log Analytics per la lettura dei log dei servizi di Azure che scrivono dati di diagnostica in un archivio tabelle o dei log IIS scritti in un archivio BLOB:
  * Service Fabric
  * Ruoli Web
  * Ruoli di lavoro
  * Macchine virtuali

## <a name="next-steps"></a>Passaggi successivi
* [Analizzare i log di diagnostica di Azure con Log Analytics](log-analytics-azure-storage-json.md) per leggere i log dei servizi di Azure che scrivono dati di diagnostica in un archivio BLOB in formato JSON.
* [Usare l'archiviazione BLOB per IIS e l'archiviazione tabelle per gli eventi](log-analytics-azure-storage-iis-table.md) per leggere i log dei servizi di Azure che scrivono dati di diagnostica in un archivio tabelle o i log IIS scritti in un archivio BLOB.
* [Abilitare soluzioni](log-analytics-add-solutions.md) per fornire informazioni dettagliate sui dati.
* [Usare query di ricerca](log-analytics-log-searches.md) per analizzare i dati.




<!--HONumber=Nov16_HO3-->


