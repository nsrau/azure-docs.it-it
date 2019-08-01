---
title: Esplorare e trasformare i dati (classe DataSet)
titleSuffix: Azure Machine Learning service
description: Esplorare i dati usando statistiche di riepilogo e preparare i dati tramite la pulizia dei dati, la trasformazione e la progettazione delle funzionalità
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/23/2019
ms.openlocfilehash: f680a1cb15edf0141897c74da3b7c7afa01acae0
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699110"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>Esplorare e preparare i dati con la classe DataSet (anteprima)

Informazioni su come esplorare e preparare i dati con il pacchetto azureml-DataSets in [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). La classe [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) (anteprima) consente di esplorare e preparare i dati fornendo funzioni quali: campionamento, statistiche di riepilogo e trasformazioni intelligenti. I passaggi della trasformazione vengono salvati nelle [definizioni del set di dati](how-to-manage-dataset-definitions.md) con la possibilità di gestire più file di grandi dimensioni di schemi diversi in modo estremamente scalabile.

> [!Important]
> Alcune classi del set di dati (anteprima) presentano dipendenze dal pacchetto [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) (GA). Mentre le funzioni di trasformazione possono essere eseguite direttamente con le [funzioni di preparazione dei dati](how-to-transform-data.md)di GA'ed, è consigliabile usare i wrapper del pacchetto del set di dati descritti in questo articolo se si sta creando una nuova soluzione. Azure Machine Learning set di dati (anteprima) consentono non solo di trasformare i dati, ma anche di creare [snapshot dei dati](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py) e archiviare le [definizioni del set](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py)di dati con versione. DataSets è la versione successiva di data Prep SDK, che offre funzionalità espanse per la gestione dei set di dati nelle soluzioni di intelligenza artificiale.

## <a name="prerequisites"></a>Prerequisiti

Per esplorare e preparare i dati, è necessario:

* Una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

* Un'area di lavoro del servizio Azure Machine Learning. Vedere [creare un'area di lavoro del servizio Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* SDK Azure Machine Learning per Python (versione 1.0.21 o successiva), che include il pacchetto azureml-DataSets. Per eseguire l'installazione o l'aggiornamento alla versione più recente dell'SDK, vedere [installare o aggiornare l'SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* SDK per la preparazione dei dati Azure Machine Learning. Per installare o aggiornare alla versione più recente, vedere [Install or Update the data Prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install).

* Scaricare i file di esempio da seguire insieme agli esempi: [Crime. csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) e [City. JSON](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json).

## <a name="sampling"></a>campionamento

Prendere un esempio di dati per ottenere una conoscenza iniziale dell'architettura dei dati e del contenuto. A questo punto, il [`sample()`](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) metodo della classe DataSet supporta le strategie di campionamento primi N, semplici casuali e stratificate.

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>Top N Sample

Per il campionamento Top N, i primi n record del set di dati sono l'esempio. Questa operazione è utile se si sta tentando semplicemente di ottenere un'idea di come appaiono i record di dati o di visualizzare i campi presenti nei dati.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||id|Case Number|Data|Blocco|IUCR|Tipo principale|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|PRATICA INGANNEVOLE|...
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|FURTO|...
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|PRATICA INGANNEVOLE|...
3|10519591|HZ261534|4/15/2016 9:00|AVE 113XX S PRAIRIE|1120|PRATICA INGANNEVOLE|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|FURTO|...

### <a name="simple-random-sample"></a>Esempio semplice casuale

In un campionamento casuale semplice, ogni membro del popolamento dei dati ha la stessa probabilità di essere selezionato come parte dell'esempio. Nella strategia di esempio, i record del set di dati vengono selezionati in base alla probabilità specificata e restituisce un set di dati modificato. `simple_random` Il parametro seed è facoltativo.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||id|Case Number|Data|Blocco|IUCR|Tipo principale|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|FURTO|...
1|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|PRATICA INGANNEVOLE|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|FURTO|...

