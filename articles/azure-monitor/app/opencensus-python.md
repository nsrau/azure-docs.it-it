---
title: Monitorare le applicazioni Python con Monitoraggio di Azure (anteprima) | Microsoft Docs
description: Fornisce istruzioni per collegare OpenCensus Python con Monitoraggio di Azure
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6b8343d08962d8ce749e1160b0226b68571571f8
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/24/2020
ms.locfileid: "83815724"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Configurare Monitoraggio di Azure per l'applicazione Python

Monitoraggio di Azure supporta la traccia distribuita, la raccolta di metriche e la registrazione delle applicazioni Python tramite l'integrazione con [OpenCensus](https://opencensus.io). Questo articolo descrive il processo di configurazione di OpenCensus per Python e invio dei dati di monitoraggio a Monitoraggio di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Installazione di Python. Questo articolo usa [Python 3.7.0](https://www.python.org/downloads/), ma apportando modifiche minime funzioneranno anche le versioni precedenti.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Creare una risorsa di Application Insights in Monitoraggio di Azure

Prima di tutto, è necessario creare in Monitoraggio di Azure una risorsa di Application Insights, che genererà una chiave di strumentazione (iKey). La chiave di strumentazione viene quindi usata per configurare l'SDK OpenCensus per inviare i dati di telemetria a Monitoraggio di Azure.

1. Selezionare **Crea una risorsa** > **Strumenti di sviluppo** > **Application Insights**.

   ![Aggiunta di una risorsa di Application Insights](./media/opencensus-python/0001-create-resource.png)

1. Viene visualizzata una finestra di configurazione. Usare la tabella seguente per completare i campi di input.

   | Impostazione        | valore           | Descrizione  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valore globalmente univoco | Nome che identifica l'app da monitorare |
   | **Gruppo di risorse**     | myResourceGroup      | Nome del nuovo gruppo di risorse in cui ospitare i dati di Application Insights |
   | **Posizione** | Stati Uniti orientali | Una località nelle vicinanze o vicina a quella in cui è ospitata l'app |

1. Selezionare **Create** (Crea).

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumentare con l'SDK OpenCensus Python per Monitoraggio di Azure

Installare le utilità di esportazione di Monitoraggio di Azure per OpenCensus:

```console
python -m pip install opencensus-ext-azure
```

Per un elenco completo dei pacchetti e delle integrazioni, vedere i [pacchetti OpenCensus](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus).

> [!NOTE]
> Il comando `python -m pip install opencensus-ext-azure` presuppone la presenza di una variabile di ambiente `PATH` impostata per l'installazione di Python. Se questa non è stata configurata, sarà necessario specificare il percorso completo della directory in cui si trova l'eseguibile di Python. Il risultato è un comando simile al seguente: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

L'SDK usa tre utilità di esportazione di Monitoraggio di Azure per inviare diversi tipi di dati di telemetria a Monitoraggio di Azure: tracce, metriche e log. Per altre informazioni su questi tipi di telemetria, vedere la [panoramica della piattaforma dati](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Usare le istruzioni seguenti per inviare questi tipi di dati di telemetria tramite le tre utilità di esportazione.

## <a name="telemetry-type-mappings"></a>Mapping dei tipi di telemetria

Ecco le utilità di esportazione fornite da OpenCensus, mappate ai tipi di dati di telemetria che verranno visualizzati in Monitoraggio di Azure.

