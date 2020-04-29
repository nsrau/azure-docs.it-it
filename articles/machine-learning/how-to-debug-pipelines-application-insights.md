---
title: Eseguire il debug e risolvere i problemi delle pipeline di Machine Learning in Application Insights
titleSuffix: Azure Machine Learning
description: Aggiungere la registrazione alle pipeline di assegnazione dei punteggi di training e batch e visualizzare i risultati registrati in Application Insights.
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.custom: seodec18
ms.openlocfilehash: b3e4bf19a7ec153f85483f3c5028e468e06ed7f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80982362"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines-in-application-insights"></a>Eseguire il debug e risolvere i problemi delle pipeline di Machine Learning in Application Insights
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

La libreria Python di [OpenCensus](https://opencensus.io/quickstart/python/) può essere usata per indirizzare i log ai Application Insights dagli script. L'aggregazione dei log dalle esecuzioni di pipeline in un'unica posizione consente di compilare query e diagnosticare i problemi. L'uso di Application Insights consente di tenere traccia dei log nel tempo e confrontare i log della pipeline tra le esecuzioni.

La presenza dei log in una sola posizione fornirà una cronologia delle eccezioni e dei messaggi di errore. Poiché Application Insights si integra con gli avvisi di Azure, è anche possibile creare avvisi basati sulle query Application Insights.

## <a name="prerequisites"></a>Prerequisiti

* Seguire i passaggi per creare un'area di lavoro [Azure Machine Learning](./how-to-manage-workspace.md) e [creare la prima pipeline](./how-to-create-your-first-pipeline.md)
* [Configurare il proprio ambiente di sviluppo](./how-to-configure-environment.md) per installare l'SDK di Azure Machine Learning.
* Installare il pacchetto dell'utilità di [esportazione di monitoraggio di Azure OpenCensus](https://pypi.org/project/opencensus-ext-azure/) localmente:
  ```python
  pip install opencensus-ext-azure
  ```
* Creare un' [istanza di Application Insights](../azure-monitor/app/opencensus-python.md) (questo documento contiene anche informazioni su come ottenere la stringa di connessione per la risorsa)

## <a name="getting-started"></a>Introduzione

Questa sezione è un'introduzione specifica dell'uso di OpenCensus da una pipeline Azure Machine Learning. Per un'esercitazione dettagliata, vedere [OpenCensus Azure monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

Aggiungere un PythonScriptStep alla pipeline di Azure ML. Configurare [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) con la dipendenza da opencensus-EXT-Azure. Configurare la `APPLICATIONINSIGHTS_CONNECTION_STRING` variabile di ambiente.

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

Creare un file denominato `sample_step.py`. Importare la classe AzureLogHandler per indirizzare i log a Application Insights. Sarà anche necessario importare la libreria di registrazione di Python.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Aggiungere quindi AzureLogHandler al logger di Python.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Registrazione con dimensioni personalizzate
 
Per impostazione predefinita, i log che vengono trasmessi a Application Insights non avranno un contesto sufficiente per ripercorrere l'esecuzione o l'esperimento. Per rendere i log interoperabili per la diagnosi dei problemi, sono necessari campi aggiuntivi. 

Per aggiungere questi campi, è possibile aggiungere dimensioni personalizzate per fornire il contesto a un messaggio di log. Un esempio è quando qualcuno vuole visualizzare i log in più passaggi nella stessa esecuzione della pipeline.

Le dimensioni personalizzate costituiscono un dizionario di coppie chiave-valore (archiviate come stringa, stringa). Il dizionario viene quindi inviato a Application Insights e visualizzato come una colonna nei risultati della query. Le singole dimensioni possono essere utilizzate come [parametri di query](#additional-helpful-queries).

### <a name="helpful-context-to-include"></a>Contesto utile da includere

| Campo                          | Ragionamento/esempio                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | Consente di eseguire query sui log con lo stesso parent_run_id per visualizzare i log nel tempo per tutti i passaggi, anziché dover approfondire ogni singolo passaggio.                                        |
| step_id                        | Consente di eseguire query sui log con lo stesso step_id per vedere dove si è verificato un problema con un ambito limitato solo al singolo passaggio                                                        |
| step_name                      | Consente di eseguire query sui log per visualizzare le prestazioni del passaggio nel tempo. Consente inoltre di trovare un step_id per le esecuzioni recenti senza immergersi nell'interfaccia utente del portale                                          |
| experiment_name                | Consente di eseguire query nei log per visualizzare le prestazioni dell'esperimento nel tempo. Aiuta anche a trovare un parent_run_id o step_id per le esecuzioni recenti senza immergersi nell'interfaccia utente del portale                   |
| run_url                 | Può fornire un collegamento direttamente all'esecuzione per l'analisi. |

**Altri campi utili**

Questi campi possono richiedere strumentazione di codice aggiuntiva e non vengono forniti dal contesto di esecuzione.

| Campo                   | Ragionamento/esempio                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | Se si usa CI/CD per la distribuzione, questo campo può correlare i log alla versione del codice che ha fornito il passaggio e la logica della pipeline. Questo collegamento consente di diagnosticare i problemi o di identificare i modelli con tratti specifici (valori di log/metrica) |
| run_type                       | Può distinguere tra tipi di modelli diversi o esecuzioni di training rispetto a punteggi |

### <a name="creating-a-custom-dimensions-dictionary"></a>Creazione di un dizionario di dimensioni personalizzate

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

Il AzureLogHandler OpenCensus viene usato per indirizzare i log Python a Application Insights. Di conseguenza, è necessario prendere in considerazione le sfumature di registrazione di Python. Quando viene creato, un logger dispone di un livello di registrazione predefinito e Visualizza i log maggiori o uguali a tale livello. Un riferimento efficace per l'uso delle funzionalità di registrazione di Python è il Cookbook per la [registrazione](https://docs.python.org/3/howto/logging-cookbook.html).

La `APPLICATIONINSIGHTS_CONNECTION_STRING` variabile di ambiente è necessaria per la libreria OpenCensus. È consigliabile impostare questa variabile di ambiente anziché passarla come parametro della pipeline per evitare il passaggio delle stringhe di connessione in testo non crittografato.

## <a name="querying-logs-in-application-insights"></a>Esecuzione di query sui log in Application Insights

I log indirizzati a Application Insights vengono visualizzati in ' TRACES ' o ' Exceptions '. Assicurarsi di modificare l'intervallo di tempo per includere l'esecuzione della pipeline.

![Risultato della query Application Insights](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

Il risultato in Application Insights mostrerà il messaggio e il livello del log, il percorso del file e il numero di riga del codice. Mostrerà anche eventuali dimensioni personalizzate incluse. In questa immagine, il dizionario customDimensions Mostra le coppie chiave/valore dell'esempio di [codice](#creating-a-custom-dimensions-dictionary)precedente.

### <a name="additional-helpful-queries"></a>Query aggiuntive utili

Per alcune delle query seguenti viene usato ' customDimensions. Level '. Questi livelli di gravità corrispondono al livello con cui il log Python è stato inviato originariamente. Per ulteriori informazioni sulle query, vedere [query di log di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

| Caso d'uso                                                               | Query                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Registrare i risultati per una dimensione personalizzata specifica, ad esempio "parent_run_id" | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| Risultati dei log per tutte le esecuzioni di training negli ultimi 7 giorni                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Registra i risultati con errore severityLevel degli ultimi 7 giorni              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| Numero di risultati dei log con errore severityLevel negli ultimi 7 giorni     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato i log nell'istanza di Application Insights, è possibile usarli per impostare gli [avvisi di monitoraggio di Azure](../azure-monitor/platform/alerts-overview.md#what-you-can-alert-on) in base ai risultati della query.

È anche possibile aggiungere i risultati delle query a un [dashboard di Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards#add-logs-analytics-query) per informazioni aggiuntive.
