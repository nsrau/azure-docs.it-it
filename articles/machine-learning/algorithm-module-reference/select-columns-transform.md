---
title: 'Trasformazione di selezioni colonne: riferimento moduloSelect Columns Transform: Module reference'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Trasformazione di colonne selezionate in Azure Machine Learning per creare una trasformazione che seleziona lo stesso sottoinsieme di colonne del set di dati specificato.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5264c14294f84858cd489f5892b8cdd19e117d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455911"
---
# <a name="select-columns-transform"></a>Select Columns Transform

Questo articolo descrive come usare il modulo Trasformazione seleziona colonne in Progettazione Azure Machine Learning (anteprima). Lo scopo del modulo Seleziona trasformazione colonne è garantire che nelle operazioni di apprendimento automatico a valle venga usato un set di colonne prevedibile e coerente.

Questo modulo è utile per attività come il punteggio, che richiedono colonne specifiche. Le modifiche nelle colonne disponibili potrebbero interrompere la pipeline o modificare i risultati.

Utilizzare Seleziona trasformazione colonne per creare e salvare un set di colonne. Quindi, utilizzare il modulo [Applica trasformazione](apply-transformation.md) per applicare tali selezioni ai nuovi dati.

## <a name="how-to-use-select-columns-transform"></a>Come utilizzare Seleziona trasformazione colonne

In questo scenario si presuppone che si desideri utilizzare la selezione delle funzionalità per generare un set dinamico di colonne che verranno utilizzate per il training di un modello. Per assicurarsi che le selezioni di colonna siano le stesse per il processo di assegnazione del punteggio, utilizzare il modulo Seleziona trasformazione colonne per acquisire le selezioni di colonna e applicarle in un altro punto della pipeline.

1. Aggiungere un set di dati di input alla pipeline nella finestra di progettazione.

2. Aggiungere un'istanza di [Selezione funzionalità basata su filtro](filter-based-feature-selection.md).

3. Collegare i moduli e configurare il modulo di selezione delle funzionalità per trovare automaticamente una serie di funzionalità migliori nel set di dati di input.

4. Aggiungere un'istanza del modello di [training](train-model.md) e usare l'output di [Selezione funzionalità basata](filter-based-feature-selection.md) su filtro come input per il training.

    > [!IMPORTANT]
    > Poiché l'importanza della funzionalità si basa sui valori nella colonna, non è possibile sapere in anticipo quali colonne potrebbero essere disponibili per l'input nel [modello](train-model.md)di training .  
5. Associare un'istanza del modulo Trasformazione Seleziona colonne. 

    Questo passaggio genera una selezione di colonna come trasformazione che può essere salvata o applicata ad altri set di dati. Questo passaggio assicura che le colonne identificate nella selezione delle funzionalità vengano salvate per il riutilizzo da parte di altri moduli.

6. Aggiungere il modulo Modello di [punteggio.](score-model.md) 

   *Non collegare il set di dati di input.* Aggiungere invece il modulo [Applica trasformazione](apply-transformation.md) e connettere l'output della trasformazione di selezione delle funzionalità.

   > [!IMPORTANT]
   > Non è possibile prevedere di applicare la [selezione di funzionalità basata](filter-based-feature-selection.md) su filtro al set di dati di assegnazione del punteggio e ottenere gli stessi risultati. Poiché la selezione delle funzionalità è basata sui valori, potrebbe scegliere un set di colonne diverso, che causerebbe l'esito negativo dell'operazione di assegnazione del punteggio.
7. Inviare la pipeline.

Questo processo di salvataggio e applicazione di una selezione di colonne garantisce che lo stesso schema di dati sia disponibile per il training e il punteggio.


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 