### <a name="stratified-sample"></a>Campione stratificato

Gli esempi stratificati assicurano che determinati gruppi di un popolamento siano rappresentati nell'esempio. Nella strategia di `fractions` esempio,lapopolazioneèdivisainstrati,osottogruppi,inbasealleanalogie,eirecordvengonoselezionatiinmodocasualedaognistratoinbaseaipesideglistrati`stratified` indicati dal parametro.

Nell'esempio seguente viene raggruppato ogni record per le colonne specificate e viene incluso il record detto in base alle informazioni sul peso di strati `fractions`X in. Se non viene specificato alcun Strata o il record non può essere raggruppato, il peso predefinito è 0.

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||id|Case Number|Data|Blocco|IUCR|Tipo principale|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|FURTO|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|FURTO|...
2|10535059|HZ278872|4/15/2016 4:30|004XX S KILBOURN AVE|810|FURTO|...

## <a name="explore-with-summary-statistics"></a>Esplora con statistiche di riepilogo

 Rilevare le anomalie, i valori mancanti o i conteggi [`get_profile()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) degli errori con il metodo. Questa funzione ottiene il profilo e le statistiche di riepilogo dei dati, che a sua volta consente di determinare le operazioni di preparazione dei dati necessarie da applicare.

```Python
dataset.get_profile()
```

||Type|Min|Max|Conteggio|Totale mancanti|Totale non mancanti|Percentuale mancanti|Totale errori|Totale vuoti|Quantile 0,1%|Quantile 1%|Quantile 5%|Quantile 25%|Quantile 50%|Quantile 75%|Quantile 95%|Quantile 99%|Quantile 99,9%|Media|Deviazione standard|Variance|Asimmetria|Curtosi
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType. INTEGER|1.04986 e + 07|1.05351 e + 07|10,0|0.0|10,0|0.0|0.0|0.0|1.04986 e + 07|1.04992 e + 07|1.04986 e + 07|1.05166 e + 07|1.05209 e + 07|1.05259 e + 07|1.05351 e + 07|1.05351 e + 07|1.05351 e + 07|1.05195 e + 07|12302,7|1.51358 e + 08|-0,495701|-1,02814
Case Number|FieldType.STRING|HZ239907|HZ278872|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Date|FieldType. DATE|2016-04-04 23:56:00 + 00:00|2016-04-15 17:00:00 + 00:00|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Blocca|FieldType.STRING|004XX S KILBOURN AVE|AVE 113XX S PRAIRIE|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType. INTEGER|810|1154|10,0|0.0|10,0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058,5|137,285|18847,2|-0,785501|-1,3543
Tipo principale|FieldType.STRING|PRATICA INGANNEVOLE|FURTO|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Descrizione|FieldType.STRING|CONTROLLO FASULLO|OLTRE $500|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Descrizione della posizione|FieldType.STRING||SCUOLA, PUBBLICO, COMPILAZIONE|10,0|0.0|10,0|0.0|0.0|1.0||||||||||||||
Arresto|FieldType. BOOLEAN|False|False|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Nazionale|FieldType. BOOLEAN|False|False|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Beat|FieldType. INTEGER|531|2433|10,0|0.0|10,0|0.0|0.0|0.0|531|531|531|614|1318,5|1911|2433|2433|2433|1371,1|692,094|478994|0,105418|-1,60684
Distretto|FieldType. INTEGER|5|24|10,0|0.0|10,0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13,5|6,94822|48,2778|0,0930109|-1,62325
Corsia|FieldType. INTEGER|1|48|10,0|0.0|10,0|0.0|0.0|0.0|1|5|1|9|22,5|40|48|48|48|24.5|16,2635|264,5|0,173723|-1,51271
Area della community|FieldType. INTEGER|4|77|10,0|0.0|10,0|0.0|0.0|0.0|4|8.5|4|24|37,5|71|77|77|77|41,2|26,6366|709,511|0,112157|-1,73379
Codice FBI|FieldType. INTEGER|6|11|10,0|0.0|10,0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2,36643|5.6|-0,702685|-1,59582
Coordinata X|FieldType. INTEGER|1.16309 e + 06|1.18336 e + 06|10,0|7.0|3.0|0,7|0.0|0.0|1.16309 e + 06|1.16309 e + 06|1.16309 e + 06|1.16401 e + 06|1.16678 e + 06|1.17921 e + 06|1.18336 e + 06|1.18336 e + 06|1.18336 e + 06|1.17108 e + 06|10793,5|1.165 e + 08|0,335126|-2,33333
Coordinata Y|FieldType. INTEGER|1.8315 e + 06|1.908 e + 06|10,0|7.0|3.0|0,7|0.0|0.0|1.8315 e + 06|1.8315 e + 06|1.8315 e + 06|1.83614 e + 06|1.85005 e + 06|1.89352 e + 06|1.908 e + 06|1.908 e + 06|1.908 e + 06|1.86319 e + 06|39905,2|1.59243 e + 09|0,293465|-2,33333
Year|FieldType. INTEGER|2016|2016|10,0|0.0|10,0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Aggiornato il|FieldType. DATE|2016-05-11 15:48:00 + 00:00|2016-05-27 15:45:00 + 00:00|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Latitudine|FieldType.DECIMAL|41,6928|41,9032|10,0|7.0|3.0|0,7|0.0|0.0|41,6928|41,6928|41,6928|41,7057|41,7441|41,8634|41,9032|41,9032|41,9032|41,78|0,109695|0,012033|0,292478|-2,33333
Longitudine|FieldType.DECIMAL|-87,6764|-87,6043|10,0|7.0|3.0|0,7|0.0|0.0|-87,6764|-87,6764|-87,6764|-87,6734|-87,6645|-87,6194|-87,6043|-87,6043|-87,6043|-87,6484|0,0386264|0,001492|0,344429|-2,33333
Location|FieldType.STRING||(41,903206037,-87,676361925)|10,0|0.0|10,0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>Attribuire i valori mancanti

Nei set di dati, i valori null, i valori NaN e che non contengono contenuto sono considerati valori mancanti. Questi possono influenzare le prestazioni dei modelli di Machine Learning o causare conclusioni non valide. L'imputazione è un approccio comune per risolvere i valori mancanti ed è utile quando si dispone di una percentuale elevata di record di valori mancanti che non è possibile eliminare semplicemente.

Dal profilo del set di dati generato nella sezione precedente, si noterà `Latitude` che `Longitude` le colonne e hanno una percentuale elevata di valori mancanti. In questo esempio vengono calcolati i valori di media e di imputazione mancanti per queste due colonne.

Prima di tutto, ottenere la definizione più recente del [`get_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) set di dati con e ridurre [`keep_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#keep-columns-columns--multicolumnselection--validate-column-exists--bool---false-----azureml-dataprep-api-dataflow-dataflow)i dati con, quindi è possibile visualizzare solo le colonne da indirizzare.

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||id|Arresto| Latitude|Longitudine|
-|---------|-----|---------|----------|
|0|10498554|False|41,692834|-87,604319|
|1|10516598|False| 41,744107 |-87,664494|
|2|10519196|False| NaN|NaN|

Controllare quindi il `MEAN` valore della colonna Latitudine utilizzando la [`summarize()`](/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-ml-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow--summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) funzione. Questa funzione accetta una matrice di colonne nel parametro `group_by_columns` per specificare il livello di aggregazione. Il `summary_columns` parametro accetta la `SummaryColumnsValue` funzione, che specifica il nome della colonna corrente, il nuovo nome del campo calcolato e `SummaryFunction` l'oggetto da eseguire.

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
|0|False|41,780049|

Una volta verificati i valori da imputare, [`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) utilizzare per apprendere un'espressione fissa che imputa le colonne con un valore `MIN`calcolato `MAX`, `MEAN` , o un `CUSTOM` valore. Quando `group_by_columns` viene specificato, verranno attribuiti i valori mancanti dal gruppo con `MIN`, `MAX` e `MEAN` calcolati per ogni gruppo.

