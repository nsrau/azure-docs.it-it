---
title: Metodo Similarity - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Usare il metodo Similarity per calcolare la somiglianza teorica di due stringhe.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 01/18/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 855d29d2c55b841bbbe4e9eadce8c29ad85fad90
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704859"
---
# <a name="similarity-method"></a>Metodo Similarity

L'API REST **Similarity** viene usata per calcolare la similarità accademica tra due stringhe. 
<br>

**Endpoint REST:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>Parametri richiesta

Parametro        |Tipo di dati      |Obbligatoria | Descrizione
----------|----------|----------|------------
**s1**        |String   |Yes  |Stringa* da confrontare
**s2**        |String   |Sì  |Stringa* da confrontare

<sub> *Le stringhe da confrontare possono avere una lunghezza massima pari a 1 MB. </sub>
<br>

## <a name="response"></a>Risposta

Name | Descrizione
--------|---------
**SimilarityScore**        |Valore a virgola mobile che rappresenta la similarità del coseno di s1 e s2. I valori più vicini a 1,0 indicano maggiore similarità e i valori più vicini a -1,0 indicano minore similarità

<br>

## <a name="successerror-conditions"></a>Condizioni di riuscita/errore

Stato HTTP | `Reason` | Risposta
-----------|----------|--------
**200**         |Riuscito | Numero a virgola mobile
**400**         | Richiesta non valida | Messaggio di errore      
**500**         |Errore interno del server | Messaggio di errore
**Timeout**     | Timeout della richiesta.  | Messaggio di errore

<br>

## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Esempio: calcolare la similarità di due abstract parziali
#### <a name="request"></a>Richiesta:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
In questo esempio, viene generato il punteggio di similarità tra due abstract parziali tramite l'API **Similarity**.
#### <a name="response"></a>Risposta:
```
0.520
```
#### <a name="remarks"></a>Osservazioni:
Il punteggio di similarità viene determinato dalla valutazione dei concetti accademici tramite la rappresentazione distribuita delle parole. In questo esempio, 0,52 significa che i due abstract parziali sono abbastanza simili.
<br>
