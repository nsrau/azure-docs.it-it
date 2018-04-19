---
title: Guida dettagliata sull'uso dell'operazionalizzazione della preparazione dei dati di Azure Machine Learning | Microsoft Docs
description: Questo documento contiene informazioni dettagliate sull'esecuzione di origini dati precedentemente progettate e sui i pacchetti di preparazioni dati
services: machine-learning
author: hughz
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/13/2018
ms.openlocfilehash: 0849747fe6d66d55d11c131b51b07d8f689774e1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="data-preparation-operationalization"></a>Operazionalizzazione della preparazione dei dati 

## <a name="operationalization-scenario"></a>Senario di operazionalizzazione

Di seguito sono indicati i diversi scenari di operazionalizzazione della preparazione dei dati che si potrebbero presentare a un utente.

### <a name="develop-your-model-based-on-training-data"></a>Sviluppare il modello in base ai dati di training

Si supponga che si intenda creare e distribuire un servizio API di assegnazione del punteggio in tempo reale. Il primo passaggio consiste nello sviluppare un modello per l'assegnazione del punteggio in base a un set di dati di training. La preparazione dei dati prevede l'importazione di un campione di dati di training come una nuova origine dati. Dopo avere preparato i dati, è possibile seguire i passaggi di preparazione inclusi nel pacchetto DataPrep. Utilizzando un account di sperimentazione di AzureML, l'utente può eseguire l'iterazione su un modello di machine learning per la generazione di etichette per ogni input nei dati di training.

Dal momento che è necessario aggiornare le funzionalità dei dati, l'utente torna al pacchetto DataPrep per preparare i dati in modo nuovo e salvare questi passaggi. Questo processo di iterazione di generazione di nuove funzionalità e di modifica del modello di machine learning continua fino a quando non viene perfezionata la funzionalità di assegnazione del punteggio ai dati di test. 

### <a name="deploy-your-model-to-the-azure-model-management-service"></a>Distribuire il modello nel servizio Gestione modelli di Azure

Si supponga di volere assegnare un punteggio in tempo reale ai dati cliente. Usando il servizio Gestione modelli di Azure, è possibile distribuire questo modello come servizio dall'interfaccia della riga di comando di Python AzureML. Il servizio distribuito espone un endpoint REST per la ricezione di dati cliente in tempo reale e la restituzione delle etichette di output corrispondenti dal modello sottoposto a training.

Per semplicità d'uso, l'endpoint di modello accetta i dati in formato JSON. L'input deve essere una stringa JSON che rappresenta una matrice di dati bidimensionale, che verranno passati attraverso la trasformazione ReadJSONLiteral e convertiti in un'origine dati DataPrep. Il pacchetto di preparazione dei dati creato durante la fase di sperimentazione può essere quindi eseguito sui dati JSON di streaming. Il dataframe risultante può quindi essere passato al modello sottoposto a training per l'assegnazione del punteggio.

## <a name="read-json-literal-transformation"></a>Trasformazione ReadJsonLiteral

### <a name="description"></a>DESCRIZIONE

Per motivi di operazionalizzazione, la preparazione dei dati include una trasformazione **ReadJsonLiteral** per l'esecuzione di un'attività e la generazione di un dataframe di Pandas o Spark. Questa trasformazione accetta in modo univoco come input un pacchetto di preparazione dei dati e un'origine dei dati JSON. Questa trasformazione viene esposta tramite l'interfaccia della riga di comando di Python DataPrep.

### <a name="instructions"></a>Istruzioni

#### <a name="pythoncli"></a>PythonCLI

Da Azure Machine Learning Workbench aprire l'interfaccia della riga di comando (File > Apri prompt dei comandi).

In questo esempio viene usato il pacchetto di preparazione dei dati TrainingPreparationSteps per preparare i dati e aggiungere la funzionalità di volume a ogni input.

```
>>> import azureml.dataprep.package as azdp

>>> azdp.run_on_data.__doc__
"Generate a dataframe based on a selected dataflow in a package using in-memory data sources.
'user_config' is expected as a dictionary that maps the absolute path of a dsource file to an in-memory data source represented as a list of lists."

>>> real_time_customer_data = [[1.0, 1.5, 2.5], [2.5, 1.5, 3]]
>>> azdp.run_on_data({ "C:\\TrainingSampleData.dsource": real_time_customer_data}, "C:\\TrainingPreparationSteps.dprep")
    Height   Width   Depth  Volumne
0   1.0       1.5    2.5    3.75
1   2.5       1.5    3.0    11.25
```

`run_on_data()` contiene i parametri facoltativi seguenti
 - `dataflow_idx`: specificare l'indice del flusso di dati desiderato da eseguire nel pacchetto
 - `secrets`: dizionario archivio segreti (chiave: secretId, valore: segreto archiviato)
 - `spark`: fornire una sessione di spark per l'esecuzione di Spark

#### <a name="input"></a>Input

Un input di matrice bidimensionale con 2 ("matrice di matrici"). Nella finestra Python l'input deve essere un elenco di elenchi. Poiché JSON non presenta un tipo di data nativo, tutti gli oggetti datetime.datetime Python verranno convertiti in stringhe di data in formato ISO. Per gli endpoint REST, l'input deve essere una stringa letterale JSON che rappresenta una matrice JSON bidimensionale.

#### <a name="output"></a>Output

Un DataFrame di Pandas o Spark. Il tipo di DataFrame è determinato dal framework selezionato nella configurazione di esecuzione (`.runconfig`). Il dataframe risultante può quindi essere passato al modello sottoposto a training per l'assegnazione del punteggio.
