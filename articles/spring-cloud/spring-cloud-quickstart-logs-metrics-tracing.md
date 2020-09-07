---
title: Avvio rapido - Monitoraggio di app Azure Spring Cloud con log, metriche e traccia
description: Usare lo streaming di log, l'analisi dei log, le metriche e la traccia per monitorare le app di esempio Piggymetrics in Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: f9f03c355e1e619d004c8ec8c1cc2f91932db744
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046834"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Avvio rapido: Monitoraggio di app Azure Spring Cloud con log, metriche e traccia

Le funzionalità di monitoraggio integrate in Azure Spring Cloud consentono di eseguire il debug e il monitoraggio di problemi complessi. Azure Spring Cloud integra [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) con [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) di Azure. Questa integrazione fornisce log, metriche e funzionalità di traccia distribuita avanzate dal portale di Azure. Le procedure seguenti illustrano come usare lo streaming di log, l'analisi dei log, le metriche e la traccia distribuita con le app PiggyMetrics distribuite.

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi precedenti: 

* [Effettuare il provisioning di un'istanza di Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md)
* [Configurare il server di configurazione](spring-cloud-quickstart-setup-config-server.md)
* [Compilare e distribuire le app](spring-cloud-quickstart-deploy-apps.md)

## <a name="logs"></a>Log

È possibile visualizzare i log in Azure Spring Cloud in due modi: con lo **streaming di log** per i log in tempo reale per istanza di app o con l'**analisi dei log** per i log aggregati con funzionalità di query avanzate.

### <a name="log-streaming"></a>Streaming dei log

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

È possibile usare lo streaming di log nell'interfaccia della riga di comando di Azure con il comando seguente.

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

I log hanno un aspetto simile al seguente:

[ ![Streaming di log dall'interfaccia della riga di comando di Azure](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> Usare `az spring-cloud app logs -h` per esplorare altri parametri e funzionalità di streaming dei log.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Per ottenere i log tramite Azure Toolkit for IntelliJ:

1. Selezionare **Azure Explorer**, quindi **Spring Cloud**.

1. Fare clic con il pulsante destro del mouse sull'app in esecuzione.

1. Selezionare **Streaming Logs** (Log in streaming) dall'elenco a discesa.

   ![Selezionare log in streaming](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. Selezionare **Instance** (Istanza).

   ![Selezionare l'istanza](media/spring-cloud-intellij-howto/select-instance.png)
    
1. Il log in streaming sarà visibile nella finestra output.

   ![Output del log in streaming](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. Passare alla pagina **service | Overview** (servizio | Panoramica) e selezionare **Logs** (Log) nella sezione **Monitoring** (Monitoraggio). Fare clic su **Run** (Esegui) in una delle query di esempio per Azure Spring Cloud. 

   [ ![Voce di analisi dei log](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Vengono visualizzati i log filtrati. Per altre informazioni sulla scrittura di query, vedere la [ documentazione di Azure Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries).

   [ ![Query di log Analytics](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>Metriche

1. Passare alla pagina **service | Overview** (servizio | Panoramica) e selezionare **Metrics** (Metriche) nella sezione **Monitoring** (Monitoraggio). Aggiungere la prima metrica selezionando `system.cpu.usage` per **Metric** (Metrica) e `Avg` per **Aggregation** (Aggregazione) per visualizzare la sequenza temporale per l'utilizzo complessivo della CPU.

   [ ![Voce di metriche](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. Fare clic su **Add filter** (Aggiungi filtro) sulla barra degli strumenti e selezionare `App=Gateway` per visualizzare l'utilizzo della CPU solo per l'app **gateway**.

   [ ![Uso dei filtri nelle metriche](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. Rimuovere il filtro creato in precedenza, fare clic su **Apply Splitting** (Applica separazione) e selezionare `App` per **Values** (Valori) per visualizzare l'utilizzo della CPU da parte di diverse app.

   [ ![Applicare la separazione nelle metriche](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>Analisi distribuita

1. Passare alla pagina **service | Overview** (servizio | Panoramica) e selezionare **Distributed tracing** (Traccia distribuita) nella sezione **Monitoring** (Monitoraggio). Quindi fare clic sulla scheda **View application map** (Visualizza mappa delle applicazioni) a destra.

   [ ![Voce di traccia distribuita](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. A questo punto è possibile visualizzare lo stato delle chiamate tra le app Piggymetrics. 

   [ ![Panoramica della traccia distribuita](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. Fare clic sul collegamento tra **gateway** e **account-service** per visualizzare altri dettagli, ad esempio le chiamate più lente da parte di metodi HTTP.

   [ ![Traccia distribuita](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. Infine, fare clic su **Investigate Performance** (Esamina prestazioni) per accedere a un'analisi delle prestazioni integrata più avanzata.

   [ ![Prestazioni della traccia distribuita](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

## <a name="clean-up-resources"></a>Pulire le risorse

Nei passaggi precedenti sono state create risorse di Azure in un gruppo di risorse. Se si ritiene che queste risorse non saranno necessarie in futuro, eliminare il gruppo di risorse dal portale eseguendo questo comando in Cloud Shell:

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

Nelle procedure precedenti è stato anche impostato il nome del gruppo di risorse predefinito. Per cancellarlo, eseguire il comando seguente in Cloud Shell:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla funzionalità di monitoraggio predefinite per Azure Spring Cloud, vedere:

> [!div class="nextstepaction"]
> [Servizi di diagnostica](diagnostic-services.md)
> [Traccia distribuita](spring-cloud-tutorial-distributed-tracing.md)
> [Streaming di log in tempo reale](spring-cloud-howto-log-streaming.md)
