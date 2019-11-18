---
title: Monitorare le applicazioni Python con monitoraggio di Azure (anteprima) | Microsoft Docs
description: Fornisce istruzioni per collegare OpenCensus Python con monitoraggio di Azure
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: ca34a92dc69cb500efb55f575420d47607cd1a46
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132218"
---
# <a name="set-up-azure-monitor-for-your-python-application-preview"></a>Configurare monitoraggio di Azure per l'applicazione Python (anteprima)

Monitoraggio di Azure supporta la traccia distribuita, la raccolta delle metriche e la registrazione delle applicazioni Python tramite l'integrazione con [OpenCensus](https://opencensus.io). Questo articolo illustra il processo di configurazione di OpenCensus per Python e l'invio dei dati di monitoraggio a monitoraggio di Azure.

## <a name="prerequisites"></a>prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Installazione di Python. Questo articolo usa [Python 3.7.0](https://www.python.org/downloads/), anche se le versioni precedenti potrebbero funzionare con modifiche minime.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Creare una risorsa Application Insights in monitoraggio di Azure

Prima di tutto è necessario creare una risorsa Application Insights in monitoraggio di Azure, che genererà una chiave di strumentazione (IKey). IKey viene quindi usato per configurare OpenCensus SDK per inviare i dati di telemetria a monitoraggio di Azure.

1. Selezionare **Crea una risorsa** > **Strumenti di sviluppo** > **Application Insights**.

   ![Aggiunta di una risorsa Application Insights](./media/opencensus-python/0001-create-resource.png)

1. Viene visualizzata una finestra di configurazione. Usare la tabella seguente per compilare i campi di input.

   | Impostazione        | Valore           | DESCRIZIONE  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valore univoco globale | Nome che identifica l'app che si sta monitorando |
   | **Gruppo di risorse**     | myResourceGroup      | Nome del nuovo gruppo di risorse per ospitare i dati Application Insights |
   | **Località** | Stati Uniti Orientali | Una località nelle vicinanze o vicino alla posizione in cui è ospitata l'app |

1. Selezionare **Create**.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumentare con OpenCensus Python SDK per monitoraggio di Azure

Installare gli esportatori di monitoraggio di Azure OpenCensus:

```console
python -m pip install opencensus-ext-azure
```

Per un elenco completo dei pacchetti e delle integrazioni, vedere [pacchetti OpenCensus](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus).

> [!NOTE]
> Il comando `python -m pip install opencensus-ext-azure` presuppone che sia stata impostata una variabile di ambiente `PATH` per l'installazione di Python. Se questa variabile non è stata configurata, è necessario fornire il percorso completo della directory in cui si trova il file eseguibile di Python. Il risultato è un comando simile al seguente: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

L'SDK usa tre utilità di esportazione di monitoraggio di Azure per inviare diversi tipi di dati di telemetria a monitoraggio di Azure: traccia, metriche e log. Per altre informazioni su questi tipi di telemetria, vedere [Panoramica della piattaforma dati](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Usare le istruzioni seguenti per inviare questi tipi di dati di telemetria tramite i tre esportatori.

### <a name="trace"></a>Trace

1. Prima di tutto, è necessario generare alcuni dati di traccia localmente. In Python inattivo, o l'editor preferito, immettere il codice seguente.

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

2. L'esecuzione del codice genera una richiesta ripetuta di immissione di un valore. Con ogni voce, il valore verrà stampato nella shell e il modulo Python di OpenCensus genererà un componente di `SpanData`corrispondente. Il progetto OpenCensus definisce una [traccia come albero di intervalli](https://opencensus.io/core-concepts/tracing/).
    
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

3. Sebbene l'immissione di valori sia utile a scopo dimostrativo, in definitiva si vuole creare il `SpanData` in monitoraggio di Azure. Modificare il codice del passaggio precedente in base all'esempio di codice seguente:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
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

4. A questo punto, quando si esegue lo script Python, viene comunque richiesto di immettere i valori, ma solo il valore viene stampato nella shell. Il `SpanData` creato verrà inviato a monitoraggio di Azure. È possibile trovare i dati di intervallo emessi in `dependencies`.

