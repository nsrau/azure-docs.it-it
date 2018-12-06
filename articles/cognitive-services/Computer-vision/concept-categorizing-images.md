---
title: Classificazione delle immagini - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi alla classificazione delle immagini tramite l'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 7062d98d40c15f4e9e873038fc12fc1b104c996d
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333480"
---
# <a name="categorizing-images"></a>Classificazione delle immagini

Oltre ai tag e alle descrizioni, Visione artificiale restituisce le categorie basate su tassonomia definite nelle versioni precedenti. Tali categorie sono organizzate in una tassonomia con gerarchie ereditarie padre-figlio. Tutte le categorie sono in inglese e possono essere usate singolarmente o con i nuovi modelli di assegnazione di tag.

## <a name="the-86-category-concept"></a>Categoria basata su 86 concetti

In base all'elenco di 86 concetti illustrati nel diagramma seguente, un'immagine può essere classificata a partire da un concetto ampio fino ad arrivare a uno più specifico. Per la classificazione completa in formato testo, vedere [Category Taxonomy](category-taxonomy.md) (Tassonomia delle categorie).

![elenchi raggruppati di tutte le categorie nella tassonomia delle categorie](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Esempi di classificazione delle immagini

La risposta JSON seguente illustra le informazioni restituite da Visione artificiale quando esegue la classificazione dell'immagine di esempio in base alle caratteristiche visive rilevate.

![Donna su un tetto](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

La tabella seguente illustra una serie di immagini tipiche e la categoria restituita da Visione artificiale per ogni immagine.

| Image | Categoria |
|-------|----------|
| ![Foto di famiglia](./Images/family_photo.png) | people_group |
| ![Cucciolo di cane](./Images/cute_dog.png) | animal_dog |
| ![Panorama montano](./Images/mountain_vista.png) | outdoor_mountain |
| ![Pane - Analisi visione](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>Passaggi successivi

Concetti relativi all'[assegnazione di tag alle immagini](concept-tagging-images.md) e alla [descrizione delle immagini](concept-describing-images.md).