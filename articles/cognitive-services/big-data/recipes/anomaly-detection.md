---
title: 'Ricetta: manutenzione predittiva con servizi cognitivi per Big Data'
titleSuffix: Azure Cognitive Services
description: Questa Guida introduttiva illustra come eseguire il rilevamento delle anomalie distribuite con i servizi cognitivi per Big Data
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: tracking-python
ms.openlocfilehash: d872e4486bdc8fcd3629feb452b0d436872c70d8
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86189421"
---
# <a name="recipe-predictive-maintenance-with-the-cognitive-services-for-big-data"></a>Ricetta: manutenzione predittiva con servizi cognitivi per Big Data

Questa ricetta Mostra come usare Azure sinapsi Analytics e servizi cognitivi in Spark per la manutenzione predittiva di dispositivi Internet. Verrà seguito l'esempio di [collegamento CosmosDB e sinapsi](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples) . Per semplificare le operazioni, in questa ricetta i dati verranno letti direttamente da un file con estensione CSV anziché da un collegamento a un file con CosmosDB e una sinapsi. Si consiglia vivamente di esaminare l'esempio di collegamento sinapsi.

## <a name="hypothetical-scenario"></a>Scenario ipotetico

Lo scenario ipotetico è un impianto di alimentazione, in cui i dispositivi di tutto il monitorano le [turbine a vapore](https://en.wikipedia.org/wiki/Steam_turbine). La raccolta IoTSignals presenta rivoluzioni al minuto (RPM) e megawatt (MW) per ogni turbina. I segnali provenienti da turbine a vapore vengono analizzati e vengono rilevati segnali anomali.

Potrebbero essere presenti outlier nei dati in frequenza casuale. In questi casi, i valori RPM aumenteranno e l'output di MW diminuirà, per la protezione del circuito. L'idea è quella di visualizzare i dati che variano allo stesso tempo, ma con segnali diversi.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/)
* [Area di lavoro sinapsi di Azure](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace) configurata con un [pool Spark](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool)

## <a name="setup"></a>Configurazione

### <a name="create-an-anomaly-detector-resource"></a>Creare una risorsa di Rilevamento anomalie

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa per Translator usando il [portale di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) o l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli). È anche possibile:

- Visualizzare una risorsa esistente nell' [portale di Azure](https://portal.azure.com/).

Prendere nota dell'endpoint e della chiave per questa risorsa, che sarà necessaria in questa guida.

## <a name="enter-your-service-keys"></a>Immettere le chiavi del servizio

Per iniziare, aggiungere la chiave e il percorso.

```python
service_key = None # Paste your anomaly detector key here
location = None # Paste your anomaly detector location here

assert (service_key is not None)
assert (location is not None)
```

## <a name="read-data-into-a-dataframe"></a>Leggere i dati in un frame di dati

Leggere quindi il file IoTSignals in un dataframe. Aprire un nuovo notebook nell'area di lavoro sinapsi e creare un dataframe dal file.

```python
df_device_info = spark.read.csv("wasbs://publicwasb@mmlspark.blob.core.windows.net/iot/IoTSignals.csv", header=True, inferSchema=True)
```

### <a name="run-anomaly-detection-using-cognitive-services-on-spark"></a>Eseguire il rilevamento di anomalie usando servizi cognitivi in Spark

L'obiettivo è individuare le istanze in cui i segnali provenienti dai dispositivi di Internet delle cose hanno comportato valori anomali, in modo da poter vedere quando si verificano problemi ed eseguire una manutenzione predittiva. A tale scopo, usare il rilevatore di anomalie in Spark:

```python
from pyspark.sql.functions import col, struct
from mmlspark.cognitive import SimpleDetectAnomalies
from mmlspark.core.spark import FluentAPI

detector = (SimpleDetectAnomalies()
    .setSubscriptionKey(service_key)
    .setLocation(location)
    .setOutputCol("anomalies")
    .setGroupbyCol("grouping")
    .setSensitivity(95)
    .setGranularity("secondly"))

df_anomaly = (df_signals
    .where(col("unitSymbol") == 'RPM')
    .withColumn("timestamp", col("dateTime").cast("string"))
    .withColumn("value", col("measureValue").cast("double"))
    .withColumn("grouping", struct("deviceId"))
    .mlTransform(detector)).cache()

df_anomaly.createOrReplaceTempView('df_anomaly')
```

Diamo uno sguardo ai dati:

```python
df_anomaly.select("timestamp","value","deviceId","anomalies.isAnomaly").show(3)
```

Questa cella deve restituire un risultato simile al seguente:

| timestamp           |   Valore | deviceId   | Anomalie   |
|:--------------------|--------:|:-----------|:------------|
| 2020-05-01 18:33:51 |    3174 | dev-7      | Falso       |
| 2020-05-01 18:33:52 |    2976 | dev-7      | Falso       |
| 2020-05-01 18:33:53 |    2714 | dev-7      | Falso       |


 ## <a name="visualize-anomalies-for-one-of-the-devices"></a>Visualizzare le anomalie per uno dei dispositivi

IoTSignals.csv presenta segnali da più dispositivi Internet. Ci si concentrerà su un dispositivo specifico e visualizzerà output anomali dal dispositivo.

```python
df_anomaly_single_device = spark.sql("""
select
  timestamp,
  measureValue,
  anomalies.expectedValue,
  anomalies.expectedValue + anomalies.upperMargin as expectedUpperValue,
  anomalies.expectedValue - anomalies.lowerMargin as expectedLowerValue,
  case when anomalies.isAnomaly=true then 1 else 0 end as isAnomaly
from
  df_anomaly
where deviceid = 'dev-1' and timestamp < '2020-04-29'
order by timestamp
limit 200""")
```

Ora che è stato creato un frame di dati che rappresenta le anomalie per un dispositivo specifico, è possibile visualizzare queste anomalie:

```python
import matplotlib.pyplot as plt
from pyspark.sql.functions import col

adf = df_anomaly_single_device.toPandas()
adf_subset = df_anomaly_single_device.where(col("isAnomaly") == 1).toPandas()

plt.figure(figsize=(23,8))
plt.plot(adf['timestamp'],adf['expectedUpperValue'], color='darkred', linestyle='solid', linewidth=0.25, label='UpperMargin')
plt.plot(adf['timestamp'],adf['expectedValue'], color='darkgreen', linestyle='solid', linewidth=2, label='Expected Value')
plt.plot(adf['timestamp'],adf['measureValue'], 'b', color='royalblue', linestyle='dotted', linewidth=2, label='Actual')
plt.plot(adf['timestamp'],adf['expectedLowerValue'],  color='black', linestyle='solid', linewidth=0.25, label='Lower Margin')
plt.plot(adf_subset['timestamp'],adf_subset['measureValue'], 'ro', label = 'Anomaly')
plt.legend()
plt.title('RPM Anomalies with Confidence Intervals')
plt.show()
```

Se l'operazione ha esito positivo, l'output avrà un aspetto simile al seguente:

![Tracciato anomalie Detector](../media/anomaly-output.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come eseguire la manutenzione predittiva su larga scala con servizi cognitivi di Azure, Azure sinapsi Analytics e Azure CosmosDB. Per altre informazioni, vedere l'esempio completo su [GitHub](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples/tree/master/IoT).
