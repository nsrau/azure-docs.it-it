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
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Log di monitoraggio di Azure per Load Balancer di base pubblici

>[!IMPORTANT] 
>Azure Load Balancer supporta due tipi diversi: Basic e Standard. Questo articolo illustra Load Balancer Basic. Per ulteriori informazioni su Load Balancer Standard, vedere [Panoramica di Load Balancer Standard](load-balancer-standard-overview.md) che espone i dati di telemetria tramite le metriche multidimensionali in Monitoraggio di Azure.

È possibile usare diversi tipi di log in Azure per gestire e risolvere i problemi di Load Balancer Basic. Alcuni di questi log sono accessibili tramite il portale I log possono essere trasmessi a un hub eventi o a un'area di lavoro Log Analytics. Tutti i log possono essere estratti dall'archiviazione BLOB di Azure e visualizzati in diversi strumenti, ad esempio Excel e Power BI.  L'elenco seguente contiene altre informazioni sui diversi tipi di log.

* **Log attività:** È possibile usare [Visualizza log attività per monitorare le azioni sulle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) per visualizzare tutte le attività inviate alle sottoscrizioni di Azure e il relativo stato. I log attività sono abilitati per impostazione predefinita e possono essere visualizzati nel portale di Azure.
* **Log eventi di avviso:** è possibile usare questo log per visualizzare gli avvisi generati per il bilanciamento del carico. Le informazioni di stato per il bilanciamento del carico vengono raccolte ogni cinque minuti. Questo log viene scritto solo se viene generato un evento di avviso del bilanciamento del carico.
* **Log del probe di integrità:** è possibile usare questo registro per visualizzare i problemi rilevati dal probe di integrità, ad esempio il numero di istanze del pool di back-end che non stanno ricevendo richieste dal servizio di bilanciamento del carico a causa di problemi del probe di integrità. Questo log viene scritto quando si apporta una modifica nello stato del probe di integrità.

> [!IMPORTANT]
> I log di monitoraggio di Azure attualmente funzionano solo per i bilanciamenti del carico di base pubblici. I log sono disponibili solo per le risorse distribuite nel modello di distribuzione di Gestione risorse. Non è possibile usare i log per le risorse nel modello di distribuzione classica. Per altre informazioni su questi modelli di distribuzione, vedere l'articolo relativo alle [informazioni sulla distribuzione di Gestione risorse e sulla distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Abilitazione della registrazione

Registrazione attività viene abilitata automaticamente per tutte le risorse di Resource Manager. Abilitare la registrazione di eventi e probe di integrità per iniziare a raccogliere i dati disponibili tramite tali log. Eseguire questa procedura per abilitare la registrazione.

