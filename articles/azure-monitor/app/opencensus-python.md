---
title: Monitorare le applicazioni Python con monitoraggio di Azure (anteprima) | Microsoft Docs
description: Fornisce istruzioni per collegare OpenCensus Python con monitoraggio di Azure
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: ed61cb1bc88c48fe89c4a9390f04747749bd48c5
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329470"
---
# <a name="set-up-azure-monitor-for-your-python-application-preview"></a>Configurare monitoraggio di Azure per l'applicazione Python (anteprima)

Monitoraggio di Azure supporta la traccia distribuita, la raccolta delle metriche e la registrazione delle applicazioni Python tramite l'integrazione con [OpenCensus](https://opencensus.io). Questo articolo illustra in modo dettagliato il processo di configurazione di OpenCensus per Python e di recupero dei dati di monitoraggio in monitoraggio di Azure.

## <a name="prerequisites"></a>Prerequisiti

- È necessaria una sottoscrizione di Azure.
- Python deve essere installato, questo articolo usa [Python 3.7.0](https://www.python.org/downloads/), anche se le versioni precedenti probabilmente funzioneranno con regolazioni minime.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-application-insights-resource-in-azure-monitor"></a>Creare Application Insights risorsa in monitoraggio di Azure

Prima di tutto è necessario creare una risorsa Application Insights in monitoraggio di Azure, che genererà una chiave di strumentazione (IKey). IKey viene quindi usato per configurare OpenCensus SDK per inviare i dati di telemetria a monitoraggio di Azure.

1. Selezionare **Crea una risorsa** > **Strumenti di sviluppo** > **Application Insights**.

   ![Aggiunta di una risorsa di Application Insights](./media/opencensus-python/0001-create-resource.png)

   Verrà visualizzata una casella di configurazione. Usare la tabella seguente per completare i campi di input.

    | Impostazioni        | Value           | Description  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valore globalmente univoco | Nome che identifica l'app da monitorare |
   | **Gruppo di risorse**     | myResourceGroup      | Nome del nuovo gruppo di risorse per l'hosting dei dati di Application Insights |
   | **Località** | Stati Uniti Orientali | Scegliere una località nelle vicinanze o vicina a quella in cui è ospitata l'app |

2. Fare clic su **Create**(Crea).

## <a name="instrumenting-with-opencensus-python-sdk-for-azure-monitor"></a>Strumentazione con OpenCensus Python SDK per monitoraggio di Azure

1. Installare gli esportatori di monitoraggio di Azure OpenCensus:

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` presuppone la presenza di una variabile di ambiente PATH impostata per l'installazione di Python. Se questa non è stata configurata, sarà necessario specificare il percorso completo della directory in cui si trova l'eseguibile di Python, che può restituire un comando simile a questo: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

2. L'SDK usa tre utilità di esportazione di monitoraggio di Azure per inviare diversi tipi di dati di telemetria a monitoraggio di Azure: traccia, metriche e log. Per altri dettagli su questi tipi diversi, vedere [Panoramica della piattaforma dati](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform) . Seguire le istruzioni riportate di seguito per vedere come inviare questi tipi diversi tramite le tre utilità di esportazione.

### <a name="trace"></a>Trace

1. Prima di tutto, è necessario generare alcuni dati di traccia in locale. In Python inattivo, o l'editor preferito, immettere il codice seguente.

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2. L'esecuzione del codice genera una richiesta ripetuta di immissione di un valore. Con ogni voce, il valore verrà stampato nella shell e un elemento corrispondente di **SpanData** verrà generato dal modulo OpenCensus Python. Il progetto OpenCensus definisce una [ _traccia sotto forma di albero di intervalli_](https://opencensus.io/core-concepts/tracing/).
    
    ```
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

3. Sebbene sia utile a scopo dimostrativo, in definitiva si vuole creare la @no__t da 0 a monitoraggio di Azure. Modificare il codice del passaggio precedente in base all'esempio di codice seguente:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        ),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

4. A questo punto, quando si esegue lo script Python, viene comunque richiesto di immettere i valori, ma ora viene stampato solo il valore nella shell. Il `SpanData` creato verrà inviato a monitoraggio di Azure. È possibile trovare i dati di intervallo emessi in `dependencies`.

### <a name="metrics"></a>Metriche

1. Prima di tutto, è necessario generare alcuni dati sulle metriche locali. Viene creata una metrica semplice per tenere traccia del numero di volte in cui l'utente preme INVIO.

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
2. L'esecuzione del codice richiederà ripetutamente di premere INVIO. Viene creata una metrica per tenere traccia del numero di immissioni premuti. Con ogni voce, il valore verrà incrementato e le informazioni sulla metrica verranno visualizzate nella console, con il valore corrente e il timestamp corrente al momento dell'aggiornamento della metrica.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Sebbene sia utile a scopo dimostrativo, in definitiva si vuole creare i dati delle metriche in monitoraggio di Azure. Modificare il codice del passaggio precedente in base all'esempio di codice seguente:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

4. L'utilità di esportazione invierà i dati delle metriche al monitoraggio di Azure a intervalli fissi, il valore predefinito è ogni 15 secondi. Viene monitorata una singola metrica, quindi i dati delle metriche, con qualsiasi valore e timestamp in esso contenuti, verranno inviati ogni intervallo. È possibile trovare i dati in `customMetrics`.

### <a name="logs"></a>Log

1. Prima di tutto, è necessario generare alcuni dati di log locali.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2.  Il codice chiederà di richiedere continuamente un valore da immettere. Viene emessa una voce di log per ogni valore immesso che contiene il valore indicato.

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. Sebbene sia utile a scopo dimostrativo, in definitiva si vuole creare i dati delle metriche in monitoraggio di Azure. Modificare il codice del passaggio precedente in base all'esempio di codice seguente:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

4. L'utilità di esportazione invierà i dati di log a monitoraggio di Azure. È possibile trovare i dati in `traces`.

## <a name="start-monitoring-in-the-azure-portal"></a>Avviare il monitoraggio nel portale di Azure

1. È ora possibile riaprire la pagina **Panoramica** di Application Insights nel portale di Azure per visualizzare i dettagli relativi all'applicazione attualmente in esecuzione. Selezionare **Flusso metriche attive**.

   ![Screenshot del riquadro Panoramica con Flusso metriche attive selezionato nella casella rossa](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Tornare alla pagina **Panoramica** e selezionare **Mappa delle applicazioni** per ottenere un layout visivo delle relazioni di dipendenza e dei tempi di chiamata tra i componenti dell'applicazione.

    ![Screenshot della mappa delle applicazioni di base](./media/opencensus-python/0007-application-map.png)

    Poiché è stata tracciata solo una chiamata a un metodo, la mappa delle applicazioni non è così interessante. Tuttavia, la mappa delle applicazioni può essere ridimensionata in modo da visualizzare applicazioni molto più distribuite:

   ![Mappa delle applicazioni](media/opencensus-python/application-map.png)

3. Selezionare **Esamina prestazioni** per eseguire un'analisi dettagliata delle prestazioni e determinare la causa principale del rallentamento delle prestazioni.

    ![Screenshot del riquadro delle prestazioni](./media/opencensus-python/0008-performance.png)

4. Selezionando **Esempi** e quindi facendo clic su uno degli esempi visualizzati nel riquadro a destra, viene avviata l'esperienza dettagliata delle transazioni end-to-end. Mentre l'app di esempio mostrerà un solo evento, un'applicazione più complessa permetterebbe di esplorare le transazioni end-to-end fino al livello dello stack di chiamate di ogni singolo evento.

     ![Screenshot dell'interfaccia delle transazioni end-to-end](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="view-your-data-with-queries"></a>Visualizzare i dati con le query

1. È possibile visualizzare i dati di telemetria inviati dall'applicazione tramite la scheda log (Analytics).

    ![Screenshot del riquadro Panoramica con i log (Analytics) selezionati in una casella rossa](./media/opencensus-python/0010-logs-query.png)

2. Per i dati di telemetria inviati con l'utilità di esportazione della traccia di monitoraggio di Azure, le richieste in ingresso verranno visualizzate in `requests` e le richieste out-going/in process verranno visualizzate sotto `dependencies`.

3. Per i dati di telemetria inviati con l'utilità di esportazione delle metriche di monitoraggio di Azure, le metriche inviate vengono visualizzate sotto `customMetrics`.

4. Per i dati di telemetria inviati con l'utilità di esportazione dei log di monitoraggio di Azure, i log vengono visualizzati in `traces` e le eccezioni vengono visualizzate in `exceptions`.

5. Per informazioni più dettagliate su come usare le query e i log, vedere i [log in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) .

## <a name="opencensus-for-python"></a>OpenCensus per Python

* [OpenCensus Python su GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Personalizzazione](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Integrazione di Flask](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Integrazione di Django](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [Integrazione di MySQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Passaggi successivi

* [Riepilogo API](./../../azure-monitor/app/api-custom-events-metrics.md)
* [Mappa delle applicazioni](./../../azure-monitor/app/app-map.md)
* [Monitoraggio delle prestazioni end-to-end](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Avvisi

* [Test di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md): creare test per verificare che il sito sia visibile sul Web.
* [Diagnostica intelligente](../../azure-monitor/app/proactive-diagnostics.md): questi test vengono eseguiti automaticamente e non è quindi necessario effettuare alcuna operazione per configurarli. Se l'app ha una frequenza insolita di richieste non riuscite, verrà comunicato automaticamente.
* [Avvisi metrica](../../azure-monitor/app/alerts.md): impostare gli avvisi in modo da ricevere un avviso se una metrica supera una soglia. È possibile impostarli nelle metriche personalizzate di cui si scrive il codice nell'app.