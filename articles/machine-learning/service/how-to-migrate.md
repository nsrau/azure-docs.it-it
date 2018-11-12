---
title: Eseguire la migrazione al servizio Azure Machine Learning
description: Informazioni su come eseguire l'aggiornamento o la migrazione del servizio Azure Machine Learning da una versione precedente alla più recente.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: haining
author: haining
ms.date: 09/24/2018
ms.openlocfilehash: e2b3545c020f41f25f19843eab158cfb1b419164
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253449"
---
# <a name="migrate-to-the-latest-version-of-azure-machine-learning-service"></a>Eseguire la migrazione del servizio Azure Machine Learning alla versione più recente 

**Se è stata installata l'applicazione Workbench (anteprima) e/o si dispone di account di anteprima per la sperimentazione e la gestione dei modelli, usare questo articolo per eseguire la migrazione alla versione più recente.**  Se non si dispone dell'anteprima di Workbench installata o di un account per la sperimentazione e/o la gestione dei modelli, non è necessario eseguire alcuna migrazione.

## <a name="what-can-i-migrate"></a>Di cosa è possibile eseguire la migrazione?
Molti artefatti creati nella prima anteprima del servizio Azure Machine Learning si trovano nello spazio di archiviazione locale o cloud. Questi artefatti non verranno eliminati. Per eseguire la migrazione, registrare nuovamente gli artefatti con il servizio Azure Machine Learning aggiornato. 

La tabella e l'articolo seguenti illustrano cosa è possibile fare con asset e risorse esistenti prima o dopo il passaggio alla versione più recente del servizio Azure Machine Learning. È anche possibile continuare a usare la versione precedente e i propri asset per un certo periodo di tempo: [vedere la sequenza temporale relativa al supporto della transizione](overview-what-happened-to-workbench.md#timeline).

