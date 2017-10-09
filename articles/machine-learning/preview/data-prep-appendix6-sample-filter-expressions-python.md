---
title: Espressioni filtro di esempio con la preparazione dei dati di Microsoft Azure Machine Learning | Microsoft Docs
description: Questo documento fornisce un set di esempi di espressioni filtro possibili con la preparazione dei dati di Microsoft Azure Machine Learning
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
ms.openlocfilehash: a80e43f84b518a7c9ce609fbebe34c531e1ab187
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="sample-of-filter-expressions-python"></a>Esempio di espressioni filtro (Python) 
Prima di leggere questa appendice, leggere la [panoramica dell'estendibilità di Python](data-prep-python-extensibility-overview.md)

## <a name="filter-with-equivalence-test"></a>Filtrare con test di equivalenza
Filtrare solo le righe in cui il valore di Col2 (numerico) è maggiore di 4 

```python
    row["Col2"] > 4
```

## <a name="filter-with-multiple-columns"></a>Filtrare con più colonne 
Filtrare solo le righe in cui Col1 contiene il valore "Good" e Col2 contiene il valore 1 (numerico) 
```python
    row["Col1"] == 'Good' and row["Col2"] == 1
```

## <a name="test-filter-against-null"></a>Filtro di test rispetto a null
Filtrare solo le righe dove Col1 è un valore null 
```python
    pd.isnull(row["Col1"])
```

