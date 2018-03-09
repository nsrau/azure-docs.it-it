---
title: Connessione ad Azure Cosmos DB come origine dati in Azure Machine Learning Workbench | Microsoft Docs
description: Questo documento illustra un esempio di come connettersi ad Azure Cosmos DB tramite Azure Machine Learning Workbench
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: d36b394a528dc4bc1b6e0a9e0e5dbde728cbee1b
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="connecting-to-azure-cosmos-db-as-a-data-source"></a>Connessione ad Azure Cosmos DB come origine dati
Questo articolo contiene un esempio di Python che consente di connettersi a Cosmos DB in Azure Machine Learning Workbench.

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>Caricare dati Azure Cosmos DB nella preparazione dati

Creare un nuovo flusso di dati basato su script e quindi usare lo script seguente per caricare i dati da Azure Cosmos DB. 

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

# Initialize the Python DocumentDB client.
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```

## <a name="other-data-source-connections"></a>Altre connessioni all'origine dati
Per altri esempi, vedere [Esempio di connessioni di origine personalizzate](data-prep-appendix8-sample-source-connections-python.md)
