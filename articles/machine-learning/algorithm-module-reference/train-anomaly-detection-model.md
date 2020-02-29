---
title: 'Modello di rilevamento delle anomalie del training: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Train Anomaly Detection Model per creare un modello di rilevamento delle anomalie con training.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 55c5b89eb23581543d57187ee4d7899d97eafe66
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925956"
---
# <a name="train-anomaly-detection-model"></a>Train Anomaly Detection Model

Questo articolo descrive come usare il modulo **Train Anomaly Detection Model** in Azure Machine Learning Designer (Preview) per creare un modello di rilevamento delle anomalie con training.

Il modulo accetta come input un set di parametri del modello per il modello di rilevamento delle anomalie e un set di dati senza etichetta. Restituisce un modello di rilevamento delle anomalie con training, insieme a un set di etichette per i dati di training.  

Per ulteriori informazioni sugli algoritmi di rilevamento delle anomalie disponibili nella finestra di progettazione, vedere gli argomenti seguenti: 

+ [Rilevamento di anomalie basato su PCA](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Come configurare il modello di rilevamento delle anomalie di training 

1.  Aggiungere il modulo **Train Anomaly Detection Model** alla pipeline nella finestra di progettazione. È possibile trovare questo modulo nella categoria **Rilevamento anomalie** .

2. Connettere uno dei moduli progettati per il rilevamento delle anomalie, ad esempio il [rilevamento di anomalie basato su PCA](pca-based-anomaly-detection.md)

    Altri tipi di modelli non sono supportati. Quando si esegue la pipeline si riceve l'errore: tutti i modelli devono avere lo stesso tipo di apprendimento.  

3.  Configurare il modulo di rilevamento delle anomalie scegliendo la colonna etichetta e impostando altri parametri specifici per l'algoritmo.  

4.  Alleghi un set di dati di training all'input di destra del **modello di rilevamento delle anomalie**del training.  

5.  Eseguire la pipeline.  

## <a name="results"></a>Risultati

Al termine del training:

+ Per visualizzare i parametri del modello, fare clic con il pulsante destro del mouse sul modulo e scegliere **Visualizza**. 

+ Per creare stime, utilizzare [Score Model](score-model.md) con i nuovi dati di input.

+ Per salvare uno snapshot del modello sottoposto a training, selezionare il modulo e fare clic sull'icona **registra set di dati** nella scheda **output + log** nel riquadro di destra.   

 
## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 

Vedere [le eccezioni e i codici di errore per la finestra di progettazione (anteprima)](designer-error-codes.md) per un elenco di errori specifici dei moduli della finestra di progettazione.
