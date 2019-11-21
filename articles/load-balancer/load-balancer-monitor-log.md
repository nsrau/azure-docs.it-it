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
ms.openlocfilehash: 23a3a2629c6f2f89c4b8f6d5af57bcf3b6bb67dd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214921"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Azure Monitor logs for public Basic Load Balancer

>[!IMPORTANT] 
>Azure Load Balancer supporta due tipi diversi: Basic e Standard. Questo articolo illustra Load Balancer Basic. Per ulteriori informazioni su Load Balancer Standard, vedere [Panoramica di Load Balancer Standard](load-balancer-standard-overview.md) che espone i dati di telemetria tramite le metriche multidimensionali in Monitoraggio di Azure.

È possibile usare diversi tipi di log in Azure per gestire e risolvere i problemi di Load Balancer Basic. Alcuni di questi log sono accessibili tramite il portale Logs can be streamed to an event hub or a Log Analytics workspace. All logs can be extracted from Azure blob storage, and viewed in different tools, such as Excel and Power BI.  L'elenco seguente contiene altre informazioni sui diversi tipi di log.

* **Activity logs:** You can use [View activity logs to monitor actions on resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) to view all activity being submitted to your Azure subscription(s), and their status. Activity logs are enabled by default, and can be viewed in the Azure portal.
* **Log eventi di avviso:** è possibile usare questo log per visualizzare gli avvisi generati per il bilanciamento del carico. Le informazioni di stato per il bilanciamento del carico vengono raccolte ogni cinque minuti. Questo log viene scritto solo se viene generato un evento di avviso del bilanciamento del carico.
* **Log del probe di integrità:** è possibile usare questo registro per visualizzare i problemi rilevati dal probe di integrità, ad esempio il numero di istanze del pool di back-end che non stanno ricevendo richieste dal servizio di bilanciamento del carico a causa di problemi del probe di integrità. Questo log viene scritto quando si apporta una modifica nello stato del probe di integrità.

> [!IMPORTANT]
> Azure Monitor logs currently works only for public Basic load balancers. I log sono disponibili solo per le risorse distribuite nel modello di distribuzione di Gestione risorse. Non è possibile usare i log per le risorse nel modello di distribuzione classica. Per altre informazioni su questi modelli di distribuzione, vedere l'articolo relativo alle [informazioni sulla distribuzione di Gestione risorse e sulla distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Abilitazione della registrazione

Registrazione attività viene abilitata automaticamente per tutte le risorse di Resource Manager. Enable event and health probe logging to start collecting the data available through those logs. Eseguire questa procedura per abilitare la registrazione.

