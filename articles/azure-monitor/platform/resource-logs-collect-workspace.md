---
title: Raccogliere i log delle risorse di Azure nell'area di lavoro Log Analytics
description: Informazioni su come trasmettere i log delle risorse di Azure a un'area di lavoro di Log Analytics in Monitoraggio di Azure.Learn how to stream Azure resource logs to a Log Analytics workspace in Azure Monitor.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 36bd464624118b7671a3879bcc1d34114bba9ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248593"
---
# <a name="collect-azure-platform-logs-in-log-analytics-workspace-in-azure-monitor"></a>Raccogliere i log della piattaforma Azure nell'area di lavoro di Log Analytics in Monitoraggio di AzureCollect Azure platform logs in Log Analytics workspace in Azure Monitor
[I log della piattaforma](platform-logs-overview.md) in Azure, inclusi i log attività di Azure e i log delle risorse, forniscono informazioni dettagliate su diagnostica e controllo per le risorse di Azure e la piattaforma di Azure da cui dipendono. Questo articolo descrive la raccolta dei log delle risorse in un'area di lavoro di Log Analytics che consente di analizzarla con altri dati di monitoraggio raccolti nei log di Monitoraggio di Azure usando potenti query di log e anche di sfruttare altre funzionalità di Monitoraggio di Azure, ad esempio avvisi e Visualizzazioni. 


## <a name="what-you-can-do-with-platform-logs-in-a-workspace"></a>Operazioni che è possibile eseguire con i log della piattaforma in un'area di lavoroWhat you can do with platform logs in a workspace
La raccolta dei log della piattaforma in un'area di lavoro di Log Analytics consente di analizzare insieme i log di tutte le risorse di Azure e di sfruttare tutte le funzionalità disponibili per i log di monitoraggio di [Azure,](data-platform-logs.md) incluse le funzionalità seguenti:Collecting platform logs into a Log Analytics workspace allows you to analyze the logs of all your Azure resources together and to take advantage of all the features available to Azure Monitor Logs which includes the following:

* **Query di** [log:](../log-query/log-query-overview.md) creare query di log usando un linguaggio di query potente per analizzare e ottenere rapidamente informazioni dettagliate sui dati di diagnostica e per analizzarle con i dati raccolti da altre origini in Monitoraggio di Azure.Log queries - Create log queries using a powerful query language to quickly analyze and gain insights into your diagnostic data, and to analyze it with data collected from other sources in Azure Monitor.
* Avvisi: ottenere la notifica proattiva delle condizioni e dei modelli critici identificati nei log delle risorse usando gli avvisi di log in Monitoraggio di Azure.Alerting - Get proactive notification of critical conditions and patterns identified in your resource logs using [log alerts in Azure Monitor](alerts-log.md). **Alerting**
* **Visualizzazioni:** aggiungere i risultati di una query di log a un dashboard di Azure o includerla in una cartella di lavoro come parte di un report interattivo.

## <a name="prerequisites"></a>Prerequisiti
È necessario [creare una nuova area di lavoro](../learn/quick-create-workspace.md) se non ne è già presente. Non è necessario che l'area di lavoro si tratti della stessa sottoscrizione della risorsa che invia i log, purché l'utente che configura l'impostazione disponga dell'accesso RBAC appropriato a entrambe le sottoscrizioni.

