---
title: Attributi di entità di Academic Graph - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi dell'entità che è possibile usare con Academic Graph nell'API Academic Knowledge.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 596ce35a0d744691edd75d6071d4758691e0031b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705028"
---
# <a name="entity-attributes"></a>Attributi di entità

L'entità Academic Graph è composta da 7 tipi di entità. Tutte le entità avranno un ID entità e un tipo di entità.

## <a name="common-entity-attributes"></a>Attributi di entità comuni
NOME    |Descrizione                |Type       | Operazioni
------- | ------------------------- | --------- | ----------------------------
ID      |ID entità                  |Int64      |Equals
Ty      |Tipo di entità                |enum   |Equals

## <a name="entity-type-enum"></a>Tipo di enumerazione dell'entità
NOME                                                            |value
----------------------------------------------------------------|-----
[Documento](PaperEntityAttributes.md)                               |0
[Autore](AuthorEntityAttributes.md)                             |1
[Journal](JournalEntityAttributes.md)                           |2
[Serie di conferenze](JournalEntityAttributes.md)                 |3
[Istanza di conferenza](ConferenceInstanceEntityAttributes.md)    |4
[Affiliazione](AffiliationEntityAttributes.md)                   |5
[Campo di studi](FieldsOfStudyEntityAttributes.md)                      |6

