---
title: Inserimento dati con Azure Data Factory
titleSuffix: Azure Machine Learning
description: Informazioni su come creare una pipeline di inserimento dati con Azure Data Factory.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ad04566699b2eebb0cbd7a9f242de38bc75e2015
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90986411"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Inserimento dati con Azure Data Factory

Questo articolo illustra come creare una pipeline di inserimento dati con Azure Data Factory (ADF). Questa pipeline viene utilizzata per inserire dati da utilizzare con Azure Machine Learning. Azure Data Factory consente di estrarre, trasformare e caricare facilmente dati (ETL). Una volta che i dati sono stati trasformati e caricati nella risorsa di archiviazione, possono essere usati per eseguire il training dei modelli di machine learning.

Una semplice trasformazione dei dati può essere gestita con attività e strumenti ADF nativi come il [flusso di dati](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity). Quando si tratta di scenari più complicati, i dati possono essere elaborati con codice personalizzato. Ad esempio, Python o codice R.

Sono disponibili diverse tecniche comuni di utilizzo di Azure Data Factory per trasformare i dati durante l'inserimento. Ogni tecnica presenta vantaggi e svantaggi che determinano se è una scelta ottimale per un caso d'uso specifico:

| Tecnica | Vantaggi | Svantaggi |
| ----- | ----- | ----- |
| ADF e funzioni di Azure | Bassa latenza, calcolo senza server</br>Funzioni con stato</br>Funzioni riutilizzabili | Valido solo per l'elaborazione a breve esecuzione |
| ADF + componente personalizzato | Elaborazione parallela su larga scala</br>Adatto per algoritmi pesanti | Wrapping del codice in un eseguibile</br>Complessità della gestione delle dipendenze e di IO |
| ADF + notebook Azure Databricks | Apache Spark</br>Ambiente Python nativo | Può essere costoso</br>La creazione di cluster inizialmente richiede tempo e aumenta la latenza

## <a name="adf-with-azure-functions"></a>ADF con funzioni di Azure

