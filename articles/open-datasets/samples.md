---
title: Jupyter Notebook di esempio con un set di dati aperto NOAA
titleSuffix: Azure Open Datasets
description: I Jupyter Notebook di esempio per Azure Open Datasets illustrano come caricare set di dati aperti e usarli per arricchire i dati demo. Le tecniche includono l'uso di Spark e Pandas per elaborare i dati.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 11/04/2019
ms.openlocfilehash: 2ccdb7adcae40d22a36fbfb47fba6e4c87b3349a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496580"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>I Jupyter Notebook di esempio illustrano come arricchire i dati con i set di dati aperti 
I Jupyter Notebook di esempio per Azure Open Datasets illustrano come caricare set di dati aperti e usarli per arricchire i dati demo. Le tecniche includono l'uso di Apache Spark e Pandas per elaborare i dati.

>[!IMPORTANT]
>Quando si lavora in un ambiente non Spark, Open Datasets consente di scaricare solo un mese di dati alla volta con determinate classi per evitare errori di memoria in caso di set di dati di grandi dimensioni.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>Caricare dati ISD (Integrated Surface Database) NOAA 
|Blocco appunti        | DESCRIZIONE                                    |
|----------------|------------------------------------------------|
|[Caricare un mese di dati meteo recenti in un dataframe Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Informazioni su come caricare dati meteo storici in un dataframe Pandas a scelta. |
|[Caricare un mese di dati meteo recenti in un dataframe Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Informazioni su come caricare dati meteo storici in un dataframe Spark a scelta.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Unire dati demo con dati ISD NOAA 
|Blocco appunti        | DESCRIZIONE                                    |
|----------------|------------------------------------------------|
|[Unire dati demo con dati meteo - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Unire un mese di dati demo di un set di dati di posizioni di sensori con le letture meteo di un dataframe Pandas.  |
|[Unire dati demo con dati meteo - Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Unire un set di dati di posizioni di sensori con le letture meteo di un dataframe Spark. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Unire dati sui taxi di New York con dati ISD NOAA 
|Blocco appunti        | DESCRIZIONE                                    |
|----------------|------------------------------------------------|
|[Dati sulle corse dei taxi arricchiti con dati meteo - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Caricare più di un mese di dati sui taxi verdi di New York e arricchirli con dati meteo in un dataframe Pandas. Questo esempio esegue l'override del metodo `get_pandas_limit` e bilancia le prestazioni del carico di dati con la quantità di dati.|
|[Dati sulle corse dei taxi arricchiti con dati meteo - Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Caricare i dati sui taxi verdi di New York e arricchirli con dati meteo in un dataframe Spark.  |

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Creare un modello di regressione con Machine Learning automatizzato e un set di dati aperto](tutorial-opendatasets-automl.md)
* [Python SDK per Open Datasets](/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)
* [Catalogo di Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/)
