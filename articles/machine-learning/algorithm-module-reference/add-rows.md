---
title: 'Aggiungi righe: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Add Rows nel servizio Azure Machine Learning per concatenare due set di impostazioni.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: c8d8b6a873ee17d1658b0cb47de830848b215b89
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693241"
---
# <a name="add-rows-module"></a>Modulo Aggiungi righe

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per concatenare due set di impostazioni. Nella concatenazione, le righe del secondo set di dati vengono aggiunte alla fine del primo set di dati.  
  
La concatenazione delle righe è utile in scenari come i seguenti:  
  
+ È stata generata una serie di statistiche di valutazione che si desidera combinare in una tabella per semplificare la creazione di report.  
  
+ Sono stati elaborati set di dati diversi e si desidera combinare i set di dati per creare un set di dati finale.  

## <a name="how-to-use-add-rows"></a>Come utilizzare Aggiungi righe  

Per concatenare le righe di due set di impostazioni, è necessario che le righe includano esattamente lo stesso schema. Ciò significa che lo stesso numero di colonne e lo stesso tipo di dati nelle colonne.

1.  Trascinare il modulo **Aggiungi righe** nella pipeline, che può essere trovato in **trasformazione dati**nella categoria **modifica** .

2. Connettere i set di dati alle due porte di input. Il set di dati che si desidera aggiungere deve essere connesso alla seconda porta (destra). 
  
3.  Eseguire la pipeline. Il numero di righe nel set di dati di output deve essere uguale alla somma delle righe di entrambi i set di dati di input.

    Se si aggiunge lo stesso set di dati a entrambi gli input del modulo **Aggiungi righe** , il set di dati viene duplicato. 

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 