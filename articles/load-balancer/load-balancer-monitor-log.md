---
title: Monitorare operazioni, eventi e contatori per Load Balancer Basic pubblico
titleSuffix: Azure Load Balancer
description: Informazioni su come abilitare gli eventi di avviso e la registrazione dello stato di integrità del probe per Load Balancer Basic pubblico
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: allensu
ms.openlocfilehash: 0a21af683d9fa7849d3e96c545983c9f40a8d4c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935328"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Log di Monitoraggio di Azure per Load Balancer Basic pubblico

>[!IMPORTANT]
>Azure Load Balancer supporta due tipi diversi: Basic e Standard. Questo articolo illustra Load Balancer Basic. Per ulteriori informazioni su Load Balancer Standard, vedere [Panoramica di Load Balancer Standard](load-balancer-standard-overview.md) che espone i dati di telemetria tramite le metriche multidimensionali in Monitoraggio di Azure.

È possibile usare diversi tipi di log in Azure per gestire e risolvere i problemi di Load Balancer Basic. Alcuni di questi log sono accessibili tramite il portale I log possono essere trasmessi a un hub eventi o a un'area di lavoro di Log Analytics.Logs can be streamed to an event hub or a Log Analytics workspace. Tutti i log possono essere estratti dall'archiviazione BLOB di Azure e visualizzati in diversi strumenti, ad esempio Excel e Power BI.  L'elenco seguente contiene altre informazioni sui diversi tipi di log.

* **Registri attività:** È possibile usare [Visualizza log attività per monitorare le azioni sulle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) per visualizzare tutte le attività inviate alle sottoscrizioni di Azure e il relativo stato. I log attività sono abilitati per impostazione predefinita e possono essere visualizzati nel portale di Azure.Activity logs are enabled by default, and can be viewed in the Azure portal.
* **Log eventi di avviso:** è possibile usare questo log per visualizzare gli avvisi generati per il bilanciamento del carico. Le informazioni di stato per il bilanciamento del carico vengono raccolte ogni cinque minuti. Questo log viene scritto solo se viene generato un evento di avviso del bilanciamento del carico.
* **Log del probe di integrità:** è possibile usare questo registro per visualizzare i problemi rilevati dal probe di integrità, ad esempio il numero di istanze del pool di back-end che non stanno ricevendo richieste dal servizio di bilanciamento del carico a causa di problemi del probe di integrità. Questo log viene scritto quando si apporta una modifica nello stato del probe di integrità.

> [!IMPORTANT]
> I log di Monitoraggio di Azure attualmente funzionano solo per i servizi di bilanciamento del carico di base pubblici. I log sono disponibili solo per le risorse distribuite nel modello di distribuzione di Gestione risorse. Non è possibile usare i log per le risorse nel modello di distribuzione classica. Per altre informazioni su questi modelli di distribuzione, vedere l'articolo relativo alle [informazioni sulla distribuzione di Gestione risorse e sulla distribuzione classica](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Abilitazione della registrazione

Registrazione attività viene abilitata automaticamente per tutte le risorse di Resource Manager. Abilitare la registrazione di eventi e probe di integrità per avviare la raccolta dei dati disponibili tramite tali registri. Eseguire questa procedura per abilitare la registrazione.

