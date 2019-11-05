---
title: Eseguire il debug e risolvere i problemi delle pipeline di Machine Learning
titleSuffix: Azure Machine Learning
description: Eseguire il debug e risolvere i problemi delle pipeline di Machine Learning in Azure Machine Learning SDK per Python. Informazioni sui problemi più comuni per lo sviluppo di pipeline e suggerimenti per semplificare il debug degli script prima e durante l'esecuzione remota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 10/03/2019
ms.openlocfilehash: fc19e864f00489d3ebc0162705af864785af0811
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497073"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Eseguire il debug e risolvere i problemi delle pipeline di Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come eseguire il debug e risolvere i problemi relativi alle [pipeline di Machine Learning](concept-ml-pipelines.md) in [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

Le sezioni seguenti forniscono una panoramica dei problemi comuni quando si compilano pipeline e strategie diverse per il debug del codice in esecuzione in una pipeline. Usare i suggerimenti seguenti quando si verificano problemi durante l'esecuzione di una pipeline come previsto. 

## <a name="testing-scripts-locally"></a>Testing degli script in locale

Uno degli errori più comuni in una pipeline è che uno script associato (script per la pulizia dei dati, script di assegnazione dei punteggi e così via) non è in esecuzione come previsto o contiene errori di runtime nel contesto di calcolo remoto difficili da eseguire il debug nell'area di lavoro nel computer di Azure Learning Studio. 

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

## <a name="debugging-scripts-from-remote-context"></a>Debug di script dal contesto remoto

Il testing degli script in locale è un ottimo modo per eseguire il debug di frammenti di codice e logica complessa prima di iniziare a creare una pipeline, ma a un certo punto è probabile che sia necessario eseguire il debug degli script durante l'esecuzione effettiva della pipeline, soprattutto quando si diagnostica il comportamento che si verifica durante l'interazione tra i passaggi della pipeline. Si consiglia di usare le istruzioni `print()` negli script dei passaggi per visualizzare lo stato dell'oggetto e i valori previsti durante l'esecuzione remota, in modo analogo a come si esegue il debug del codice JavaScript.

Il file di log `70_driver_log.txt` contiene: 

* Tutte le istruzioni stampate durante l'esecuzione dello script
* Traccia dello stack per lo script 

Per trovare questo e altri file di log nel portale, fare prima clic sulla pipeline nell'area di lavoro.

![Pagina pipeline nel portale](./media/how-to-debug-pipelines/pipeline-1.png)

Passare all'esecuzione dell'elemento padre della pipeline.

![Pipeline esecuzione padre](./media/how-to-debug-pipelines/pipeline-2.png)

Fare clic sull'ID di esecuzione per il passaggio specifico.

![Pagina pipeline nel portale](./media/how-to-debug-pipelines/pipeline-3.png)

Passare alla scheda **logs** . Altri log includono informazioni sul processo di compilazione dell'immagine dell'ambiente e gli script di preparazione dei passaggi.

![Pagina pipeline nel portale](./media/how-to-debug-pipelines/pipeline-4.png)

> [!TIP]
> Le esecuzioni per le *pipeline pubblicate* sono reperibili nella scheda **pipeline** dell'area di lavoro nel portale. Le esecuzioni per le *pipeline non pubblicate* sono disponibili negli **esperimenti**.

## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi

La tabella seguente contiene problemi comuni durante lo sviluppo di pipeline, con potenziali soluzioni.

| Problema | Possibile soluzione |
|--|--|
| Non è possibile passare i dati alla directory `PipelineData` | Assicurarsi di aver creato una directory nello script che corrisponde alla posizione in cui la pipeline prevede i dati di output del passaggio. Nella maggior parte dei casi, un argomento di input definirà la directory di output, quindi la directory verrà creata in modo esplicito. Usare `os.makedirs(args.output_dir, exist_ok=True)` per creare la directory di output. Vedere l' [esercitazione](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) per un esempio di script di assegnazione dei punteggi che mostra questo schema progettuale. |
| Bug di dipendenza | Se gli script sono stati sviluppati e testati localmente, ma sono stati riscontrati problemi di dipendenza durante l'esecuzione in un calcolo remoto nella pipeline, assicurarsi che le dipendenze e le versioni dell'ambiente di calcolo corrispondano all'ambiente di test. |
| Errori ambigui con le destinazioni di calcolo | Eliminando e ricreando le destinazioni di calcolo è possibile risolvere determinati problemi con le destinazioni di calcolo. |
| La pipeline non riusa i passaggi | Il riutilizzo dei passaggi è abilitato per impostazione predefinita, ma assicurarsi che non sia stato disabilitato in un passaggio della pipeline. Se il riutilizzo è disabilitato, il `allow_reuse` parametro nel passaggio verrà impostato su `False`. |
| La pipeline è stata rieseguita inutilmente | Per assicurarsi che i passaggi vengano rieseguiti solo quando cambiano i dati o gli script sottostanti, separare le directory per ogni passaggio. Se si usa la stessa directory di origine per più passaggi, è possibile che si verifichino riesecuzioni non necessarie. Usare il parametro `source_directory` in un oggetto step della pipeline per puntare alla directory isolata per questo passaggio e assicurarsi che non si usi lo stesso percorso `source_directory` per più passaggi. |

## <a name="next-steps"></a>Passaggi successivi

* Vedere le informazioni di riferimento su SDK per il pacchetto [azureml-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e il pacchetto [azureml-Pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .

* Seguire l' [esercitazione avanzata](tutorial-pipeline-batch-scoring-classification.md) sull'uso delle pipeline per il Punteggio batch.
