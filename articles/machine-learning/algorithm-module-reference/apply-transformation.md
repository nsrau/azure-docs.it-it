---
title: 'Applica trasformazione: riferimento modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Applica trasformazione in Azure Machine Learning per modificare un set di dati di input in base a una trasformazione calcolata in precedenza.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/05/2020
ms.openlocfilehash: ccf9d0c3eef50c7dfd838f1929e52506e8984879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78395256"
---
# <a name="apply-transformation-module"></a>Modulo Apply Transformation

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare questo modulo per modificare un set di dati di input in base a una trasformazione calcolata in precedenza.

Ad esempio, se sono stati usati i punteggi z per normalizzare i dati di training usando il modulo **Normalizza dati,** è consigliabile usare il valore del punteggio z calcolato per il training anche durante la fase di assegnazione del punteggio. In Azure Machine Learning è possibile salvare il metodo di normalizzazione come trasformazione e quindi usare **Applica trasformazione** per applicare il punteggio z ai dati di input prima del punteggio.

## <a name="how-to-save-transformations"></a>Come salvare le trasformazioni

La finestra di progettazione consente di salvare le trasformazioni dei dati come set di **dati** in modo da poterle usare in altre pipeline.

1. Selezionare un modulo di trasformazione dati che è stato eseguito correttamente.

1. Selezionare la scheda **Output e registri.**

1. Selezionare **l'icona Salva** per salvare la **trasformazione Risultato**.

## <a name="how-to-use-apply-transformation"></a>Come usare Applica trasformazioneHow to use Apply Transformation  
  
1. Aggiungere il modulo **Applica trasformazione** alla pipeline. Questo modulo è riportato nella sezione **Punteggio modello & Valutazione** della tavolozza dei moduli. 
  
1. Individuare la trasformazione salvata che si desidera utilizzare in Set di **dati** > **Set di** dati personali nella tavolozza dei moduli.

1. Connettere l'output della trasformazione salvata alla porta di input sinistra del modulo **Applica trasformazione.**

    Il set di dati deve avere esattamente lo stesso schema (numero di colonne, nomi di colonna, tipi di dati) del set di dati per il quale la trasformazione è stata progettata per la prima volta.  
  
1. Connettere l'output del set di dati del modulo desiderato alla porta di input corretta del modulo **Applica trasformazione.**
  
1. Per applicare una trasformazione al nuovo set di dati, eseguire la pipeline.  

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 