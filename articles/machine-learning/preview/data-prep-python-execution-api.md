---
title: Guida dettagliata sull'uso delle API di esecuzione per la preparazione dei dati di Azure Machine Learning | Microsoft Docs
description: Questo documento contiene informazioni dettagliate sull'esecuzione di origini dati precedentemente progettate e sui i pacchetti di preparazione dei dati
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
ms.openlocfilehash: ceecc718f8198e9a88cae4acbe97e7f26d95f984
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="executing-data-sources-and-data-preparation-packages-from-python"></a>Esecuzione di origini dati e pacchetti di preparazione dei dati da Python

Per usare un'origine dati o un pacchetto di preparazione dei dati all'interno di Python:
- Passare alla scheda Dati del progetto.
- Fare clic con il pulsante destro del mouse sull'origine appropriata.
- Selezionare "Generate Data Access Code File" (Genera file del codice di accesso ai dati).

Questa azione crea uno script di Python breve che esegue il pacchetto e restituisce un dataframe.

## <a name="data-sources"></a>Origini dati

Il modulo `azureml.dataprep.datasource` contiene un'unica funzione per eseguire un'origine dati e restituire un dataframe: `load_datasource(path, secrets=None, spark=None)`.
- `path` è il percorso dell'origine dati, ovvero il file .dsource.
- `secrets` è un parametro facoltativo dict che esegue il mapping delle chiavi nei segreti.
- `spark` è un bool facoltativo che specifica se restituire un dataframe di Spark o di dati Pandas. Per impostazione predefinita, Azure Machine Learning Workbench determina il tipo di dataframe da restituire durante il runtime in base al contesto.

## <a name="data-preparation-packages"></a>Pacchetti di preparazione dei dati

Il modulo `azureml.dataprep.package` contiene tre funzioni che eseguono un flusso di dati da un pacchetto di preparazione dei dati.

### <a name="execution-functions"></a>Funzioni di esecuzione

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)`: invia il flusso di dati specificato per l'esecuzione, ma non restituisce un dataframe.
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)`: esegue il flusso di dati specificato e restituisce i risultati come dataframe.
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)`: esegue il flusso di dati specificato in base a un'origine dati in memoria e restituisce i risultati come dataframe. L'argomento `user_config` è un DICT che esegue il mapping del percorso assoluto di un'origine dati, ovvero il file .dsource, in un'origine dati in memoria rappresentata come un elenco di elenchi.

### <a name="common-arguments"></a>Argomenti comuni

- `package_path` è il percorso del pacchetto di preparazione dati, ovvero il file .dprep.
- `dataflow_idx` è l'indice in base zero del quale si esegue il flusso di dati nel pacchetto. Se il flusso di dati specificato fa riferimento ad altri flussi di dati oppure origini dati, vengono eseguiti anche questi.
- `secrets` è un parametro facoltativo dict che esegue il mapping delle chiavi nei segreti.
- `spark` è un bool facoltativo che specifica se restituire un dataframe di Spark o di dati Pandas. Per impostazione predefinita, Azure Machine Learning Workbench determina il tipo di dataframe da restituire durante il runtime in base al contesto.
