---
title: 'Aggiungi righe: riferimento modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Aggiungi righe in Azure Machine Learning per concatenare due set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd9b5f8f182c4deab746d2c41e516a6ac23fb7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477732"
---
# <a name="add-rows-module"></a>Modulo Aggiungi righe

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare questo modulo per concatenare due set di dati. In concatenazione, le righe del secondo set di dati vengono aggiunte alla fine del primo set di dati.  
  
La concatenazione delle righe è utile in scenari come quelli seguenti:  
  
+ È stata generata una serie di statistiche di valutazione che si desidera combinare in una tabella per agevolare il reporting.  
  
+ Sono stati usati diversi set di dati che si desidera combinare per creare il set di dati finale.  

## <a name="how-to-use-add-rows"></a>Come utilizzare Aggiungi righe  

Per concatenare le righe di due set di dati, le righe devono avere esattamente lo stesso schema. Ciò significa che lo stesso numero di colonne e lo stesso tipo di dati nelle colonne.

1.  Trascinare il modulo **Aggiungi righe** nella pipeline, È possibile trovarlo in **Trasformazione dati**.

2. Collegare i set di dati alle due porte di input. Il set di dati che si desidera aggiungere deve essere collegato alla seconda porta (destra). 
  
3.  Inviare la pipeline. Il numero di righe nel set di dati di output deve essere uguale alla somma delle righe di entrambi i set di dati di input.

    Se si aggiunge lo stesso set di dati a entrambi gli input del modulo **Aggiungi righe,** il set di dati viene duplicato. 

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 