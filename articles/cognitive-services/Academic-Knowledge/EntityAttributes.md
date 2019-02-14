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
ms.openlocfilehash: accc2803895f3892075cdd9877ca98344ab88bd1
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884818"
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

