---
title: Attributi di entità di Academic Graph - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi di entità che è possibile usare con Academic Graph nell'API Academic Knowledge.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: a203fdf6562dabb1b9d6e8ab5bb8f46ff6d5dc27
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902791"
---
# <a name="entity-attributes"></a>Attributi di entità

L'entità Academic Graph è composta da 7 tipi di entità. Tutte le entità avranno un ID entità e un tipo di entità.

## <a name="common-entity-attributes"></a>Attributi di entità comuni
Nome    |Descrizione                |Tipo       | Operazioni
------- | ------------------------- | --------- | ----------------------------
ID      |ID entità                  |Int64      |Uguale a
Ty      |Tipo di entità                |enum   |Uguale a

## <a name="entity-type-enum"></a>Tipo di enumerazione dell'entità
Nome                                                            |valore
----------------------------------------------------------------|-----
[Documento](PaperEntityAttributes.md)                               |0
[Autore](AuthorEntityAttributes.md)                             |1
[Journal](JournalEntityAttributes.md)                           |2
[Serie di conferenze](JournalEntityAttributes.md)                 |3
[Istanza di conferenza](ConferenceInstanceEntityAttributes.md)    |4
[Affiliazione](AffiliationEntityAttributes.md)                   |5
[Campo di studi](FieldsOfStudyEntityAttributes.md)                      |6

