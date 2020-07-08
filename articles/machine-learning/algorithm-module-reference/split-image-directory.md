---
title: Split Image Directory
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Score Image Model in Azure Machine Learning per generare stime usando un modello di immagine con training.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 9673b3260425fd9244c635beaf77d367a14cac54
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84809918"
---
# <a name="split-image-directory"></a>Split Image Directory

In questo argomento viene descritto come utilizzare il modulo Split Image directory in Azure Machine Learning Designer (Preview) per dividere le immagini di una directory immagine in due set distinti.

Questo modulo è particolarmente utile quando è necessario separare i dati delle immagini in set di training e di testing. 

## <a name="how-to-configure-split-image-directory"></a>Come configurare la directory Split Image

1. Aggiungere il modulo **Split Image directory** alla pipeline. È possibile trovare questo modulo nella categoria ' Visione artificiale/immagine trasformazione dati '.

2. Connetterlo al modulo di cui l'output è image directory.

3. **Frazione di input di immagini nel primo output** per specificare la percentuale di dati da inserire nella suddivisione a sinistra, per impostazione predefinita 0,9.


## <a name="technical-notes"></a>Note tecniche

### <a name="expected-inputs"></a>Input previsti

| Nome                  | Type           | Description              |
| --------------------- | -------------- | ------------------------ |
| Directory dell'immagine di input | ImageDirectory | Directory immagine da dividere |

### <a name="module-parameters"></a>Parametri del modulo

| Nome                                   | Type  | Range | Facoltativo | Descrizione                            | Predefinito |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| Frazione di immagini nel primo output | Float | 0-1   | Necessario | Frazione di immagini nel primo output | 0.9     |

### <a name="outputs"></a>Output

| Nome                    | Type           | Description                              |
| ----------------------- | -------------- | ---------------------------------------- |
| Immagine di output directory1 | ImageDirectory | Directory immagini che contiene le immagini selezionate |
| Immagine di output directory2 | ImageDirectory | Directory immagini che contiene tutte le altre immagini |

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 

