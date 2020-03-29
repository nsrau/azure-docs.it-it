---
title: Debug and troubleshoot machine learning pipelines in Application Insights
titleSuffix: Azure Machine Learning
description: Aggiungere la registrazione alle pipeline di punteggio batch e di formazione e visualizzare i risultati registrati in Application Insights.Add logging to your training and batch scoring pipelines and view the logged results in Application Insights.
services: machine-learning
author: aburek
ms.author: anrode
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.custom: seodec18
ms.openlocfilehash: 85dcd9ef98deb2ea0117f2db280e49c4a57bf00f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776300"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines-in-application-insights"></a>Debug and troubleshoot machine learning pipelines in Application Insights
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

La libreria python [OpenCensus](https://opencensus.io/quickstart/python/) può essere utilizzata per instradare i log ad Application Insights dagli script. L'aggregazione dei log dalle esecuzioni della pipeline in un'unica posizione consente di creare query e diagnosticare i problemi. L'utilizzo di Application Insights consente di tenere traccia dei log nel tempo e confrontare i log della pipeline tra le esecuzioni.

La presenza dei log una sola posizione fornirà una cronologia delle eccezioni e dei messaggi di errore. Poiché Application Insights si integra con Avvisi di Azure, è anche possibile creare avvisi basati su query di Application Insights.As Application Insights integrates with Azure Alerts, you can also create alerts based on Application Insights queries.

## <a name="prerequisites"></a>Prerequisiti

* Seguire i passaggi per creare un'area di lavoro di Azure Machine Learning e [creare la prima pipelineFollow](./how-to-create-your-first-pipeline.md) the steps to create an Azure [Machine Learning](./how-to-manage-workspace.md) workspace and create your first pipeline
* [Configurare il proprio ambiente di sviluppo](./how-to-configure-environment.md) per installare l'SDK di Azure Machine Learning.
* Installare il pacchetto OpenCensus Azure Monitor Exporter in locale:Install the [OpenCensus Azure Monitor Exporter](https://pypi.org/project/opencensus-ext-azure/) package locally:
  ```python
  pip install opencensus-ext-azure
  ```
* Creare [un'istanza](../azure-monitor/app/opencensus-python.md) di Application Insights (questo documento contiene anche informazioni su come ottenere la stringa di connessione per la risorsa)Create an Application Insights instance (this doc also contains information on getting the connection string for the resource)

## <a name="getting-started"></a>Introduzione

Questa sezione è un'introduzione specifica per l'uso di OpenCensus da una pipeline di Azure Machine Learning.This section is an introduction specific to using OpenCensus from an Azure Machine Learning pipeline. Per un'esercitazione dettagliata, vedere [OpenCensus Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

Aggiungere un PythonScriptStep alla pipeline di Azure ML. Configurare [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) con la dipendenza da opencensus-ext-azure. Configurare `APPLICATIONINSIGHTS_CONNECTION_STRING` la variabile di ambiente.

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

Creare un file denominato `sample_step.py`. Importare la classe AzureLogHandler per instradare i log a Application Insights.Import the AzureLogHandler class to route logs to Application Insights. Dovrai anche importare la libreria di registrazione Python.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Successivamente, aggiungere AzureLogHandler al logger di python.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Registrazione con dimensioni personalizzateLogging with Custom Dimensions
 
Per impostazione predefinita, i log inoltrati ad Application Insights non divoranno un contesto sufficiente per risalire all'esecuzione o all'esperimento. Per rendere i registri utilizzabili per la diagnosi dei problemi, sono necessari campi aggiuntivi. 

Per aggiungere questi campi, è possibile aggiungere dimensioni personalizzate per fornire contesto a un messaggio di log. Un esempio è quando un utente desidera visualizzare i log in più passaggi nella stessa esecuzione della pipeline.

Le dimensioni personalizzate costituiscono un dizionario di coppie chiave-valore (archiviate come stringa, stringa). Il dizionario viene quindi inviato ad Application Insights e visualizzato come colonna nei risultati della query. Le singole dimensioni possono essere utilizzate come parametri di [query.](#additional-helpful-queries)

### <a name="helpful-context-to-include"></a>Contesto utile da includere

| Campo                          | Motivazione/Esempio                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | Può interrogare i log per quelli con lo stesso parent_run_id per visualizzare i registri nel tempo per tutti i passaggi, invece di dover approfondire ogni singolo passaggio                                        |
| step_id                        | Può eseguire una query sui log per quelli con lo stesso step_id per vedere dove si è verificato un problema con un ambito ristretto solo per il singolo passaggio                                                        |
| step_name                      | Può eseguire query sui log per visualizzare le prestazioni dei passaggi nel tempo. Aiuta anche a trovare un step_id per le corse recenti senza immergersi nell'interfaccia utente del portale                                          |
| experiment_name                | Può eseguire query nei log per visualizzare le prestazioni dell'esperimento nel tempo. Aiuta anche a trovare un parent_run_id o un step_id per le corse recenti senza immergersi nell'interfaccia utente del portale                   |
| run_url                 | Può fornire un link direttamente alla corsa per l'indagine. |

**Altri campi utili**

Questi campi possono richiedere una strumentazione del codice aggiuntiva e non vengono forniti dal contesto di esecuzione.

| Campo                   | Motivazione/Esempio                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | Se si usa CI/CD per la distribuzione, questo campo può correlare i log alla versione del codice che ha fornito la logica del passaggio e della pipeline. Questo collegamento può ulteriormente aiutare a diagnosticare i problemi o identificare modelli con tratti specifici (valori di log/metriche) |
| run_type                       | Può distinguere tra diversi tipi di modelli o corsi di training e punteggio |

### <a name="creating-a-custom-dimensions-dictionary"></a>Creazione di un dizionario delle quote personalizzate

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", custom_dimensions)
```

## <a name="opencensus-python-logging-considerations"></a>Considerazioni sulla registrazione di OpenCensus Python

Il OpenCensus AzureLogHandler viene usato per instradare i log Python a Application Insights.The OpenCensus AzureLogHandler is used to route Python logs to Application Insights. Di conseguenza, le sfumature di registrazione Python devono essere considerate. Quando viene creato un logger, ha un livello di log predefinito e mostra i registri maggiori o uguali a tale livello. Un buon riferimento per l'utilizzo delle funzionalità di registrazione Python è la [guida di riferimento per](https://docs.python.org/3/howto/logging-cookbook.html)la registrazione .

La `APPLICATIONINSIGHTS_CONNECTION_STRING` variabile di ambiente è necessaria per la libreria OpenCensus.The environment variable is needed for the OpenCensus library. È consigliabile impostare questa variabile di ambiente anziché passarla come parametro della pipeline per evitare il passaggio di stringhe di connessione in testo non crittografato.

## <a name="querying-logs-in-application-insights"></a>Esecuzione di query sui log in Application InsightsQuerying logs in Application Insights

I log indirizzati ad Application Insights verranno visualizzati in 'tracce' o 'eccezioni'. Assicurarsi di regolare l'intervallo di tempo per includere l'esecuzione della pipeline.

![Risultato della query di Application Insights](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

Il risultato in Application Insights mostrerà il messaggio di log e il livello, il percorso del file e il numero della riga di codice. Mostrerà anche tutte le dimensioni personalizzate incluse. In questa immagine, il dizionario customDimensions mostra le coppie chiave/valore [dell'esempio](#creating-a-custom-dimensions-dictionary)di codice precedente.

### <a name="additional-helpful-queries"></a>Ulteriori query utili

Alcune delle query seguenti usano 'customDimensions.Level'. Questi livelli di gravità corrispondono al livello con cui è stato originariamente inviato il log Python. Per altre informazioni sulle query, vedere Query log [di monitoraggio](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)di Azure .

| Caso d'uso                                                               | Query                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Registrare i risultati per una dimensione personalizzata specifica, ad esempio 'parent_run_id'Log results for specific custom dimension, for example 'parent_run_id' | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| Registrare i risultati di tutte le esecuzioni di formazione negli ultimi 7 giorni                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Registrare i risultati con severityLevel Errore degli ultimi 7 giorni              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| Conteggio dei risultati del log con errore severityLevel negli ultimi 7 giorniCount of log results with severityLevel Error over the last 7 days     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato i log nell'istanza di Application Insights, è possibile usati per impostare [gli avvisi](../azure-monitor/platform/alerts-overview.md#what-you-can-alert-on) di Monitoraggio di Azure in base ai risultati delle query.

È anche possibile aggiungere risultati dalle query a un dashboard di [Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards#add-logs-analytics-query) per informazioni aggiuntive.