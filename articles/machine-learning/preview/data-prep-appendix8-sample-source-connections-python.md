---
title: Connessioni ai dati di origine aggiuntive di esempio con la preparazione dei dati per Microsoft Azure Machine Learning | Microsoft Docs
description: Questo documento fornisce un set di esempi di connessioni ai dati di origine possibili con la preparazione dei dati di Microsoft Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 550cca100314009f63eec2136e8c65426d8bf07f
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="sample-of-custom-source-connections-python"></a>Esempio di connessioni di origine personalizzate (Python) 
Prima di leggere questa appendice, leggere la [panoramica dell'estendibilità di Python](data-prep-python-extensibility-overview.md)

## <a name="loading-data-from-dataworld"></a>Caricamento dei dati da data.world

### <a name="prerequisites"></a>Prerequisiti

#### <a name="register-yourself-at-dataworld"></a>Registrarsi a data.world
È necessario un token API che è possibile ottenere dal sito Web data.world.

#### <a name="install-dataworld-library"></a>Installare la raccolta data.world

Aprire l'interfaccia della riga di comando di Azure Machine Learning Workbench da _File - >Open command-line interface_ (File - Apri interfaccia della riga di comando).

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

Eseguire quindi `dw configure` nella riga di comando, che richiede il token. Quando si immette il token, viene creata una directory .dw/ nella directory principale e il token viene archiviato qui.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
A questo punto dovrebbe essere possibile importare le raccolte data.world.

#### <a name="load-data-into-data-preparation"></a>Caricare i dati nella preparazione dei dati

Creare un nuovo flusso di dati basato su script e usare lo script seguente per caricare i dati da data.world

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#load the dataset
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset
df = lds.dataframes['simpsons_episodes']

```

## <a name="load-cosmosdb-data-into-data-preparation"></a>Caricare i dati CosmosDB nella preparazione dei dati

Creare un nuovo flusso di dati basato su script e usare lo script seguente per caricare i dati da CosmosDB; è necessario prima installare le raccolte. Vedere il documento di riferimento con collegamento riportato sopra.

```python
import pydocumentdb
import pydocumentdb.document_client as document_client

import pandas as pd

config = { 
    'ENDPOINT': '<Endpoint>',
    'MASTERKEY': '<Key>',
    'DOCUMENTDB_DATABASE': '<DBName>',
    'DOCUMENTDB_COLLECTION': '<collectionname>'
};

# Initialize the Python DocumentDB client
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```

