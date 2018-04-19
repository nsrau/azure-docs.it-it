---
title: Espressioni filtro di esempio con la preparazione dei dati di Microsoft Azure Machine Learning | Microsoft Docs
description: Questo documento fornisce un set di esempi di espressioni filtro possibili con la preparazione dei dati di Microsoft Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 973c56b8b2821c8e3d63161e6a233243639c74f4
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="sample-of-filter-expressions-python"></a>Esempio di espressioni filtro (Python) 
Prima di leggere questa appendice, leggere la [panoramica dell'estendibilità di Python](data-prep-python-extensibility-overview.md).

## <a name="filter-with-equivalence-test"></a>Filtrare con test di equivalenza
Filtrare solo le righe in cui il valore di Col2 (numerico) è maggiore di 4. 

```python
    row["Col2"] > 4
```

## <a name="filter-with-multiple-columns"></a>Filtrare con più colonne 
Filtrare solo le righe in cui Col1 contiene il valore **Good** e Col2 contiene il valore 1 (numerico). 
```python
    row["Col1"] == 'Good' and row["Col2"] == 1
```

## <a name="test-filter-against-null"></a>Filtro di test rispetto a null
Filtrare solo le righe dove Col1 è un valore Null. 
```python
    pd.isnull(row["Col1"])
```
