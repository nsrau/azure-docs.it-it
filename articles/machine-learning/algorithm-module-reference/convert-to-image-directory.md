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
ms.date: 10/09/2020
ms.openlocfilehash: 2e597299c9b157d79a5317c97550fc30820636d6
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940361"
---
# <a name="convert-to-image-directory"></a>Convert to Image Directory

Questo articolo descrive come usare il modulo Convert to Image directory per convertire il set di dati dell'immagine nel tipo di dati *directory image* , che è formato dati standardizzato nelle attività correlate all'immagine, ad esempio la classificazione delle immagini in Azure Machine Learning Designer.

## <a name="how-to-use-convert-to-image-directory"></a>Come usare Convert to Image directory  

1. Preparare prima il set di dati dell'immagine. 

    Per l'apprendimento supervisionato, è necessario specificare l'etichetta del set di dati di training. Il file del set di dati dell'immagine deve avere la struttura seguente:
    
    ```
    Your_image_folder_name/Category_1/xxx.png
    Your_image_folder_name/Category_1/xxy.jpg
    Your_image_folder_name/Category_1/xxz.jpeg
    
    Your_image_folder_name/Category_2/123.png
    Your_image_folder_name/Category_2/nsdf3.png
    Your_image_folder_name/Category_2/asd932_.png
    ```
    
    Nella cartella del set di dati dell'immagine sono presenti più sottocartelle. Ogni sottocartella contiene rispettivamente immagini di una categoria. I nomi delle sottocartelle vengono considerati come etichette per attività come la classificazione delle immagini. Per ulteriori informazioni, vedere set di dati [torchvision](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) .

    > [!WARNING]
    > I set di dati attualmente etichettati esportati da etichette dati non sono supportati nella finestra di progettazione.

    Sono supportate le immagini con queste estensioni (in lettere minuscole):'. jpg ','. jpeg ','. png ','. ppm ','. bmp ','. PGM ','. TIF ','. TIFF ','. webp '. È anche possibile avere più tipi di immagini in una cartella. Non è necessario che contenga lo stesso numero di immagini in ogni cartella di categoria.

    È possibile usare la cartella o il file compresso con estensione '. zip ','. tar ','. gz ' è. bz2'. **Per ottenere prestazioni migliori, è consigliabile usare file compressi.** 
    
    ![Set di dati di esempio image](./media/module/image-sample-dataset.png)

    Per l'assegnazione dei punteggi, la cartella del set di dati dell'immagine deve contenere solo immagini non classificate.

1. [Registrare il set di dati dell'immagine come set di dati di file](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) nell'area di lavoro, poiché l'input di Convert to Image Directory Module deve essere un **set di dati di file**.

1. Aggiungere il set di dati dell'immagine registrata nell'area di disegno. È possibile trovare il set di dati registrato nella categoria **set** di dati nell'elenco dei moduli a sinistra dell'area di disegno. Attualmente la finestra di progettazione non supporta il set di dati di visualizzazione immagine.

    > [!WARNING]
    > **Non è possibile** usare il modulo **Import Data** per importare il set di dati dell'immagine, perché il tipo di output del modulo **Import Data** è la directory dataframe, che contiene solo la stringa del percorso del file.

1. Aggiungere il modulo **Convert to Image directory** nell'area di disegno. È possibile trovare questo modulo nella categoria ' Visione artificiale/immagine trasformazione dati ' nell'elenco dei moduli. Connetterlo al set di dati image.
    
3.  Inviare la pipeline. Questo modulo può essere eseguito su GPU o CPU.

## <a name="results"></a>Risultati

L'output del modulo **Convert to Image directory** è in formato di **directory immagine** e può essere connesso ad altri moduli correlati alle immagini di cui il formato della porta di input è anche image directory.

![Converti in output directory immagini](./media/module/convert-to-image-directory-output.png)

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
