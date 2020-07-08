---
title: ResNet
titleSuffix: Azure Machine Learning
description: Informazioni su come creare un modello di classificazione delle immagini usando l'algoritmo ResNet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: ea8c13e134eceeb27bd064e794d46d711092a867
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84450105"
---
# <a name="resnet"></a>ResNet

Questo articolo descrive come usare il modulo **Resnet** in Azure Machine Learning Designer (anteprima) per creare un modello di classificazione delle immagini usando l'algoritmo ResNet.  

Questo algoritmo di classificazione è un metodo di apprendimento supervisionato e richiede un set di dati con etichetta. Per ulteriori informazioni su come ottenere una directory di immagini con etichetta, vedere il modulo [Convert to Image directory](convert-to-image-directory.md) . È possibile eseguire il training del modello fornendo un modello e una directory di immagini con etichetta come input per il [training del modello Pytorch](train-pytorch-model.md). Il modello con Training può quindi essere usato per stimare i valori per i nuovi esempi di input usando il [modello di immagine del Punteggio](score-image-model.md).

### <a name="more-about-resnet"></a>Altre informazioni su ResNet

Per ulteriori informazioni su ResNet, fare riferimento a [questo documento](https://pytorch.org/docs/stable/torchvision/models.html?highlight=resnext101_32x8d#torchvision.models.resnext101_32x8d) .

## <a name="how-to-configure-resnet"></a>Come configurare ResNet

1.  Aggiungere il modulo **Resnet** alla pipeline nella finestra di progettazione.  

2.  Per **nome modello**specificare il nome di una determinata struttura Resnet ed è possibile scegliere tra Resnet supportati:' resnet18',' resnet34',' resnet50',' resnet101',' resnet152',' resnet152',' resnext50 \_ 32x4d ',' resnext101 \_ 32x8d ',' wide_resnet50 \_ 2',' wide_resnet101 \_ 2'.

3.  Per il **Training**preliminare, specificare se si desidera utilizzare un modello con training preliminare in imagent. Se questa opzione è selezionata, è possibile ottimizzare il modello in base al modello pre-sottoposto a training selezionato; Se deselezionata, è possibile eseguire il training da zero.

4.  Connettere l'output del modulo **DenseNet** Module, Training and Validation image DataSet al [modello Train Pytorch](train-pytorch-model.md). 

5. Inviare la pipeline.

## <a name="results"></a>Risultati

Al termine dell'esecuzione della pipeline, per usare il modello per il punteggio, connettere il [modello Train Pytorch](train-pytorch-model.md) al modello di [immagine del Punteggio](score-image-model.md)per stimare i valori per i nuovi esempi di input.

## <a name="technical-notes"></a>Note tecniche  

###  <a name="module-parameters"></a>Parametri del modulo  

| Nome       | Range | Type    | Predefinito           | Descrizione                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| Nome modello | Qualsiasi   | Mode    | \_32x8d resnext101 | Nome di una determinata struttura ResNet       |
| Training preliminare | Qualsiasi   | Boolean | True              | Indica se utilizzare un modello pre-sottoposto a training in imagent |
|            |       |         |                   |                                          |

###  <a name="output"></a>Output  

| Nome            | Type                    | Description                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Untrained model | UntrainedModelDirectory | Modello ResNet non sottoposto a training che può essere connesso al training del modello Pytorch. |

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 