Accedere al [portale di Azure](https://portal.azure.com). Prima di procedere, [creare un servizio di bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) , se non se ne ha già uno.

1. In the portal, click **Resource groups**.
2. Select **\<resource-group-name>** where your load balancer is.
3. Select your load balancer.
4. Select **Monitoring** > **Diagnostic settings**.
5. In the **Diagnostics settings** pane, under **Diagnostics settings**, select **+ Add diagnostic setting**.
6. In the **Diagnostics settings** creation pane, enter **myLBDiagnostics** in the **Name** field.
7. You have three options for the **Diagnostics settings**.  You can choose one, two or all three and configure each for your requirements:
   * **Archive to a storage account**
   * **Stream to an event hub**
   * **Send to Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Archivia in un account di archiviazione
    You'll need a storage account already created for this process.  To create a storage account, see [Create a storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    1. Select the checkbox next to **Archive to a storage account**.
    2. Select **Configure** to open the **Select a storage account** pane.
    3. Select the **Subscription** where your storage account was created in the pull-down box.
    4. Select the name of your storage account under **Storage account** in the pull-down box. 
    5. Select OK.

    ### <a name="stream-to-an-event-hub"></a>Streaming in un hub eventi
    You'll need an event hub already created for this process.  To create an event hub, see [Quickstart: Create an event hub using Azure portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

    1. Select the checkbox next to **Stream to an event hub**
    2. Select **Configure** to open the **Select event hub** pane.
    3. Select the **Subscription** where your event hub was created in the pull-down box.
    4. **Select event hub namespace** in the pull-down box.
    5. **Select event hub policy name** in the pull-down box.
    6. Select OK.

    ### <a name="send-to-log-analytics"></a>Invia a Log Analytics
    You'll need to already have a log analytics workspace created and configured for this process.  To create a Log Analytics workspace, see [Create a Log Analytics workspace in the Azure portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

    1. Select the checkbox next to **Send to Log Analytics**.
    2. Select the **Subscription** where your Log Analytics workspace is in the pull-down box.
    3. Select the **Log Analytics Workspace** in the pull-down box.


8. Beneath the **LOG** section in the **Diagnostics settings** pane, select the check box next to both:
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  Beneath the **METRIC** section in the **Diagnostics settings** pane, select the check box next to:
   * **AllMetrics**

11. Verify everything looks correct and click **Save** at the top of the create **Diagnostic settings** pane.

## <a name="activity-log"></a>Log attività

The activity log is generated by default. I log vengono conservati per 90 giorni nell'archivio dei log eventi di Azure. Learn more about these logs by reading the [View activity logs to monitor actions on resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) article.

## <a name="archive-to-storage-account-logs"></a>Archive to storage account logs

### <a name="alert-event-log"></a>Log eventi di avviso

Questo log viene generato solo se è stato abilitato per ogni bilanciamento del carico. Gli eventi vengono registrati in formato JSON e archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. The following example is of an event.

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

The JSON output shows the *eventname* property, which will describe the reason for the load balancer created an alert. In this case, the alert generated was because of TCP port exhaustion caused by source IP NAT limits (SNAT).

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

L'output JSON mostra nel campo proprietà le informazioni di base per lo stato di integrità del probe. The *dipDownCount* property shows the total number of instances on the back-end, which are not receiving network traffic because of failed probe responses.

### <a name="view-and-analyze-the-audit-log"></a>Visualizzare e analizzare il log di controllo

È possibile visualizzare e analizzare i dati del log di controllo con uno dei metodi seguenti:

* **Azure tools:** Retrieve information from the audit logs through Azure PowerShell, the Azure Command Line Interface (CLI), the Azure REST API, or the Azure portal. Per istruzioni dettagliate per ogni metodo, vedere l'articolo [Operazioni di controllo con Gestione risorse](../azure-resource-manager/resource-group-audit.md) .
* **Power BI:** se non esiste ancora un account [Power BI](https:// .microsoft.com/pricing) , è possibile crearne uno di prova gratuitamente. Con il [pacchetto di contenuto dei log di controllo di Azure per Power BI](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs) è possibile analizzare i dati con dashboard preconfigurati oppure personalizzare le viste in base alle esigenze.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Visualizzare e analizzare il log eventi e del probe di integrità

Connect to your storage account and retrieve the JSON log entries for event and health probe logs. Once you download the JSON files, you can convert them to CSV and view in Excel, Power BI, or any other data visualization tool.

> [!TIP]
> Se si ha familiarità con Visual Studio e i concetti di base della modifica dei valori di costanti e variabili in C#, è possibile usare i [convertitori di log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibili in GitHub.

## <a name="stream-to-an-event-hub"></a>Streaming in un hub eventi
When diagnostic information is streamed to an event hub, it can be used for centralized log analysis in a third-party SIEM tool with Azure Monitor Integration. For more information, see [Stream Azure monitoring data to an event hub](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Invia a Log Analytics
Resources in Azure can have their diagnostic information sent directly to a Log Analytics workspace where complex queries can be run against the information for troubleshooting and analysis.  For more information, see [Collect Azure resource logs in Log Analytics workspace in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace)

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sui probe di bilanciamento del carico](load-balancer-custom-probe-overview.md)
