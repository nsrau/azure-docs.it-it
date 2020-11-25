---
title: Visualizza dati con Apache Spark
description: Creare visualizzazioni di dati avanzate usando i notebook di Apache Spark e Azure sinapsi Analytics
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: 820dd8b607f5ec2fdc44d25063e0a941f76237ad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919741"
---
# <a name="analyze-data-with-apache-spark"></a>Analizzare i dati con Apache Spark

In questa esercitazione si apprenderà come eseguire l'analisi esplorativa dei dati usando i set di dati aperti di Azure e Apache Spark e visualizzare quindi i risultati in un notebook di Azure sinapsi Studio.

In particolare, si analizzerà il set di dati di [New York City (NYC) taxi](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) . I dati sono disponibili tramite set di dati aperti di Azure. Questo subset del set di dati contiene informazioni sulle corse di taxi gialle, incluse informazioni su ogni corsa, l'ora di inizio e di fine e le località, il costo e altri attributi interessanti.
  
## <a name="before-you-begin"></a>Prima di iniziare
- Creare un pool di Apache Spark seguendo l' [esercitazione creare un pool di Apache Spark](../articles/../quickstart-create-apache-spark-pool-studio.md) 

## <a name="download-and-prepare-the-data"></a>Scaricare e preparare i dati
1. Creare un notebook usando il kernel PySpark. Per istruzioni, vedere [creare un notebook](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook). 
   
> [!Note]
> 
> Dato che è stato usato il kernel PySpark, non è necessario creare contesti in modo esplicito. Il contesto Spark viene creata automaticamente quando si esegue la prima cella di codice.
>

2. In questa esercitazione si useranno diverse librerie che consentono di visualizzare il set di dati. Per eseguire questa analisi, è necessario importare le librerie seguenti: 

```python
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
```

3. Poiché i dati non elaborati sono in formato Parquet, è possibile usare il contesto di Spark per eseguire direttamente il pull del file in memoria come dataframe. Creare un dataframe di Spark recuperando i dati tramite l'API dei set di dati aperti. In questo esempio si userà lo schema di dataframe di Spark sulle proprietà di *lettura* per dedurre i tipi di oggetto e lo schema.

```python
from azureml.opendatasets import NycTlcYellow
from datetime import datetime
from dateutil import parser

end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
df = nyc_tlc.to_spark_dataframe()
```

4. Una volta letti i dati, sarà necessario eseguire un filtro iniziale per pulire il set di dati. È possibile rimuovere le colonne non necessarie e aggiungere altre colonne che estraggono informazioni importanti. Inoltre, si filtrano le anomalie all'interno del set di dati.

```python
# Filter the dataset 
from pyspark.sql.functions import *

filtered_df = df.select('vendorID', 'passengerCount', 'tripDistance','paymentType', 'fareAmount', 'tipAmount'\
                                , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                , dayofweek('tpepPickupDateTime').alias('day_of_week')\
                                , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month'))\
                            .filter((df.passengerCount > 0)\
                                & (df.tipAmount >= 0)\
                                & (df.fareAmount >= 1) & (df.fareAmount <= 250)\
                                & (df.tripDistance > 0) & (df.tripDistance <= 200))

filtered_df.createOrReplaceTempView("taxi_dataset")
```

## <a name="analyze-data"></a>Analizzare i dati
 Gli analisti dei dati hanno a disposizione una vasta gamma di strumenti che consentono di estrarre informazioni dettagliate dai dati. In questa parte dell'esercitazione verranno illustrati alcuni strumenti utili disponibili all'interno dei notebook di Azure sinapsi Analytics. In questa analisi si desidera comprendere i fattori che producono più suggerimenti per i taxi per il periodo selezionato.

###  <a name="apache-spark-sql-magic"></a>Apache Spark Magic SQL 
In primo luogo, si eseguirà l'analisi esplorativa dei dati tramite Apache Spark comandi SQL e Magic con il notebook sinapsi. Una volta ottenuta la query, i risultati vengono visualizzati usando la funzionalità incorporata ```chart options``` . 

1. All'interno del notebook creare una nuova cella e copiare il codice riportato di seguito. Utilizzando questa query, è necessario comprendere come sono stati modificati gli importi della mancia media nel periodo selezionato. Questa query consentirà anche di identificare altre informazioni utili, tra cui l'importo minimo/massimo della mancia al giorno e l'importo medio della tariffa.
   
