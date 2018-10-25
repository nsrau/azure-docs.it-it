---
title: Attributi dell'entità Conference Series - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi che è possibile usare con l'entità Conference Series.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: f9f28afd7005d7a61aa0d2f4dba69ca598034b52
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900749"
---
# <a name="conference-series-entity"></a>Entità Conference Series

<sub> *Gli attributi seguenti sono specifici dell'entità Conference Series. (Ty = '3') </sub>

Nome    |Descrizione                            |Tipo       | Operazioni
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entità                              |Int64      |Uguale a
CN      |Nome normalizzato della serie di conferenze      |string     |Uguale a
DCN     |Nome visualizzato della serie di conferenze         |string     |Nessuno
CC      |Numero totale delle citazioni della serie di conferenze         |Int32      |Nessuno  
ECC     |Numero totale stimato delle citazioni della serie di conferenze   |Int32      |Nessuno
F.FId   |ID entità del campo di studi associato alla serie di conferenze |Int64  | Uguale a
F.FN    |Nome del campo di studi associato alla serie di conferenze  | Uguale a<br/>StartsWith