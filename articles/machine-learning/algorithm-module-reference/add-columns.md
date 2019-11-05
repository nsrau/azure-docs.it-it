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
ms.openlocfilehash: 55981279cb1902424d1a0f77af097dc379d7222f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493962"
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

- Per visualizzare le prime righe del nuovo set di dati, fare clic con il pulsante destro del mouse sull'output di **Aggiungi colonne** e selezionare Visualizza.

Il numero di colonne nel nuovo set di dati equivale alla somma delle colonne di entrambi i set di dati di input.

Se sono presenti due colonne con lo stesso nome nei set di dati di input, viene aggiunto un suffisso numerico al nome della colonna. Se, ad esempio, sono presenti due istanze di una colonna denominata TargetOutcome, la colonna sinistra verrà rinominata TargetOutcome_1 e la colonna destra verrà rinominata TargetOutcome_2.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 