Accedere al [portale di Azure](https://portal.azure.com). Prima di procedere, [creare un servizio di bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) , se non se ne ha già uno.

1. Nel portale fare clic su **gruppi di risorse**.
2. Selezionare **\<Resource-Group-name >** in cui si trova il servizio di bilanciamento del carico.
3. Selezionare il servizio di bilanciamento del carico.
4. Selezionare **monitoraggio** > **impostazioni di diagnostica**.
5. Nel riquadro **Impostazioni** di diagnostica, in **impostazioni di diagnostica**, selezionare **+ Aggiungi impostazione di diagnostica**.
6. Nel riquadro di creazione **impostazioni di diagnostica** immettere **MyLBDiagnostics** nel campo **nome** .
7. Sono disponibili tre opzioni per le **impostazioni di diagnostica**.  È possibile scegliere uno, due o tutti e tre e configurare ognuno per i requisiti:
   * **Archivia in un account di archiviazione**
   * **Trasmettere a un hub eventi**
   * **Invia a Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Archivia in un account di archiviazione
    Per questo processo è necessario un account di archiviazione già creato.  Per creare un account di archiviazione, vedere [creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    1. Selezionare la casella di controllo accanto a **archivia in un account di archiviazione**.
    2. Selezionare **Configura** per aprire il riquadro **selezionare un account di archiviazione** .
    3. Selezionare la **sottoscrizione** in cui è stato creato l'account di archiviazione nella casella a discesa.
    4. Selezionare il nome dell'account di archiviazione in **account di archiviazione** nella casella di riepilogo a discesa. 
    5. Fare clic su OK.

    ### <a name="stream-to-an-event-hub"></a>Streaming in un hub eventi
    Per questo processo è necessario un hub eventi già creato.  Per creare un hub eventi, vedere [Guida introduttiva: creare un hub eventi usando portale di Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

    1. Selezionare la casella di controllo accanto a **Stream a un hub eventi**
    2. Selezionare **Configura** per aprire il riquadro **Seleziona Hub eventi** .
    3. Selezionare la **sottoscrizione** in cui è stato creato l'hub eventi nella casella a discesa.
    4. Nella casella a discesa **selezionare spazio dei nomi dell'hub eventi** .
    5. Nella casella a discesa **selezionare Nome criterio Hub eventi** .
    6. Fare clic su OK.

    ### <a name="send-to-log-analytics"></a>Invia a Log Analytics
    È necessario che sia già stata creata e configurata un'area di lavoro di log Analytics per questo processo.  Per creare un'area di lavoro Log Analytics, vedere [creare un'area di lavoro log Analytics nel portale di Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

    1. Selezionare la casella di controllo accanto a **Invia a log Analytics**.
    2. Selezionare la **sottoscrizione** in cui si trova l'area di lavoro log Analytics nella casella a discesa.
    3. Selezionare l' **area di lavoro log Analytics** nella casella a discesa.


8. Sotto la sezione **log** nel riquadro **impostazioni di diagnostica** Selezionare la casella di controllo accanto a entrambi:
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  Sotto la sezione **metrica** del riquadro **impostazioni di diagnostica** , selezionare la casella di controllo accanto a:
   * **AllMetrics**

11. Verificare che tutti gli elementi siano corretti e fare clic su **Salva** nella parte superiore del riquadro crea **impostazioni di diagnostica** .

## <a name="activity-log"></a>Log attività

Il log attività viene generato per impostazione predefinita. I log vengono conservati per 90 giorni nell'archivio dei log eventi di Azure. Per altre informazioni su questi log, leggere l'articolo [visualizzare i log attività per monitorare le azioni sulle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) .

## <a name="archive-to-storage-account-logs"></a>Archivia nei log degli account di archiviazione

### <a name="alert-event-log"></a>Log eventi di avviso

Questo log viene generato solo se è stato abilitato per ogni bilanciamento del carico. Gli eventi vengono registrati in formato JSON e archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. L'esempio seguente è relativo a un evento.

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

L'output JSON Mostra la proprietà *EventName* , che descrive il motivo per cui il servizio di bilanciamento del carico ha creato un avviso. In questo caso, l'avviso è stato generato a causa dell'esaurimento delle porte TCP causato da limiti NAT IP di origine (SNAT).

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

L'output JSON mostra nel campo proprietà le informazioni di base per lo stato di integrità del probe. La proprietà *dipDownCount* Visualizza il numero totale di istanze nel back-end che non ricevono il traffico di rete a causa di risposte probe non riuscite.

### <a name="view-and-analyze-the-audit-log"></a>Visualizzare e analizzare il log di controllo

È possibile visualizzare e analizzare i dati del log di controllo con uno dei metodi seguenti:

* **Strumenti di Azure:** Recuperare le informazioni dai log di controllo tramite Azure PowerShell, l'interfaccia della riga di comando di Azure (CLI), l'API REST di Azure o il portale di Azure. Per istruzioni dettagliate per ogni metodo, vedere l'articolo [Operazioni di controllo con Gestione risorse](../azure-resource-manager/resource-group-audit.md) .
* **Power BI:** se non esiste ancora un account [Power BI](https:// .microsoft.com/pricing) , è possibile crearne uno di prova gratuitamente. Con il [pacchetto di contenuto dei log di controllo di Azure per Power BI](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs) è possibile analizzare i dati con dashboard preconfigurati oppure personalizzare le viste in base alle esigenze.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Visualizzare e analizzare il log eventi e del probe di integrità

Connettersi all'account di archiviazione e recuperare le voci di log JSON per i log di eventi e di probe di integrità. Una volta scaricati i file JSON, è possibile convertirli in CSV e visualizzarli in Excel, Power BI o qualsiasi altro strumento di visualizzazione dei dati.

> [!TIP]
> Se si ha familiarità con Visual Studio e i concetti di base della modifica dei valori di costanti e variabili in C#, è possibile usare i [convertitori di log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibili in GitHub.

## <a name="stream-to-an-event-hub"></a>Streaming in un hub eventi
Quando le informazioni di diagnostica vengono trasmesse a un hub eventi, possono essere usate per l'analisi centralizzata dei log in uno strumento SIEM di terze parti con l'integrazione di monitoraggio di Azure. Per altre informazioni, vedere [trasmettere i dati di monitoraggio di Azure a un hub eventi](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Invia a Log Analytics
Le risorse in Azure possono avere le informazioni di diagnostica inviate direttamente a un'area di lavoro Log Analytics in cui è possibile eseguire query complesse sulle informazioni per la risoluzione dei problemi e l'analisi.  Per altre informazioni, vedere [raccogliere i log delle risorse di Azure nell'area di lavoro log Analytics in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace)

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sui probe di bilanciamento del carico](load-balancer-custom-probe-overview.md)
