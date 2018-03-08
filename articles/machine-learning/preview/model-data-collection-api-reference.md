---
title: Informazioni di riferimento sulle API di raccolta di dati del modello di Azure Machine Learning | Microsoft Docs
description: Informazioni di riferimento sulle API di raccolta di dati del modello di Azure Machine Learning.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 2de5e8bc8880fea5e7f57c88590e32a9fbb60ac6
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="azure-machine-learning-model-data-collection-api-reference"></a>Informazioni di riferimento sulle API di raccolta di dati del modello di Azure Machine Learning

La raccolta di dati del modello consente di archiviare gli input e le stime del modello da un servizio Web di Machine Learning. Vedere la [Guida alle procedure per la raccolta dati del modello](how-to-use-model-data-collection.md) per comprendere come installare `azureml.datacollector` in computer Windows e Linux.

In questa guida di riferimento alle API, viene usato un approccio graduale su come raccogliere input e stime del modello da un servizio Web di Machine Learning.

## <a name="enable-model-data-collection-in-azure-ml-workbench-environment"></a>Abilitare la raccolta di dati del modello nell'ambiente di Azure Machine Learning Workbench

 Cercare il file conda\_dependencies.yml nel progetto, nella cartella aml_config e assicurarsi che il file conda\_dependencies.yml includa il modulo azureml.datacollector nella sezione pip, come illustrato di seguito. Si tratta solo di una sottosezione di un file conda\_dependencies.yml completo:

    dependencies:
      - python=3.5.2
      - pip:
        - azureml.datacollector==0.1.0a13

>[!NOTE] 
>Attualmente è possibile usare il modulo di agente di raccolta dati in Azure Machine Learning Workbench mediante l'esecuzione in modalità docker. La modalità locale potrebbe non funzionare per tutti gli ambienti.




## <a name="enable-model-data-collection-in-the-scoring-file"></a>Abilitare la raccolta di dati del modello nel file di assegnazione dei punteggi

Nel file di assegnazione dei punteggi che viene usato per l'operazionalizzazione, importare il modulo dell'agente di raccolta dati e la classe ModelDataCollector:

    from azureml.datacollector import ModelDataCollector


## <a name="model-data-collector-instantiation"></a>Creazione di un'istanza dell'agente di raccolta dati del modello
Creare una nuova istanza di ModelDataCollector:

dc = ModelDataCollector(model_name, identifier='default', feature_names=None, model_management_account_id='unknown', webservice_name='unknown', model_id='unknown', model_version='unknown')

Vedere i dettagli di Classe e Parametro:

### <a name="class"></a>Classe
| NOME | DESCRIZIONE |
|--------------------|--------------------|
| ModelDataCollector | Una classe nello spazio dei nomi azureml.datacollector. Un'istanza di questa classe verrà usata per raccogliere i dati del modello. Un singolo file di assegnazione dei punteggi può contenere più ModelDataCollectors. Ogni istanza deve essere usata per raccogliere i dati in un'unica posizione discreta nel file di assegnazione dei punteggi in modo che lo schema dei dati raccolti rimanga coerente (vale a dire, input e stima)|


### <a name="parameters"></a>Parametri

| NOME | type | DESCRIZIONE |
|-------------|------------|-------------------------|
| model_name | stringa | Il nome del modello per cui sono stati raccolti i dati |
| identificatore | stringa | La posizione nel codice che identifica questi dati, ad esempio 'RawInput' o 'Prediction' |
| feature_names | elenco di stringhe | Un elenco di nomi di funzionalità che diventano l'intestazione CSV quando specificati |
| model_management_account_id | stringa | L'identificatore per l'account di gestione del modello in cui è archiviato questo modello. Viene popolato automaticamente quando i modelli sono operativi tramite AML |
| webservice_name | stringa | Il nome del servizio Web in cui questo modello è distribuito attualmente. Viene popolato automaticamente quando i modelli sono operativi tramite AML |
| model_id | stringa | L'identificatore univoco per questo modello nel contesto di un account di gestione modelli. Viene popolato automaticamente quando i modelli sono operativi tramite AML |
| model_version | stringa | Il numero di versione di questo modello nel contesto di un account di gestione modelli. Viene popolato automaticamente quando i modelli sono operativi tramite AML |



 

## <a name="collecting-the-model-data"></a>Raccolta dei dati del modello

È possibile raccogliere i dati del modello usando un'istanza di ModelDataCollector creata in precedenza.

    dc.collect(input_data, user_correlation_id="")

Vedere i dettagli di Metodo e Parametro:

### <a name="method"></a>Metodo
| NOME | DESCRIZIONE |
|--------------------|--------------------|
| collect | Usato per raccogliere i dati per un input o una stima del modello|


### <a name="parameters"></a>Parametri

| NOME | type | DESCRIZIONE |
|-------------|------------|-------------------------|
| input_data | più tipi | I dati da raccogliere (attualmente accetta l'elenco di tipi, numpy.array, pandas.DataFrame, pyspark.sql.DataFrame). Per i tipi di frame di dati, se esiste un'intestazione con i nomi delle funzionalità, queste informazioni vengono incluse nella destinazione dei dati (senza la necessità di passare in modo esplicito i nomi delle funzionalità nel costruttore ModelDataCollector) |
| user_correlation_id | stringa | Un ID di correlazione facoltativo che può essere fornito dall'utente per correlare questa stima |

