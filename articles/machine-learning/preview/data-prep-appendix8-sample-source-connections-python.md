---
title: Connessioni aggiuntive di esempio ai dati di origine con la preparazione dati di Microsoft Azure Machine Learning | Microsoft Docs
description: Questo documento offre un set di esempi di connessioni ai dati di origine possibili con la preparazione dati di Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 66c356d6d42254e7443b645bff3393daca67012b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="sample-of-custom-source-connections-python"></a>Esempio di connessioni di origine personalizzate (Python) 
Prima di leggere questa appendice, leggere la [panoramica dell'estendibilità di Python](data-prep-python-extensibility-overview.md).

## <a name="load-data-from-dataworld"></a>Caricare dati da data.world

### <a name="prerequisites"></a>prerequisiti

#### <a name="register-yourself-at-dataworld"></a>Registrarsi a data.world
È necessario un token API dal sito Web data.world.

#### <a name="install-dataworld-library"></a>Installare la raccolta data.world

Aprire l'interfaccia della riga di comando di Azure Machine Learning Workbench selezionando **File** > **Open command-line interface** (Apri interfaccia della riga di comando).

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

Eseguire quindi `dw configure` nella riga di comando. Verrà richiesto il token. Quando si immette il token, viene creata una directory .dw/ nella directory principale e il token viene archiviato qui.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
A questo punto dovrebbe essere possibile importare le raccolte data.world.

#### <a name="load-data-into-data-preparation"></a>Caricare i dati nella preparazione dei dati

Creare una trasformazione Transform Dataflow (Script) (Trasforma flusso di dati - Script). Usare quindi lo script seguente per caricare i dati da data.world.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```
## <a name="azure-cosmos-db-as-a-data-source-connection"></a>Azure Cosmos DB come connessione all'origine dati
Per un esempio di Azure Cosmos DB come connessione dati, leggere [Caricare Azure Cosmos DB come origine dati](data-prep-load-azure-cosmos-db.md).
