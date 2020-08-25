---
title: Sintassi di query di ricerca Graph
titleSuffix: Azure Machine Learning
description: Informazioni su come usare la sintassi di query di ricerca per cercare i nodi in in Graph della pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 8/24/2020
ms.openlocfilehash: 122da78206ef2055b4867727b174293e74133c05
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817553"
---
# <a name="graph-search-query-syntax"></a>Sintassi di query di ricerca Graph

In questo articolo vengono fornite informazioni sulla sintassi di query di ricerca Graph in Azure Machine Learning. La funzionalità di ricerca Graph consente di cercare un nodo in base al nome e alle proprietà. 

 ![Schermata animata che mostra un'esperienza di ricerca di grafici di esempio](media/search/graph-search.gif)

La ricerca Graph supporta la ricerca di parole chiave full-text sul nome e sui commenti del nodo. È anche possibile filtrare sulla proprietà del nodo, ad esempio runStatus, Duration, computeTarget. La ricerca di parole chiave è basata sulla query Lucene. Una query di ricerca completa ha un aspetto simile al seguente:  

**[query Lucene | [query di filtro]** 

È possibile usare una query Lucene o un filtro. Per usare entrambi, usare il **|** separatore. La sintassi della query di filtro è più restrittiva della query Lucene. Quindi, se l'input del cliente può essere analizzato come entrambi, viene applicata la query del filtro.

 

## <a name="lucene-query"></a>Query Lucene

La ricerca Graph usa la query semplice Lucene come sintassi della ricerca full-text nel nodo "Name" e "comment". Sono supportati gli operatori Lucene seguenti:

 
- AND/OR
- Corrispondenza con caratteri jolly con **?** **\*** operatori e.

### <a name="examples"></a>Esempi

- Ricerca semplice: `JSON Data`

- E/O: `JSON AND Validation`

- Più e/o: `(JSON AND Validation) OR (TSV AND Training)`

 
- Corrispondenza con caratteri jolly: 
    - `machi?e learning`
    -   `mach*ing`
 
>[!NOTE]
> Non è possibile avviare una query Lucene con un carattere "*".

##  <a name="filter-query"></a>Filtra query

 
Le query di filtro usano il modello seguente:
 
    **[key1] [operator1] [value1]; [key2] [operator1] [value2];**

 
È possibile utilizzare le seguenti proprietà del nodo come chiavi:

- runStatus
- compute
- duration
- riutilizzare

E usare gli operatori seguenti:

- Maggiore o uguale a: `>=`
- Minore o uguale a: `<=`
- Maggiore `>`
- Meno `<`
- Uguale `==`
- Contenere `=`
- NotEqual `!=`
- In `in`

 
 

### <a name="example"></a>Esempio

- durata > 100;
- stato in {failed, NotStarted}
- calcolo in {GPU-cluster}; runStatus in {Completed}

## <a name="technical-notes"></a>Note tecniche

- La relazione tra più filtri è "e"
- Se `>=,  >,  <, or  <=` si sceglie, i valori verranno convertiti automaticamente in tipo di numero. In caso contrario, vengono utilizzati i tipi stringa per il confronto.
- Per tutti i valori di tipo stringa, case è insensitive nel confronto.
- L'operatore "in" prevede una raccolta come valore. la sintassi della raccolta è `{name1, name2, name3}`
- Lo spazio verrà ignorato tra le parole chiave