![Screenshot del mapping dei tipi di dati di telemetria da OpenCensus a Monitoraggio di Azure](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>Trace

> [!NOTE]
> `Trace` in OpenCensus si riferisce alla [traccia distribuita](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing). `AzureExporter` invia la telemetria per `requests` e `dependency` a Monitoraggio di Azure.

1. Prima di tutto, è necessario generare alcuni dati di traccia in locale. In Python IDLE o nell'editor preferito immettere il codice seguente.

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

2. L'esecuzione del codice genera una richiesta ripetuta di immissione di un valore. Con ogni voce, il valore verrà stampato nella shell e il modulo OpenCensus Python genererà un elemento corrispondente di `SpanData`. Nel progetto OpenCensus, una [trace è un albero di span](https://opencensus.io/core-concepts/tracing/).
    
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

3. Sebbene l'immissione di valori sia utile a scopo dimostrativo, in definitiva si vogliono trasmettere `SpanData` in Monitoraggio di Azure. Passare la stringa di connessione direttamente all'utilità di esportazione oppure specificarla in una variabile di ambiente `APPLICATIONINSIGHTS_CONNECTION_STRING`. Modificare il codice del passaggio precedente in base all'esempio di codice seguente:

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

4. A questo punto, quando si esegue lo script Python, verrà ancora richiesto di immettere valori, ma verrà stampato nella shell solo il valore. L'elemento `SpanData` creato verrà inviato a Monitoraggio di Azure. È possibile trovare i dati di span emessi in `dependencies`. Per altri dettagli sulle richieste in uscita, vedere le [dipendenze](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency) di OpenCensus Python.
Per altri dettagli sulle richieste in ingresso, vedere le [richieste](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request) di OpenCensus Python.

#### <a name="sampling"></a>campionamento

Per informazioni sul campionamento in OpenCensus, vedere l'articolo sul [campionamento in OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Correlazione delle tracce

Per informazioni dettagliate sulla correlazione della telemetria nei dati di traccia, vedere [Correlazione di dati di telemetria in OpenCensus Python](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python).

#### <a name="modify-telemetry"></a>Modificare la telemetria

Per informazioni dettagliate su come modificare la telemetria registrata prima dell'invio a Monitoraggio di Azure, vedere i [processori di telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors) OpenCensus Python.

### <a name="metrics"></a>Metriche

1. Prima di tutto, generare alcuni dati di metrica locali. Verrà creata una metrica semplice per tenere traccia del numero di volte che l'utente preme INVIO.

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
2. L'esecuzione del codice genera una richiesta ripetuta di premere INVIO. Viene creata una metrica per tenere traccia del numero di volte che si è premuto INVIO. Con ogni voce, il valore verrà incrementato e le informazioni sulla metrica verranno visualizzate nella console. Le informazioni includono il valore corrente e il timestamp corrente al momento dell'aggiornamento della metrica.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Sebbene l'immissione di valori sia utile a scopo dimostrativo, in definitiva si vogliono trasmettere i dati della metrica in Monitoraggio di Azure. Passare la stringa di connessione direttamente all'utilità di esportazione oppure specificarla in una variabile di ambiente `APPLICATIONINSIGHTS_CONNECTION_STRING`. Modificare il codice del passaggio precedente in base all'esempio di codice seguente:

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

4. L'utilità di esportazione invierà i dati di metrica a Monitoraggio di Azure a intervalli fissi. Il valore predefinito è ogni 15 secondi. Si sta monitorando una singola metrica, quindi questi dati di metrica, con qualsiasi valore e timestamp contenuti, verranno inviati a ogni intervallo. È possibile trovare i dati in `customMetrics`.

#### <a name="standard-metrics"></a>Metriche standard

Per impostazione predefinita, l'utilità di esportazione delle metriche invierà un set di metriche standard a Monitoraggio di Azure. Si può disabilitare questa impostazione impostando il flag di `enable_standard_metrics` su `False` nel costruttore dell'utilità di esportazione delle metriche.

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```
Di seguito è riportato un elenco di metriche standard attualmente inviate:

- Memoria disponibile (byte)
- Tempo processore CPU (percentuale)
- Frequenza richieste in ingresso (al secondo)
- Tempo di esecuzione medio richieste in ingresso (millisecondi)
- Frequenza richieste in uscita (al secondo)
- Utilizzo CPU processi (percentuale)
- Byte privati processi (byte)

Dovrebbe essere possibile visualizzare queste metriche in `performanceCounters`. La frequenza delle richieste in ingresso è in `customMetrics`. Per altre informazioni, vedere i [contatori delle prestazioni](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters).

#### <a name="modify-telemetry"></a>Modificare la telemetria

Per informazioni dettagliate su come modificare la telemetria registrata prima dell'invio a Monitoraggio di Azure, vedere i [processori di telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors) OpenCensus Python.

### <a name="logs"></a>Log

1. Prima di tutto, generare alcuni dati di log locali.

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

2.  Il codice richiederà continuamente l'immissione di un valore. Per ogni valore immesso viene generata una voce di log.

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

3. Sebbene l'immissione di valori sia utile a scopo dimostrativo, in definitiva si vogliono trasmettere i dati di log in Monitoraggio di Azure. Passare la stringa di connessione direttamente all'utilità di esportazione oppure specificarla in una variabile di ambiente `APPLICATIONINSIGHTS_CONNECTION_STRING`. Modificare il codice del passaggio precedente in base all'esempio di codice seguente:

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

4. L'utilità di esportazione invierà i dati di log a Monitoraggio di Azure. È possibile trovare i dati in `traces`. 

    > [!NOTE]
    > `traces` in questo contesto non è uguale a `Tracing`. `traces` si riferisce al tipo di dati di telemetria che si vedranno in Monitoraggio di Azure quando si utilizza `AzureLogHandler`. `Tracing` si riferisce a un concetto di OpenCensus ed è relativo alla [traccia distribuita](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing).

5. Per formattare i messaggi di log, è possibile usare degli oggetti `formatters` nell'[API di registrazione Python](https://docs.python.org/3/library/logging.html#formatter-objects) integrata.

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

6. È anche possibile aggiungere proprietà personalizzate ai messaggi di log nell'argomento della parola chiave *extra* usando il campo custom_dimensions. Verranno visualizzate come coppie chiave-valore in `customDimensions` in Monitoraggio di Azure.
    > [!NOTE]
    > Per il corretto funzionamento di questa caratteristica, è necessario passare un dizionario al campo custom_dimensions. Se si passano argomenti di un altro tipo, il logger li ignorerà.

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

OpenCensus Python non monitora e invia automaticamente i dati di telemetria `exception`. Questi dati vengono inviati tramite `AzureLogHandler` usando le eccezioni tramite la libreria di registrazione Python. È possibile aggiungere proprietà personalizzate, come con la registrazione normale.

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
Poiché è necessario registrare le eccezioni in modo esplicito, la scelta del modo in cui registrare le eccezioni non gestite spetta all'utente. OpenCensus non pone restrizioni rispetto al metodo da seguire, purché i dati di telemetria delle eccezioni vengano esplicitamente registrati.

#### <a name="sampling"></a>campionamento

Per informazioni sul campionamento in OpenCensus, vedere l'articolo sul [campionamento in OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Correlazione dei log

Per informazioni dettagliate su come arricchire i log con i dati del contesto di traccia, vedere l'[integrazione dei log](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation) OpenCensus Python.

#### <a name="modify-telemetry"></a>Modificare la telemetria

Per informazioni dettagliate su come modificare la telemetria registrata prima dell'invio a Monitoraggio di Azure, vedere i [processori di telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors) OpenCensus Python.

## <a name="view-your-data-with-queries"></a>Visualizzare i dati usando query

È possibile visualizzare i dati di telemetria inviati dall'applicazione tramite la scheda **Log (Analisi)** .

![Screenshot del riquadro della panoramica con "Log (Analisi)" selezionato in una casella rossa](./media/opencensus-python/0010-logs-query.png)

Nell'elenco sotto **Attiva**:

- Per i dati di telemetria inviati con l'utilità di esportazione log di Monitoraggio di Azure, le richieste in ingresso vengono visualizzate in `requests`. Le richieste in uscita o in elaborazione vengono visualizzate in `dependencies`.
- Per i dati di telemetria inviati con l'utilità di esportazione delle metriche di Monitoraggio di Azure, le metriche inviate vengono visualizzate in `customMetrics`.
- Per i dati di telemetria inviati con l'utilità di esportazione log di Monitoraggio di Azure, i log vengono visualizzati in `traces`. Le eccezioni vengono visualizzate in `exceptions`.

Per informazioni più dettagliate su come usare le query e i log, vedere [Log in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## <a name="learn-more-about-opencensus-for-python"></a>Altre informazioni su OpenCensus per Python

* [OpenCensus Python su GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Personalizzazione](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Utilità di esportazione di Monitoraggio di Azure su GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [Integrazioni di OpenCensus](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Applicazioni di esempio di Monitoraggio di Azure](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare le richieste in ingresso](./../../azure-monitor/app/opencensus-python-dependency.md)
* [Monitorare le richieste in uscita](./../../azure-monitor/app/opencensus-python-request.md)
* [Mappa delle applicazioni](./../../azure-monitor/app/app-map.md)
* [Monitoraggio delle prestazioni end-to-end](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Avvisi

* [Test di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md): creare test per assicurarsi che il sito sia visibile sul Web.
* [Diagnostica intelligente](../../azure-monitor/app/proactive-diagnostics.md): questi test vengono eseguiti automaticamente e non è quindi necessario effettuare alcuna operazione per configurarli. Se l'app ha una frequenza insolita di richieste non riuscite, verrà comunicato automaticamente.
* [Avvisi delle metriche](../../azure-monitor/platform/alerts-log.md): Impostare avvisi per essere avvertiti se una metrica supera una soglia. È possibile impostarli nelle metriche personalizzate di cui si scrive il codice nell'app.