Accetta una stringa column_id e un oggetto `ReplaceValueFunction` per specificare il tipo di imputazione. [`ImputeColumnArguments`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) Per il valore di longitudine mancante, imputarlo a-87 in base a una Knowledge base esterna.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

I passaggi imputabili possono essere concatenati in `ImputeMissingValuesBuilder` un oggetto usando [`Builders`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) la funzione [`impute_missing_values()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder)Class. Il parametro `impute_columns` accetta una matrice di oggetti `ImputeColumnArguments`.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

Chiamare la [`learn()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none) funzione per archiviare i passaggi di imputazione e applicarli a un oggetto del flusso di [`to_dataflow()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow)dati tramite.

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

Come illustrato nella tabella di output seguente, la latitudine mancante è stata imputata `MEAN` al valore di `Arrest==False` Group e la longitudine mancante è stata imputata a-87.

||id|Arresto|Latitude|Longitudine
-|---------|-----|---------|----------
0|10498554|False|41,692834|-87,604319
1|10516598|False|41,744107|-87,664494
2|10519196|False|41,780049|-87,000000

Aggiornare la definizione del set di [`update_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) dati con per evitare i passaggi di trasformazione eseguiti.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||id|Arresto|Latitude|Longitudine
-|---------|-----|---------|----------
0|10498554|False|41,692834|-87,604319
1|10516598|False|41,744107|-87,664494
2|10519196|False|41,780049|-87,000000

