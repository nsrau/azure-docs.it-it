---
title: Attributi di entità di Academic Graph - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi dell'entità che è possibile usare con Academic Graph nell'API Academic Knowledge.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: f227cc03578adcfbf73fec3ae8941045e8352513
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55183000"
---
# <a name="entity-attributes"></a>Attributi di entità

L'entità Academic Graph è composta da 7 tipi di entità. Tutte le entità avranno un ID entità e un tipo di entità.

## <a name="common-entity-attributes"></a>Attributi di entità comuni
NOME    |DESCRIZIONE                |Type       | Operazioni
------- | ------------------------- | --------- | ----------------------------
ID      |ID entità                  |Int64      |Uguale a
Ty      |Tipo di entità                |enum   |Uguale a

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

