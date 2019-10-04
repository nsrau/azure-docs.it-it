---
title: 'Aggiungi colonne: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Add columns nel servizio Azure Machine Learning per concatenare due set di impostazioni.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 630f8f7d4561ed6e7e9f943f4b3b123daeee4d67
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129028"
---
# <a name="add-columns-module"></a>Modulo Aggiungi colonne

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per concatenare due set di impostazioni. Per creare un singolo set di dati, è possibile combinare tutte le colonne dei due set di dati specificati come input. Se è necessario concatenare più di due set di impostazioni, utilizzare diverse istanze di **Aggiungi colonne**.



## <a name="how-to-configure-add-columns"></a>Come configurare Aggiungi colonne
1. Aggiungere il modulo **Aggiungi colonne** all'esperimento.

2. Connettere i due set di impostazioni che si desidera concatenare. Se si desidera combinare più di due set di impostazioni, è possibile concatenare diverse combinazioni di **Aggiungi colonne**.

    - È possibile combinare due colonne con un numero diverso di righe. Il set di dati di output viene riempito con valori mancanti per ogni riga della colonna di origine di dimensioni inferiori.

    - Non è possibile scegliere singole colonne da aggiungere. Tutte le colonne di ogni set di dati vengono concatenate quando si usa **Aggiungi colonne**. Se pertanto si desidera aggiungere solo un subset delle colonne, utilizzare Seleziona colonne nel set di dati per creare un set di dati con le colonne desiderate.

3. Eseguire l'esperimento.

### <a name="results"></a>Risultati
Dopo l'esecuzione dell'esperimento:

- Per visualizzare le prime righe del nuovo set di dati, fare clic con il pulsante destro del mouse sull'output di **Aggiungi colonne** e selezionare Visualizza.

Il numero di colonne nel nuovo set di dati equivale alla somma delle colonne di entrambi i set di dati di input.

Se sono presenti due colonne con lo stesso nome nei set di dati di input, viene aggiunto un suffisso numerico al nome della colonna. Se, ad esempio, sono presenti due istanze di una colonna denominata TargetOutcome, la colonna sinistra verrà rinominata TargetOutcome_1 e la colonna destra verrà rinominata TargetOutcome_2.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 