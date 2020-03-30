---
title: 'Train Anomalia Modello di rilevamento: Riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come utilizzare il modulo Train Anomaly Detection Model per creare un modello di rilevamento anomalie addestrato.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 78ed2d85729cce94e8dfa579545f558d2cfe4651
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502971"
---
# <a name="train-anomaly-detection-model"></a>Train Anomaly Detection Model

Questo articolo descrive come usare il modulo **Train Anomaly Detection Model** in Azure Machine Learning Designer (anteprima) per creare un modello di rilevamento anomalie sottoposto a training.

Il modulo accetta come input un set di parametri del modello per il modello di rilevamento delle anomalie e un set di dati senza etichetta. Restituisce un modello di rilevamento delle anomalie sottoposte a training, insieme a un set di etichette per i dati di training.  

Per altre informazioni sugli algoritmi di rilevamento delle anomalie forniti nella finestra di progettazione, vedere gli argomenti seguenti:For more information about the anomaly detection algorithms provided in the designer, see these topics: 

+ [Rilevamento anomalie basato su PCA](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Come configurare Train Anomaly Detection Model 

1.  Aggiungere il modulo **Train Anomaly Detection Model** alla pipeline nella finestra di progettazione. È possibile trovare questo modulo nella categoria **Rilevamento anomalie.**

2. Collegare uno dei moduli progettati per il rilevamento delle anomalie, ad esempio il rilevamento anomalie [basato su PCA](pca-based-anomaly-detection.md)

    Altri tipi di modelli non sono supportati; durante l'esecuzione della pipeline si otterrà l'errore: Tutti i modelli devono avere lo stesso tipo di Allievo.  

3.  Configurare il modulo di rilevamento anomalie scegliendo la colonna label e impostando altri parametri specifici per l'algoritmo.  

4.  Collegare un set di dati di training all'input a destra di **Train Anomaly Detection Model**.  

5.  Inviare la pipeline.  

## <a name="results"></a>Risultati

Al termine dell'allenamento:

+ Per visualizzare i parametri del modello, fare clic con il pulsante destro del mouse sul modulo e selezionare **Visualizza**. 

+ Per creare stime, usare Modello di [partitura](score-model.md) con nuovi dati di input.

+ Per salvare un'istantanea del modello sottoposto a training, selezionare il modulo e fare clic sull'icona **Registra set di dati** nella scheda Output e **registri** nel riquadro di destra.   

 
## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 

Per un elenco di errori specifici dei moduli della finestra di progettazione, vedere Eccezioni e codici di errore per la finestra di [progettazione (anteprima).](designer-error-codes.md)
'