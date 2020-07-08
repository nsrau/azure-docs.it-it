---
title: Apply Image Transformation
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Apply Image Transformation per applicare una trasformazione immagine a una directory image.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 7ff135911742c49c2c52ce30d1dca00bc89b3a56
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84450095"
---
# <a name="apply-image-transformation"></a>Apply Image Transformation 

Questo articolo descrive come usare il modulo Apply Image Transformation in Azure Machine Learning Designer (Preview) per modificare la directory di un'immagine di input in base a una trasformazione immagine precedentemente specificata.  

È necessario connettere un modulo [init Image Transformation](init-image-transformation.md) per specificare la trasformazione, quindi è possibile applicare tale trasformazione alla directory di input Image del modulo Apply Image Transformation.

## <a name="how-to-use-apply-image-transformation"></a>Come usare la trasformazione applica immagine  

1. Aggiungere il modulo **Apply Image Transformation** alla pipeline. È possibile trovare questo modulo in *visione artificiale/immagine trasformazione dati* categoria. 

2. Connettere l'output della **trasformazione dell'immagine init** all'input di sinistra della **trasformazione applica immagine**.

     > [!NOTE]
     > Solo la trasformazione immagine generata dal modulo [init Image Transformation](init-image-transformation.md) è accettata in questo modulo. Per un altro tipo di trasformazione, connetterlo per [applicare la trasformazione](apply-transformation.md). in caso contrario, verrà generata l'eccezione ' InvalidTransformationDirectoryError '.


3. Connettere la directory dell'immagine che si desidera trasformare.

4. Per **mode**specificare per quale scopo usare la trasformazione input:' for Training ' o ' for inferenza '. 

   Se si seleziona **per il training**, verrà applicata tutta la trasformazione specificata nella trasformazione immagine init.

   Se si seleziona **per l'inferenza, la**trasformazione come la creazione di nuovi campioni viene esclusa in modo casuale prima di essere applicata. Ciò è dovuto al fatto che le operazioni di trasformazione per creare nuovi esempi in modo casuale come ' capovolgimento orizzontale casuale ' vengono usate per l'aumento dei dati nel training, che dovrebbe essere rimosso in inferenza perché gli esempi di inferenza devono essere corretti per una stima e una valutazione accurate.

   > [!NOTE]
   > Le trasformazioni che verranno escluse in modalità **per l'inferenza** sono: ritaglio casuale ridimensionato, ritaglio casuale, Flip orizzontale casuale, flip verticale casuale, rotazione casuale, affinità casuale, scala di grigi casuale, prospettiva casuale, cancellazione casuale.

5. Per applicare una trasformazione immagine a una nuova directory di immagini, inviare la pipeline.  

### <a name="module-parameters"></a>Parametri del modulo

| Nome | Range | Type | Predefinito                   | Descrizione                              |
| ---- | ----- | ---- | ------------------------- | ---------------------------------------- |
| Mode | Qualsiasi   | Mode | (Richiedere all'utente di specificare) | Per quale scopo si usa la trasformazione input. È necessario escludere le operazioni di trasformazione ' Random ' nell'inferenza ma mantenerle nel training |

### <a name="expected-inputs"></a>Input previsti  

| Nome                       | Type                    | Description                       |
| -------------------------- | ----------------------- | --------------------------------- |
| Trasformazione immagine di input | TransformationDirectory | Trasformazione immagine di input        |
| Directory dell'immagine di input      | ImageDirectory          | Directory dell'immagine da trasformare |

### <a name="outputs"></a>Output  

| Nome                   | Type           | Description            |
| ---------------------- | -------------- | ---------------------- |
| Directory dell'immagine di output | ImageDirectory | Directory dell'immagine di output |

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
