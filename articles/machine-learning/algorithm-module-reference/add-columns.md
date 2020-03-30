---
title: 'Aggiungere colonne: riferimento moduloAdd Columns: Module Reference'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Aggiungi colonne in Azure Machine Learning per concatenare due set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: f2e067f76d6ed7d89a38e9b8920c407f161969a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456779"
---
# <a name="add-columns-module"></a>Modulo Aggiungi colonne

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare questo modulo per concatenare due set di dati. Tutte le colonne dei due set di dati specificati come input consentono di creare un singolo set di dati. Se è necessario concatenare più di due set di dati, utilizzare diverse istanze di **Aggiungi colonne**.



## <a name="how-to-configure-add-columns"></a>Come configurare Aggiungi colonne
1. Aggiungere il modulo **Aggiungi colonne** alla pipeline.

2. Connettere i due set di dati che si desidera concatenare. Se si desidera combinare più di due set di dati, è possibile concatenare diverse combinazioni di **Aggiungi colonne**.

    - È possibile combinare due colonne con un numero diverso di righe. Il set di dati di output viene riempito con valori mancanti per ogni riga nella colonna di origine più piccola.

    - Non è possibile scegliere singole colonne da aggiungere. Tutte le colonne di ogni set di dati vengono concatenate quando si utilizza **Aggiungi colonne**. Pertanto, se si desidera aggiungere solo un subset delle colonne, utilizzare Seleziona colonne nel set di dati per creare un set di dati con le colonne desiderate.

3. Inviare la pipeline.

### <a name="results"></a>Risultati
Dopo l'esecuzione della pipeline:

- Per visualizzare le prime righe del nuovo set di dati, fare clic con il pulsante destro del mouse sul modulo **Aggiungi colonne** e selezionare Visualizza. Oppure Selezionare il modulo e passare alla scheda **Uscite** nel pannello di destra, fare clic sull'icona dell'istogramma nelle **uscite di** porta per visualizzare il risultato.

Il numero di colonne nel nuovo set di dati equivale alla somma delle colonne di entrambi i set di dati di input.

Se sono presenti due colonne con lo stesso nome nei set di dati di input, viene aggiunto un suffisso numerico al nome della colonna. Ad esempio, se sono presenti due istanze di una colonna denominata TargetOutcome, la colonna di sinistra verrebbe rinominata TargetOutcome_1 e la colonna di destra verrebbe rinominata TargetOutcome_2.

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 