## <a name="create-a-diagnostic-setting"></a>Creare un'impostazione di diagnostica
Inviare i log della piattaforma a un'area di lavoro di Log Analytics e ad altre destinazioni creando un'impostazione di diagnostica per una risorsa di Azure.Send platform logs to a Log Analytics workspace and other destinations by creating a diagnostic setting for an Azure resource. Per informazioni dettagliate, vedere [Creare l'impostazione di diagnostica per raccogliere log e metriche in Azure.See Create diagnostic setting to collect logs and metrics in Azure](diagnostic-settings.md) for details.


## <a name="activity-log-collection"></a>Raccolta log attività
È possibile inviare il log attività da qualsiasi singola sottoscrizione a un massimo di cinque aree di lavoro di Log Analytics.You can send the Activity log from any single subscription to up to five Log Analytics workspaces. I dati del log delle risorse raccolti in un'area di lavoro di Log Analytics vengono archiviati nella tabella **AzureActivity.Resource** log data collected in a Log Analytics workspace is stored in the AzureActivity table. 

## <a name="resource-log-collection-mode"></a>Modalità di raccolta log delle risorse
I dati del log delle risorse raccolti in un'area di lavoro di Log Analytics vengono archiviati in tabelle, come descritto in Struttura dei log [di Monitoraggio di Azure](../log-query/logs-structure.md). Le tabelle usate dai log delle risorse dipendono dal tipo di raccolta usato dalla risorsa:The tables used by resource logs depend on what type of collection the resource is using:

- Diagnostica di Azure: tutti i dati scritti si trova nella tabella _AzureDiagnostics.Azure_ diagnostics - All data written is to the AzureDiagnostics table.
- Specifico della risorsa: i dati vengono scritti in una singola tabella per ogni categoria della risorsa.

### <a name="azure-diagnostics-mode"></a>Modalità di diagnostica di AzureAzure Diagnostics mode 
In questa modalità, tutti i dati da qualsiasi [impostazione di diagnostica](diagnostic-settings.md) verranno raccolti nella tabella _AzureDiagnostics.In_ this mode, all data from any diagnostic setting will be collected in the AzureDiagnostics table. Questo è il metodo legacy usato oggi dalla maggior parte dei servizi di Azure.This is the legacy method used today by most Azure services.

Poiché più tipi di risorse inviano dati alla stessa tabella, il relativo schema è il superset degli schemi di tutti i diversi tipi di dati raccolti.

Si consideri l'esempio seguente in cui le impostazioni di diagnostica vengono raccolte nella stessa area di lavoro per i tipi di dati seguenti:Consider the following example where diagnostic settings are being collected in the same workspace for the following data types:

- Registri di controllo del servizio 1 (con uno schema costituito dalle colonne A, B e C)  
- Log degli errori del servizio 1 (con uno schema costituito dalle colonne D, E e F)  
- Registri di controllo del servizio 2 (con uno schema costituito dalle colonne G, H e I)  

La tabella AzureDiagnostics avrà il seguente aspetto:The AzureDiagnostics table will look as follows:  

| ResourceProvider    | Category     | Una   | b  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Service1 | AuditLogs    | x1 (in modo non in0 | y1 | Z1 |    |    |    |    |    |    |
| Microsoft.Service1 | ErrorLogs (Registri errori)    |    |    |    | q1 | w1 (in seguito a un | e1 |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j1 | k1 | l1 |
| Microsoft.Service1 | ErrorLogs (Registri errori)    |    |    |    | q2 | w2 (in seguito al numero di | e2 |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | l3 |
| Microsoft.Service1 | AuditLogs    | x5 (in questo modo) | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Specifico della risorsa
In questa modalità, vengono create singole tabelle nell'area di lavoro selezionata per ogni categoria selezionata nell'impostazione di diagnostica. Questo metodo è consigliato poiché rende molto più semplice lavorare con i dati nelle query di log, fornisce una migliore individuabilità degli schemi e della relativa struttura, migliora le prestazioni sia nella latenza di inserimento che nei tempi di query e la possibilità di concedere diritti RBAC su un tabella specifica. Tutti i servizi di Azure verranno infine migrati alla modalità Specifica risorsa. 

L'esempio precedente comporterebbe la creazione di tre tabelle:The example above would result in three tables being created:
 
- Tabella Service1AuditLogs come indicato di seguito:Table *Service1AuditLogs* as follows:

    | Provider di risorse | Category | Una  | b | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 (in modo non in0 | y1 | Z1 |
    | Service1 | AuditLogs | x5 (in questo modo) | y5 | z5 |
    | ... |

- Tabella Service1ErrorLogs come indicato di seguito:Table *Service1ErrorLogs* as follows:  

    | Provider di risorse | Category | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs (Registri errori) |  q1 | w1 (in seguito a un | e1 |
    | Service1 | ErrorLogs (Registri errori) |  q2 | w2 (in seguito al numero di | e2 |
    | ... |

- Tabella Service2AuditLogs come indicato di seguito:Table *Service2AuditLogs* as follows:  

    | Provider di risorse | Category | G | H | I |
    | -- | -- | -- | -- | -- |
    | Servizio2 | AuditLogs | j1 | k1 | l1|
    | Servizio2 | AuditLogs | j3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>Selezionare la modalità di raccolta
La maggior parte delle risorse di Azure scriverà i dati nell'area di lavoro in **modalità** **Diagnostica di Azure** o Specifica risorsa senza offrire una scelta. Vedere la [documentazione per ogni servizio](diagnostic-logs-schema.md) per informazioni dettagliate sulla modalità utilizzata. Tutti i servizi di Azure utilizzeranno la modalità Specifica risorsa. Come parte di questa transizione, alcune risorse consentono di selezionare una modalità nell'impostazione di diagnostica. È necessario specificare la modalità specifica della risorsa per le nuove impostazioni di diagnostica, poiché ciò semplifica la gestione dei dati e può aiutare a evitare migrazioni complesse in un secondo momento.
  
   ![Selettore della modalità Impostazioni di diagnostica](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> Attualmente, la **diagnostica di Azure** e la modalità specifica della risorsa possono essere selezionate solo quando si configura l'impostazione di diagnostica nel portale di Azure.Currently, Azure diagnostics and **Resource-specific** mode can only be selected when configuring the diagnostic setting in the Azure portal. Se si configura l'impostazione tramite l'interfaccia della riga di comando, PowerShell o l'API Rest, verrà uscita per impostazione predefinita la diagnostica di Azure.If you configure the setting using CLI, PowerShell, or Rest API, it will default to **Azure diagnostic**.

È possibile modificare un'impostazione di diagnostica esistente in modalità specifica della risorsa. In questo caso, i dati già raccolti rimarranno nella tabella _di AzureDiagnostics_ fino a quando non verranno rimossi in base all'impostazione di conservazione per l'area di lavoro. Nuovi dati saranno raccolti nella tabella dedicata. Utilizzare l'operatore [union](https://docs.microsoft.com/azure/kusto/query/unionoperator) per eseguire query sui dati in entrambe le tabelle.

Continuare a guardare il blog [sugli aggiornamenti](https://azure.microsoft.com/updates/) di Azure per gli annunci sui servizi di Azure che supportano la modalità specifica delle risorse.

### <a name="column-limit-in-azurediagnostics"></a>Limite di colonne in AzureDiagnosticsColumn limit in AzureDiagnostics
Esiste un limite di 500 proprietà per qualsiasi tabella nei log di Monitoraggio di Azure.There is a 500 property limit for any table in Azure Monitor Logs. Una volta raggiunto questo limite, tutte le righe contenenti dati con qualsiasi proprietà al di fuori dei primi 500 verranno eliminate al momento dell'inserimento. La tabella *AzureDiagnostics* è particolarmente soggetta a questo limite poiché include proprietà per tutti i servizi di Azure che vi scrivono.

Se si raccolgono log delle risorse da più servizi, _AzureDiagnostics_ può superare questo limite e i dati verranno persi. Fino a quando tutti i servizi di Azure supportano la modalità specifica delle risorse, è necessario configurare le risorse per scrivere in più aree di lavoro per ridurre la possibilità di raggiungere il limite di 500 colonne.

### <a name="azure-data-factory"></a>Data factory di Azure
Azure Data Factory, a causa di un set di log molto dettagliato, è un servizio noto per scrivere un numero elevato di colonne e potenzialmente causare _azureDiagnostics_ per superare il limite. Per tutte le impostazioni di diagnostica configurate prima dell'abilitazione della modalità specifica della risorsa, verrà creata una nuova colonna per ogni parametro utente con nome univoco rispetto a qualsiasi attività. Più colonne verranno create a causa della natura dettagliata degli input e degli output dell'attività.
 
È consigliabile eseguire la migrazione dei log per utilizzare la modalità specifica della risorsa appena possibile. Se non è possibile eseguire questa operazione immediatamente, un'alternativa provvisoria consiste nell'isolare i log di Azure Data Factory nella propria area di lavoro per ridurre al minimo la possibilità che questi log influiscano su altri tipi di log raccolti nelle aree di lavoro.


## <a name="next-steps"></a>Passaggi successivi

* [Ulteriori informazioni sui log delle risorse](platform-logs-overview.md).
* [Creare l'impostazione di diagnostica per raccogliere log e metriche in Azure.Create diagnostic setting to collect logs and metrics in Azure](diagnostic-settings.md).
