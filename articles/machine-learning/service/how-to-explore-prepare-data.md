---
title: Esplorare e preparare i dati (set di dati classe)
titleSuffix: Azure Machine Learning service
description: Esplorare i dati usando le statistiche di riepilogo e preparare i dati attraverso la pulizia dei dati, trasformazione e la progettazione di funzionalità
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/19
ms.openlocfilehash: f4e7fcbe403017a6d957a60a8e5664f2e6c5ba26
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409832"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>Esplorare e preparare i dati con la classe di set di dati (anteprima)

Informazioni su come esplorare e preparare i dati con il [SDK di Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Il [set di dati](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) classe (anteprima) consente di esplorare e preparare i dati, fornendo funzioni, ad esempio: campionamento, le statistiche di riepilogo e le trasformazioni intelligente. Passaggi di trasformazione vengono salvati nella [le definizioni di set di dati](how-to-manage-dataset-definitions.md) grazie alla possibilità di gestire più file di grandi dimensioni di schemi diversi in un modo altamente scalabile.

> [!Important]
> Alcune classi di set di dati (anteprima) hanno dipendenze sul SDK di preparazione dei dati (GA). Mentre le funzioni di trasformazione possono essere eseguite direttamente con il GA'ed [funzioni di Data Prep SDK](how-to-transform-data.md), è consigliabile i wrapper di pacchetto di set di dati descritti in questo articolo se si sta creando una nuova soluzione. Azure Machine Learning i set di dati (anteprima) consentono non solo di trasformare i dati, ma anche [dei dati dello snapshot](how-to-create-dataset-snapshots.md) e archiviare [definizioni di set di dati con controllo delle versioni](how-to-manage-dataset-definitions.md). I set di dati è la prossima versione di SDK di preparazione dei dati, che offre funzionalità avanzate per la gestione dei set di dati in soluzioni di intelligenza artificiale.

## <a name="prerequisites"></a>Prerequisiti

Per esplorare e preparare i dati, è necessario:

* Una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

* Un'area di lavoro del servizio Azure Machine Learning. Visualizzare [creare un'area di lavoro del servizio di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* Azure Machine Learning SDK per Python (versione 1.0.21 o versione successiva). Per installare o aggiornare la versione più recente del SDK, vedere [installare o aggiornare il SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* Azure Machine Learning Data Prep SDK. Per installare o aggiornare la versione più recente, vedere [installare o aggiornare il SDK di preparazione dei dati](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install).

* Scaricare i file di esempio per proseguire con gli esempi: [crime.csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) e [city.json](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json).

## <a name="sampling"></a>campionamento

Richiedere un campione di dati per ottenere le informazioni iniziali l'architettura dei dati e il contenuto. A questo punto, il [ `sample()` ](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) metodo dalla classe di set di dati supporta le strategie di campionamento Top N casuale semplice e Stratified.

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>Esempio di primi N

Per il campionamento Top N, i primi n record del set di dati sono l'esempio. Ciò è utile se si sta solo tentando di avere un'idea di quali l'aspetto di record dei dati, ad esempio o per vedere quali sono i campi nei dati.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||ID|Case Number|Data|Blocco|IUCR|Tipo principale|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|PROCEDURE CONSIGLIATE INGANNEVOLI|...
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|FURTO|...
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|PROCEDURE CONSIGLIATE INGANNEVOLI|...
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE AVE|1120|PROCEDURE CONSIGLIATE INGANNEVOLI|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|FURTO|...

### <a name="simple-random-sample"></a>Campionamento casuale semplice

Nel campionamento casuale semplice, ogni membro della popolazione dei dati ha un'uguale probabilità di essere selezionate come parte dell'esempio. Nel `simple_random` strategia di esempio, i record dal set di dati vengono selezionati in base alla probabilità specificata e restituisce un set di dati modificati. Il parametro di valore di inizializzazione è facoltativo.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||ID|Case Number|Data|Blocco|IUCR|Tipo principale|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|FURTO|...
1|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|PROCEDURE CONSIGLIATE INGANNEVOLI|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|FURTO|...

### <a name="stratified-sample"></a>Campione stratificato

Esempi stratificati assicurarsi che determinati gruppi di una popolazione vengano rappresentati nell'esempio. Nel `stratified` strategia di esempio, il popolamento è suddivisa in strata o sottogruppi, in base alla similarità, e i record vengono selezionati casualmente da ogni strato in base ai pesi degli strati indicati dal `fractions` parametro.

Nell'esempio seguente, si raggruppa ogni record per le colonne specificate e includere tale registro sulla base delle informazioni degli strati X peso in `fractions`. Se non viene specificato un strata o il record non può essere raggruppato, lo spessore predefinito all'esempio è 0.

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||ID|Case Number|Data|Blocco|IUCR|Tipo principale|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|FURTO|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|FURTO|...
2|10535059|HZ278872|4/15/2016 4:30|004XX S KILBOURN AVE|810|FURTO|...

## <a name="explore-with-summary-statistics"></a>Esplorare le statistiche summary

 Rilevamento di anomalie, i valori mancanti, o conteggi degli errori con il [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) (metodo). Questa funzione recupera il profilo e le statistiche di riepilogo dei dati, che a sua volta consentono di determinano le operazioni di preparazione dei dati necessari da applicare.

```Python
dataset.get_profile()
```

||Type|Min|Max|Conteggio|Totale mancanti|Totale non mancanti|Percentuale mancanti|Totale errori|Totale vuoti|Quantile 0,1%|Quantile 1%|Quantile 5%|Quantile 25%|Quantile 50%|Quantile 75%|Quantile 95%|Quantile 99%|Quantile 99,9%|Media|Deviazione standard|Variance|Asimmetria|Curtosi
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e+07|1.05351e + 07|10,0|0.0|10,0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e + 07|1.05259e+07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e+08|-0.495701|-1.02814
Case Number|FieldType.STRING|HZ239907|HZ278872|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Data|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Blocca|FieldType.STRING|004XX S KILBOURN AVE|113XX S PRAIRIE AVE|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10,0|0.0|10,0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Tipo principale|FieldType.STRING|PROCEDURE CONSIGLIATE INGANNEVOLI|FURTO|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Descrizione|FieldType.STRING|CONTROLLO FITTIZIO|SU 500 DOLLARI|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Descrizione della posizione|FieldType.STRING||ISTITUTO DI ISTRUZIONE, PUBBLICA, CREAZIONE|10,0|0.0|10,0|0.0|0.0|1.0||||||||||||||
Arresto|FieldType.BOOLEAN|Falso|Falso|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Nazionale|FieldType.BOOLEAN|Falso|Falso|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Beat|FieldType.INTEGER|531|2433|10,0|0.0|10,0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
Distretto|FieldType.INTEGER|5|24|10,0|0.0|10,0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13,5|6.94822|48.2778|0.0930109|-1.62325
Corsia|FieldType.INTEGER|1|48|10,0|0.0|10,0|0.0|0.0|0.0|1|5|1|9|22,5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
Area della community|FieldType.INTEGER|4|77|10,0|0.0|10,0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379
Codice FBI|FieldType.INTEGER|6|11|10,0|0.0|10,0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2.36643|5.6|-0.702685|-1.59582
Coordinata X|FieldType.INTEGER|1.16309e + 06|1.18336e+06|10,0|7.0|3.0|0.7|0.0|0.0|1.16309e + 06|1.16309e + 06|1.16309e + 06|1.16401e + 06|1.16678e+06|1.17921e+06|1.18336e+06|1.18336e+06|1.18336e+06|1.17108e+06|10793.5|1.165e+08|0.335126|-2.33333
Coordinata Y|FieldType.INTEGER|1.8315e + 06|1.908e+06|10,0|7.0|3.0|0.7|0.0|0.0|1.8315e + 06|1.8315e + 06|1.8315e + 06|1.83614e+06|1.85005e + 06|1.89352e+06|1.908e+06|1.908e+06|1.908e+06|1.86319e + 06|39905.2|1.59243e+09|0.293465|-2.33333
Year|FieldType.INTEGER|2016|2016|10,0|0.0|10,0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Aggiornato il|FieldType.DATE|2016-05-11 15:48:00+00:00|2016-05-27 15:45:00+00:00|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Latitudine|FieldType.DECIMAL|41.6928|41.9032|10,0|7.0|3.0|0.7|0.0|0.0|41.6928|41.6928|41.6928|41.7057|41.7441|41.8634|41.9032|41.9032|41.9032|41.78|0.109695|0.012033|0.292478|-2.33333
Longitudine|FieldType.DECIMAL|-87.6764|-87.6043|10,0|7.0|3.0|0.7|0.0|0.0|-87.6764|-87.6764|-87.6764|-87.6734|-87.6645|-87.6194|-87.6043|-87.6043|-87.6043|-87.6484|0.0386264|0.001492|0.344429|-2.33333
Località|FieldType.STRING||(41.903206037, -87.676361925)|10,0|0.0|10,0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>Attribuire i valori mancanti

Nei set di dati, i valori null, NaN e i valori che non contengono contenuto sono considerati valori mancanti. Tali può compromettere le prestazioni di modelli di machine learning o portare a conclusioni non valide. Attribuzione è un approccio comune per risolvere i valori mancanti e risulta utile quando si dispone di un'elevata percentuale di andare persi valore record che non è possibile eliminare semplicemente.

Dal profilo del set di dati generato nella sezione precedente, è possibile vedere che `Latitude` e `Longitude` colonne hanno una percentuale elevata di valori mancanti. In questo esempio, si calcola la media e attribuire valori mancanti per le due colonne.

Innanzitutto, ottenere l'ultima definizione del set di dati con [ `get_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) e ridurre la quantità di dati con [ `keep_columns()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#keep-columns-columns--multicolumnselection-----azureml-dataprep-api-dataflow-dataflow), pertanto è visualizzare solo le colonne che si desidera all'indirizzo.

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||ID|Arresto| Latitude|Longitudine|
-|---------|-----|---------|----------|
|0|10498554|Falso|41.692834|-87.604319|
|1|10516598|Falso| 41.744107 |-87.664494|
|2|10519196|Falso| NaN|NaN|

Successivamente, controllare la `MEAN` valore dell'oggetto colonna latitudine utilizzando i [ `summarize()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) (funzione). Questa funzione accetta una matrice di colonne nel parametro `group_by_columns` per specificare il livello di aggregazione. Il `summary_columns` parametro accetta il `SummaryColumnsValue` funzione, che specifica il nome della colonna corrente, il nome del nuovo campo calcolato e il `SummaryFunction` eseguire.

```Python
lat_mean = ds_def.summarize(group_by_columns = ['Arrest'],
                            summary_columns = [dprep.SummaryColumnsValue(column_id = 'Latitude',
                                                                         summary_column_name = 'Latitude_MEAN',
                                                                         summary_function = dprep.SummaryFunction.MEAN)])
lat_mean = lat_mean.filter(lat_mean['Arrest'] == False)
lat_mean.head(1)
```

||Arresto|Latitude_MEAN|
--|-----|--------|
|0|Falso|41.780049|

Dopo la verifica i valori per l'attribuzione, usare [ `ImputeMissingValuesBuilder` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) per informazioni su un'espressione di tipo fissa che imputa le colonne con un calcolato `MIN`, `MAX`, `MEAN` valore, o un `CUSTOM` valore. Quando `group_by_columns` viene specificato, verranno attribuiti i valori mancanti dal gruppo con `MIN`, `MAX` e `MEAN` calcolati per ogni gruppo.

Il [ `ImputeColumnArguments` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) accetta una stringa di column_id e un `ReplaceValueFunction` per specificare il tipo impute. Per il valore di longitudine mancante, attribuire con-87 basata Knowledge Base esterne.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

Attribuire i passaggi possono essere concatenati in una `ImputeMissingValuesBuilder` utilizzando il [ `Builders` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) classe funzione [ `impute_missing_values()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder). Il parametro `impute_columns` accetta una matrice di oggetti `ImputeColumnArguments`.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

Chiamare il [ `learn()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none) funzionare per archiviare i passaggi impute e applicarle a un oggetto del flusso di dati usando [ `to_dataflow()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow).

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

Come illustrato nella tabella di output seguente, la latitudine manca è stata attribuita con il `MEAN` pari a `Arrest==False` gruppo e la longitudine manca è stata attribuiti con-87.

||ID|Arresto|Latitude|Longitudine
-|---------|-----|---------|----------
0|10498554|Falso|41.692834|-87.604319
1|10516598|Falso|41.744107|-87.664494
2|10519196|Falso|41.780049|-87.000000

Aggiornare la definizione del set di dati, con [ `update_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) per mantenere la procedura di trasformazione eseguita.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||ID|Arresto|Latitude|Longitudine
-|---------|-----|---------|----------
0|10498554|Falso|41.692834|-87.604319
1|10516598|Falso|41.744107|-87.664494
2|10519196|Falso|41.780049|-87.000000

## <a name="create-assertion-rules"></a>Creare regole di asserzione

Spesso, i dati ci impegniamo con durante la pulizia e preparazione dei dati è solo un subset dei dati totali che è necessario per la produzione. Di conseguenza, alcuni dei presupposti che rendiamo come parte del nostro pulizia potrebbe risultare su false. Ad esempio, in un set di dati che vengono aggiornati continuamente, una colonna che originalmente conteneva solo i numeri all'interno di un determinato intervallo potrebbe contenere una vasta gamma di valori nelle esecuzioni successive. Questi errori spesso causano pipeline interrotte o i dati errati.

Supporto di set di dati creando asserzioni sui dati, che vengono valutati quando si esegue la pipeline. Queste asserzioni farci di verificare che l'ipotesi sui dati continuino a essere accurato e, se non, per gestire gli errori di conseguenza.

Ad esempio, se si desidera vincolare `Latitude` e `Longitude` i valori nel set di dati a intervalli numerici specifici, il [ `assert_value()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) metodo garantisce questo è sempre così.

```Python
from azureml.dataprep import value
from azureml.core.dataset import Dataset

# get the latest definition of the Dataset
ds_def = dataset.get_definition()

# set assertion rules for `Latitude` and `Longitude` columns
ds_def = ds_def.assert_value('Latitude', (value <= 90) & (value >= -90), error_code='InvalidLatitude')
ds_def = ds_def.assert_value('Longitude', (value <= 180) & (value >= -87), error_code='InvalidLongitude')

ds_def.get_profile()
```

||Type|Min|Max|Conteggio|Totale mancanti|Totale non mancanti|Percentuale mancanti|Totale errori|Totale vuoti|Quantile 0,1%|Quantile 1%|Quantile 5%|Quantile 25%|Quantile 50%|Quantile 75%|Quantile 95%|Quantile 99%|Quantile 99,9%|Media|Deviazione standard|Variance|Asimmetria|Curtosi
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e+07|1.05351e + 07|10,0|0.0|10,0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e + 07|1.05259e+07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e+08|-0.495701|-1.02814
Arresto|FieldType.BOOLEAN|Falso|Falso|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Latitudine|FieldType.DECIMAL|41.6928|41.9032|10,0|0.0|10,0|0.0|0.0|0.0|41.6928|41.7185|41.6928|41.78|41.78|41.78|41.9032|41.9032|41.9032|41.78|0.0517107|0.002674|0.837593|1,05
Longitudine|FieldType.INTEGER|-87|-87|10,0|0.0|10,0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

Dal profilo, noterete che il `Error Count` per il `Longitude` colonna è 3. Il codice seguente filtra il set di dati, consente di recuperare l'errore e visualizza il valore causa l'esito negativo dell'asserzione. A questo punto, modificare il codice e pulire i dati in modo appropriato.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>Derivare le colonne in base all'esempio

Uno degli strumenti più avanzati per i set di dati è la possibilità di derivare le colonne con esempi di risultati desiderati. Ciò consente di fornire un esempio, il SDK in modo che può generare codice per ottenere le trasformazioni desiderate.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||ID|Case Number|Data|Blocca|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO AVE|...

Ad esempio è necessario trasformare il formato di data e ora per ' 2016-04-04 PM di 10 ore 00:00-'. Nel [ `derive_column_by_example()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow) argomento, vengono forniti esempi di output desiderato nel `example_data` parametro nel formato seguente: *(output originale, output desiderato)*.

Il codice seguente vengono forniti due esempi di output desiderato ("2016-04-04-23:56:00", "2016-04-04 10 PM-12 AM") e ("2016-04-15 17:00:00", "2016-04-15 4 PM - 6 PM")

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

Nella tabella seguente, si noti che una nuova colonna, Date_Time_Range contiene i record nel formato specificato.

||ID|Data|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10PM-12AM
1|10516598|2016-04-15 17:00:00|2016-04-15 4PM-6PM
2|10519196|2016-04-15 10:00:00|2016-04-15 10AM-12PM

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>Raggruppamento fuzzy

Quando si raccolgono dati da diverse origini possono verificarsi variazioni di ortografia, maiuscole/minuscole o le abbreviazioni delle entità stessa. Automaticamente standardizzare e risolvere le differenze tra queste varianti con la funzionalità raggruppamento fuzzy, o testo clustering, del SDK.

Ad esempio, la colonna `inspections.business.city` contiene varie forme del nome di città "San Francisco".

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|...|
-|-----|-------------------------|------------|--|---
0|16162|Quick-N-Ezee Rupia alimenti|3861 24 St|SF|...
1|67565|Re di Cafe possibile Thai|1541 TARAVAL St|SAN FRANCISCO|...
2|67565|Re di Cafe possibile Thai|1541 TARAVAL St|SAN FRANCISCO|...
3|68701|Grindz|St clement 832|SF|...
4|69186|Cucina Premier & eventi, Inc.|1255 22 St|S.F.|...

È possibile usare la [ `fuzzy_group_column()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) metodo per aggiungere una colonna con la forma canonica automaticamente rilevata del "Livorno". Gli argomenti `source_column` e `new_column_name` sono necessari. Hai anche la possibilità di:

* Creare una nuova colonna, `similarity_score_column_name`, che mostra il punteggio di somiglianza tra ogni coppia di valori originali e canonici.

* Fornire un `similarity_threshold`, che costituisce il punteggio di somiglianza minima per i valori vengono raggruppati insieme.

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|city_grouped|similarity_score|...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|Quick-N-Ezee Rupia alimenti|3861 24 St|SF|San Francisco|0.814806|...
1|67565|Re di Cafe possibile Thai|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
2|67565|Re di Cafe possibile Thai|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
3|68701|Grindz|St clement 832|SF|San Francisco|0.814806|...
4|69186|Cucina Premier & eventi, Inc.|1255 22 St|S.F.|San Francisco|0.814806|...

Nella definizione del set di dati risulta, tutte le diverse varianti di rappresentare "Livorno" nei dati sono state normalizzate sulla stessa stringa, "Livorno".

Salvare questo passaggio di trasformazione Raggruppamento fuzzy nella definizione del set di dati più recente con `update_definition()`.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>Passaggi successivi

* [Gestire il ciclo di vita delle definizioni di set di dati](how-to-manage-dataset-definitions.md).

* Vedere l'apprendimento automatico [esercitazione](tutorial-auto-train-models.md) per un esempio di modello di regressione.

* Vedere il SDK [Panoramica](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) per esempi di utilizzo e i modelli di progettazione.

* Per un esempio dell'uso di set di dati, vedere la [notebook di esempio](https://aka.ms/dataset-tutorial).