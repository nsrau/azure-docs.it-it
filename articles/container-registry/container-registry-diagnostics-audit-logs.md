---
title: Raccogliere i log delle risorse in & Analyze
description: Registrare e analizzare gli eventi del log delle risorse per il Registro di sistema del contenitore di Azure, ad esempio l'autenticazione, il push dell'immagine e il pull delle immagini.
ms.topic: article
ms.date: 01/03/2020
ms.openlocfilehash: 00f9468721126bd166051df47cec1596356e9b54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409644"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Log del Registro di sistema del contenitore di Azure per la valutazione e il controllo di diagnosticaAzure Container Registry logs for diagnostic evaluation and auditing

Questo articolo illustra come raccogliere i dati di log per un registro dei contenitori di Azure usando le funzionalità di [Monitoraggio di Azure.](../azure-monitor/overview.md) Monitoraggio di Azure raccoglie [i log delle risorse](../azure-monitor/platform/platform-logs-overview.md) (precedentemente denominati log di *diagnostica*) per gli eventi guidati dall'utente nel Registro di sistema. Raccogliere e utilizzare questi dati per soddisfare esigenze quali:Collect and consume this data to meet needs such as:

* Controllare gli eventi di autenticazione del Registro di sistema per garantire sicurezza e conformitàAudit registry authentication events to ensure security and compliance 

* Fornire un percorso di attività completo sugli elementi del Registro di sistema, ad esempio gli eventi pull e pull, in modo da poter diagnosticare i problemi operativi con il Registro di sistemaProvide a complete activity trail on registry artifacts such as pull and pull events so can diagnose operational issues with your registry 

La raccolta dei dati del log delle risorse tramite Monitoraggio di Azure può comportare costi aggiuntivi. Vedere [Prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/). 

## <a name="repository-events"></a>Eventi del repository

Attualmente vengono registrati i seguenti eventi a livello di repository per le immagini e altri elementi:

* **Eventi push**
* **Eventi pull**
* **Eventi untag**
* **Eliminare gli eventi** (inclusi gli eventi di eliminazione del repository)

Eventi a livello di repository che non sono attualmente registrati: elimina eventi.

## <a name="registry-resource-logs"></a>Registri delle risorse del Registro di sistemaRegistry resource logs

I log delle risorse contengono informazioni generate dalle risorse di Azure che ne descrivono l'operazione interna. Per un registro contenitori di Azure, i log contengono eventi a livello di autenticazione ed archivio archiviati nelle tabelle seguenti. 

* **ContainerRegistryLoginEvents** : eventi di autenticazione del Registro di sistema e lo stato, inclusi l'identità in ingresso e l'indirizzo IP
* **ContainerRegistryRepositoryEvents:** operazioni quali push e pull per immagini e altri elementi nei repository del Registro di sistemaContainerRegistryRepositoryEvents - Operations such as push and pull for images and other artifacts in registry repositories
* **Metriche** - del Registro di[sistema del contenitore](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries) AzureMetrics, ad esempio conteggi push e pull aggregati.

Per le operazioni, i dati di log includono:
  * Stato di esito positivo o negativo
  * Indicatori di data e ora di inizio e di fine

Oltre ai log delle risorse, Azure fornisce un [log attività,](../azure-monitor/platform/platform-logs-overview.md)un singolo record a livello di sottoscrizione degli eventi di gestione di Azure, ad esempio la creazione o l'eliminazione di un registro contenitori.

## <a name="enable-collection-of-resource-logs"></a>Abilitare la raccolta dei log delle risorseEnable collection of resource logs

