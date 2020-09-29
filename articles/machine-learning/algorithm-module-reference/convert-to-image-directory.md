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
ms.date: 09/28/2020
ms.openlocfilehash: 9f5f4b2b069ebc65430fba4bc31a9891ed61fedf
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450106"
---
# <a name="convert-to-image-directory"></a>Convert to Image Directory

Questo articolo descrive come usare il modulo Convert to Image directory per convertire il set di dati dell'immagine nel tipo di dati ' image directory ', che è formato dati standardizzato nelle attività correlate all'immagine, ad esempio la classificazione delle immagini in Azure Machine Learning Designer.

## <a name="how-to-use-convert-to-image-directory"></a>Come usare Convert to Image directory  

1.  Aggiungere il modulo **Convert to Image directory** nell'area di disegno. È possibile trovare questo modulo nella categoria ' Visione artificiale/immagine trasformazione dati ' nell'elenco dei moduli. 

2.  L'input del modulo **Convert to Image directory** deve essere un set di dati di file. [Registrare un set di dati dell'immagine](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) e connetterlo alla porta di input del modulo. Assicurarsi che sia presente un'immagine nel set di dati di input. Attualmente la finestra di progettazione non supporta il set di dati di visualizzazione immagine.
 
    Sono supportati i formati di set di dati seguenti:

    - File compresso in queste estensioni:'. zip ','. tar ','. gz ','. bz2'.
    - Cartella contenente le immagini. Si **consiglia vivamente di comprimere tale cartella prima di usare il file compresso come set di dati**.

    > [!WARNING]
    > **Non è possibile** usare il modulo **Import Data** per importare il set di dati dell'immagine, perché il tipo di output del modulo **Import Data** è la directory dataframe, che contiene solo la stringa del percorso del file.
    

    > [!NOTE]
    > - Se si usa il set di dati dell'immagine nell'apprendimento supervisionato, è necessario specificare l'etichetta del set di dati di training.
    > - Per l'attività di classificazione delle immagini, l'etichetta può essere generata come immagine ' Category ' nell'output del modulo se il set di dati dell'immagine è organizzato nel formato torchvision ImageFolder. In caso contrario, solo le immagini vengono salvate senza etichetta. Di seguito è riportato un esempio di come è possibile organizzare il set di dati dell'immagine per ottenere l'etichetta, usare la categoria Image come nome della sottocartella. 
    > - Non è necessario caricare lo stesso numero di immagini in ogni cartella di categoria.
    > - Sono supportate le immagini con queste estensioni (in lettere minuscole):'. jpg ','. jpeg ','. png ','. ppm ','. bmp ','. PGM ','. TIF ','. TIFF ','. webp '. È anche possibile avere più tipi di immagini in una cartella.    
    > - Per ulteriori informazioni, fare riferimento ai set di dati [torchvision](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) .
    >
    > ```
    > Your_image_folder_name/Category_1/xxx.png
    > Your_image_folder_name/Category_1/xxy.jpg
    > Your_image_folder_name/Category_1/xxz.jpeg
    >
    > Your_image_folder_name/Category_2/123.png
    > Your_image_folder_name/Category_2/nsdf3.png
    > Your_image_folder_name/Category_2/asd932_.png
    > ```
    > - Se usa il set di dati dell'immagine per il punteggio, il set di dati del file di input di questo modulo deve contenere immagini non classificate.
    
3.  Inviare la pipeline. Questo modulo può essere eseguito su GPU o CPU.

## <a name="results"></a>Risultati

L'output del modulo **Convert to Image directory** è in formato di directory immagine e può essere connesso ad altri moduli correlati alle immagini di cui il formato della porta di input è anche image directory.

## <a name="technical-notes"></a>Note tecniche 

###  <a name="expected-inputs"></a>Input previsti  

| Nome          | Tipo                  | Descrizione   |
| ------------- | --------------------- | ------------- |
| Set di dati di input | AnyDirectory, ZipFile | Set di dati di input |

###  <a name="output"></a>Output  

| Nome                   | Tipo           | Descrizione            |
| ---------------------- | -------------- | ---------------------- |
| Directory dell'immagine di output | ImageDirectory | Directory dell'immagine di output |

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