|Asset o risorsa dalla versione precedente|È possibile eseguire la migrazione?|Azioni|
|-----------------|:-------------:|-------------|
|Modelli di Machine Learning (come file locali)|Yes|No. Funziona come prima.|
|Dipendenze e schemi di modelli (come file locali)|Yes|No. Funziona come prima.|
|Progetti|Yes|[Collegare la cartella locale alla nuova area di lavoro](#projects).|
|Cronologie di esecuzione|No |[Scaricabile](#history) per un periodo di tempo.|
|I file di preparazione dei dati|No |[Preparare un set di dati di qualsiasi dimensione](#dataprep) per la modellazione usando il nuovo Azure Machine Learning Data Prep SDK o usare Azure Databricks.|
|Destinazioni di calcolo|No |Registrarle nella nuova area di lavoro.|
|Modelli registrati|No |Registrare nuovamente il modello in una nuova area di lavoro.|
|Manifesti registrati|No |No. I manifesti non esistono più come concetto nella nuova area di lavoro.|
|Immagini registrate|No |Ricreare l'immagine Docker di distribuzione in una nuova area di lavoro.|
|Servizi Web distribuiti|No |No. Continueranno a funzionare così come sono <br/>oppure [distribuirli nuovamente usando la versione più recente](#services).|
|Account per la sperimentazione <br/>e la gestione dei modelli|No |[Creare invece un'area di lavoro](#resources).|
|Interfaccia della riga di comando e SDK di Machine Learning|No |Usare la nuova [interfaccia della riga di comando](reference-azure-machine-learning-cli.md) e il nuovo [SDK](https://aka.ms/aml-sdk) per un nuovo lavoro.|


Altre informazioni sulle [modifiche apportate in questa versione](overview-what-happened-to-workbench.md).

>[!Warning]
>Questo articolo non riguarda gli utenti di Azure Machine Learning Studio. Riguarda i clienti di Azure Machine Learning che hanno installato l'applicazione Workbench (anteprima) e/o dispongono di account di anteprima per la sperimentazione e la gestione dei modelli.

<a name="resources"></a>

## <a name="azure-resources"></a>Risorse di Azure

Non è possibile eseguire la migrazione del servizio Azure Machine Learning alla versione più recente per risorse come gli account per la sperimentazione, gli account per la gestione dei modelli e gli ambienti di calcolo di apprendimento automatico. Vedere la [sequenza temporale](overview-what-happened-to-workbench.md#timeline) per sapere per quanto tempo le risorse continueranno a funzionare.

È possibile iniziare dall'ultima versione, creando un'area di lavoro del servizio Azure Machine Learning nel [portale di Azure](quickstart-get-started.md). Il dashboard dell'area di lavoro del portale è supportato solo nei browser Edge, Chrome e Firefox.

Questa nuova area di lavoro è la risorsa di servizio di massimo livello e consente di usare tutte le funzioni più recenti del servizio Azure Machine Learning. Altre informazioni su questa [area di lavoro e architettura](concept-azure-machine-learning-architecture.md).

<a name="projects"></a>

## <a name="projects"></a>Progetti

Ora, nella versione più recente, i progetti sono directory del computer locale, anziché trovarsi in un'area di lavoro nel cloud. Vedere un diagramma [dell'architettura più recente](concept-azure-machine-learning-architecture.md). 

Per continuare a usare la directory locale che contiene i file e gli script, specificare il nome della directory nel comando Python "[experiment.submit](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)" o tramite il comando "az ml project attach" dell'interfaccia della riga di comando.

Ad esempio: 
```python
run = exp.submit(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
```

<a name="services"></a>

## <a name="deployed-web-services"></a>Servizi Web distribuiti

Per eseguire la migrazione dei servizi Web, ridistribuire i modelli con il nuovo SDK o la nuova interfaccia della riga di comando alle nuove destinazioni di distribuzione. Non è necessario modificare il file di assegnazione dei punteggi originale, il file del modello, i file delle dipendenze, il file di ambiente e i file di schema. 

Nella versione più recente, i modelli sono distribuiti come servizi Web nelle [istanze di contenitore di Azure](how-to-deploy-to-aci.md) (ACI) o nei cluster del [servizio Kubernetes di Azure](how-to-deploy-to-aks.md) (AKS). 

Per altre informazioni, vedere gli articoli seguenti:
+ [Distribuire in ACI](how-to-deploy-to-aci.md)
+ [Distribuire in AKS](how-to-deploy-to-aks.md)
+ [Esercitazione: Distribuire modelli con il servizio Azure Machine Learning](tutorial-deploy-models-with-aml.md)

Quando il [supporto per l'interfaccia della riga di comando precedente terminerà](overview-what-happened-to-workbench.md#timeline), non sarà più possibile gestire i servizi Web distribuiti originariamente con l'account di Gestione modelli. Tali servizi Web continueranno a funzionare fino a quando sarà supportato il servizio contenitore di Azure (ACS).

<a name="history"></a>

## <a name="run-history-records"></a>Record della cronologia di esecuzione

Anche se non è possibile continuare ad aggiungere informazioni alle cronologie di esecuzione esistenti nell'area di lavoro precedente, è possibile esportare le cronologie disponibili usando l'interfaccia della riga di comando precedente. Quando il [supporto per l'interfaccia della riga di comando precedente terminerà](overview-what-happened-to-workbench.md#timeline), non sarà più possibile esportare queste cronologie di esecuzione.

Iniziare ad eseguire il training dei modelli e a rilevare le cronologie di esecuzione usando la nuova interfaccia della riga di comando e il nuovo SDK. La procedura è descritta in [Tutorial: train models with Azure Machine Learning service](tutorial-train-models-with-aml.md) (Esercitazione: Eseguire il training dei modelli con il servizio Azure Machine Learning).

Per esportare la cronologia di esecuzione con l'interfaccia della riga di comando precedente:

```azurecli
#list all runs
az ml history list

#get details about a particular run
az ml history info

# download all artifacts of a run
az ml history download
```

<a name="dataprep"></a>

## <a name="data-preparation-files"></a>File di preparazione dei dati
I file di preparazione dei dati non sono portabili senza Workbench. Tuttavia è possibile preparare un set di dati di qualsiasi dimensione per la modellazione usando il nuovo Azure Machine Learning Data Prep SDK o usare Azure Databricks per set di dati di grandi dimensioni.  [Informazioni su come ottenere l'SDK di preparazione dati](how-to-data-prep.md). 

## <a name="next-steps"></a>Passaggi successivi

Per una guida introduttiva che illustra come creare un'area di lavoro, creare un progetto, eseguire uno script ed esplorare la cronologia di esecuzione dello script con la versione più recente del servizio Azure Machine Learning, vedere [Get started with Azure Machine Learning service](quickstart-get-started.md) (Introduzione al servizio Azure Machine Learning).

Per un'analisi più approfondita del flusso di lavoro, svolgere l'esercitazione completa che contiene i passaggi dettagliati per eseguire il training e distribuire i modelli con il servizio Azure Machine Learning. 

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training e distribuire modelli](tutorial-train-models-with-aml.md)