## <a name="create-assertion-rules"></a>Creare regole di asserzione

Spesso i dati utilizzati durante la pulizia e la preparazione dei dati sono solo un subset dei dati totali necessari per la produzione. Di conseguenza, alcune ipotesi apportate nell'ambito della pulizia potrebbero risultare false. Ad esempio, in un set di dati che si aggiorna continuamente, una colonna che originariamente conteneva solo numeri in un determinato intervallo potrebbe contenere un intervallo di valori più ampio nelle esecuzioni successive. Questi errori spesso generano pipeline interrotte o dati non validi.

I set di dati supportano la creazione di asserzioni nei dati, che vengono valutati durante l'esecuzione della pipeline. Queste asserzioni consentiranno di verificare che le ipotesi sui dati continuino a essere accurate e, in caso contrario, a gestire gli errori di conseguenza.

Se, ad esempio, si desidera `Latitude` vincolare `Longitude` i valori e nel set di dati a intervalli numerici specifici, il [`assert_value()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) metodo garantisce che questo sia sempre il caso.

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
id|FieldType. INTEGER|1.04986 e + 07|1.05351 e + 07|10,0|0.0|10,0|0.0|0.0|0.0|1.04986 e + 07|1.04992 e + 07|1.04986 e + 07|1.05166 e + 07|1.05209 e + 07|1.05259 e + 07|1.05351 e + 07|1.05351 e + 07|1.05351 e + 07|1.05195 e + 07|12302,7|1.51358 e + 08|-0,495701|-1,02814
Arresto|FieldType. BOOLEAN|False|False|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Latitudine|FieldType.DECIMAL|41,6928|41,9032|10,0|0.0|10,0|0.0|0.0|0.0|41,6928|41,7185|41,6928|41,78|41,78|41,78|41,9032|41,9032|41,9032|41,78|0,0517107|0,002674|0,837593|1,05
Longitudine|FieldType. INTEGER|-87|-87|10,0|0.0|10,0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

Dal profilo si noterà che `Error Count` per la `Longitude` colonna è 3. Il codice seguente Filtra il set di dati, recupera l'errore e rileva quale valore causa l'esito negativo dell'asserzione. Da qui, modificare il codice e pulire i dati in modo appropriato.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>Derivare colonne per esempio

Uno degli strumenti più avanzati per i set di dati è la possibilità di derivare colonne utilizzando esempi di risultati desiderati. Questo consente di fornire un esempio di SDK, in modo da poter generare il codice per ottenere le trasformazioni desiderate.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||id|Case Number|Data|Blocca|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO AVE|...

Supponiamo di dover trasformare il formato di data e ora in "2016-04-04 10:00-12AM". Nell'argomento, fornire esempi dell'output desiderato `example_data` nel parametro in questo formato: *(output originale, output desiderato)* . [`derive_column_by_example()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow)

