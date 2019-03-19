---
title: 'Trasformazioni: preparazione dei dati con Python SDK'
titleSuffix: Azure Machine Learning service
description: Informazioni sulla trasformazione e la pulizia dei dati con Azure Machine Learning Data Prep SDK. Usare i metodi di trasformazione per aggiungere colonne, filtrare le righe o le colonne non desiderate e attribuire i valori mancanti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 12a914b2cdef0a40493dac1a539cf0c2a7703093
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999818"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>Trasformare i dati con Azure Machine Learning Data Prep SDK

In questo articolo descrive diversi metodi di caricamento dei dati usando il SDK di Azure Machine Learning Data Prep. il SDK offre le funzioni che rendono più semplice aggiungere colonne e filtrare le colonne o le righe non desiderate attribuire valori mancanti. Per visualizzare la documentazione di riferimento per il SDK, vedere la [Panoramica](https://aka.ms/data-prep-sdk).

Questo argomento vengono illustrati esempi per le attività seguenti:

- Aggiungere una colonna tramite un'espressione
- [Attribuire i valori mancanti](#impute-missing-values)
- [Derivare le colonne in base a un esempio](#derive-column-by-example)
- [Filtri](#filtering)
- [Trasformazioni di Python personalizzate](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>Aggiungere una colonna tramite un'espressione

Azure Machine Learning Data Prep SDK contiene le espressioni `substring` che è possibile usare per calcolare un valore dalle colonne esistenti e quindi inserire tale valore in una nuova colonna. In questo esempio si caricano i dati e si cerca di aggiungere colonne a tali dati di input.

```python
import azureml.dataprep as dprep

# loading data
dflow = dprep.read_csv(path=r'data\crime0-10.csv')
dflow.head(3)
```

||ID|Case Number|Data|Blocco|IUCR|Tipo principale|DESCRIZIONE|Descrizione della posizione|Arresto|Nazionale|...|Corsia|Area della community|Codice FBI|Coordinata X|Coordinata Y|Year|Aggiornato il|Latitude|Longitudine|Località|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|05/07/2015 23:50:00|050XX N NEWLAND AVE|0820|FURTO|$500 E MENO|VIA|false|false|...|41|10|06|1129230|1933315|2015|12/07/2015 12:42:46|41,973309466|-87,800174996|(41,973309466, -87,800174996)|
|1|10139776|HY329265|05/07/2015 23:30:00|011XX W MORSE AVE|0460|BATTERIA|SEMPLICE|VIA|false|true|...|49|1|08B|1167370|1946271|2015|12/07/2015 12:42:46|42,008124017|-87,65955018|(42,008124017, -87,65955018)|
|2|10140270|HY329253|05/07/2015 23:20:00 PM|121XX S FRONT AVE|0486|BATTERIA|SEMPLICE BATTERIA NAZIONALE|VIA|false|true|...|9|53|08B|||2015|12/07/2015 12:42:46|


Usare l'espressione `substring(start, length)` per estrarre il prefisso dalla colonna Case Number e inserire tale stringa in una nuova colonna, `Case Category`. Passando la variabile `substring_expression` al parametro `expression` viene creata una nuova colonna calcolata che esegue l'espressione su ogni record.

```python
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||ID|Case Number|Case Category|Data|Blocco|IUCR|Tipo principale|DESCRIZIONE|Descrizione della posizione|Arresto|Nazionale|...|Corsia|Area della community|Codice FBI|Coordinata X|Coordinata Y|Year|Aggiornato il|Latitude|Longitudine|Località|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|HY|05/07/2015 23:50:00|050XX N NEWLAND AVE|0820|FURTO|$500 E MENO|VIA|false|false|...|41|10|06|1129230|1933315|2015|12/07/2015 12:42:46|41,973309466|-87,800174996|(41,973309466, -87,800174996)|
|1|10139776|HY329265|HY|05/07/2015 23:30:00|011XX W MORSE AVE|0460|BATTERIA|SEMPLICE|VIA|false|true|...|49|1|08B|1167370|1946271|2015|12/07/2015 12:42:46|42,008124017|-87,65955018|(42,008124017, -87,65955018)|
|2|10140270|HY329253|HY|05/07/2015 23:20:00 PM|121XX S FRONT AVE|0486|BATTERIA|SEMPLICE BATTERIA NAZIONALE|VIA|false|true|...|9|53|08B|||2015|12/07/2015 12:42:46|



Usare l'espressione `substring(start)` per estrarre solo il numero dalla colonna Case Number e creare una nuova colonna. Convertirla in un tipo di dati numerico usando la funzione `to_number()` e passare il nome della colonna stringa come parametro.

```python
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
```

## <a name="impute-missing-values"></a>Attribuire i valori mancanti

L'SDK può attribuire i valori mancanti in colonne specificate. In questo esempio si caricano i valori di latitudine e longitudine e quindi si cerca di attribuire i valori mancanti nei dati di input.

```python
import azureml.dataprep as dprep

# loading input data
dflow = dprep.read_csv(r'data\crime0-10.csv')
dflow = dflow.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
dflow = dflow.to_number(['Latitude', 'Longitude'])
dflow.head(3)
```

||ID|Arresto|Latitude|Longitudine|
|-----|------|-----|------|-----|
|0|10140490|false|41,973309|-87,800175|
|1|10139776|false|42,008124|-87,659550|
|2|10140270|false|NaN|NaN|

Nel terzo record mancano i valori di latitudine e longitudine. Per attribuire i valori mancanti, si utilizza [ `ImputeMissingValuesBuilder` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) per informazioni su un'espressione fissa. È possibile attribuire le colonne con un valore calcolato `MIN`, `MAX` o `MEAN` o con un valore `CUSTOM`. Quando `group_by_columns` viene specificato, verranno attribuiti i valori mancanti dal gruppo con `MIN`, `MAX` e `MEAN` calcolati per ogni gruppo.

Controllare la `MEAN` valore dell'oggetto colonna latitudine utilizzando i [ `summarize()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) (funzione). Questa funzione accetta una matrice di colonne nel parametro `group_by_columns` per specificare il livello di aggregazione. Il parametro `summary_columns` accetta una chiamata `SummaryColumnsValue`. Questa chiamata di funzione specifica il nome della colonna corrente, il nome del nuovo campo calcolato e la `SummaryFunction` da eseguire.

```python
dflow_mean = dflow.summarize(group_by_columns=['Arrest'],
                       summary_columns=[dprep.SummaryColumnsValue(column_id='Latitude',
                                                                 summary_column_name='Latitude_MEAN',
                                                                 summary_function=dprep.SummaryFunction.MEAN)])
dflow_mean = dflow_mean.filter(dprep.col('Arrest') == 'false')
dflow_mean.head(1)
```

||Arresto|Latitude_MEAN|
|-----|-----|----|
|0|false|41,878961|

Il valore `MEAN` della latitudine sembra corretto, usare la funzione `ImputeColumnArguments` per attribuirlo. Questa funzione accetta una stringa `column_id` e una `ReplaceValueFunction` per specificare il tipo di attribuzione. Al valore di longitudine mancante viene attribuito il 42 in base a informazioni esterne.

I passaggi di attribuzione possono essere concatenati in un oggetto `ImputeMissingValuesBuilder` usando la funzione di compilazione `impute_missing_values()`. Il parametro `impute_columns` accetta una matrice di oggetti `ImputeColumnArguments`. Chiamare la funzione `learn()` per archiviare i passaggi di attribuzione, quindi applicarla a un oggetto flusso di dati usando `to_dataflow()`.

```python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id='Latitude',
                                          impute_function=dprep.ReplaceValueFunction.MEAN)
# impute with custom value 42
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=42)
# get instance of ImputeMissingValuesBuilder
impute_builder = dflow.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])

impute_builder.learn()
dflow_imputed = impute_builder.to_dataflow()
dflow_imputed.head(3)
```

||ID|Arresto|Latitude|Longitudine|
|-----|------|-----|------|-----|
|0|10140490|false|41,973309|-87,800175|
|1|10139776|false|42,008124|-87,659550|
|2|10140270|false|41,878961|42,000000|

Come illustrato nel risultato precedente, alla latitudine mancante è stato attribuito il valore `MEAN` del gruppo `Arrest=='false'`. Alla longitudine mancante è stato attribuito il valore 42.

```python
imputed_longitude = dflow_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>Derivare le colonne in base a un esempio

Uno degli strumenti più avanzati in Azure Machine Learning Data Prep SDK è la possibilità di derivare colonne tramite esempi dei risultati desiderati. Ciò consente di fornire all'SDK un esempio in modo che possa generare il codice appropriato per ottenere la trasformazione prevista.

```python
import azureml.dataprep as dprep
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
dflow.head(4)
```

||DATE|REPORTTPYE|HOURLYDRYBULBTEMPF|HOURLYRelativeHumidity|HOURLYWindSpeed|
|----|----|----|----|----|----|
|0|1/1/2015 00:54|FM-15|22|50|10|
|1|1/1/2015 01:00|FM-12|22|50|10|
|2|1/1/2015 01:54|FM-15|22|50|10|
|3|1/1/2015 02:54|FM-15|22|50|11|

Si supponga che sia necessario aggiungere questo file a un set di dati in cui data e ora sono in formato "10 marzo 2018 | 02:00 -04:00".

```python
builder = dflow.builders.derive_column_by_example(source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(source_data=dflow.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview(count=5) 
```

||DATE|date_timerange|
|----|----|----|
|0|1/1/2015 00:54|1 gennaio 2015 00:00-02:00|
|1|1/1/2015 01:00|1 gennaio 2015 00:00-02:00|
|2|1/1/2015 01:54|1 gennaio 2015 00:00-02:00|
|3|1/1/2015 02:54|1 gennaio 2015 02:00-0400|
|4|1/1/2015 03:54|1 gennaio 2015 02:00-0400|

Il codice riportato sopra crea per prima cosa un generatore per la colonna derivata. Vengono forniti una matrice di colonne di origine da prendere in considerazione (`DATE`) e un nome per la nuova colonna da aggiungere. Come primo esempio, si passa alla seconda riga (indice 1) e si assegna un valore previsto per la colonna derivata.

Infine, si chiama `builder.preview(skip=30, count=5)` ed è possibile visualizzare la colonna derivata accanto alla colonna di origine. Il formato sembra corretto, ma si visualizzano solo i valori per la stessa data "1 gennaio 2015".

A questo punto, passare il numero di righe che si desidera su `skip` dalla parte superiore per visualizzare le righe più in basso.

> [!NOTE]
> La funzione preview() ignora le righe, ma non il nuovo numero dell'indice di output. Nell'esempio seguente, l'indice 0 nella tabella corrisponde all'indice 30 nel flusso di dati.

```python
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|1 gennaio 2015 22:00-00:00|
|1|1/1/2015 23:54|1 gennaio 2015 22:00-00:00|
|2|1/1/2015 23:59|1 gennaio 2015 22:00-00:00|
|3|1/2/2015 00:54|1 febbraio 2015 00:00-02:00|
|4|1/2/2015 01:00|1 febbraio 2015 00:00-02:00|

Qui si nota un problema con il programma generato. Basandosi esclusivamente sull'esempio fornito in precedenza, il programma derive ha scelto di analizzare la data nel formato "Giorno/mese/anno", che non è ciò che si vuole in questo caso. Per risolvere questo problema, un indice specifico di record di destinazione e specificare un altro esempio tramite il `add_example()` funzionano nel `builder` variabile.

```python
builder.add_example(source_data=dflow.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|1 gennaio 2015 22:00-00:00|
|1|1/1/2015 23:54|1 gennaio 2015 22:00-00:00|
|2|1/1/2015 23:59|1 gennaio 2015 22:00-00:00|
|3|1/2/2015 00:54|2 gennaio 2015 00:00-02:00|
|4|1/2/2015 01:00|2 gennaio 2015 00:00-02:00|

Ora gestiscano correttamente le righe ' 1, 2 o 2015' come '2 gennaio 2015', ma se si osserva oltre indice 76 della colonna derivata, noterete che i valori alla fine abbiano nulla nella colonna derivata.

```python
builder.preview(skip=75, count=5)
```


||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|Jan 3, 2015 6AM-8AM|
|1|1/3/2015 7:54|Jan 3, 2015 6AM-8AM|
|2|1/29/2015 6:54|Nessuna|
|3|1/29/2015 7:00|Nessuna|
|4|1/29/2015 7:54|Nessuna|

```python
builder.add_example(source_data=dflow.iloc[77], example_value='Jan 29, 2015 6AM-8AM')
builder.preview(skip=75, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|Jan 3, 2015 6AM-8AM|
|1|1/3/2015 7:54|Jan 3, 2015 6AM-8AM|
|2|1/29/2015 6:54|Jan 29, 2015 6AM-8AM|
|3|1/29/2015 7:00|Jan 29, 2015 6AM-8AM|
|4|1/29/2015 7:54|Jan 29, 2015 6AM-8AM|

 Per visualizzare un elenco di derivazioni di esempio corrente chiamare `list_examples()` sull'oggetto generatore.

```python
examples = builder.list_examples()
```

| |DATE|esempio|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 01:00|1 gennaio 2015 00:00-02:00|-1|
|1|1/2/2015 00:54|2 gennaio 2015 00:00-02:00|-2|
|2|29/01/2015 20:54|29 gennaio 2015 20:00-22:00|-3|


In alcuni casi se si desidera eliminare esempi che non sono corretti, è possibile passare una `example_row` da pandas DataFrame, o `example_id` valore. Ad esempio, se si esegue `builder.delete_example(example_id=-1)`, Elimina il primo esempio di trasformazione.


Chiamare `to_dataflow()` sul generatore, che restituisce un flusso di dati con le colonne derivate desiderate aggiunte.

```python
dflow = builder.to_dataflow()
df = dflow.to_pandas_dataframe()
```

## <a name="filtering"></a>Filtri

il SDK include i metodi [ `drop_columns()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#drop-columns-columns--multicolumnselection-----azureml-dataprep-api-dataflow-dataflow) e [ `filter()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py) per consentire di filtrare le colonne o righe.

### <a name="initial-setup"></a>Configurazione iniziale

```python
import azureml.dataprep as dprep
from datetime import datetime
dflow = dprep.read_csv(path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|
|1|01-08-2013 08:14:37|01-08-2013 09:09:06|N|1|0|0|0|0|1|.00|0|0|21,25|
|2|01-08-2013 09:13:00|01-08-2013 11:38:00|N|1|0|0|0|0|2|.00|0|0|75|
|3|01-08-2013 09:48:00|01-08-2013 09:49:00|N|5|0|0|0|0|1|.00|0|1|2.1|
|4|01-08-2013 10:38:35|01-08-2013 10:38:51|N|1|0|0|0|0|1|.00|0|0|3.25|

### <a name="filtering-columns"></a>Filtri delle colonne

Per filtrare le colonne, usare `drop_columns()`. Questo metodo accetta un elenco di colonne da eliminare o un argomento più complesso denominato [ `ColumnSelector` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.columnselector?view=azure-dataprep-py).

#### <a name="filtering-columns-with-list-of-strings"></a>Filtrare le colonne con un elenco di stringhe

In questo esempio `drop_columns()` accetta un elenco di stringhe. Ogni stringa deve corrispondere esattamente alla colonna desiderata da eliminare.

```python
dflow = dflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|
|1|01-08-2013 08:14:37|01-08-2013 09:09:06|0|0|0|0|1|.00|0|0|21,25|

#### <a name="filtering-columns-with-regex"></a>Filtrare le colonne con regex

In alternativa, usare l'espressione `ColumnSelector` per eliminare le colonne che corrispondono a un'espressione regex. In questo esempio si eliminano tutte le colonne che corrispondono all'espressione `Column*|.*longitude|.*latitude`.

```python
dflow = dflow.drop_columns(dprep.ColumnSelector('Column*|.*longitud|.*latitude', True, True))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|
|1|01-08-2013 08:14:37|01-08-2013 09:09:06|1|.00|0|0|21,25|

## <a name="filtering-rows"></a>Filtro delle righe

Per filtrare le righe, usare `filter()`. Questo metodo accetta un'espressione Azure Machine Learning Data Prep SDK come argomento e restituisce un nuovo flusso di dati con le righe che l'espressione valuta come True. Le espressioni vengono compilate usando i generatori di espressioni (`col`, `f_not`, `f_and`, `f_or`) e gli operatori normali (>, <, >=, <=, ==,!=).

### <a name="filtering-rows-with-simple-expressions"></a>Filtrare le righe con delle espressioni semplici

Usare il generatore di espressioni `col`, specificare il nome di colonna come argomento di stringa `col('column_name')`. Usare questa espressione in combinazione con uno degli operatori standard >, <, >=, <=, ==, != per creare un'espressione come `col('Tip_amount') > 0`. Infine, passare l'espressione compilata nella funzione `filter()`.

In questo esempio `dflow.filter(col('Tip_amount') > 0)` restituisce un nuovo flusso di dati con le righe in cui il valore di `Tip_amount` è maggiore di 0.

> [!NOTE] 
> `Tip_amount` viene prima convertito in numerico, il che consente di compilare un'espressione che lo confronta con altri valori numerici.

```python
dflow = dflow.to_number(['Tip_amount'])
dflow = dflow.filter(dprep.col('Tip_amount') > 0)
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|01-08-2013 19:33:28|01-08-2013 19:35:21|5|.00|0,08|0|4,58|
|1|05-08-2013 13:16:38|05-08-2013 13:18:24|1|.00|0,30|0|3.8|

### <a name="filtering-rows-with-complex-expressions"></a>Filtro delle righe con espressioni complesse

Per filtrare tramite espressioni complesse, combinare una o più espressioni semplici con i generatori di espressioni `f_not`, `f_and`, o `f_or`.

In questo esempio `dflow.filter()` restituisce un nuovo flusso di dati con le righe in cui `'Passenger_count'` è minore di 5 e `'Tolls_amount'` è maggiore di 0.

```python
dflow = dflow.to_number(['Passenger_count', 'Tolls_amount'])
dflow = dflow.filter(dprep.f_and(dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|08-08-2013 12:16:00|08-08-2013 12:16:00|1.0|.00|2.25|5,00|19,75|
|1|12-08-2013 14:43:53|12-08-2013 15:04:50|1.0|5,28|6,46|5,33|32,29|

È anche possibile filtrare le righe combinazione più di un generatore di espressioni per creare un'espressione annidata.

> [!NOTE]
> `lpep_pickup_datetime` e `Lpep_dropoff_datetime` vengono prima convertiti in datetime, il che consente di compilare un'espressione che li confronta con altri valori di datetime.

```python
dflow = dflow.to_datetime(['lpep_pickup_datetime', 'Lpep_dropoff_datetime'], ['%Y-%m-%d %H:%M:%S'])
dflow = dflow.to_number(['Total_amount', 'Trip_distance'])
mid_2013 = datetime(2013,7,1)
dflow = dflow.filter(
    dprep.f_and(
        dprep.f_or(
            dprep.col('lpep_pickup_datetime') > mid_2013,
            dprep.col('Lpep_dropoff_datetime') > mid_2013),
        dprep.f_and(
            dprep.col('Total_amount') > 40,
            dprep.col('Trip_distance') < 10)))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|13-08-2013 06:11:06+00:00|13-08-2013 06:30:28+00:00|1.0|9,57|7,47|5,33|44,80|
|1|23-08-2013 12:28:20+00:00|23-08-2013 12:50:28+00:00|2.0|8.22|8,08|5,33|40,41|

## <a name="custom-python-transforms"></a>Trasformazioni di Python personalizzate

Esisteranno sempre situazioni in cui il modo più facile di creare una trasformazione è con la scrittura di uno script personalizzato. L'SDK fornisce tre punti di estensione che è possibile usare per creare script Python personalizzati.

- Nuova colonna di script
- Nuovo filtro di script
- Trasformare la partizione

Ciascuna delle estensioni è supportata nel runtime di scalabilità verticale e orizzontale. Un vantaggio principale nell'uso di questi punti di estensione è che non è necessario eseguire il pull di tutti i dati per creare un frame di dati. Il codice di Python personalizzato verrà eseguito come altre trasformazioni, su larga scala, per partizione e in genere in parallelo.

### <a name="initial-data-preparation"></a>Preparazione iniziale dei dati

Avviare il caricamento di alcuni dati dal BLOB di Azure.

```python
import azureml.dataprep as dprep
col = dprep.col

dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
dflow.head(2)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|
|0|ALABAMA|1|101710|Contea Hale|10171002158| |
|1|ALABAMA|1|101710|Contea Hale|10171002162| |

Ridurre il set di dati ed eseguire alcune trasformazioni di base tra cui la rimozione di colonne, sostituendo i valori e conversione dei tipi.

```python
dflow = dflow.keep_columns(['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
dflow = dflow.replace_na(columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
dflow = dflow.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
dflow.head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|Contea Hale|1,017100e+10|Nessuna|
|1|ALABAMA|Contea Hale|1,017100e+10|Nessuna|

Cercare i valori Null tramite il filtro seguente.

```python
dflow.filter(col('MAM_MTH00numvalid_1011').is_null()).head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|Contea Hale|1,017100e+10|Nessuna|
|1|ALABAMA|Contea Hale|1,017100e+10|Nessuna|

### <a name="transform-partition"></a>Trasformare la partizione

Uso [ `transform_partition()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#transform-partition-script--str-----azureml-dataprep-api-dataflow-dataflow) per sostituire tutti i valori null con un valore 0. Questo codice verrà eseguito per partizione, non nell'intero set di dati in una volta sola. Questo significa che in un set di dati di grandi dimensioni questo codice può essere eseguito in parallelo mentre il runtime elabora i dati, partizione per partizione.

Lo script Python deve definire una funzione chiamata `transform()` che accetta due argomenti, `df` e `index`. L'argomento `df` sarà un dataframe pandas che contiene i dati della partizione, mentre l'argomento `index` è un identificatore univoco della partizione. La funzione di trasformazione può modificare completamente il dataframe passato, ma deve restituire un dataframe. Qualsiasi libreria importata dallo script Python deve essere presente nell'ambiente in cui viene eseguito il flusso di dati.

```python
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
df.head(2)
```

||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|Contea Hale|1,017100e+10|0.0|
|1|ALABAMA|Contea Hale|1,017100e+10|0.0|

### <a name="new-script-column"></a>Nuova colonna di script

È possibile usare uno script Python per creare una nuova colonna con il relativo nome e il nome dello stato, nonché da convertire in maiuscolo il nome dello stato. A questo scopo, usare il [ `new_script_column()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-column-new-column-name--str--insert-after--str--script--str-----azureml-dataprep-api-dataflow-dataflow) metodo sul flusso di dati.

Lo script Python deve definire una funzione chiamata `newvalue()` che accetta un solo argomento `row`. L'argomento `row` è un dict (`key`: nome colonna, `val`: valore corrente) e verrà passato a questa funzione per ogni riga del set di dati. Questa funzione deve restituire un valore da usare nella nuova colonna. Qualsiasi libreria importata dallo script Python deve essere presente nell'ambiente in cui viene eseguito il flusso di dati.

```python
dflow = dflow.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Contea Hale|Contea Hale, Alabama|1,017100e+10|0.0|
|1|ALABAMA|Contea Hale|Contea Hale, Alabama|1,017100e+10|0.0|

### <a name="new-script-filter"></a>Nuovo filtro di script

Compilare un'espressione di Python con [ `new_script_filter()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-filter-script--str-----azureml-dataprep-api-dataflow-dataflow) per filtrare il set di dati solo le righe in cui 'Hale' non è nel nuovo `county_state` colonna. L'espressione restituisce `True` se si desidera mantenere la riga, e `False` per eliminare la riga.

```python
dflow = dflow.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Contea Jefferson|Contea Jefferson, Alabama|1.019200e+10|1.0|
|1|ALABAMA|Contea Jefferson|Contea Jefferson, Alabama|1.019200e+10|0.0|

## <a name="next-steps"></a>Passaggi successivi

* Vedere il SDK [Panoramica](https://aka.ms/data-prep-sdk) per esempi di utilizzo e i modelli di progettazione
* Vedere il SDK di Azure Machine Learning Data Prep [esercitazione](tutorial-data-prep.md) per un esempio di risoluzione di uno scenario specifico