```sql
%%sql
SELECT 
    day_of_month
    , MIN(tipAmount) AS minTipAmount
    , MAX(tipAmount) AS maxTipAmount
    , AVG(tipAmount) AS avgTipAmount
    , AVG(fareAmount) as fareAmount
FROM taxi_dataset 
GROUP BY day_of_month
ORDER BY day_of_month ASC
```

2. Al termine dell'esecuzione della query, è possibile visualizzare i risultati passando alla **visualizzazione grafico**. In questo esempio verrà creato un grafico a **linee** specificando il ```day_of_month``` campo come **chiave** e ```avgTipAmount``` come **valore**. Una volta effettuate le selezioni, fare clic su **applica** per aggiornare il grafico. 
   
## <a name="visualize-data"></a>Visualizzare i dati
Oltre alle opzioni predefinite per la creazione di grafici dei notebook, è anche possibile usare le librerie open source più diffuse per creare visualizzazioni personalizzate. Negli esempi seguenti si useranno Seaborn e Matplotlib, che sono comunemente usate in librerie Python per la visualizzazione dei dati. 

> [!Note]
> 
> Per impostazione predefinita, ogni pool di Apache Spark di Azure sinapsi Analytics contiene un set di librerie predefinite e di uso comune. È possibile visualizzare l'elenco completo delle librerie nel [runtime della sinapsi di Azure](../spark/apache-spark-version-support.md). online. Inoltre, per rendere disponibile per le applicazioni di terze parti o codice compilato localmente, è possibile [installare una libreria](../spark/apache-spark-azure-portal-add-libraries.md) in uno dei pool Spark (anteprima).
>

1. Per rendere più semplice e meno costoso lo sviluppo, viene sottocampionato il set di dati. Si utilizzerà la funzionalità di campionamento Apache Spark incorporata. Inoltre, Seaborn e matplotlib richiedono un dataframe Pandas o una matrice numpy. Per ottenere un dataframe Pandas, si userà il ```toPandas()``` comando per convertire il frame di dataframe.

```python
# To make development easier, faster and less expensive down sample for now
sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()
```

1. Per prima cosa, è necessario comprendere la distribuzione dei suggerimenti nel set di dati. Si userà matplotlib per creare un istogramma che mostra la distribuzione dell'importo della Mancia e del conteggio. In base alla distribuzione, è possibile notare che i suggerimenti sono inclinati verso un importo minore o uguale a $10.

```python
# Look at tips by count histogram using Matplotlib

ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()
```

![Istogramma di suggerimenti](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. Si desidera ora comprendere la relazione tra i suggerimenti per una determinata corsa e il giorno della settimana. Si userà Seaborn per creare un plot box che riepiloga le tendenze per ogni giorno della settimana. 

```python
# View the distribution of tips by day of week using Seaborn
ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
ax.set_title('Tip amount distribution per day')
ax.set_xlabel('Day of Week')
ax.set_ylabel('Tip Amount ($)')
plt.show()

```
![Distribuzione di suggerimenti al giorno](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. A questo punto, è possibile che esista una relazione positiva tra il numero di passeggeri e l'importo totale delle mance dei taxi. Per verificare questa relazione, si eseguirà il codice seguente per generare un plot box che illustra la distribuzione dei suggerimenti per ogni conteggio passeggeri. 
   
```python
# How many passengers tipped by various amounts 
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
ax2.set_ylim(0,30)
plt.suptitle('')
plt.show()
```
![Tracciato box baffi](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. Infine, si desidera comprendere la relazione tra l'importo della Mancia della tariffa. In base ai risultati, si noterà che sono presenti diverse osservazioni, in cui gli utenti non puntano. Tuttavia, viene anche visualizzata una relazione positiva tra la tariffa complessiva e gli importi delle mance.
   
```python
# Look at the relationship between fare and tip amounts

ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```
![Grafico a dispersione](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>Arrestare l'istanza di Spark

Al termine dell'esecuzione dell'applicazione, arrestare il notebook per rilasciare le risorse chiudendo la scheda o selezionando **Termina sessione** dal pannello di stato nella parte inferiore del notebook.

## <a name="see-also"></a>Vedere anche

- [Panoramica: Apache Spark in Azure Synapse Analytics](apache-spark-overview.md)
- [Creare un modello di Machine Learning con Apache SparkML](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>Passaggi successivi

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentazione ufficiale di Apache Spark](https://spark.apache.org/docs/latest/)