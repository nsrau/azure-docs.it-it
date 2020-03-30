---
title: Monitorare le applicazioni Python con Monitor di Azure (anteprima)Monitor Python applications with Azure Monitor (preview) Documenti Microsoft
description: Fornisce istruzioni per collegare OpenCensus Python con Azure Monitor
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6ef0675e3ae3f7a5da38138177f3033051723411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537109"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Configurare Monitoraggio di Azure per l'applicazione PythonSet up Azure Monitor for your Python application

Monitoraggio di Azure supporta la traccia distribuita, la raccolta di metriche e la registrazione delle applicazioni Python tramite l'integrazione con [OpenCensus](https://opencensus.io). Questo articolo illustra il processo di configurazione di OpenCensus per Python e l'invio dei dati di monitoraggio a Monitoraggio di Azure.This article will walk you through the process of setting up OpenCensus for Python and sending your monitoring data to Azure Monitor.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Installazione di Python. In questo articolo viene utilizzato [Python 3.7.0](https://www.python.org/downloads/), anche se le versioni precedenti probabilmente funzioneranno con modifiche minori.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale](https://portal.azure.com/)di Azure .

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Creare una risorsa di Application Insights in Monitoraggio di AzureCreate an Application Insights resource in Azure Monitor

In primo luogo è necessario creare una risorsa di Application Insights in Monitoraggio di Azure, che genererà una chiave di strumentazione (ikey). The ikey is then used to configure the OpenCensus SDK to send telemetry data to Azure Monitor.

1. Selezionare **Crea una risorsa** > **Strumenti** > di sviluppo**Application Insights**.

   ![Aggiunta di una risorsa di Application Insights](./media/opencensus-python/0001-create-resource.png)

1. Viene visualizzata una casella di configurazione. Utilizzare la tabella seguente per compilare i campi di input.

   | Impostazione        | valore           | Descrizione  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valore unico a livello globale | Nome che identifica l'app da monitorare |
   | **Gruppo di risorse**     | myResourceGroup      | Nome del nuovo gruppo di risorse per ospitare i dati di Application InsightsName for the new resource group to host Application Insights data |
   | **Percorso** | Stati Uniti orientali | Una posizione vicino a te o vicino alla posizione in cui è ospitata l'app |

1. Selezionare **Crea**.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Strumento con OpenCensus Python SDK per Azure Monitor

Installare gli esportatori di Monitor di Azure OpenCensus:Install the OpenCensus Azure Monitor exporters:

```console
python -m pip install opencensus-ext-azure
```

Per un elenco completo dei pacchetti e delle integrazioni, consultate [Pacchetti OpenCensus.](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus)

> [!NOTE]
> Il `python -m pip install opencensus-ext-azure` comando presuppone che `PATH` sia stata impostata una variabile di ambiente per l'installazione di Python.The command assumes that you have a environment variable set for your Python installation. Se non hai configurato questa variabile, devi dare il percorso completo della directory dove si trova l'eseguibile Python. Il risultato è un `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`comando come questo: .

L'SDK usa tre strumenti di esportazione di Monitoraggio di Azure per inviare diversi tipi di dati di telemetria a Monitoraggio di Azure: traccia, metriche e log. Per altre informazioni su questi tipi di telemetria, vedere [Panoramica della piattaforma dati.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform) Usare le istruzioni seguenti per inviare questi tipi di telemetria tramite i tre esportatori.

## <a name="telemetry-type-mappings"></a>Mapping dei tipi di telemetriaTelemetry type mappings

Ecco gli esportatori forniti da OpenCensus mappati ai tipi di telemetria visualizzati in Monitoraggio di Azure.Here are the exporters that OpenCensus provides mapped to the types of telemetry that you will see in Azure Monitor.

![Screenshot del mapping dei tipi di telemetria da OpenCensus a Monitoraggio di Azure](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>Trace

> [!NOTE]
> `Trace`in OpenCensus si riferisce [all'analisi distribuita](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing). L'invio `dependency` e la telemetria in Monitoraggio di Azure.The `AzureExporter` sends `requests` and telemetry to Azure Monitor.

1. In primo luogo, è possibile generare alcuni dati di traccia in locale. In Python IDLE, o l'editor di scelta, immettere il codice seguente.

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

2. L'esecuzione del codice genera una richiesta ripetuta di immissione di un valore. Con ogni voce, il valore verrà stampato nella shell e il modulo `SpanData`Python OpenCensus genererà una parte corrispondente di . Il progetto OpenCensus definisce una [traccia sotto forma di albero di intervalli](https://opencensus.io/core-concepts/tracing/).
    
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

3. Anche se l'immissione di valori è utile `SpanData` a scopo dimostrativo, in ultima analisi, si vuole generare il monitor di Azure.Although entering values is helpful for demonstration purposes, ultimately we want to emit the to Azure Monitor. Passare la stringa di connessione direttamente nell'esportatore oppure specificarla in una variabile `APPLICATIONINSIGHTS_CONNECTION_STRING`di ambiente . Modificare il codice del passaggio precedente in base all'esempio di codice seguente:Modify your code from the previous step based on the following code sample:

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

4. Ora, quando si esegue lo script Python, dovrebbe essere ancora richiesto di immettere i valori, ma solo il valore viene stampato nella shell. La `SpanData` creazione verrà inviata a Monitoraggio di Azure.The created will be sent to Azure Monitor. È possibile trovare i dati `dependencies`dell'intervallo emessi in . Per ulteriori informazioni sulle richieste in uscita, consultate [Dipendenze](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency)Python OpenCensus.
Per ulteriori informazioni sulle richieste in arrivo, consultate [Richieste](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request)Python OpenCensus.

#### <a name="sampling"></a>campionamento

Per informazioni sul campionamento in OpenCensus, dai un'occhiata al [campionamento in OpenCensus.](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)

#### <a name="trace-correlation"></a>Correlazione traccia

Per informazioni dettagliate sulla correlazione dei dati di telemetria nei dati di traccia, vedere [la correlazione](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)di telemetria di OpenCensus Python .

#### <a name="modify-telemetry"></a>Modificare i dati di telemetriaModify telemetry

Per informazioni dettagliate su come modificare i dati di telemetria rilevati prima che vengano inviati ad Azure Monitor, vedere Processori di [telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)Python OpenCensus.

### <a name="metrics"></a>Metriche

1. In primo luogo, è possibile generare alcuni dati di metrica locale. Creeremo una metrica semplice per tenere traccia del numero di volte in cui l'utente preme Invio.

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
2. L'esecuzione del codice richiederà ripetutamente di premere INVIO. Viene creata una metrica per tenere traccia del numero di volte in cui Invio viene premuto. Con ogni voce, il valore verrà incrementato e le informazioni sulla metrica verranno visualizzate nella console. Le informazioni includono il valore corrente e il timestamp corrente quando la metrica è stata aggiornata.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Anche se l'immissione di valori è utile a scopo dimostrativo, in ultima analisi, si desidera generare i dati delle metriche in Monitoraggio di Azure.Although entering values is helpful for demonstration purposes, ultimately we want to emit the metric data to Azure Monitor. Passare la stringa di connessione direttamente nell'esportatore oppure specificarla in una variabile `APPLICATIONINSIGHTS_CONNECTION_STRING`di ambiente . Modificare il codice del passaggio precedente in base all'esempio di codice seguente:Modify your code from the previous step based on the following code sample:

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

4. L'esportatore invierà i dati delle metriche ad Monitoraggio di Azure a intervalli fissi. Il valore predefinito è ogni 15 secondi. Stiamo monitorando una singola metrica, quindi questi dati della metrica, con qualsiasi valore e timestamp in esso contenuti, verranno inviati ogni intervallo. È possibile trovare `customMetrics`i dati in .

#### <a name="standard-metrics"></a>Metriche standard

Per impostazione predefinita, l'esefservere di metriche invierà un set di metriche standard a Monitoraggio di Azure.By default, the metrics exporter will send a set of standard metrics to Azure Monitor. È possibile disabilitare `enable_standard_metrics` questa `False` opzione impostando il flag su nel costruttore dell'esportatore di metriche.

    ```python
    ...
    exporter = metrics_exporter.new_metrics_exporter(
      enable_standard_metrics=False,
      connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    ...
    ```
Di seguito è riportato un elenco delle metriche standard attualmente inviate:

- Memoria disponibile (byte)
- Tempo processore CPU (percentuale)
- Frequenza richieste in ingresso (al secondo)
- Tempo medio di esecuzione delle richieste in ingresso (millisecondi)Incoming Request Average Execution Time (milliseconds)
- Frequenza richieste in uscita (al secondo)
- Utilizzo CPU processo (percentuale)
- Elabora byte privati (byte)

Dovresti essere in grado di `performanceCounters`vedere queste metriche in . La frequenza delle `customMetrics`richieste in ingresso sarebbe inferiore a . Per ulteriori informazioni, vedere [contatori delle prestazioni](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters).

#### <a name="modify-telemetry"></a>Modificare i dati di telemetriaModify telemetry

Per informazioni dettagliate su come modificare i dati di telemetria rilevati prima che vengano inviati ad Azure Monitor, vedere Processori di [telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)Python OpenCensus.

### <a name="logs"></a>Log

1. In primo luogo, è possibile generare alcuni dati di log locali.

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

2.  Il codice richiederà continuamente l'immissione di un valore. Viene generata una voce di log per ogni valore immesso.

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

3. Anche se l'immissione di valori è utile a scopo dimostrativo, in ultima analisi, si desidera generare i dati di log in Monitoraggio di Azure.Although entering values is helpful for demonstration purposes, ultimately we want to emit the log data to Azure Monitor. Passare la stringa di connessione direttamente nell'esportatore oppure specificarla in una variabile `APPLICATIONINSIGHTS_CONNECTION_STRING`di ambiente . Modificare il codice del passaggio precedente in base all'esempio di codice seguente:Modify your code from the previous step based on the following code sample:

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

4. L'esportatore invierà i dati di log ad Azure Monitor.The exporter will send log data to Azure Monitor. È possibile trovare `traces`i dati in . 

> [!NOTE]
> `traces`in questo contesto non `Tracing`è lo stesso di . `traces`Fa riferimento al tipo di dati di telemetria `AzureLogHandler`che verrà visualizzato in Monitoraggio di Azure quando si utilizza l'oggetto . `Tracing`si riferisce a un concetto in OpenCensus e si riferisce al [tracciamento distribuito](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing).

5. Per formattare i messaggi di `formatters` log, è possibile utilizzare nell'API di [registrazione](https://docs.python.org/3/library/logging.html#formatter-objects)Python incorporata.

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    format_str = '%(asctime)s - %(levelname)-8s - %(message)s'
    date_format = '%Y-%m-%d %H:%M:%S'
    formatter = logging.Formatter(format_str, date_format)
    # TODO: replace the all-zero GUID with your instrumentation key.
    handler = AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

6. È inoltre possibile aggiungere proprietà personalizzate ai messaggi di log nell'argomento della parola chiave *aggiuntiva* utilizzando il campo custom_dimensions. Questi verranno visualizzati come `customDimensions` coppie chiave-valore in Monitoraggio di Azure.These will appear as key-value pairs in Azure Monitor.
> [!NOTE]
> Affinché questa funzione funzioni, è necessario passare un dizionario al campo custom_dimensions. Se si passano argomenti di qualsiasi altro tipo, il logger li ignorerà.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="sending-exceptions"></a>Invio di eccezioni

OpenCensus Python non tiene `exception` traccia e invia automaticamente dati di telemetria. Vengono inviati tramite `AzureLogHandler` le eccezioni tramite la libreria di registrazione Python. È possibile aggiungere proprietà personalizzate come con la registrazione normale.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in exception logs
    try:
        result = 1 / 0  # generate a ZeroDivisionError
    except Exception:
        logger.exception('Captured an exception.', extra=properties)
    ```
Poiché è necessario registrare le eccezioni in modo esplicito, spetta all'utente come desidera registrare le eccezioni non gestite. OpenCensus non imposta restrizioni nel modo in cui un utente desidera eseguire questa operazione, purché registri in modo esplicito una telemetria delle eccezioni.

#### <a name="sampling"></a>campionamento

Per informazioni sul campionamento in OpenCensus, dai un'occhiata al [campionamento in OpenCensus.](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)

#### <a name="log-correlation"></a>Correlazione dei registri

Per informazioni dettagliate su come arricchire i log con dati del contesto di traccia, vedere [Integrazione](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation)dei log Python OpenCensus .

#### <a name="modify-telemetry"></a>Modificare i dati di telemetriaModify telemetry

Per informazioni dettagliate su come modificare i dati di telemetria rilevati prima che vengano inviati ad Azure Monitor, vedere Processori di [telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)Python OpenCensus.

## <a name="view-your-data-with-queries"></a>Visualizzare i dati con le query

È possibile visualizzare i dati di telemetria inviati dall'applicazione tramite la scheda **Registri (Analisi).**

![Screenshot del riquadro panoramico con l'opzione "Registri (analitica)" selezionata in una casella rossa](./media/opencensus-python/0010-logs-query.png)

Nell'elenco in **Attivo**:

- Per i dati di telemetria inviati con `requests`l'elenco di esportazione di traccia di Monitoraggio di Azure, le richieste in ingresso vengono visualizzate in . Le richieste in uscita `dependencies`o in-process vengono visualizzate in .
- Per i dati di telemetria inviati con `customMetrics`l'esportatore di metriche di Monitoraggio di Azure, le metriche inviate vengono visualizzate in .
- Per i dati di telemetria inviati con `traces`l'elenco di esportazione dei log di Monitoraggio di Azure, i log vengono visualizzati in . Le eccezioni `exceptions`vengono visualizzate in .

Per informazioni più dettagliate su come usare query e log, vedere [Log in Monitoraggio di Azure.For](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)more detailed information about how to use queries and logs, see Logs in Azure Monitor .

## <a name="learn-more-about-opencensus-for-python"></a>Scopri di più su OpenCensus per Python

* [OpenCensus Python su GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Personalizzazione](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Azure Monitor Exporters on GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [Integrazioni OpenCensus](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Applicazioni di esempio di Monitoraggio di AzureAzure Monitor Sample Applications](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Passaggi successivi

* [Monitoraggio delle richieste in arrivo](./../../azure-monitor/app/opencensus-python-dependency.md)
* [Monitoraggio delle richieste in uscitaTracking Out-going requests](./../../azure-monitor/app/opencensus-python-request.md)
* [Mappa delle applicazioni](./../../azure-monitor/app/app-map.md)
* [Monitoraggio delle prestazioni end-to-end](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Avvisi

* [Test di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md): creare test per verificare che il sito sia visibile sul Web.
* [Diagnostica intelligente](../../azure-monitor/app/proactive-diagnostics.md): questi test vengono eseguiti automaticamente e non è quindi necessario effettuare alcuna operazione per configurarli. Se l'app ha una frequenza insolita di richieste non riuscite, verrà comunicato automaticamente.
* [Avvisi metriche:](../../azure-monitor/app/alerts.md)imposta avvisi per avvisarti se una metrica supera una soglia. È possibile impostarli nelle metriche personalizzate di cui si scrive il codice nell'app.
