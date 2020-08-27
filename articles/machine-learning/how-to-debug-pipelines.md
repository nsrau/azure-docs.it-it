---
title: Debug & risolvere i problemi delle pipeline ML
titleSuffix: Azure Machine Learning
description: Eseguire il debug delle pipeline di Azure Machine Learning in Python. Informazioni sui problemi più comuni per lo sviluppo di pipeline e suggerimenti per semplificare il debug degli script prima e durante l'esecuzione remota. Informazioni su come usare Visual Studio Code per eseguire il debug interattivo delle pipeline di machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: troubleshooting, devx-track-python
ms.openlocfilehash: a036cb4212b0237bea1c8509532dc78d469acb17
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950154"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Eseguire il debug e risolvere i problemi delle pipeline di Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come risolvere i problemi e come eseguire il debug di [pipeline di Machine Learning](concept-ml-pipelines.md) in [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) e [Azure Machine Learning Designer (anteprima)](https://docs.microsoft.com/azure/machine-learning/concept-designer). 

## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi

La tabella seguente contiene problemi comuni durante lo sviluppo di pipeline, con potenziali soluzioni.

| Problema | Possibile soluzione |
|--|--|
| Non è possibile passare i dati alla `PipelineData` Directory | Assicurarsi di aver creato una directory nello script che corrisponde alla posizione in cui la pipeline prevede i dati di output del passaggio. Nella maggior parte dei casi, un argomento di input definirà la directory di output, quindi la directory verrà creata in modo esplicito. Utilizzare `os.makedirs(args.output_dir, exist_ok=True)` per creare la directory di output. Vedere l' [esercitazione](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) per un esempio di script di assegnazione dei punteggi che mostra questo schema progettuale. |
| Bug di dipendenza | Se vengono visualizzati errori di dipendenza nella pipeline remota che non si sono verificati durante il test localmente, verificare che le dipendenze e le versioni dell'ambiente remoto corrispondano a quelle presenti nell'ambiente di test. (Vedere [compilazione, memorizzazione nella cache e riutilizzo dell'ambiente](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Errori ambigui con le destinazioni di calcolo | Provare a eliminare e ricreare le destinazioni di calcolo. La ricreazione di destinazioni di calcolo è rapida e può risolvere alcuni problemi temporanei. |
| La pipeline non riusa i passaggi | Il riutilizzo dei passaggi è abilitato per impostazione predefinita, ma assicurarsi che non sia stato disabilitato in un passaggio della pipeline. Se il riutilizzo è disabilitato, il `allow_reuse` parametro nel passaggio verrà impostato su `False` . |
| La pipeline è stata rieseguita inutilmente | Per assicurarsi che i passaggi vengano rieseguiti solo quando cambiano i dati o gli script sottostanti, separare le directory del codice sorgente per ogni passaggio. Se si usa la stessa directory di origine per più passaggi, è possibile che si verifichino riesecuzioni non necessarie. Usare il `source_directory` parametro in un oggetto step della pipeline per puntare alla directory isolata per questo passaggio e assicurarsi che non si usi lo stesso `source_directory` percorso per più passaggi. |


## <a name="debugging-techniques"></a>Tecniche di debug

Sono disponibili tre tecniche principali per il debug delle pipeline: 

* Eseguire il debug dei singoli passaggi della pipeline nel computer locale
* Usare la registrazione e Application Insights per isolare e diagnosticare l'origine del problema
* Connessione di un debugger remoto a una pipeline in esecuzione in Azure

### <a name="debug-scripts-locally"></a>Debug di script in locale

Uno degli errori più comuni in una pipeline è che lo script del dominio non viene eseguito come previsto o contiene errori di runtime nel contesto di calcolo remoto difficili da eseguire il debug.

Le pipeline non possono essere eseguite localmente, ma l'esecuzione degli script in isolamento nel computer locale consente di eseguire il debug più velocemente perché non è necessario attendere il processo di compilazione di calcolo e ambiente. Per eseguire questa operazione, è necessario eseguire alcune operazioni di sviluppo:

* Se i dati si trova in un archivio dati cloud, sarà necessario scaricare i dati e renderli disponibili per lo script. L'uso di un piccolo campione di dati è un modo efficace per ridurre il tempo di esecuzione e ottenere commenti e suggerimenti sul comportamento degli script
* Se si sta tentando di simulare un passaggio della pipeline intermedia, potrebbe essere necessario compilare manualmente i tipi di oggetto previsti dallo script specifico dal passaggio precedente
* Sarà inoltre necessario definire un ambiente personalizzato e replicare le dipendenze definite nell'ambiente di calcolo remoto

Una volta completata l'installazione di uno script nell'ambiente locale, è molto più semplice eseguire attività di debug come:

* Associazione di una configurazione di debug personalizzata
* Sospensione dell'esecuzione e controllo dello stato dell'oggetto
* Rilevamento di errori logici o di tipo che non verranno esposti fino al runtime

> [!TIP] 
> Quando è possibile verificare che lo script sia in esecuzione come previsto, un passaggio successivo consiste nell'eseguire lo script in una pipeline a singolo passaggio prima di provare a eseguirlo in una pipeline con più passaggi.

## <a name="configure-write-to-and-review-pipeline-logs"></a>Configurare, scrivere ed esaminare i log della pipeline

Il testing degli script in locale è un ottimo modo per eseguire il debug di frammenti di codice e logica complessa prima di iniziare a creare una pipeline, ma a un certo punto è probabile che sia necessario eseguire il debug degli script durante l'esecuzione effettiva della pipeline, soprattutto quando si diagnostica il comportamento che si verifica durante l'interazione tra i passaggi della pipeline. È consigliabile usare `print()` le istruzioni in modo liberato negli script dei passaggi, in modo che sia possibile visualizzare lo stato dell'oggetto e i valori previsti durante l'esecuzione remota, in modo analogo al debug del codice JavaScript.

### <a name="logging-options-and-behavior"></a>Opzioni di registrazione e comportamento

La tabella seguente fornisce informazioni per diverse opzioni di debug per le pipeline. Non si tratta di un elenco esaustivo, perché esistono altre opzioni oltre a Azure Machine Learning, Python e OpenCensus.

| Libreria                    | Type   | Esempio                                                          | Destination                                  | Risorse                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metrica | `run.log(name, val)`                                             | Interfaccia utente del portale di Azure Machine Learning             | [Come tenere traccia degli esperimenti](how-to-track-experiments.md)<br>[Classe azureml. Core. Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Stampa/registrazione Python    | Registro    | `print(val)`<br>`logging.info(message)`                          | Log driver, progettazione Azure Machine Learning | [Come tenere traccia degli esperimenti](how-to-track-experiments.md)<br><br>[Registrazione Python](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Registro    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights-TRACES                | [Debug delle pipeline in Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[Esportatori di Monitoraggio di Azure per OpenCensus](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Cookbook per la registrazione di Python](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Esempio di opzioni di registrazione

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

### <a name="finding-and-reading-pipeline-log-files"></a>Ricerca e lettura dei file di log della pipeline

Il file di log `70_driver_log.txt` contiene: 

* Tutte le istruzioni stampate durante l'esecuzione dello script
* Traccia dello stack per lo script 

Per trovare questo e altri file di log nel portale, fare prima clic sull'esecuzione della pipeline nell'area di lavoro.

![Pagina elenco esecuzione pipeline](./media/how-to-debug-pipelines/pipelinerun-01.png)

Passare alla pagina dei dettagli dell'esecuzione della pipeline.

![Pagina Dettagli esecuzione pipeline](./media/how-to-debug-pipelines/pipelinerun-02.png)

Fare clic sul modulo per il passaggio specifico. Passare alla scheda **logs** . Altri log includono informazioni sul processo di compilazione dell'immagine dell'ambiente e gli script di preparazione dei passaggi.

![Scheda log della pagina Dettagli esecuzione pipeline](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> È possibile trovare le esecuzioni per le *pipeline pubblicate* nella scheda **endpoint** dell'area di lavoro. Le esecuzioni per le *pipeline non pubblicate* si trovano in **esperimenti** o **pipeline**.

## <a name="logging-in-azure-machine-learning-designer-preview"></a>Registrazione in Azure Machine Learning Designer (anteprima)

Per le pipeline create nella finestra di progettazione, è possibile trovare il file **70_driver_log** nella pagina Creazione e modifica o nella pagina Dettagli esecuzione pipeline.

### <a name="enable-logging-for-real-time-endpoints"></a>Abilitare la registrazione per gli endpoint in tempo reale

Per risolvere i problemi ed eseguire il debug degli endpoint in tempo reale nella finestra di progettazione, è necessario abilitare la registrazione di Application Insight usando l'SDK. La registrazione consente di risolvere i problemi relativi all'utilizzo e alla distribuzione del modello. Per ulteriori informazioni, vedere [registrazione per i modelli distribuiti](how-to-enable-logging.md#logging-for-deployed-models). 

### <a name="get-logs-from-the-authoring-page"></a>Ottenere i log dalla pagina Creazione e modifica

Quando si invia un'esecuzione della pipeline e si rimane nella pagina di creazione, è possibile trovare i file di log generati per ogni modulo al termine dell'esecuzione di ogni modulo.

1. Selezionare un modulo che ha terminato l'esecuzione nell'area di disegno di creazione.
1. Nel riquadro destro del modulo andare alla scheda  **output + log** .
1. Espandere il riquadro destro e selezionare il **70_driver_log.txt** per visualizzare il file nel browser. È anche possibile scaricare i log localmente.

    ![Riquadro di output espanso nella finestra di progettazione](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>Ottenere i log dalle esecuzioni di pipeline

È anche possibile trovare i file di log per esecuzioni specifiche nella pagina dei dettagli dell'esecuzione della pipeline, disponibile nella sezione **pipeline** o **esperimenti** di studio.

1. Consente di selezionare un'esecuzione di pipeline creata nella finestra di progettazione.

    ![Pagina esecuzione pipeline](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Selezionare un modulo nel riquadro di anteprima.
1. Nel riquadro destro del modulo andare alla scheda  **output + log** .
1. Espandere il riquadro destro per visualizzare il file di **70_driver_log.txt** nel browser oppure selezionare il file per scaricare i log localmente.

> [!IMPORTANT]
> Per aggiornare una pipeline dalla pagina dei dettagli dell'esecuzione della pipeline, è necessario **clonare** l'esecuzione della pipeline in una nuova bozza della pipeline. Un'esecuzione di pipeline è uno snapshot della pipeline. È simile a un file di log e non può essere modificato. 

## <a name="application-insights"></a>Application Insights
Per altre informazioni sull'uso della libreria Python di OpenCensus in questo modo, vedere questa guida: [eseguire il debug e risolvere i problemi delle pipeline di Machine Learning in Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>Debug interattivo con Visual Studio Code

In alcuni casi, potrebbe essere necessario eseguire il debug interattivo del codice Python usato nella pipeline ML. Utilizzando Visual Studio Code (VS Code) e debugpy, è possibile connettersi al codice durante l'esecuzione nell'ambiente di training. Per ulteriori informazioni, vedere il [debug interattivo in vs Code Guida](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines).

## <a name="next-steps"></a>Passaggi successivi

* Vedere le informazioni di riferimento su SDK per il pacchetto [azureml-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e il pacchetto [azureml-Pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .

* Vedere l'elenco di [eccezioni e codici di errore della finestra di progettazione](algorithm-module-reference/designer-error-codes.md).
