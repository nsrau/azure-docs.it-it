---
title: 'Aggiungere colonne: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo di aggiunta di colonne nel servizio Azure Machine Learning per concatenare due set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f1e087e97007c6ba271651a9791c7c3b38a9b9b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029356"
---
# <a name="add-columns-module"></a>Aggiungere un modulo di colonne

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per concatenare due set di dati. È combinare tutte le colonne da due set di dati specificato come input per creare un singolo set di dati. Se è necessario concatenare più di due set di dati, usare più istanze di **Add Columns**.



## <a name="how-to-configure-add-columns"></a>Come configurare Add Columns
1. Aggiungi il **Add Columns** modulo nell'esperimento.

2. Connettere i due set di dati che si desidera concatenare. Se si desidera combinare più di due set di dati, è possibile concatenare diverse combinazioni di **Add Columns**.

    - È possibile combinare due colonne che contengono un numero diverso di righe. Il set di dati di output viene riempito con i valori mancanti per ogni riga nella colonna di origine più piccola.

    - Non è possibile scegliere singole colonne da aggiungere. Vengono concatenate tutte le colonne da ogni set di dati quando si usa **Add Columns**. Pertanto, se si desidera aggiungere solo un subset delle colonne, è possibile usare Seleziona colonne in set di dati per creare un set di dati con le colonne desiderate.

3. Eseguire l'esperimento.

### <a name="results"></a>Risultati
Al termine dell'esecuzione dell'esperimento:

- Per visualizzare le prime righe del nuovo set di dati, fare doppio clic sull'output del **Add Columns** e scegliere Visualize.

Il numero di colonne nel nuovo set di dati equivale alla somma delle colonne di entrambi i set di dati input.

Se sono presenti due colonne con lo stesso nome nel set di dati di input, viene aggiunto un suffisso numerico al nome della colonna. Ad esempio, se sono presenti due istanze di una colonna denominata TargetOutcome, la colonna a sinistra verrà rinominata TargetOutcome_1 e nella colonna destra sarebbe rinominata TargetOutcome_2.

## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 