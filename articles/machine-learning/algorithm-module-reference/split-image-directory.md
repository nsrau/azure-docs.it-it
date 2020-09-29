---
title: Split Image Directory
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Split Image directory in Azure Machine Learning Designer (Preview) per dividere le immagini di una directory immagine in due set distinti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: fe5ba25904298fe1a394a4b01d6bdacc72d599c9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448675"
---
# <a name="split-image-directory"></a>Split Image Directory

In questo argomento viene descritto come utilizzare il modulo Split Image directory in Azure Machine Learning Designer, per dividere le immagini di una directory immagine in due set distinti.

Questo modulo è particolarmente utile quando è necessario separare i dati delle immagini in set di training e di testing. 

## <a name="how-to-configure-split-image-directory"></a>Come configurare la directory Split Image

1. Aggiungere il modulo **Split Image directory** alla pipeline. È possibile trovare questo modulo nella categoria ' Visione artificiale/immagine trasformazione dati '.

2. Connetterlo al modulo di cui l'output è image directory.

3. **Frazione di input di immagini nel primo output** per specificare la percentuale di dati da inserire nella suddivisione a sinistra, per impostazione predefinita 0,9. Se il risultato della frazione non è Integer, il modulo usa il numero intero più piccolo vicino.


## <a name="technical-notes"></a>Note tecniche

### <a name="expected-inputs"></a>Input previsti

| Nome                  | Tipo           | Descrizione              |
| --------------------- | -------------- | ------------------------ |
| Directory dell'immagine di input | ImageDirectory | Directory immagine da dividere |

### <a name="module-parameters"></a>Parametri del modulo

| Nome                                   | Tipo  | Range | Facoltativo | Descrizione                            | Predefinito |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| Frazione di immagini nel primo output | Float | 0-1   | Obbligatoria | Frazione di immagini nel primo output | 0.9     |

### <a name="outputs"></a>Output

| Nome                    | Tipo           | Descrizione                              |
| ----------------------- | -------------- | ---------------------------------------- |
| Immagine di output directory1 | ImageDirectory | Directory immagini che contiene le immagini selezionate |
| Immagine di output directory2 | ImageDirectory | Directory immagini che contiene tutte le altre immagini |

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 

