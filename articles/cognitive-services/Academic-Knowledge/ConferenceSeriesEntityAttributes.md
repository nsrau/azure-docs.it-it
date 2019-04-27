---
title: Attributi dell'entità Conference Series - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi che è possibile usare con l'entità Conference Series.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 38b4aa4c899668a68041f042ce6981ddd8c58219
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61340197"
---
# <a name="conference-series-entity"></a>Entità Conference Series

<sub> *Gli attributi seguenti sono specifici dell'entità Conference Series. (Ty = '3') </sub>

NOME    |DESCRIZIONE                            |Type       | Operazioni
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entità                              |Int64      |Uguale a
CN      |Nome normalizzato della serie di conferenze      |string     |Uguale a
DCN     |Nome visualizzato della serie di conferenze         |string     |Nessuno
CC      |Numero totale delle citazioni della serie di conferenze         |Int32      |Nessuno  
ECC     |Numero totale stimato delle citazioni della serie di conferenze   |Int32      |Nessuno
F.FId   |ID entità del campo di studi associato alla serie di conferenze |Int64  | Uguale a
F.FN    |Nome del campo di studi associato alla serie di conferenze  | Uguale a<br/>StartsWith
