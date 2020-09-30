---
title: DenseNet
titleSuffix: Azure Machine Learning
description: Informazioni su come creare un modello di classificazione delle immagini usando l'algoritmo DenseNet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 2351012738f4cf5697fb29891c9459e4cc86cd3a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91536631"
---
# <a name="densenet"></a>DenseNet

Questo articolo descrive come usare il modulo **DenseNet** in Azure Machine Learning Designer per creare un modello di classificazione delle immagini usando l'algoritmo DenseNet.  

Questo algoritmo di classificazione è un metodo di apprendimento supervisionato e richiede una directory di immagini con etichetta. 

> [!NOTE]
> Questo modulo non supporta il set di dati con etichetta generato dall' *etichettatura dei dati* in studio, ma supporta solo la directory di immagini con etichetta generata dal modulo [Convert to Image directory](convert-to-image-directory.md) . 

Per eseguire il training del modello, è possibile fornire il modello e la directory di immagini con etichetta come input per il [training del modello Pytorch](train-pytorch-model.md). Il modello con Training può quindi essere usato per stimare i valori per i nuovi esempi di input usando il [modello di immagine del Punteggio](score-image-model.md).

### <a name="more-about-densenet"></a>Altre informazioni su DenseNet

Per altre informazioni su DenseNet, vedere il documento di ricerca relativo alle [reti di convoluzione con connessione densa](https://arxiv.org/abs/1608.06993).

## <a name="how-to-configure-densenet"></a>Come configurare DenseNet

1.  Aggiungere il modulo **DenseNet** alla pipeline nella finestra di progettazione.  

2.  Per **nome modello**specificare il nome di una determinata struttura DenseNet ed è possibile scegliere tra DenseNet supportati:' densenet121',' densenet161',' densenet169',' densenet201'.

3.  Per il **Training**preliminare, specificare se si desidera utilizzare un modello con training preliminare in imagent. Se questa opzione è selezionata, è possibile ottimizzare il modello in base al modello pre-sottoposto a training selezionato; Se deselezionata, è possibile eseguire il training da zero.

4.  Per l' **efficienza della memoria**, specificare se utilizzare il checkpoint, che è molto più efficiente, ma più lento. Per ulteriori informazioni, vedere il documento di ricerca relativo all' [implementazione efficiente della memoria di DenseNets](https://arxiv.org/pdf/1707.06990.pdf).

5.  Connettere l'output del modulo del set di dati del modulo **DenseNet** , del training e dell'immagine di convalida al [modello Train Pytorch](train-pytorch-model.md). 

6. Inviare la pipeline.


## <a name="results"></a>Risultati

Al termine dell'esecuzione della pipeline, per usare il modello per il punteggio, connettere il [modello Train Pytorch](train-pytorch-model.md) al modello di [immagine del Punteggio](score-image-model.md)per stimare i valori per i nuovi esempi di input.

## <a name="technical-notes"></a>Note tecniche  

###  <a name="module-parameters"></a>Parametri del modulo  

| Nome             | Range | Type    | Predefinito     | Descrizione                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| Nome modello       | Qualsiasi   | Modalità    | densenet201 | Nome di una determinata struttura DenseNet     |
| Training preliminare       | Qualsiasi   | Boolean | True        | Indica se utilizzare un modello pre-sottoposto a training in imagent |
| Efficienza della memoria | Qualsiasi   | Boolean | Falso       | Indica se utilizzare il checkpoint, che è molto più efficiente della memoria ma più lento |

###  <a name="output"></a>Output  

| Nome            | Tipo                    | Descrizione                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Untrained model | UntrainedModelDirectory | Modello DenseNet non sottoposto a training che può essere connesso al training del modello Pytorch. |

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
