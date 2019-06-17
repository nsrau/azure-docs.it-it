---
title: Formato dei dati - API Knowledge Exploration Service
titlesuffix: Azure Cognitive Services
description: Informazioni sul formato dei dati nell'API Knowledge Exploration Service (KES).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: bba257c61509d862bb3161625750f05a86af8770
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60815098"
---
# <a name="data-format"></a>Formato dati

Il file di dati descrive l'elenco di oggetti da indicizzare.
Ogni riga nel file specifica i valori attributo di un oggetto in [formato JSON](https://json.org/) con la codifica UTF-8.
Oltre agli attributi definiti nello [schema](SchemaFormat.md), ogni oggetto ha un attributo "logprob" facoltativo che specifica la probabilità logaritmica relativa tra gli oggetti.
Quando il servizio restituisce gli oggetti in ordine di probabilità decrescente, è possibile usare "logprob" per indicare l'ordine in cui vengono restituiti gli oggetti corrispondenti.
Dato un valore di probabilità *p* compreso tra 0 e 1, la probabilità logaritmica corrispondente può essere calcolata come log(*p*), dove log() è la funzione logaritmo naturale.
Se non si specifica alcun valore per logprob, viene usato il valore predefinito di 0.

```json
{"logprob":-5.3, "Title":"latent dirichlet allocation", "Year":2003, "Author":{"Name":"david m blei", "Affiliation":"uc berkeley"}, "Author":{"Name":"andrew y ng", "Affiliation":"stanford"}, "Author":{"Name":"michael i jordan", "Affiliation":"uc berkeley"}}
{"logprob":-6.1, "Title":"probabilistic latent semantic indexing", "Year":1999, "Author":{"Name":"thomas hofmann", "Affiliation":"uc berkeley"}}
...
```

Per gli attributi String, GUID e BLOB, il valore viene rappresentato come una stringa JSON tra virgolette.  Per gli attributi numerici (Int32, Int64, Double), il valore viene rappresentato come un numero JSON.  Per gli attributi compositi, il valore è un oggetto JSON che specifica i valori degli attributi secondari.  Per compilazioni di indici più veloci, preordinare gli oggetti riducendo la probabilità logaritmica.

In generale, un attributo può avere 0 o più valori.  Se un attributo non ha valori, viene eliminato dall'oggetto JSON.  Se un attributo ha 2 o più valori, è possibile ripetere la coppia attributo-valore nell'oggetto JSON.  In alternativa, è possibile assegnare una matrice JSON che contiene i valori dell'attributo.

```json
{"logprob":0, "Title":"0 keyword"}
{"logprob":0, "Title":"1 keyword", "Keyword":"foo"}
{"logprob":0, "Title":"2 keywords", "Keyword":"foo", "Keyword":"bar"}
{"logprob":0, "Title":"2 keywords (alt)", "Keyword":["foo", "bar"]}
```