Il codice seguente fornisce due esempi di output desiderato, ("2016-04-04 23:56:00", "2016-04-04 10:00-12AM") e ("2016-04-15 17:00:00", "2016-04-15 16.00-18:00")

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

Nella tabella seguente si noti che una nuova colonna, Date_Time_Range, contiene record nel formato specificato.

||id|Date|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10PM-12AM
1|10516598|2016-04-15 17:00:00|2016-04-15 16.00-18.00
2|10519196|2016-04-15 10:00:00|2016-04-15 10.00-12.00

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>Raggruppamenti fuzzy

Quando si raccolgono dati da origini diverse, è possibile che si verifichino variazioni di ortografia, maiuscole o abbreviazioni delle stesse entità. Standardizzare e riconciliare automaticamente le varianti con la funzionalità raggruppamento fuzzy dell'SDK o clustering di testo.

Ad esempio, la colonna `inspections.business.city` contiene diverse forme del nome città "San Francisco".

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|ispezioni. business_name|inspections.business.address|ispezioni. business. City|...|
-|-----|-------------------------|------------|--|---
0|16162|Alimenti indiani eZee rapidi|3861 24a St|SF|...
1|67565|King of Thai noodles Cafe|1541 TARAVAL St|SAN FRANCISCO|...
2|67565|King of Thai noodles Cafe|1541 TARAVAL St|SAN FRANCISCO|...
3|68701|Grindz|832 Clement St|SF|...
4|69186|Premier catering & Events, Inc.|1255 22a St|S.F.|...

Si userà il [`fuzzy_group_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) metodo per aggiungere una colonna con il formato canonico rilevato automaticamente "San Francisco". Gli argomenti `source_column` e `new_column_name` sono obbligatori. È anche possibile:

* Crea una nuova colonna, `similarity_score_column_name`, che mostra il Punteggio di somiglianza tra ogni coppia di valori originali e canonici.

* Fornire un `similarity_threshold`oggetto, ovvero il Punteggio di somiglianza minimo per i valori raggruppati.

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||inspections.business.business_id|ispezioni. business_name|inspections.business.address|ispezioni. business. City|city_grouped|similarity_score|...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|Alimenti indiani eZee rapidi|3861 24a St|SF|San Francisco|0,814806|...
1|67565|King of Thai noodles Cafe|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
2|67565|King of Thai noodles Cafe|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
3|68701|Grindz|832 Clement St|SF|San Francisco|0,814806|...
4|69186|Premier catering & Events, Inc.|1255 22a St|S.F.|San Francisco|0,814806|...

Nella definizione del set di dati risultante, tutte le diverse varianti di che rappresentano "San Francisco" nei dati sono state normalizzate alla stessa stringa "San Francisco".

Salvare questo passaggio raggruppamento fuzzy nella definizione del set di dati più recente `update_definition()`con.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>Passaggi successivi

* Per un esempio di modello di regressione, vedere l' [esercitazione](tutorial-auto-train-models.md) automatizzata di machine learning.

* Vedere [Cenni preliminari](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) sull'SDK per modelli di progettazione ed esempi di utilizzo.

* Per un esempio dell'uso dei set di impostazioni, vedere i [notebook di esempio](https://aka.ms/dataset-tutorial).
