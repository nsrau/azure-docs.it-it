---
title: 'Applica trasformazione: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Apply Transformation in Azure Machine Learning per modificare un set di dati di input in base a una trasformazione calcolata in precedenza.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/26/2020
ms.openlocfilehash: a5db3935ae445ee7dcf8129eb1d4c75fcb64302f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003906"
---
# <a name="apply-transformation-module"></a>Modulo Apply Transformation

Questo articolo descrive un modulo in Azure Machine Learning Designer.

Utilizzare questo modulo per modificare un set di dati di input in base a una trasformazione calcolata in precedenza. Questo modulo è necessario in se è necessario aggiornare le trasformazioni nelle pipeline di inferenza.

Se, ad esempio, sono stati usati i punteggi z per normalizzare i dati di training usando il modulo **Normalize data** , è necessario usare il valore del punteggio z calcolato per il training anche durante la fase di assegnazione del punteggio. In Azure Machine Learning, è possibile salvare il metodo di normalizzazione come trasformazione, quindi utilizzare **Applica trasformazione** per applicare il punteggio z ai dati di input prima del punteggio.

## <a name="how-to-save-transformations"></a>Come salvare le trasformazioni

La finestra di progettazione consente di salvare le trasformazioni dei dati come **set** di dati in modo da poterle utilizzare in altre pipeline.

1. Selezionare un modulo di trasformazione dei dati che sia stato eseguito correttamente.

1. Selezionare la scheda **output + log** .

1. Individuare l'output della trasformazione e selezionare il **set di dati Register** per salvarlo come modulo nella categoria **set** di dati nella tavolozza dei moduli.

## <a name="how-to-use-apply-transformation"></a>Come utilizzare Applica trasformazione  
  
1. Aggiungere il modulo **Apply Transformation** alla pipeline. È possibile trovare questo modulo nella sezione **valutazione del punteggio &** della tavolozza dei moduli. 
  
1. Trovare la trasformazione salvata che si vuole usare nei **set di impostazioni** della tavolozza dei moduli.

1. Connettere l'output della trasformazione salvata alla porta di input sinistra del modulo **Apply Transformation** .

    Il set di dati deve avere esattamente lo stesso schema (numero di colonne, nomi di colonna e tipi di dati) come set di dati per cui la trasformazione è stata progettata per la prima volta.  
  
1. Connettere l'output del set di dati del modulo desiderato alla porta di input destra del modulo **Apply Transformation** .
  
1. Per applicare una trasformazione al nuovo set di dati, inviare la pipeline.  

> [!IMPORTANT]
> Per assicurarsi che la trasformazione aggiornata nelle pipeline di training sia possibile anche nelle pipeline di inferenza, è necessario seguire questa procedura ogni volta che viene aggiornata la trasformazione nella pipeline di training:
> 1. Nella pipeline di training registrare l'output della [trasformazione Seleziona colonne](select-columns-transform.md) come set di dati.
> ![Registrare il set di dati dell'output del modulo](media/module/select-columns-transform-register-dataset.png)
> 1. Nella pipeline di inferenza rimuovere il modulo **TD-** Module e sostituirlo con il set di dati registrato nel passaggio precedente.
> ![Sostituisci modulo TD](media/module/replace-tranformation-directory.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 