Accedere al [portale](https://portal.azure.com)di Azure . Prima di procedere, [creare un servizio di bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) , se non se ne ha già uno.

1. Nel portale fare clic su **Gruppi di risorse**.
2. Selezionare il nome del ** \<gruppo di risorse>** in cui si trova il servizio di bilanciamento del carico.
3. Selezionare il servizio di bilanciamento del carico.
4. Selezionare Impostazioni**di diagnostica** **monitoraggio** > .
5. Nel riquadro **Impostazioni di diagnostica,** in **Impostazioni di diagnostica**, selezionare Aggiungi **impostazione diagnostica**.
6. Nel riquadro Creazione impostazioni di diagnostica immettere myLBDiagnostics nel campo **Nome.In** the Diagnostics **settings** creation pane, enter **myLBDiagnostics** in the Name field.
7. Sono disponibili tre opzioni per le **impostazioni di diagnostica**.  È possibile scegliere uno, due o tutti e tre e configurare ciascuno per le vostre esigenze:
   * **Archivia in un account di archiviazione**
   * **Streaming in un hub eventi**
   * **Invia a Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Archivia in un account di archiviazione
    È necessario un account di archiviazione già creato per questo processo.  Per creare un account di archiviazione, vedere [Creare un account di archiviazioneTo create](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) a storage account, see Create a storage account

    1. Selezionare la casella di controllo accanto a **Archivia in un account di archiviazione.**
    2. Selezionare **Configura** per aprire il riquadro **Selezionare un account di archiviazione.**
    3. Selezionare la **sottoscrizione** in cui è stato creato l'account di archiviazione nella casella di riepilogo a discesa.
    4. Selezionare il nome dell'account di archiviazione in **Account di archiviazione** nella casella di riepilogo a discesa.
    5. Selezionare OK.

    ### <a name="stream-to-an-event-hub"></a>Streaming in un hub eventi
    È necessario un hub eventi già creato per questo processo.  Per creare un hub eventi, vedere [Guida introduttiva: Creare un hub eventi tramite il portale](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) di AzureTo create an event hub, see Quickstart: Create an event hub using Azure portal

    1. Selezionare la casella di controllo accanto a **Flusso di un hub eventi**
    2. Selezionare **Configura** per aprire il riquadro **Seleziona hub eventi.**
    3. Selezionare la **sottoscrizione** in cui è stato creato l'hub eventi nella casella a discesa.
    4. **Selezionare lo spazio dei nomi dell'hub eventi** nella casella di riepilogo a discesa.
    5. **Selezionare** il nome del criterio hub eventi nella casella di riepilogo a discesa.
    6. Selezionare OK.

    ### <a name="send-to-log-analytics"></a>Invia a Log Analytics
    È necessario disporre già di un'area di lavoro di analisi dei log creata e configurata per questo processo.  Per creare un'area di lavoro di Log Analytics, vedere [Creare un'area](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) di lavoro di Log Analytics nel portale di AzureTo create a Log Analytics workspace, see Create a Log Analytics workspace in the Azure portal

    1. Selezionare la casella di controllo accanto **a Invia a Log Analytics**.
    2. Selezionare la **sottoscrizione** in cui si trova l'area di lavoro di Log Analytics nella casella di riepilogo a discesa.
    3. Selezionare l'area **di lavoro log di Log Analytics** nella casella di riepilogo a discesa.


8. Sotto la sezione **LOG** del riquadro **Impostazioni di diagnostica,** selezionare la casella di controllo accanto a entrambe:
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  Sotto la sezione **METRIC** del riquadro **Impostazioni di diagnostica,** selezionare la casella di controllo accanto a:
   * **AllMetrics (Integrità allMetrics)**

11. Verificare che tutto sia corretto e fare clic su **Salva** nella parte superiore del riquadro Crea **impostazioni di diagnostica.**

## <a name="activity-log"></a>Log attività

Il log attività viene generato per impostazione predefinita. I log vengono conservati per 90 giorni nell'archivio dei log eventi di Azure. Per altre informazioni su questi log, leggere l'articolo [Visualizzare i log attività per monitorare le azioni sulle risorse.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)

## <a name="archive-to-storage-account-logs"></a>Archiviare nei log dell'account di archiviazioneArchive to storage account logs

### <a name="alert-event-log"></a>Log eventi di avviso

Questo log viene generato solo se è stato abilitato per ogni bilanciamento del carico. Gli eventi vengono registrati in formato JSON e archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. L'esempio seguente è di un evento.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

L'output JSON mostra la proprietà *eventname,* che descrive il motivo per cui il servizio di bilanciamento del carico ha creato un avviso. In questo caso, l'avviso generato è dovuto all'esaurimento della porta TCP causato dai limiti SNAT (Source IP NAT).

### <a name="health-probe-log"></a>Log del probe di integrità

Questo log viene generato solo se è stato abilitato per ogni bilanciamento del carico, come indicato in precedenza. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Viene creato un contenitore denominato 'insights-logs-loadbalancerprobehealthstatus' e vengono registrati i dati seguenti:

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

L'output JSON mostra nel campo proprietà le informazioni di base per lo stato di integrità del probe. La proprietà *dipDownCount* mostra il numero totale di istanze nel back-end, che non ricevono traffico di rete a causa di risposte probe non riuscite.

### <a name="view-and-analyze-the-activity-log"></a>Visualizzare e analizzare Log attività

È possibile visualizzare e analizzare i dati di Log attività con uno dei metodi seguenti:

* **Strumenti di Azure:Azure tools:** Recuperare informazioni dal log attività tramite Azure PowerShell, l'interfaccia della riga di comando di Azure, l'API REST di Azure o il portale di Azure.Retrieve information from the activity log through Azure PowerShell, the Azure Command Line Interface (CLI), the Azure REST API, or the Azure portal. Per istruzioni dettagliate per ogni metodo, vedere l'articolo [Operazioni di controllo con Gestione risorse](../azure-resource-manager/management/view-activity-logs.md) .
* **Power BI:** se non esiste ancora un account [Power BI](https:// .microsoft.com/pricing) , è possibile crearne uno di prova gratuitamente. Con il [pacchetto di contenuto dei log di controllo di Azure per Power BI](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs) è possibile analizzare i dati con dashboard preconfigurati oppure personalizzare le viste in base alle esigenze.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Visualizzare e analizzare il log eventi e del probe di integrità

Connettersi all'account di archiviazione e recuperare le voci del log JSON per i log di eventi e probe di integrità. Dopo aver scaricato i file JSON, è possibile convertirli in CSV e visualizzarli in Excel, Power BI o qualsiasi altro strumento di visualizzazione dei dati.

> [!TIP]
> Se si ha familiarità con Visual Studio e i concetti di base della modifica dei valori di costanti e variabili in C#, è possibile usare i [convertitori di log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibili in GitHub.

## <a name="stream-to-an-event-hub"></a>Streaming in un hub eventi
Quando le informazioni di diagnostica vengono trasmesse a un hub eventi, possono essere usate per l'analisi centralizzata dei log in uno strumento SIEM di terze parti con L'integrazione di Monitoraggio di Azure.When diagnostic information is streamed to an event hub, it can be used for centralized log analysis in a third-party SIEM tool with Azure Monitor Integration. Per altre informazioni, vedere Eseguire lo streaming dei dati di [monitoraggio di Azure in un hub eventiFor](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration) more information, see Stream Azure monitoring data to an event hub

## <a name="send-to-log-analytics"></a>Invia a Log Analytics
Le risorse in Azure possono inviare le informazioni di diagnostica direttamente a un'area di lavoro di Log Analytics in cui è possibile eseguire query complesse sulle informazioni per la risoluzione dei problemi e l'analisi.  Per altre informazioni, vedere Raccogliere log delle risorse di [Azure nell'area](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace) di lavoro Log Analytics in Monitoraggio di AzureFor more information, see Collect Azure resource logs in Log Analytics workspace in Azure Monitor

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sui probe di bilanciamento del carico](load-balancer-custom-probe-overview.md)
