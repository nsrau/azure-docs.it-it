---
title: 'Aggiungere le righe: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Add Rows nel servizio Azure Machine Learning per concatenare due set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ffd693ea3452ef48dc3e05e7bc4a6d3988a487b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028756"
---
# <a name="add-rows-module"></a>Aggiungere un modulo di righe

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per concatenare due set di dati. Nella concatenazione, le righe del secondo set di dati vengono aggiunte alla fine del primo set di dati.  
  
La concatenazione delle righe è utile nei seguenti scenari:  
  
+ È stata generata una serie di statistiche di valutazione e si vuole combinarli in una tabella per agevolare il reporting.  
  
+ Usano i set di dati diversi e si desidera combinare i set di dati per creare un set di dati finale.  

## <a name="how-to-use-add-rows"></a>Come usare Add Rows  

Per concatenare le righe da due set di dati, le righe devono avere esattamente lo stesso schema. Ciò significa, lo stesso numero di colonne e lo stesso tipo di dati nelle colonne.

1.  Trascinare il **Add Rows** modulo nell'esperimento, è disponibile in **Data Transformation**, nel **Manipulate** categoria.

2. Connettere il set di dati per le due porte di input. Il set di dati che si desidera aggiungere devono essere connessi alla seconda porta (destra). 
  
3.  Eseguire l'esperimento. Il numero di righe del set di dati di output deve essere uguale la somma delle righe di entrambi i set di dati input.

    Se si aggiunge il set di dati stesso per entrambi gli input del **Add Rows** modulo, il set di dati è duplicato. 

## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 