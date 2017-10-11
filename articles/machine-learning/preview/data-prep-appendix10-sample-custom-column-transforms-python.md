---
title: Codice Python di esempio per la derivazione di nuove colonne nella preparazione dei dati di Azure Machine Learning | Microsoft Docs
description: Questo documento fornisce esempi di codice Python per la creazione di nuove colonne nella preparazione dei dati di Azure ML
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
ms.date: 09/12/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 9a6e331e622b007232a62b34c2220d60d1d050ca
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="sample-of-custom-column-transforms-python"></a>Esempio di trasformazioni di colonna personalizzate (Python) 
Il nome di questa trasformazione nel menu è "Aggiungi colonna (Script)"

Prima di leggere questa appendice, leggere la [panoramica dell'estendibilità di Python](data-prep-python-extensibility-overview.md)

## <a name="test-equivalence-and-replace-values"></a>Testare l'equivalenza e sostituire i valori 
Se il valore in Col1 è minore di 4, la nuova colonna deve contenere il valore 1 altrimenti impostare il valore 2 

```python
    1 if row["Col1"] < 4 else 2
```
## <a name="current-date-and-time"></a>Data e ora corrente 

```python
    datetime.datetime.now()
```
## <a name="typecasting"></a>Cast del tipo 
```python
    float(row["Col1"]) / float(row["Col2"] - 1)
```
## <a name="evaluate-for-nullness"></a>Valutare la presenza di null 
Se Col1 contiene un valore null, contrassegnare la nuova colonna come non valida altrimenti contrassegnarla come valida 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>Nuova colonna calcolata 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>Calcolo di periodo 
Numero di secondi trascorsi dal periodo Unix (presupponendo che Col1 sia già una data) 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```






