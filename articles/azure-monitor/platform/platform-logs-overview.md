---
title: Panoramica dei log della piattaforma Azure Documenti Microsoft
description: Panoramica dei log in Monitoraggio di Azure che forniscono dati frequenti e avanzati sul funzionamento di una risorsa di Azure.Overview of logs in Azure Monitor which provide rich, frequent data about the operation of an Azure resource.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 89de6b3737c8a1e91832aba8f749078806b64e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659321"
---
# <a name="overview-of-azure-platform-logs"></a>Panoramica dei log della piattaforma di Azure
I log della piattaforma forniscono informazioni dettagliate sulla diagnostica e sul controllo per le risorse di Azure e la piattaforma di Azure da cui dipendono. Vengono generati automaticamente, anche se è necessario configurare determinati log della piattaforma da inoltrare a una o più destinazioni da conservare. Questo articolo fornisce una panoramica dei log della piattaforma, incluse le informazioni che forniscono e come è possibile configurarle per la raccolta e l'analisi.

## <a name="types-of-platform-logs"></a>Tipi di registri della piattaforma
The following table lists the specific platform logs that are available at different layers of Azure.

| File di log | Livello | Descrizione |
|:---|:---|:---|
| Log risorse | Risorse di Azure | Fornire informazioni dettagliate sulle operazioni eseguite all'interno di una risorsa di Azure (il *piano dati*), ad esempio per ottenere un segreto da un insieme di credenziali delle chiavi o effettuare una richiesta a un database. Il contenuto dei log delle risorse varia in base al servizio e al tipo di risorsa di Azure.The content of resource logs varies by the Azure service and resource type.<br><br>*I log delle risorse sono stati precedentemente denominati log di diagnostica.*  |
| Log attività | Sottoscrizione di Azure | Fornisce informazioni dettagliate sulle operazioni su ogni risorsa di Azure nella sottoscrizione dall'esterno (*il piano*di gestione ) oltre agli aggiornamenti sugli eventi di integrità del servizio. Utilizzare il Registro attività per determinare _cosa,_ _chi_e _quando_ per le operazioni di scrittura (PUT, POST, DELETE) eseguite sulle risorse della sottoscrizione. Consente inoltre di comprendere lo stato dell'operazione e altre proprietà specifiche.  Esiste un singolo log attività per ogni sottoscrizione di Azure.There is a single Activity log for each Azure subscription. |
| Log di Azure Active DirectoryAzure Active Directory logs | Tenant di Azure |  Contiene la cronologia dell'attività di accesso e l'audit trail delle modifiche apportate in Azure Active Directory per un tenant specifico. Vedere Che cosa sono i report di Azure Active Directory? per una descrizione completa dei log di Azure Active Directory.See [What are Azure Active Directory reports?](../../active-directory/reports-monitoring/overview-reports.md) for a complete description of Azure Active Directory Logs.   |

> [!NOTE]
> Il log attività di Azure è destinato principalmente alle attività che si verificano in Azure Resource Manager. Non tiene traccia delle risorse che usano il modello classico/RDFE. Alcuni tipi di risorse classiche dispongono di un provider di risorse proxy in Azure Resource Manager (ad esempio, Microsoft.ClassicCompute). Se un utente interagisce con un tipo di risorsa classica tramite Azure Resource Manager con questi provider di risorse di proxy, le operazioni verranno visualizzate nel log attività. Se un utente interagisce con un tipo di risorsa classica all'esterno dei proxy di Azure Resource Manager, le azioni dell'utente verranno registrate solo nel log delle operazioni. È possibile esaminare il log delle operazioni in una sezione distinta del portale.

![Panoramica dei log della piattaforma](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Visualizzazione dei registri della piattaforma
Sono disponibili diverse opzioni per la visualizzazione e l'analisi dei diversi log della piattaforma Azure.There are different options for viewing and analyzing the different Azure platform logs.

- Visualizzare il log attività nel portale di Azure e accedere agli eventi da PowerShell e l'interfaccia della riga di comando. Per informazioni dettagliate, vedere Visualizzare e recuperare gli eventi del log attività di [Azure.See View and retrieve Azure Activity log events](activity-log-view.md) for details. 
- Visualizzare i report sulla sicurezza e l'attività di Azure Active Directory nel portale di Azure.View Azure Active Directory Security and Activity reports in the Azure portal. Vedere Che cosa sono i report di [Azure Active Directory?](../../active-directory/reports-monitoring/overview-reports.md)  per informazioni dettagliate.
- I log delle risorse vengono generati automaticamente dalle risorse di Azure supportate, ma non sono disponibili per la visualizzazione a meno che non vengano inviati a una [destinazione.](#destinations) 

## <a name="destinations"></a>Destinations
È possibile inviare i log della piattaforma a una o più destinazioni nella tabella seguente a seconda dei requisiti di monitoraggio. Configurare le destinazioni per i log della piattaforma [creando un'impostazione di diagnostica](diagnostic-settings.md).

| Destination | Scenario | Riferimenti |
|:---|:---|:---|:---|
| Area di lavoro Log Analytics | Analizzare i log con altri dati di monitoraggio e sfruttare le funzionalità di Monitoraggio di Azure, ad esempio query di log e avvisi. | [Log attività e log delle risorse](resource-logs-collect-workspace.md)<br>[Log della directory attività di AzureAzure Activity Directory logs](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Archiviazione di Azure | Archiviare i log per il controllo, l'analisi statica o il backup. |[Log attività e log delle risorse](archive-diagnostic-logs.md)<br>[Log della directory attività di AzureAzure Activity Directory logs](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Hub eventi | Trasmettere i log a sistemi di telemetria e registrazione di terze parti.  |[Log attività e log delle risorse](resource-logs-stream-event-hubs.md)<br>[Log della directory attività di AzureAzure Activity Directory logs](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>Passaggi successivi

* [Visualizzare lo schema del log attività per diverse categorieView the Activity log schema for different categories](activity-log-schema.md)
* [Visualizzare lo schema del log delle risorse per diversi servizi di AzureView the resource log schema for different Azure services](diagnostic-logs-schema.md)
