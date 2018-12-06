---
title: Preparare i dati con il Software Development Kit Machine Learning Data Prep per Python di Azure
description: Informazioni su come usare il Software Development Kit Machine Learning Data Prep per Python di Azure per caricare dati di diversi formati, trasformarli per migliorarne l'usabilità e scriverli in una posizione accessibile ai propri modelli.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 11/27/2018
ms.openlocfilehash: 91d0f3565db484504a67a3b6ae0989b9291cd24f
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446427"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>Preparare i dati per la modellazione con Azure Machine Learning

Questo articolo illustra i casi d'uso e le funzionalità esclusive di Azure Machine Learning Data Prep SDK. La preparazione dei dati è la parte più importante del flusso di lavoro dell'apprendimento automatico. I dati reali sono spesso frammentari, incoerenti o impossibili da usare come dati di training senza una pulizia e una trasformazione significative. Correggere errori e anomalie nei dati non elaborati e compilare nuove funzionalità pertinenti al problema da risolvere aumenterà la precisione del modello. L'SDK è progettato per essere intuitivo per gli utenti di altre librerie di preparazione dei dati comunemente usate, offrendo vantaggi per i principali scenari e mantenendo l'interoperabilità con tali altre librerie.

È possibile preparare i dati in Python usando [ Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk).

## <a name="azure-machine-learning-data-prep-sdk"></a>Azure Machine Learning Data Prep SDK

[Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk) è una libreria Python che offre:

* Trasformazioni intelligenti, come il raggruppamento fuzzy, le colonne derivate in base a un esempio, la suddivisione automatica, la lettura intelligente dei file e l'elaborazione degli schemi non allineati a destra, che offrono vantaggi in termini di risparmio di tempo.
* Una singola API che funziona in locale sui dati di piccole dimensioni o nel cloud sui dati di grandi dimensioni, **con poche o senza modifiche al codice**.
* La possibilità di implementare una scalabilità più efficace in un singolo computer tramite l'elaborazione dei dati come flusso, senza caricarli in memoria.

### <a name="install-the-sdk"></a>Installare l'SDK

Installare l'SDK nell'ambiente Python usando il comando seguente.

```shell
pip install azureml-dataprep
```

Usare il codice seguente per importare il pacchetto.

```python
import azureml.dataprep as dprep
```

### <a name="examples-and-reference"></a>Esempi e riferimenti

Per informazioni sui moduli e le funzioni di questo SDK, vedere la [documentazione di riferimento di Data Prep SDK](https://aka.ms/data-prep-sdk).

Gli esempi seguenti evidenziano alcune delle funzionalità esclusive dell'SDK, tra cui:

* Rilevamento automatico del tipo di file
* Trasformazioni intelligenti
* Statistiche di riepilogo
* Funzionalità compatibili con diversi ambienti


#### <a name="automatic-file-type-detection"></a>Rilevamento automatico del tipo di file

Usare la funzione `smart_read_file()` per caricare i dati senza dover specificare il tipo di file. Questa funzione riconosce automaticamente e analizza il tipo di file.

```python
dataflow = dprep.smart_read_file(path="<your-file-path>")
```

#### <a name="intelligent-transforms"></a>Trasformazioni intelligenti

Usare l'SDK per suddividere e derivare le colonne sia in base a un esempio che per inferenza per automatizzare la progettazione delle funzionalità. Supponiamo che l'oggetto flusso di dati contenga un campo denominato `datetime` il cui valore è `2018-09-15 14:30:00`.

Per suddividere automaticamente il campo `datetime`, chiamare la funzione seguente.

```python
new_dataflow = dataflow.split_column_by_example(source_column="datetime")
```

Se non si definisce il parametro di esempio, la funzione suddivide automaticamente il campo `datetime` nei due nuovi campi `datetime_1` e `datetime_2`. I valori risultanti sono rispettivamente `2018-09-15` e `14:30:00`. È anche possibile fornire un modello di esempio, sulla base del quale l'SDK stimerà ed eseguirà la trasformazione desiderata. Usando lo stesso oggetto `datetime`, il codice seguente creerà una nuova colonna `datetime_weekday` per il giorno della settimana in base all'esempio fornito.

```python
new_dataflow = dataflow.derive_column_by_example(
        source_columns="datetime", 
        new_column_name="datetime_weekday", 
        example_data=[("2009-01-04 10:12:00", "Sunday"), ("2013-08-22 17:00:00", "Thursday")]
    )
```

#### <a name="summary-statistics"></a>Statistiche di riepilogo

È possibile generare brevi statistiche di riepilogo per un flusso di dati con una sola riga di codice. Questo metodo è un modo pratico per comprendere i dati e la modalità di distribuzione.

```python
dataflow.get_profile()
```

L'output restituito dalla chiamata di questa funzione su un oggetto flusso di dati è simile alla tabella seguente.

![Output delle statistiche di riepilogo](./media/concept-data-preparation/output-example.png)

## <a name="multiple-environment-compatibilities"></a>Compatibilità con più ambienti

L'SDK consente inoltre di serializzare e aprire gli oggetti flusso di dati in *qualsiasi* ambiente Python. L'ambiente in cui un oggetto viene aperto può essere diverso dall'ambiente in cui viene salvato. Questa funzionalità facilita il trasferimento tra ambienti Python e favorisce una rapida integrazione con i modelli di Azure Machine Learning.

Usare il codice seguente per salvare gli oggetti flusso di dati.

```python
package = dprep.Package([dataflow_1, dataflow_2])
package.save("<your-local-path>")
```

Usare il codice seguente per riaprire il pacchetto in un ambiente qualsiasi e recuperare un elenco di oggetti flusso di dati.

```python
package = dprep.Package.open("<your-local-path>")
dataflow_list = package.dataflows
```

## <a name="data-preparation-pipeline"></a>Pipeline di preparazione dei dati

Per accedere a codice ed esempi dettagliati per ogni passaggio di preparazione, usare le guide pratiche seguenti:

1. [Caricare dati](how-to-load-data.md), che possono essere in vari formati
2. [Trasformare](how-to-transform-data.md) i dati in una struttura più utilizzabile
3. [Scrivere](how-to-write-data.md) tali dati in un percorso accessibile ai propri modelli

![Preparazione dei dati](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>Passaggi successivi

Consultare un [notebook di esempio](https://github.com/Microsoft/AMLDataPrepDocs/tree/master/tutorials/getting-started/getting-started.ipynb) sulla preparazione dei dati con il Software Development Kit Azure Machine Learning Data Prep.

[Documentazione di riferimento](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py) di Azure Machine Learning Data Prep SDK.
