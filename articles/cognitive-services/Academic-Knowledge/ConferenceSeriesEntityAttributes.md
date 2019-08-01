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
ROBOTS: NOINDEX
ms.openlocfilehash: feed324202f6a75ceb7e9089875b899c51cd8ae6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705035"
---
# <a name="conference-series-entity"></a>Entità Conference Series

<sub> *Gli attributi seguenti sono specifici dell'entità Conference Series. (Ty = '3') </sub>

Name    |Descrizione                            |Type       | Operazioni
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entità                              |Int64      |Equals
CN      |Nome normalizzato della serie di conferenze      |String     |Equals
DCN     |Nome visualizzato della serie di conferenze         |String     |none
Cc      |Numero totale delle citazioni della serie di conferenze         |Int32      |none  
ECC     |Numero totale stimato delle citazioni della serie di conferenze   |Int32      |none
F.FId   |ID entità del campo di studi associato alla serie di conferenze |Int64  | Equals
F.FN    |Nome del campo di studi associato alla serie di conferenze  | Uguale a<br/>StartsWith