5. Per informazioni sul campionamento in OpenCensus, vedere [campionamento in OpenCensus](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-fixed-rate-sampling-in-opencensus-python).

6. Per informazioni dettagliate sulla correlazione dei dati di telemetria nei dati di traccia, esaminare la [correlazione di telemetria](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)di OpenCensus.

### <a name="metrics"></a>Metrica

1. Prima di tutto, è necessario generare alcuni dati sulle metriche locali. Verrà creata una metrica semplice per tenere traccia del numero di volte in cui l'utente preme INVIO.

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
2. L'esecuzione del codice richiederà ripetutamente di premere INVIO. Viene creata una metrica per tenere traccia del numero di volte in cui viene premuto INVIO. Con ogni voce, il valore verrà incrementato e le informazioni sulla metrica verranno visualizzate nella console di. Le informazioni includono il valore corrente e il timestamp corrente al momento dell'aggiornamento della metrica.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Sebbene l'immissione di valori sia utile a scopo dimostrativo, in definitiva si vuole creare i dati delle metriche in monitoraggio di Azure. Modificare il codice del passaggio precedente in base all'esempio di codice seguente:

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

4. L'utilità di esportazione invierà i dati delle metriche al monitoraggio di Azure a intervalli fissi. Il valore predefinito è ogni 15 secondi. Stiamo monitorando una singola metrica, quindi questi dati della metrica, con qualsiasi valore e timestamp in esso contenuti, verranno inviati ogni intervallo. È possibile trovare i dati in `customMetrics`.

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

2.  Il codice richiederà continuamente l'immissione di un valore. Viene emessa una voce di log per ogni valore immesso.

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

3. Sebbene l'immissione di valori sia utile a scopo dimostrativo, in definitiva si vuole creare i dati delle metriche in monitoraggio di Azure. Modificare il codice del passaggio precedente in base all'esempio di codice seguente:

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

5. Per informazioni dettagliate su come arricchire i log con i dati del contesto di traccia, vedere OpenCensus Python [logs Integration](https://docs.microsoft.com/azure/azure-monitor/app/correlation#logs-correlation).

## <a name="view-your-data-with-queries"></a>Visualizzare i dati con le query

È possibile visualizzare i dati di telemetria inviati dall'applicazione tramite la scheda **log (Analytics)** .

![Screenshot del riquadro della panoramica con "logs (Analytics)" selezionato in una casella rossa](./media/opencensus-python/0010-logs-query.png)

Nell'elenco in **attivo**:

- Per la telemetria inviata con l'utilità di esportazione della traccia di monitoraggio di Azure, le richieste in ingresso vengono visualizzate in `requests`. Le richieste in uscita o in-process vengono visualizzate in `dependencies`.
- Per i dati di telemetria inviati con l'utilità di esportazione delle metriche di monitoraggio di Azure, le metriche inviate vengono visualizzate in `customMetrics`.
- Per i dati di telemetria inviati con l'utilità di esportazione log di monitoraggio di Azure, i log vengono visualizzati in `traces`. Le eccezioni vengono visualizzate in `exceptions`.

Per informazioni più dettagliate su come usare le query e i log, vedere [log in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## <a name="learn-more-about-opencensus-for-python"></a>Scopri di più su OpenCensus per Python

* [OpenCensus Python su GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Personalizzazione](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Integrazione Flask](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Integrazione con Django](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [Integrazione con MySQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Passaggi successivi

* [Mappa delle applicazioni](./../../azure-monitor/app/app-map.md)
* [Monitoraggio delle prestazioni end-to-end](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Alerts

* [Test di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md): creare test per verificare che il sito sia visibile sul Web.
* [Diagnostica intelligente](../../azure-monitor/app/proactive-diagnostics.md): questi test vengono eseguiti automaticamente e non è quindi necessario effettuare alcuna operazione per configurarli. Se l'app ha una frequenza insolita di richieste non riuscite, verrà comunicato automaticamente.
* [Avvisi metrica](../../azure-monitor/app/alerts.md): impostare gli avvisi in modo da ricevere un avviso se una metrica supera una soglia. È possibile impostarli nelle metriche personalizzate di cui si scrive il codice nell'app.