La raccolta di log delle risorse per un registro contenitori non è abilitata per impostazione predefinita. Abilitare in modo esplicito le impostazioni di diagnostica per ogni Registro di sistema che si desidera monitorare. Per le opzioni per abilitare le impostazioni di diagnostica, vedere [Creare l'impostazione di diagnostica per raccogliere i log e](../azure-monitor/platform/diagnostic-settings.md)le metriche della piattaforma in Azure.For options to enable diagnostic settings, see Create diagnostic setting to collect platform logs and metrics in Azure.

Ad esempio, per visualizzare i log e le metriche per un registro contenitori quasi in tempo reale in Monitoraggio di Azure, raccogliere i log delle risorse in un'area di lavoro di Log Analytics.For example, to view logs and metrics for a container registry in near real-time in Azure Monitor, collect the resource logs in a Log Analytics workspace. Per abilitare questa impostazione di diagnostica tramite il portale di Azure:To enable this diagnostic setting using the Azure portal:

1. Se non si dispone già di un'area di lavoro, creare un'area di lavoro usando il portale di [Azure.](../azure-monitor/learn/quick-create-workspace.md) Per ridurre al minimo la latenza nella raccolta dei dati, assicurarsi che l'area di lavoro si trova nella **stessa area** del Registro di sistema del contenitore.
1. Nel portale selezionare il Registro di sistema e selezionare **Monitoraggio > Impostazioni di diagnostica >'impostazione di diagnostica**.
1. Immettere un nome per l'impostazione e selezionare **Invia a Log Analytics**.
1. Selezionare l'area di lavoro per i log di diagnostica del Registro di sistema.
1. Selezionare i dati di registro che si desidera raccogliere e fare clic su **Salva**.

L'immagine seguente mostra la creazione di un'impostazione di diagnostica per un Registro di sistema tramite il portale.

![Abilitare le impostazioni di diagnostica](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Raccogli solo i dati di cui hai bisogno, bilanciando i costi e le tue esigenze di monitoraggio. Ad esempio, se è necessario controllare solo gli eventi di autenticazione, selezionare solo il **registro ContainerRegistryLoginEvents.For** example, if you only need to audit authentication events, select only the ContainerRegistryLoginEvents log. 

## <a name="view-data-in-azure-monitor"></a>Visualizzare i dati in Monitoraggio di AzureView data in Azure Monitor

Dopo aver abilitato la raccolta dei log di diagnostica in Log Analytics, la visualizzazione dei dati in Monitoraggio di Azure può richiedere alcuni minuti. Per visualizzare i dati nel portale, selezionare il Registro di sistema e selezionare **Monitoraggio > registri**. Selezionare una delle tabelle che contiene i dati per il Registro di sistema. 

Eseguire query per visualizzare i dati. Vengono fornite diverse query di esempio o eseguite personalizzate. Ad esempio, la query seguente recupera le 24 ore più recenti di dati dalla tabella **ContainerRegistryRepositoryEvents:**

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

L'immagine seguente mostra l'output di esempio:The following image shows sample output:

![Eseguire query sui dati di log](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Per un'esercitazione sull'uso di Log Analytics nel portale di Azure, vedere Introduzione ad [Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md)oppure provare l'ambiente [Demo](https://portal.loganalytics.io/demo)di Log Analytics. 

Per altre informazioni sulle query di log, vedere [Panoramica delle query di log in Monitoraggio di Azure.For](../azure-monitor/log-query/log-query-overview.md)more information on log queries, see Overview of log queries in Azure Monitor .

### <a name="additional-query-examples"></a>Esempi di query aggiuntivi

#### <a name="100-most-recent-registry-events"></a>100 eventi del Registro di sistema più recenti

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>Destinazioni di log aggiuntive

Oltre a inviare i log a Log Analytics o in alternativa, uno scenario comune consiste nel selezionare un account di Archiviazione di Azure come destinazione del log. Per archiviare i log in Archiviazione di Azure, creare un account di archiviazione prima di abilitare l'archiviazione tramite le impostazioni di diagnostica.

È anche possibile trasmettere gli eventi del log di diagnostica in un hub eventi di [Azure.](../event-hubs/event-hubs-what-is-event-hubs.md) Hub eventi è in grado di inserire milioni di eventi al secondo, che è quindi possibile trasformare e archiviare tramite un qualsiasi provider di analisi in tempo reale. 

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni sull'utilizzo di [Log Analytics](../azure-monitor/log-query/get-started-portal.md) e sulla creazione di query di [log](../azure-monitor/log-query/get-started-queries.md).
* Vedere [Panoramica dei log della piattaforma Azure](../azure-monitor/platform/platform-logs-overview.md) per informazioni sui log della piattaforma disponibili a diversi livelli di Azure.See Overview of Azure platform logs to learn about platform logs that are available at different layers of Azure.

