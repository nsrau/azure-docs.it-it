---
title: Attributi dell'entità Academic Graph nell'API Academic Knowledge | Microsoft Docs
description: Informazioni sugli attributi dell'entità che è possibile usare con Academic Graph nell'API Academic Knowledge.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: f771969c3c6f05e5d2c99b1fbf593ae039ccb12c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372692"
---
# <a name="entity-attributes"></a>Attributi di entità

L'entità Academic Graph è composta da 7 tipi di entità. Tutte le entità avranno un ID entità e un tipo di entità.

## <a name="common-entity-attributes"></a>Attributi di entità comuni
NOME    |DESCRIZIONE                |type       | Operazioni
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

