---
title: 'Ricetta: esplorazione artistica intelligente con servizi cognitivi per Big Data'
titleSuffix: Azure Cognitive Services
description: Questa ricetta Mostra come creare un database artistico ricercabile usando ricerca di Azure e MMLSpark.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 5a65ff28a38e42e05844063a330c0325f16b2247
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363290"
---
# <a name="recipe-intelligent-art-exploration-with-the-cognitive-services-for-big-data"></a>Ricetta: esplorazione artistica intelligente con servizi cognitivi per Big Data

In questo esempio verranno usati i servizi cognitivi per Big data per aggiungere annotazioni intelligenti alla raccolta Open Access dal Metropolitan Museum of Art (MET). Questo consentirà di creare un motore di ricerca intelligente usando ricerca di Azure anche senza annotazioni manuali. 

## <a name="prerequisites"></a>Prerequisiti

* È necessario disporre di una chiave di sottoscrizione per Visione artificiale e ricerca cognitiva. Seguire le istruzioni in [creare un account servizi cognitivi](../../cognitive-services-apis-create-account.md) per sottoscrivere visione artificiale e ottenere la chiave.
  > [!NOTE]
  > Per informazioni sui prezzi, vedere [ricerca cognitiva di Azure](https://azure.microsoft.com/services/search/#pricing).

## <a name="import-libraries"></a>Importa librerie

Eseguire il comando seguente per importare le librerie per questa ricetta.

```python
import os, sys, time, json, requests
from pyspark.ml import Transformer, Estimator, Pipeline
from pyspark.ml.feature import SQLTransformer
from pyspark.sql.functions import lit, udf, col, split
```

## <a name="set-up-subscription-keys"></a>Configurare le chiavi di sottoscrizione

Eseguire il comando seguente per impostare le variabili per le chiavi del servizio. Inserire le chiavi di sottoscrizione per Visione artificiale e ricerca cognitiva di Azure.

```python
VISION_API_KEY = 'INSERT_COMPUTER_VISION_SUBSCRIPTION_KEY'
AZURE_SEARCH_KEY = 'INSERT_AZURE_COGNITIVE_SEARCH_SUBSCRIPTION_KEY'
search_service = "mmlspark-azure-search"
search_index = "test"
```

## <a name="read-the-data"></a>Leggere i dati

Eseguire il comando seguente per caricare i dati dalla raccolta di apertura accessi SODDISFATTa.

```python
data = spark.read\
  .format("csv")\
  .option("header", True)\
  .load("wasbs://publicwasb@mmlspark.blob.core.windows.net/metartworks_sample.csv")\
  .withColumn("searchAction", lit("upload"))\
  .withColumn("Neighbors", split(col("Neighbors"), ",").cast("array<string>"))\
  .withColumn("Tags", split(col("Tags"), ",").cast("array<string>"))\
  .limit(25)
```

<a name="AnalyzeImages"></a>

## <a name="analyze-the-images"></a>Analizzare le immagini

Eseguire il comando seguente per usare Visione artificiale nella raccolta di opere di accesso aperte SODDISFATTe. Di conseguenza, si otterranno le funzionalità visive dalle illustrazioni.

```python
from mmlspark.cognitive import AnalyzeImage
from mmlspark.stages import SelectColumns

#define pipeline
describeImage = (AnalyzeImage()
  .setSubscriptionKey(VISION_API_KEY)
  .setLocation("eastus")
  .setImageUrlCol("PrimaryImageUrl")
  .setOutputCol("RawImageDescription")
  .setErrorCol("Errors")
  .setVisualFeatures(["Categories", "Tags", "Description", "Faces", "ImageType", "Color", "Adult"])
  .setConcurrency(5))

df2 = describeImage.transform(data)\
  .select("*", "RawImageDescription.*").drop("Errors", "RawImageDescription")
```

<a name="CreateSearchIndex"></a>

## <a name="create-the-search-index"></a>Creare l'indice di ricerca

Eseguire il comando seguente per scrivere i risultati in ricerca di Azure per creare un motore di ricerca delle illustrazioni con i metadati arricchiti da Visione artificiale.

```python
from mmlspark.cognitive import *
df2.writeToAzureSearch(
  subscriptionKey=AZURE_SEARCH_KEY,
  actionCol="searchAction",
  serviceName=search_service,
  indexName=search_index,
  keyCol="ObjectID"
)
```

## <a name="query-the-search-index"></a>Eseguire una query sull'indice di ricerca

Eseguire il comando seguente per eseguire una query sull'indice di ricerca di Azure.

```python
url = 'https://{}.search.windows.net/indexes/{}/docs/search?api-version=2019-05-06'.format(search_service, search_index)
requests.post(url, json={"search": "Glass"}, headers = {"api-key": AZURE_SEARCH_KEY}).json()
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare [Servizi cognitivi per Big data per il rilevamento delle anomalie](anomaly-detection.md).