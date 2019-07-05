---
title: Monitorare le applicazioni Python con Azure Application Insights | Microsoft Docs
description: Vengono fornite istruzioni per collegare i OpenCensus Python con Application Insights
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 07/02/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 2c043ad793dcf5e59eaf460d1ec4aa7a3b48810d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541446"
---
# <a name="collect-distributed-traces-from-python-preview"></a>Raccogliere tracce distribuite da Python (anteprima)

Application Insights ora supporta l'analisi delle applicazioni Python attraverso l'integrazione con distribuita [OpenCensus](https://opencensus.io). Questo articolo descrive in dettaglio come attraverso il processo di impostazione OpenCensus per Python e il monitoraggio dei dati ad Application Insights.

## <a name="prerequisites"></a>Prerequisiti

- È necessaria una sottoscrizione di Azure.
- Deve essere installato Python. Questo articolo usa [Python 3.7.0](https://www.python.org/downloads/), ma apportando modifiche minime funzioneranno anche le versioni precedenti.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Creare una risorsa di Application Insights

Prima di tutto è necessario creare una risorsa di Application Insights, che genererà un key(ikey) strumentazione. La chiave di strumentazione viene quindi utilizzato per configurare il SDK OpenCensus per inviare i dati di telemetria ad Application Insights.

1. Selezionare **Crea una risorsa** > **Strumenti di sviluppo** > **Application Insights**.

   ![Aggiunta di una risorsa di Application Insights](./media/opencensus-python/0001-create-resource.png)

   Verrà visualizzata una casella di configurazione. Usare la tabella seguente per completare i campi di input.

    | Impostazioni        | Value           | DESCRIZIONE  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valore globalmente univoco | Nome che identifica l'app da monitorare |
   | **Gruppo di risorse**     | myResourceGroup      | Nome del nuovo gruppo di risorse per l'hosting dei dati di Application Insights |
   | **Location** | East US | Scegliere una località nelle vicinanze o vicina a quella in cui è ospitata l'app |

2. Fare clic su **Create**(Crea).

## <a name="install-opencensus-azure-monitor-exporters"></a>Installare Azure OpenCensus monitoraggio esportabili

1. Installare l'utilità di esportazione di Azure OpenCensus monitoraggio:

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` presuppone la presenza di una variabile di ambiente PATH impostata per l'installazione di Python. Se questa non è stata configurata, sarà necessario specificare il percorso completo della directory in cui si trova l'eseguibile di Python, che può restituire un comando simile a questo: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

2. Prima di tutto, è necessario generare alcuni dati di traccia in locale. In Python IDLE o nell'editor preferito immettere il codice seguente:

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

3. L'esecuzione del codice genera una richiesta ripetuta di immissione di un valore. Con ogni voce, il valore verrà stampato nella shell e un elemento corrispondente di **SpanData** verrà generato dal modulo OpenCensus Python. Il progetto OpenCensus definisce una [ _traccia sotto forma di albero di intervalli_](https://opencensus.io/core-concepts/tracing/).
    
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

4. Mentre utile a scopo dimostrativo, in definitiva si desidera generare il SpanData ad Application Insights. Modificare il codice nel passaggio precedente basato sull'esempio di codice seguente:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            instrumentation_key='00000000-0000-0000-0000-000000000000',
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
5. Quando si esegue lo script di Python, deve ancora essere richiesto di immettere i valori, ma ora solo il valore viene stampato nella shell.

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

## <a name="opencensus-for-python"></a>OpenCensus per Python

* [Personalizzazione](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Integrazione di Flask](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Integrazione di Django](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [Integrazione di MySQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Passaggi successivi

* [OpenCensus Python su GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Mappa delle applicazioni](./../../azure-monitor/app/app-map.md)
* [Monitoraggio delle prestazioni end-to-end](./../../azure-monitor/learn/tutorial-performance.md)