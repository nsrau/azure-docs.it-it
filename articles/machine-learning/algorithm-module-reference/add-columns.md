---
title: 'Aggiungi colonne: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Aggiungi colonne in Azure Machine Learning per concatenare due set di impostazioni.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 8b882e6fb697d272ddc06e8d263f4c2144ebd017
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76546929"
---
# <a name="add-columns-module"></a>Modulo Aggiungi colonne

Questo articolo descrive un modulo in Azure Machine Learning Designer (anteprima).

Usare questo modulo per concatenare due set di impostazioni. Per creare un singolo set di dati, è possibile combinare tutte le colonne dei due set di dati specificati come input. Se è necessario concatenare più di due set di impostazioni, utilizzare diverse istanze di **Aggiungi colonne**.



## <a name="how-to-configure-add-columns"></a>Come configurare Aggiungi colonne
1. Aggiungere il modulo **Aggiungi colonne** alla pipeline.

2. Connettere i due set di impostazioni che si desidera concatenare. Se si desidera combinare più di due set di impostazioni, è possibile concatenare diverse combinazioni di **Aggiungi colonne**.

    - È possibile combinare due colonne con un numero diverso di righe. Il set di dati di output viene riempito con valori mancanti per ogni riga della colonna di origine di dimensioni inferiori.

    - Non è possibile scegliere singole colonne da aggiungere. Tutte le colonne di ogni set di dati vengono concatenate quando si usa **Aggiungi colonne**. Se pertanto si desidera aggiungere solo un subset delle colonne, utilizzare Seleziona colonne nel set di dati per creare un set di dati con le colonne desiderate.

3. Eseguire la pipeline.

### <a name="results"></a>Risultati
Dopo l'esecuzione della pipeline:

- Per visualizzare le prime righe del nuovo set di dati, fare clic con il pulsante destro del mouse sul modulo **Aggiungi colonne** e scegliere Visualizza. In alternativa, selezionare il modulo e passare alla scheda **output** nel riquadro di destra e fare clic sull'icona istogramma nell' **output della porta** per visualizzare il risultato.

Il numero di colonne nel nuovo set di dati equivale alla somma delle colonne di entrambi i set di dati di input.

Se sono presenti due colonne con lo stesso nome nei set di dati di input, viene aggiunto un suffisso numerico al nome della colonna. Se, ad esempio, sono presenti due istanze di una colonna denominata TargetOutcome, la colonna sinistra verrebbe rinominata TargetOutcome_1 e la colonna a destra verrebbe rinominata TargetOutcome_2.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 