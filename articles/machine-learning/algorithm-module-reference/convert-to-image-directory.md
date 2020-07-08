---
title: Convert to Image Directory
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Convert to Image directory per convertire il set di dati nel formato di directory image.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 41724753df0d529e4c44344e8e975e68ee5eafd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84904593"
---
# <a name="convert-to-image-directory"></a>Convert to Image Directory

Questo articolo descrive come usare il modulo Convert to Image directory per convertire il set di dati dell'immagine nel tipo di dati ' image directory ', che è formato dati standardizzato nelle attività correlate all'immagine, ad esempio la classificazione delle immagini in Azure Machine Learning Designer (Preview).

## <a name="how-to-use-convert-to-image-directory"></a>Come usare Convert to Image directory  

1.  Aggiungere il modulo **Convert to Image directory** all'esperimento. È possibile trovare questo modulo nella categoria ' Visione artificiale/immagine trasformazione dati ' nell'elenco dei moduli. 

2.  Connettere un set di dati dell'immagine come input. Assicurarsi che sia presente un'immagine nel set di dati di input.
    Sono supportati i formati di set di dati seguenti:

    - File compresso in queste estensioni:'. zip ','. tar ','. gz ','. bz2'.
    - Cartella contenente le immagini. Si **consiglia vivamente di comprimere tale cartella prima di usare il file compresso come set di dati**.

    > [!NOTE]
    > Se usa il set di dati dell'immagine nell'apprendimento supervisionato, l'etichetta è obbligatoria.
    > Per l'attività di classificazione delle immagini, l'etichetta può essere generata come immagine ' Category ' nell'output del modulo se il set di dati dell'immagine è organizzato nel formato torchvision ImageFolder. In caso contrario, solo le immagini vengono salvate senza etichetta. Di seguito è riportato un esempio di come è possibile organizzare il set di dati dell'immagine per ottenere l'etichetta, usare la categoria Image come nome della sottocartella. Per ulteriori informazioni, fare riferimento ai set di dati [torchvision](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) .
    >
    > ```
    > root/dog/xxx.png
    > root/dog/xxy.png
    > root/dog/xxz.png
    >
    > root/cat/123.png
    > root/cat/nsdf3.png
    > root/cat/asd932_.png
    > ```

3.  Inviare la pipeline.

## <a name="results"></a>Risultati

L'output del modulo **Convert to Image directory** è in formato di directory immagine e può essere connesso ad altri moduli correlati alle immagini di cui il formato della porta di input è anche image directory.

## <a name="technical-notes"></a>Note tecniche 

###  <a name="expected-inputs"></a>Input previsti  

| Nome          | Type                  | Descrizione   |
| ------------- | --------------------- | ------------- |
| Set di dati di input | AnyDirectory, ZipFile | Set di dati di input |

###  <a name="output"></a>Output  

| Nome                   | Type           | Descrizione            |
| ---------------------- | -------------- | ---------------------- |
| Directory dell'immagine di output | ImageDirectory | Directory dell'immagine di output |

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