![Il diagramma mostra una pipeline di Azure Data Factory, con funzioni di Azure e l'esecuzione di una pipeline M L, una pipeline Azure Machine Learning, con Train Model e il modo in cui interagiscono con i dati non elaborati e i dati preparati.](media/how-to-data-ingest-adf/adf-function.png)

Funzioni di Azure consente di eseguire piccole parti di codice (funzioni) senza doversi preoccupare dell'infrastruttura dell'applicazione. In questa opzione i dati vengono elaborati con codice Python personalizzato racchiuso in una funzione di Azure. 

La funzione viene richiamata con l' [attività della funzione Azure di ADF](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity). Questo approccio è un'opzione efficace per le trasformazioni di dati lightweight. 

* Vantaggi:
    * I dati vengono elaborati in un calcolo senza server con latenza relativamente bassa
    * La pipeline ADF può richiamare una [funzione di Azure durevole](/azure/azure-functions/durable/durable-functions-overview) che può implementare un flusso di trasformazione dei dati sofisticato 
    * I dettagli della trasformazione dei dati sono estratti dalla funzione di Azure che può essere riutilizzata e richiamata da altre posizioni
* Svantaggi:
    * È necessario creare le funzioni di Azure prima di usare con ADF
    * Funzioni di Azure è valido solo per l'elaborazione dei dati con esecuzione breve

## <a name="adf-with-custom-component-activity"></a>ADF con attività componente personalizzato

![Il diagramma mostra una pipeline di Azure Data Factory, con un componente personalizzato ed esegue la pipeline M L, una pipeline Azure Machine Learning, con Train Model e il modo in cui interagiscono con i dati non elaborati e i dati preparati.](media/how-to-data-ingest-adf/adf-customcomponent.png)

In questa opzione i dati vengono elaborati con codice Python personalizzato racchiuso in un eseguibile. Viene richiamato con un' [attività di componente personalizzato di ADF](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity). Questo approccio è più adatto per i dati di grandi dimensioni rispetto alla tecnica precedente.

* Vantaggi:
    * I dati vengono elaborati nel pool di [Azure batch](https://docs.microsoft.com/azure/batch/batch-technical-overview) , che fornisce un calcolo parallelo e ad alte prestazioni su larga scala
    * Può essere usato per eseguire algoritmi pesanti ed elaborare quantità significative di dati
* Svantaggi:
    * È necessario creare il pool di Azure Batch prima di usarlo con ADF
    * Rispetto alla progettazione correlata al wrapping del codice Python in un eseguibile. Complessità della gestione delle dipendenze e dei parametri di input/output

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF con Azure Databricks notebook Python

![Il diagramma mostra una pipeline di Azure Data Factory, con Azure Databricks Python e l'esecuzione di una pipeline M L, una pipeline Azure Machine Learning, con Train Model e il modo in cui interagiscono con dati non elaborati e dati preparati.](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/) è una piattaforma di analisi basata su Apache Spark nel cloud Microsoft.

In questa tecnica, la trasformazione dei dati viene eseguita da un [notebook di Python](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook), in esecuzione in un cluster Azure Databricks. Questo è probabilmente l'approccio più comune che sfrutta tutta la potenza di un servizio Azure Databricks. È progettato per l'elaborazione di dati distribuiti su larga scala.

* Vantaggi:
    * I dati vengono trasformati nel servizio di Azure di elaborazione dati più potente, supportato da Apache Spark ambiente
    * Supporto nativo di Python insieme a data science Framework e librerie, tra cui TensorFlow, PyTorch e Scikit-learn
    * Non è necessario eseguire il wrapping del codice Python in funzioni o moduli eseguibili. Il codice funziona così com'è.
* Svantaggi:
    * È necessario creare Azure Databricks infrastruttura prima di usarla con ADF
    * Può essere costoso a seconda della configurazione di Azure Databricks
    * La rotazione dei cluster di calcolo dalla modalità "a freddo" richiede tempo che offre una latenza elevata alla soluzione 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Utilizzo di dati in pipeline di Azure Machine Learning

![Il diagramma mostra una pipeline di Azure Data Factory e una pipeline Azure Machine Learning e il modo in cui interagiscono con i dati non elaborati e i dati preparati. La pipeline Data Factory invia i dati al database dei dati preparato, che inserisce un archivio dati, che consente di eseguire il feed dei set di dati nell'area di lavoro Machine Learning.](media/how-to-data-ingest-adf/aml-dataset.png)

I dati trasformati dalla pipeline di ADF vengono salvati nell'archivio dati, ad esempio BLOB di Azure. Azure Machine Learning possibile accedere a questi dati usando gli [archivi](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores) dati e i [set](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)di dati.

Ogni volta che viene eseguita la pipeline di ADF, i dati vengono salvati in una posizione diversa nell'archivio. Per passare il percorso alla Azure Machine Learning, la pipeline di ADF chiama una pipeline di Azure Machine Learning. Quando si chiama la pipeline ML, il percorso dei dati e l'ID esecuzione vengono inviati come parametri. La pipeline ML può quindi creare un archivio dati/DataSet usando il percorso dei dati. 

> [!TIP]
> I set di dati [supportano il controllo delle versioni](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets), quindi la pipeline ml può registrare una nuova versione del set di dati che punta ai dati più recenti della pipeline di ADF.

Una volta che i dati sono accessibili tramite un archivio dati o un set di dati, è possibile usarli per eseguire il training di un modello ML. Il processo di training potrebbe far parte della stessa pipeline di ML che viene chiamata da ADF. Oppure potrebbe trattarsi di un processo separato, ad esempio la sperimentazione in un notebook di Jupyter.

Poiché i set di dati supportano il controllo delle versioni e ogni esecuzione dalla pipeline crea una nuova versione, è facile capire quale versione dei dati è stata usata per eseguire il training di un modello.

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire un notebook di databricks in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [Accedere ai dati nei servizi di archiviazione di Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [Eseguire il training dei modelli con i set di impostazioni in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [DevOps per una pipeline di inserimento dati](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

