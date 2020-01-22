---
title: 'Aggiungi righe: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Add Rows in Azure Machine Learning per concatenare due set di impostazioni.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 39bbf3b3a38926d696233c2d77bae83ccfc85206
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314556"
---
# <a name="add-rows-module"></a>Modulo Aggiungi righe

Questo articolo descrive un modulo in Azure Machine Learning Designer.

Usare questo modulo per concatenare due set di impostazioni. Nella concatenazione, le righe del secondo set di dati vengono aggiunte alla fine del primo set di dati.  
  
La concatenazione delle righe è utile in scenari come quelli seguenti:  
  
+ È stata generata una serie di statistiche di valutazione che si desidera combinare in una tabella per agevolare il reporting.  
  
+ Sono stati usati diversi set di dati che si desidera combinare per creare il set di dati finale.  

## <a name="how-to-use-add-rows"></a>Come utilizzare Aggiungi righe  

Per concatenare le righe di due set di impostazioni, è necessario che le righe includano esattamente lo stesso schema. Ciò significa che lo stesso numero di colonne e lo stesso tipo di dati nelle colonne.

1.  Trascinare il modulo **Aggiungi righe** nella pipeline, che può essere trovato in **trasformazione dati**nella categoria **modifica** .

2. Collegare i set di dati alle due porte di input. Il set di dati che si desidera aggiungere deve essere collegato alla seconda porta (destra). 
  
3.  Eseguire la pipeline. Il numero di righe nel set di dati di output deve essere uguale alla somma delle righe di entrambi i set di dati di input.

    Se si aggiunge lo stesso set di dati a entrambi gli input del modulo **Aggiungi righe** , il set di dati viene duplicato. 

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 