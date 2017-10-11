---
title: Esempi di possibili trasformazioni del flusso di dati da trasformare con la preparazione dati di Azure Machine Learning | Microsoft Docs
description: Questo documento offre un set di esempi di possibili trasformazioni del flusso di dati da trasformare con la preparazione dati di Azure ML
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
ms.openlocfilehash: f43f65ca89349fc790684e9bd7acd2f19e15abe5
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="sample-of-custom-data-flow-transforms-python"></a>Esempio di trasformazioni del flusso di dati personalizzate in Python 
Il nome di questa trasformazione nel menu è "Transform Dataflow (Script)" (Trasforma flusso di dati - Script). Prima di leggere questa appendice leggere [Panoramica dell'estensibilità di Python](data-prep-python-extensibility-overview.md)

## <a name="transform-frame"></a>Trasformare i frame
### <a name="create-a-new-column-dynamically"></a>Creare una nuova colonna in modo dinamico 
Si crea una colonna (Città2) in modo dinamico e si riconciliano più versioni diverse di San Francisco in una versione della colonna Città esistente.
```python
    df.loc[(df['city'] == 'San Francisco') | (df['city'] == 'SF') | (df['city'] == 'S.F.') | (df['city'] == 'SAN FRANCISCO'), 'city2'] = 'San Francisco'
```

### <a name="add-new-aggregates"></a>Aggiungere nuove aggregazioni
Creare un nuovo frame con le aggregazione iniziali e con quelle più recenti calcolate per la colonna del punteggio raggruppate in base alla colonna risk_category
```python
    df = df.groupby(['risk_category'])['Score'].agg(['first','last'])
```
### <a name="winsorize-a-column"></a>Eseguire la winsorizzazione di una colonna 
Consente di riformulare i dati per soddisfare una formula al fine di ridurre gli outlier in una colonna
```python
    import scipy.stats as stats
    df['Last Order'] = stats.mstats.winsorize(df['Last Order'].values, limits=0.4)
```

## <a name="transform-data-flow"></a>Trasformare il flusso di dati
### <a name="fill-down"></a>Copiare 
Per ricopiare i valori sono necessarie due trasformazioni.
Supponendo che i dati siano simili a quelli mostrati di seguito


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

Prima di tutto creare una trasformazione "Add Column (Script)" (Aggiungi colonna - Script) che contenga il codice seguente
```python
    row['State'] if len(row['State']) > 0 else None
```
Creare ora una trasformazione Transform Data Flow (Script) (Trasforma flusso di dati - Script) che contenga il codice seguente
```python
    df = df.fillna( method='pad')
```

L'area dei dati dovrebbe ora apparire così:

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


