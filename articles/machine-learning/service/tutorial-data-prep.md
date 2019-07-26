---
title: 'Esercitazione sul modello di regressione: Preparazione dei dati'
titleSuffix: Azure Machine Learning service
description: Nella prima parte di questa esercitazione si apprenderà come preparare i dati in Python per la modellazione basata sulla regressione usando l'SDK Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: MayMSFT
ms.author: sihhu
ms.reviewer: trbye
ms.date: 07/16/2019
ms.custom: seodec18
ms.openlocfilehash: 6692f64dc7e7fa2799f9095af39171a2ddc0e76d
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360909"
---
# <a name="tutorial-prepare-data-for-regression-modeling"></a>Esercitazione: preparare i dati per la modellazione basata sulla regressione

In questa esercitazione si apprenderà come preparare i dati per la modellazione basata sulla regressione usando il [pacchetto di preparazione dei dati](https://aka.ms/data-prep-sdk) di [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Si eseguiranno varie trasformazioni per filtrare e combinare due diversi set di dati relativi ai taxi di New York.

Questa esercitazione è la **prima di una serie in due parti**. Dopo aver completato la serie di esercitazioni, sarà possibile prevedere il costo di una corsa in taxi eseguendo il training di un modello sulle funzionalità dei dati. Queste funzionalità includono il giorno e l'ora di inizio corsa, il numero di passeggeri e la località del prelievo.

In questa esercitazione:

> [!div class="checklist"]
> * Configurare un ambiente Python e importare i pacchetti.
> * Caricare due set di dati con nomi di campo diversi.
> * Pulire i dati per rimuovere le anomalie.
> * Trasformare i dati usando trasformazioni intelligenti per creare nuove funzionalità.
> * Salvare l'oggetto flusso di dati da usare in un modello di regressione.

## <a name="prerequisites"></a>Prerequisiti

Passare a [Configurazione dell'ambiente di sviluppo](#start) per leggere la procedura relativa al notebook oppure consultare le istruzioni seguenti per ottenere il notebook ed eseguirlo in Azure Notebooks o sul server notebook personale. Per eseguire il notebook sarà necessario:

* Un server notebook Python 3.6 con installati i componenti seguenti:
    * Pacchetto `azureml-dataprep` di Azure Machine Learning SDK
* Notebook dell'esercitazione

* Usare un [server notebook basato sul cloud nell'area di lavoro](#azure) 
* Usare il [server notebook personale](#server)

### <a name="azure"></a>Usare un server notebook cloud nell'area di lavoro

Iniziare a usare un server notebook basato sul cloud è semplice. Al termine della creazione di questa risorsa cloud, Azure Machine Learning SDK per Python è già installato e configurato.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* Dopo aver avviato la pagina Web del notebook, eseguire il notebook **tutorials/regression-part1-data-prep.ipynb**.

### <a name="server"></a>Usare il server notebook Jupyter personale

Usare questa proceduta per creare un server notebook Jupyter locale nel computer in uso.  Al termine, eseguire il notebook **tutorials/regression-part1-data-prep.ipynb**.

1. Completare i passaggi di installazione descritti nella [guida di avvio rapido per Python in Azure Machine Learning](setup-create-workspace.md#sdk) per creare un ambiente Miniconda e installare l'SDK.  Se si preferisce, è possibile ignorare la sezione **Creare un'area di lavoro**, ma sarà necessaria per la [seconda parte](tutorial-auto-train-models.md) di questa serie di esercitazioni.
1. Il pacchetto `azureml-dataprep` viene installato automaticamente quando si installa l'SDK.
1. Clonare il [repository GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Avviare il server notebook dalla directory clonata.

    ```shell
    jupyter notebook
    ```

## <a name="start"></a>Configurare l'ambiente di sviluppo

Tutte le impostazioni per il lavoro di sviluppo possono essere eseguite in un notebook di Python. La configurazione include le azioni seguenti:

* Installare l'SDK
* Importazione di pacchetti Python

### <a name="install-and-import-packages"></a>Installare e importare pacchetti

Usare il comando seguente per installare i pacchetti necessari se non sono già installati.

```shell
pip install "azureml-dataprep[pandas]>=1.1.0,<1.2.0"
```

Importare il pacchetto.

```python
import azureml.dataprep as dprep
```

> [!IMPORTANT]
> Assicurarsi di installare la versione più recente del pacchetto azureml.dataprep. Questa esercitazione non funzionerà con un numero di versione precedente a 1.1.0

## <a name="load-data"></a>Caricare i dati

Scaricare due diversi set di dati relativi ai taxi di New York in oggetti flusso di dati. I set di dati contengono campi leggermente diversi. Il metodo `auto_read_file()` riconosce automaticamente il tipo di file di input.

```python
from IPython.display import display
dataset_root = "https://dprepdata.blob.core.windows.net/demo"

green_path = "/".join([dataset_root, "green-small/*"])
yellow_path = "/".join([dataset_root, "yellow-small/*"])

green_df_raw = dprep.read_csv(
    path=green_path, header=dprep.PromoteHeadersMode.GROUPED)
# auto_read_file automatically identifies and parses the file type, which is useful when you don't know the file type.
yellow_df_raw = dprep.auto_read_file(path=yellow_path)

display(green_df_raw.head(5))
display(yellow_df_raw.head(5))
```

> [!Note]
> L'URL in questo stesso esempio non è un URL completo. Al contrario, fa riferimento alla cartella demo nel BLOB. L'URL completo di alcuni dati è https://dprepdata.blob.core.windows.net/demo/green-small/green_tripdata_2013-08.csv

Un oggetto `Dataflow` è simile a un dataframe e rappresenta una serie di operazioni non modificabili con valutazione differita sui dati. È possibile aggiungere le operazioni richiamando i diversi metodi di trasformazione e filtraggio disponibili. Il risultato dell'aggiunta di un'operazione a un `Dataflow` è sempre un nuovo oggetto `Dataflow`.

## <a name="cleanse-data"></a>Pulire i dati

A questo punto popolare alcune variabili con trasformazioni rapide da applicare a tutti i flussi di dati. La variabile `drop_if_all_null` si usa per eliminare i record in cui tutti i campi sono Null. La variabile `useful_columns` contiene una matrice di descrizioni di colonna che vengono mantenute in ogni flusso di dati.

```python
all_columns = dprep.ColumnSelector(term=".*", use_regex=True)
drop_if_all_null = [all_columns, dprep.ColumnRelationship(
    dprep.ColumnRelationship.ALL)]
useful_columns = [
    "cost", "distance", "dropoff_datetime", "dropoff_latitude", "dropoff_longitude",
    "passengers", "pickup_datetime", "pickup_latitude", "pickup_longitude", "store_forward", "vendor"
]
```

Elaborare prima i dati relativi ai taxi verdi per trasformarli in un formato valido che può essere combinato con i dati dei taxi gialli. Chiamare le funzioni `replace_na()`, `drop_nulls()` e `keep_columns()` usando le variabili delle trasformazioni rapide create. Rinominare inoltre tutte le colonne nel dataframe in modo che corrispondano ai nomi nella variabile `useful_columns`.


```python
green_df = (green_df_raw
            .replace_na(columns=all_columns)
            .drop_nulls(*drop_if_all_null)
            .rename_columns(column_pairs={
                "VendorID": "vendor",
                "lpep_pickup_datetime": "pickup_datetime",
                "Lpep_dropoff_datetime": "dropoff_datetime",
                "lpep_dropoff_datetime": "dropoff_datetime",
                "Store_and_fwd_flag": "store_forward",
                "store_and_fwd_flag": "store_forward",
                "Pickup_longitude": "pickup_longitude",
                "Pickup_latitude": "pickup_latitude",
                "Dropoff_longitude": "dropoff_longitude",
                "Dropoff_latitude": "dropoff_latitude",
                "Passenger_count": "passengers",
                "Fare_amount": "cost",
                "Trip_distance": "distance"
            })
            .keep_columns(columns=useful_columns))
green_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>dropoff_datetime</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>01-08-2013 08:14:37</td>
      <td>01-08-2013 09:09:06</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>21,25</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>01-08-2013 09:13:00</td>
      <td>01-08-2013 11:38:00</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>.00</td>
      <td>74.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>01-08-2013 09:48:00</td>
      <td>01-08-2013 09:49:00</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>01-08-2013 10:38:35</td>
      <td>01-08-2013 10:38:51</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>3.25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-01 11:51:45</td>
      <td>2013-08-01 12:03:52</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>8.5</td>
    </tr>
  </tbody>
</table>
</div>

Eseguire gli stessi passaggi di trasformazione nei dati dei taxi gialli. Queste funzioni garantiscono la rimozione dei dati null dal set di dati, aumentando la precisione del modello di Machine Learning.

```python
yellow_df = (yellow_df_raw
             .replace_na(columns=all_columns)
             .drop_nulls(*drop_if_all_null)
             .rename_columns(column_pairs={
                 "vendor_name": "vendor",
                 "VendorID": "vendor",
                 "vendor_id": "vendor",
                 "Trip_Pickup_DateTime": "pickup_datetime",
                 "tpep_pickup_datetime": "pickup_datetime",
                 "Trip_Dropoff_DateTime": "dropoff_datetime",
                 "tpep_dropoff_datetime": "dropoff_datetime",
                 "store_and_forward": "store_forward",
                 "store_and_fwd_flag": "store_forward",
                 "Start_Lon": "pickup_longitude",
                 "Start_Lat": "pickup_latitude",
                 "End_Lon": "dropoff_longitude",
                 "End_Lat": "dropoff_latitude",
                 "Passenger_Count": "passengers",
                 "passenger_count": "passengers",
                 "Fare_Amt": "cost",
                 "fare_amount": "cost",
                 "Trip_Distance": "distance",
                 "trip_distance": "distance"
             })
             .keep_columns(columns=useful_columns))
yellow_df.head(5)
```

Chiamare la funzione `append_rows()` sui dati dei taxi verdi per aggiungere i dati dei taxi gialli. Viene creato un nuovo dataframe combinato.

```python
combined_df = green_df.append_rows([yellow_df])
```

### <a name="convert-types-and-filter"></a>Convertire i tipi e applicare un filtro

Esaminare le statistiche di riepilogo delle coordinate di inizio e fine corsa per vedere come sono distribuiti i dati. Definire prima un oggetto `TypeConverter` per cambiare i campi di latitudine e longitudine in tipi decimali, quindi chiamare la funzione `keep_columns()` per limitare l'output ai campi di latitudine e longitudine e infine chiamare la funzione `get_profile()`. Queste chiamate di funzione creano una visualizzazione ridotta del flusso di dati mostrando solo i campi lat/long, il che rende più semplice valutare le coordinate mancanti o esterne all'ambito.


```python
decimal_type = dprep.TypeConverter(data_type=dprep.FieldType.DECIMAL)
combined_df = combined_df.set_column_types(type_conversions={
    "pickup_longitude": decimal_type,
    "pickup_latitude": decimal_type,
    "dropoff_longitude": decimal_type,
    "dropoff_latitude": decimal_type
})
combined_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>Max</th>
      <th>Conteggio</th>
      <th>Totale mancanti</th>
      <th>Totale non mancanti</th>
      <th>Percentuale mancanti</th>
      <th>Totale errori</th>
      <th>Totale vuoti</th>
      <th>Quantile 0,1%</th>
      <th>Quantile 1%</th>
      <th>Quantile 5%</th>
      <th>Quantile 25%</th>
      <th>Quantile 50%</th>
      <th>Quantile 75%</th>
      <th>Quantile 95%</th>
      <th>Quantile 99%</th>
      <th>Quantile 99,9%</th>
      <th>Deviazione standard</th>
      <th>Media</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-88.114046</td>
      <td>-73.961840</td>
      <td>-73.961964</td>
      <td>-73.947693</td>
      <td>-73.922097</td>
      <td>-73.846670</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.792672</td>
      <td>-68.833579</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>40.919121</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.682889</td>
      <td>40.675541</td>
      <td>40.721075</td>
      <td>40.756159</td>
      <td>40.803909</td>
      <td>40.849406</td>
      <td>40.870681</td>
      <td>40.891244</td>
      <td>10.345967</td>
      <td>37.936742</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-87.699611</td>
      <td>-73.984734</td>
      <td>-73.985777</td>
      <td>-73.956250</td>
      <td>-73.928948</td>
      <td>-73.866208</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.696526</td>
      <td>-68.896978</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>41.008934</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.662763</td>
      <td>40.654851</td>
      <td>40.717821</td>
      <td>40.756534</td>
      <td>40.784688</td>
      <td>40.852437</td>
      <td>40.879289</td>
      <td>40.937291</td>
      <td>10.290780</td>
      <td>37.963774</td>
    </tr>
  </tbody>
</table>



Nell'output delle statistiche di riepilogo si può notare che alcune coordinate mancano e altre non si trovano a New York (ciò è determinato dall'analisi soggettiva). Filtrare le coordinate per rimuovere le località esterne ai confini cittadini. Concatenare i comandi di filtro delle colonne con la funzione `filter()` e definire i limiti minimo e massimo per ogni campo. Quindi chiamare di nuovo la funzione `get_profile()` per verificare la trasformazione.


```python
latlong_filtered_df = (combined_df
                       .drop_nulls(
                           columns=["pickup_longitude", "pickup_latitude",
                                    "dropoff_longitude", "dropoff_latitude"],
                           column_relationship=dprep.ColumnRelationship(
                               dprep.ColumnRelationship.ANY)
                       )
                       .filter(dprep.f_and(
                           dprep.col("pickup_longitude") <= -73.72,
                           dprep.col("pickup_longitude") >= -74.09,
                           dprep.col("pickup_latitude") <= 40.88,
                           dprep.col("pickup_latitude") >= 40.53,
                           dprep.col("dropoff_longitude") <= -73.72,
                           dprep.col("dropoff_longitude") >= -74.09,
                           dprep.col("dropoff_latitude") <= 40.88,
                           dprep.col("dropoff_latitude") >= 40.53
                       )))
latlong_filtered_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>Max</th>
      <th>Conteggio</th>
      <th>Totale mancanti</th>
      <th>Totale non mancanti</th>
      <th>Percentuale mancanti</th>
      <th>Totale errori</th>
      <th>Totale vuoti</th>
      <th>Quantile 0,1%</th>
      <th>Quantile 1%</th>
      <th>Quantile 5%</th>
      <th>Quantile 25%</th>
      <th>Quantile 50%</th>
      <th>Quantile 75%</th>
      <th>Quantile 95%</th>
      <th>Quantile 99%</th>
      <th>Quantile 99,9%</th>
      <th>Deviazione standard</th>
      <th>Media</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.078156</td>
      <td>-73.736481</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.076314</td>
      <td>-73.962542</td>
      <td>-73.962893</td>
      <td>-73.948975</td>
      <td>-73.927856</td>
      <td>-73.866662</td>
      <td>-73.830438</td>
      <td>-73.823160</td>
      <td>-73.769750</td>
      <td>0.048711</td>
      <td>-73.913865</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.575485</td>
      <td>40.879852</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.632884</td>
      <td>40.713105</td>
      <td>40.711600</td>
      <td>40.721403</td>
      <td>40.758142</td>
      <td>40.805145</td>
      <td>40.848855</td>
      <td>40.867567</td>
      <td>40.877690</td>
      <td>0.048348</td>
      <td>40.765226</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.085747</td>
      <td>-73.720871</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.078828</td>
      <td>-73.985650</td>
      <td>-73.985813</td>
      <td>-73.959041</td>
      <td>-73.936681</td>
      <td>-73.884846</td>
      <td>-73.815507</td>
      <td>-73.776697</td>
      <td>-73.733471</td>
      <td>0.055961</td>
      <td>-73.920718</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.583530</td>
      <td>40.879734</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.597741</td>
      <td>40.695376</td>
      <td>40.695115</td>
      <td>40.727549</td>
      <td>40.758160</td>
      <td>40.788378</td>
      <td>40.850372</td>
      <td>40.867968</td>
      <td>40.878586</td>
      <td>0.050462</td>
      <td>40.759487</td>
    </tr>
  </tbody>
</table>

### <a name="split-and-rename-columns"></a>Dividere e rinominare le colonne

Esaminare il profilo dati per la colonna `store_forward`. Questo campo è un flag booleano che corrisponde a `Y` quando il taxi non era connesso al server dopo la corsa e si è quindi reso necessario archiviare i dati relativi alla corsa in memoria, per poi inoltrarli al server una volta ripristinata la connessione.


```python
latlong_filtered_df.keep_columns(columns='store_forward').get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>Max</th>
      <th>Conteggio</th>
      <th>Totale mancanti</th>
      <th>Totale non mancanti</th>
      <th>Percentuale mancanti</th>
      <th>Totale errori</th>
      <th>Totale vuoti</th>
      <th>Quantile 0,1%</th>
      <th>Quantile 1%</th>
      <th>Quantile 5%</th>
      <th>Quantile 25%</th>
      <th>Quantile 50%</th>
      <th>Quantile 75%</th>
      <th>Quantile 95%</th>
      <th>Quantile 99%</th>
      <th>Quantile 99,9%</th>
      <th>Deviazione standard</th>
      <th>Media</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>S</td>
      <td>7059.0</td>
      <td>99.0</td>
      <td>6960.0</td>
      <td>0.014025</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>



Nell'output del profilo dei dati nella colonna `store_forward` si può notare che i dati non sono coerenti e che alcuni valori sono mancanti o Null. Usare le funzioni `replace()` e `fill_nulls()` per sostituire questi valori con la stringa "N":


```python
replaced_stfor_vals_df = latlong_filtered_df.replace(
    columns="store_forward", find="0", replace_with="N").fill_nulls("store_forward", "N")
```

Eseguire la funzione `replace` sul campo `distance`. La funzione riformatta i valori di distanza erroneamente etichettati come `.00` e riempie i valori Null con zeri. Convertire il campo `distance` in formato numerico. Questi punti dati non corretti sono probabilmente anomalie nel sistema di raccolta dati sui taxi.


```python
replaced_distance_vals_df = replaced_stfor_vals_df.replace(
    columns="distance", find=".00", replace_with=0).fill_nulls("distance", 0)
replaced_distance_vals_df = replaced_distance_vals_df.to_number(["distance"])
```

Dividere i valori di data e ora di inizio e fine corsa nelle rispettive colonne di data e ora. Usare la funzione `split_column_by_example()` per eseguire la divisione. In questo caso, il parametro `example` facoltativo della funzione `split_column_by_example()` viene omesso. La funzione determinerà quindi automaticamente il punto in cui eseguire la divisione in base ai dati.


```python
time_split_df = (replaced_distance_vals_df
                 .split_column_by_example(source_column="pickup_datetime")
                 .split_column_by_example(source_column="dropoff_datetime"))
time_split_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>pickup_datetime_1</th>
      <th>pickup_datetime_2</th>
      <th>dropoff_datetime</th>
      <th>dropoff_datetime_1</th>
      <th>dropoff_datetime_2</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>N</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>2013-08-01</td>
      <td>17:24:00</td>
      <td>2013-08-01 17:25:00</td>
      <td>2013-08-01</td>
      <td>17:25:00</td>
      <td>N</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>2013-08-06</td>
      <td>06:51:19</td>
      <td>2013-08-06 06:51:36</td>
      <td>2013-08-06</td>
      <td>06:51:36</td>
      <td>N</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>2013-08-06</td>
      <td>13:26:34</td>
      <td>2013-08-06 13:26:57</td>
      <td>2013-08-06</td>
      <td>13:26:57</td>
      <td>N</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>2013-08-06</td>
      <td>13:27:53</td>
      <td>2013-08-06 13:28:08</td>
      <td>2013-08-06</td>
      <td>13:28:08</td>
      <td>N</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

Rinominare le colonne generate dalla funzione `split_column_by_example()` con nomi significativi.

```python
renamed_col_df = (time_split_df
                  .rename_columns(column_pairs={
                      "pickup_datetime_1": "pickup_date",
                      "pickup_datetime_2": "pickup_time",
                      "dropoff_datetime_1": "dropoff_date",
                      "dropoff_datetime_2": "dropoff_time"
                  }))
renamed_col_df.head(5)
```

Chiamare la funzione `get_profile()` per vedere le statistiche riepilogative complete dopo la procedura di pulizia.

```python
renamed_col_df.get_profile()
```

## <a name="transform-data"></a>Trasformare i dati

Eseguire un'ulteriore divisione delle date di inizio e fine corsa nei valori di giorno della settimana, giorno del mese e mese. Per ottenere il valore del giorno della settimana, usare la funzione `derive_column_by_example()`. Questa funzione accetta come parametro una matrice di oggetti di esempio che definiscono i dati di input e l'output desiderato. Determina quindi automaticamente la trasformazione desiderata. Per le colonne relative alle ore di inizio e fine corsa, dividere in ore, minuti e secondi usando la funzione `split_column_by_example()` senza alcun parametro di esempio.

Dopo aver generato le nuove funzionalità, usare la funzione `drop_columns()` per eliminare i campi originali, perché le funzionalità appena generate sono quelle preferite. Rinominare il resto dei campi con descrizioni significative.

Questa trasformazione dei dati, che permette di creare nuove funzionalità basate sul tempo, migliorerà l'accuratezza del modello di Machine Learning. Ad esempio, la generazione di una nuova funzionalità per il giorno feriale stabilirà una relazione tra il giorno della settimana e il prezzo della corsa in taxi, che è spesso più costosa in determinati giorni a causa del numero elevato di richieste.


```python
transformed_features_df = (renamed_col_df
                           .derive_column_by_example(
                               source_columns="pickup_date",
                               new_column_name="pickup_weekday",
                               example_data=[
                                   ("2009-01-04", "Sunday"), ("2013-08-22", "Thursday")]
                           )
                           .derive_column_by_example(
                               source_columns="dropoff_date",
                               new_column_name="dropoff_weekday",
                               example_data=[
                                   ("2013-08-22", "Thursday"), ("2013-11-03", "Sunday")]
                           )

                           .split_column_by_example(source_column="pickup_time")
                           .split_column_by_example(source_column="dropoff_time")
                           # The following two calls to split_column_by_example reference the column names generated from the previous two calls.
                           .split_column_by_example(source_column="pickup_time_1")
                           .split_column_by_example(source_column="dropoff_time_1")
                           .drop_columns(columns=[
                               "pickup_date", "pickup_time", "dropoff_date", "dropoff_time",
                               "pickup_date_1", "dropoff_date_1", "pickup_time_1", "dropoff_time_1"
                           ])

                           .rename_columns(column_pairs={
                               "pickup_date_2": "pickup_month",
                               "pickup_date_3": "pickup_monthday",
                               "pickup_time_1_1": "pickup_hour",
                               "pickup_time_1_2": "pickup_minute",
                               "pickup_time_2": "pickup_second",
                               "dropoff_date_2": "dropoff_month",
                               "dropoff_date_3": "dropoff_monthday",
                               "dropoff_time_1_1": "dropoff_hour",
                               "dropoff_time_1_2": "dropoff_minute",
                               "dropoff_time_2": "dropoff_second"
                           }))

transformed_features_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>pickup_weekday</th>
      <th>pickup_hour</th>
      <th>pickup_minute</th>
      <th>pickup_second</th>
      <th>dropoff_datetime</th>
      <th>dropoff_weekday</th>
      <th>dropoff_hour</th>
      <th>dropoff_minute</th>
      <th>dropoff_second</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>2013-08-01 17:22:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>N</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>24</td>
      <td>00</td>
      <td>2013-08-01 17:25:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>25</td>
      <td>00</td>
      <td>N</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>Tuesday</td>
      <td>06</td>
      <td>51</td>
      <td>19</td>
      <td>2013-08-06 06:51:36</td>
      <td>Tuesday</td>
      <td>06</td>
      <td>51</td>
      <td>36</td>
      <td>N</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>26</td>
      <td>34</td>
      <td>2013-08-06 13:26:57</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>26</td>
      <td>57</td>
      <td>N</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>27</td>
      <td>53</td>
      <td>2013-08-06 13:28:08</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>28</td>
      <td>08</td>
      <td>N</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

I dati mostrano che i componenti delle date e ore di inizio e fine corsa generati dalle trasformazioni derivate sono corretti. Eliminare le colonne `pickup_datetime` e `dropoff_datetime` perché non sono più necessarie; le funzionalità di tempo granulari, come ora, minuto e secondo, sono più utili per il training del modello.


```python
processed_df = transformed_features_df.drop_columns(
    columns=["pickup_datetime", "dropoff_datetime"])
```

Usare la funzionalità di inferenza dei tipi per controllare automaticamente il tipo di dati di ogni campo e visualizzare i risultati dell'inferenza.


```python
type_infer = processed_df.builders.set_column_types()
type_infer.learn()
type_infer
```

L'output restituito di `type_infer` è il seguente.

    Column types conversion candidates:
    'pickup_weekday': [FieldType.STRING],
    'pickup_hour': [FieldType.DECIMAL],
    'pickup_minute': [FieldType.DECIMAL],
    'pickup_second': [FieldType.DECIMAL],
    'dropoff_hour': [FieldType.DECIMAL],
    'dropoff_minute': [FieldType.DECIMAL],
    'dropoff_second': [FieldType.DECIMAL],
    'store_forward': [FieldType.STRING],
    'pickup_longitude': [FieldType.DECIMAL],
    'dropoff_longitude': [FieldType.DECIMAL],
    'passengers': [FieldType.DECIMAL],
    'distance': [FieldType.DECIMAL],
    'vendor': [FieldType.STRING],
    'dropoff_weekday': [FieldType.STRING],
    'pickup_latitude': [FieldType.DECIMAL],
    'dropoff_latitude': [FieldType.DECIMAL],
    'cost': [FieldType.DECIMAL]

I risultati dell'inferenza sembrano corretti in base ai dati. Applicare ora le conversioni di tipo al flusso di dati.


```python
type_converted_df = type_infer.to_dataflow()
type_converted_df.get_profile()
```

Prima di includere in un pacchetto il flusso di dati, eseguire due filtri finali sul set di dati. Per eliminare i punti dati acquisiti in modo non corretto, filtrare il flusso di dati in base ai record in cui i valori delle variabili `cost` e `distance` sono maggiori di zero. Questo passaggio migliorerà notevolmente l'accuratezza del modello di Machine Learning, perché i punti dati con costo o distanza zero rappresentano gli outlier principali che sbilanciano l'accuratezza della stima.

```python
final_df = type_converted_df.filter(dprep.col("distance") > 0)
final_df = final_df.filter(dprep.col("cost") > 0)
```

A questo punto, si ha un oggetto flusso di dati completamente trasformato e preparato da usare in un modello di Machine Learning. L'SDK include una funzionalità di serializzazione degli oggetti che viene usata come indicato nel codice seguente.

```python
import os

file_path = os.path.join(os.getcwd(), "dflows.dprep")
final_df.save(file_path)
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per continuare con la parte due dell'esercitazione, è necessario avere il file **dflows.dprep** nella directory corrente.

Se non si intende continuare con la parte due, eliminare il file **dflows.dprep** dalla directory corrente. Eliminare questo file sia che l'esecuzione venga completata in locale o in [Azure Notebooks](https://notebooks.azure.com/).

## <a name="next-steps"></a>Passaggi successivi

La prima parte di questa esercitazione ha mostrato come:

> [!div class="checklist"]
> * Configurare l'ambiente di sviluppo.
> * Caricare e pulire i set di dati.
> * Usare trasformazioni intelligenti per eseguire una stima di un modello logico in base a un esempio.
> * Unire set di dati e combinarli in pacchetti per il training di Machine Learning.

È ora possibile usare i dati del training nella parte due dell'esercitazione:

> [!div class="nextstepaction"]
> [Esercitazione (parte due): Eseguire il training del modello di regressione](tutorial-auto-train-models.md)
