---
title: Guida dettagliata sull'uso delle API di esecuzione per le preparazioni dati di Azure Machine Learning | Microsoft Docs
description: Questo documento contiene informazioni dettagliate sull'esecuzione di origini dati precedentemente progettate e sui i pacchetti di preparazioni dati
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 4a0e4bd58ffa4bc7062ee4844a090be43047e8a1
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2017
---
# <a name="execute-data-sources-and-data-preparations-packages-from-python"></a>Eseguire pacchetti di origini dati e preparazioni dati da Python

Per usare un pacchetto di origini dati di Azure Machine Learning o di preparazioni dati di Azure Machine Learning all'interno di Python:

1. Passare alla scheda **Dati** del progetto.

2. Fare clic con il pulsante destro del mouse sull'origine appropriata.

3. Selezionare **Generate Data Access Code File** (Genera file del codice di accesso ai dati).

Questa azione crea uno script di Python breve che esegue il pacchetto e restituisce un dataframe.

## <a name="data-sources"></a>Origini dati

Il modulo `azureml.dataprep.datasource` contiene un'unica funzione per eseguire un'origine dati e restituire un dataframe: `load_datasource(path, secrets=None, spark=None)`.
- `path` è il percorso dell'origine dati, ovvero il file .dsource.
- `secrets` è un dizionario facoltativo che esegue il mapping fra le chiavi e i segreti.
- `spark` è un bool facoltativo che specifica se restituire un dataframe di Spark o di dati Pandas. Per impostazione predefinita, Azure Machine Learning Workbench determina il tipo di dataframe da restituire durante il runtime in base al contesto.

## <a name="data-preparations-packages"></a>Pacchetti di preparazioni dati

Il modulo `azureml.dataprep.package` contiene tre funzioni che eseguono un flusso di dati da un pacchetto di preparazioni dati.

### <a name="execution-functions"></a>Funzioni di esecuzione

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)` invia il flusso di dati specificato per l'esecuzione, ma non restituisce un dataframe.
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)` esegue il flusso di dati specificato e restituisce i risultati come dataframe.
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)` esegue il flusso di dati specificato in base a un'origine dati in memoria e restituisce i risultati come dataframe. L'argomento `user_config` è un dizionario che esegue il mapping del percorso assoluto di un'origine dati, ovvero il file .dsource, in un'origine dati in memoria rappresentata come un elenco di elenchi.

### <a name="common-arguments"></a>Argomenti comuni

- `package_path` è il percorso del pacchetto di preparazioni dati, ovvero il file .dprep.
- `dataflow_idx` è l'indice in base zero che indica il flusso di dati del pacchetto da eseguire. Se il flusso di dati specificato fa riferimento ad altri flussi di dati o origini dati, vengono eseguiti anche questi.
- `secrets` è un dizionario facoltativo che esegue il mapping fra le chiavi e i segreti.
- `spark` è un bool facoltativo che specifica se restituire un dataframe di Spark o di dati Pandas. Per impostazione predefinita, Workbench determina il tipo di dataframe da restituire durante il runtime in base al contesto.
