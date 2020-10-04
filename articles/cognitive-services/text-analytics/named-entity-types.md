---
title: Categorie supportate per il riconoscimento delle entità denominate
titleSuffix: Azure Cognitive Services
description: Informazioni sulle categorie di entità supportate nel API Analisi del testo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 77b75b1134bbc8366478b1f9f4d14e86e9684f70
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2020
ms.locfileid: "91709543"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Categorie di entità supportate nella API Analisi del testo V3

Usare questo articolo per trovare le categorie di entità che possono essere restituite dal [riconoscimento delle entità denominate](how-tos/text-analytics-how-to-entity-linking.md) (ner). È disponibile anche un'anteprima di NER v 3.1, che include la possibilità di rilevare informazioni personali ( `PII` ) e di integrità ( `PHI` ). Inoltre, fare clic sulla scheda **integrità** per visualizzare un elenco di categorie supportate in analisi del testo per l'integrità.

## <a name="entity-categories"></a>Categorie di entità

#### <a name="general"></a>[Generale](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[PII](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Stato](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>Passaggi successivi

* [Come usare il riconoscimento delle entità denominate in Analisi del testo](how-tos/text-analytics-how-to-entity-linking.md)
