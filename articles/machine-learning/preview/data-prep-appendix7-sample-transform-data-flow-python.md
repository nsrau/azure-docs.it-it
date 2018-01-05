---
title: Esempi di possibili trasformazioni del flusso di dati da trasformare con la preparazione dati di Azure Machine Learning | Microsoft Docs
description: Questo documento offre un set di esempi di possibili trasformazioni del flusso di dati con la preparazione dati di Azure Machine Learning
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
ms.date: 09/11/2017
ms.openlocfilehash: 4a716c1934258e687eb48ecb4077c6be7b269c1f
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="sample-of-custom-data-flow-transforms-python"></a>Esempio di trasformazioni del flusso di dati personalizzate in Python 
Il nome della trasformazione nel menu è **Transform Dataflow (Script)** (Trasforma flusso di dati - Script). Prima di leggere questa appendice, leggere la [panoramica dell'estendibilità di Python](data-prep-python-extensibility-overview.md).

## <a name="transform-frame"></a>Trasformare i frame
### <a name="create-a-new-column-dynamically"></a>Creare una nuova colonna in modo dinamico 
Crea una colonna **(city2)** in modo dinamico e riconcilia più versioni diverse di San Francisco in una sola dalla colonna city esistente.
```python
    df.loc[(df['city'] == 'San Francisco') | (df['city'] == 'SF') | (df['city'] == 'S.F.') | (df['city'] == 'SAN FRANCISCO'), 'city2'] = 'San Francisco'
```

### <a name="add-new-aggregates"></a>Aggiungere nuove aggregazioni
Crea un nuovo frame con la prima e l'ultima aggregazione calcolate per la colonna del punteggio raggruppate in base alla colonna score. Queste vengono raggruppate in base alla colonna **risk_category**.
```python
    df = df.groupby(['risk_category'])['Score'].agg(['first','last'])
```
### <a name="winsorize-a-column"></a>Eseguire la winsorizzazione di una colonna 
Riformula i dati in modo da soddisfare una formula per ridurre gli outlier in una colonna.
```python
    import scipy.stats as stats
    df['Last Order'] = stats.mstats.winsorize(df['Last Order'].values, limits=0.4)
```

## <a name="transform-data-flow"></a>Trasformare il flusso di dati
### <a name="fill-down"></a>Copiare 
Per ricopiare i valori sono necessarie due trasformazioni. Presume dati simili ai seguenti:


|Stato         |city       |
|--------------|-----------|
|Washington    |Redmond    |
|              |Bellevue   |
|              |Issaquah   |
|              |Seattle    |
|California    |Los Angeles|
|              |San Diego  |
|              |San Jose   |
|Texas         |Dallas     |
|              |San Antonio|
|              |Houston    |

Prima creare una trasformazione Add Column (Script) (Aggiungi colonna - Script) che contenga il codice seguente:
```python
    row['State'] if len(row['State']) > 0 else None
```
Creare ora una trasformazione Transform Data Flow (Script) (Trasforma flusso di dati - Script) che contenga il codice seguente:
```python
    df = df.fillna( method='pad')
```

I dati dovrebbero ora apparire così:

|Stato         |newState         |city       |
|--------------|--------------|-----------|
|Washington    |Washington    |Redmond    |
|              |Washington    |Bellevue   |
|              |Washington    |Issaquah   |
|              |Washington    |Seattle    |
|California    |California    |Los Angeles|
|              |California    |San Diego  |
|              |California    |San Jose   |
|Texas         |Texas         |Dallas     |
|              |Texas         |San Antonio|
|              |Texas         |Houston    |


### <a name="min-max-normalization"></a>Min Max normalizzazione
```python
    df["NewCol"] = (df["Col1"]-df["Col1"].mean())/df["Col1"].std()
```