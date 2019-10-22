---
title: 'Applica trasformazione: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Apply Transformation nel servizio Azure Machine Learning per modificare un set di dati di input in base a una trasformazione calcolata in precedenza.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: adb2477fe2b7eea0dd0a98c7240843d90411d832
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693871"
---
# <a name="apply-transformation-module"></a>Modulo Apply Transformation

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Utilizzare questo modulo per modificare un set di dati di input in base a una trasformazione calcolata in precedenza.  
  
Se, ad esempio, sono stati usati i punteggi z per normalizzare i dati di training usando il modulo **Normalize data** , è necessario usare il valore del punteggio z calcolato per il training anche durante la fase di assegnazione del punteggio. In Azure Machine Learning, è possibile salvare il metodo di normalizzazione come trasformazione, quindi utilizzare **Applica trasformazione** per applicare il punteggio z ai dati di input prima del punteggio.
  
Azure Machine Learning fornisce il supporto per la creazione e l'applicazione di molti tipi diversi di trasformazioni personalizzate. Ad esempio, è possibile salvare e quindi riutilizzare le trasformazioni in:  
  
- Rimuovere o sostituire i valori mancanti usando **i dati puliti mancanti**
- Normalizzare i dati usando **i dati normalizzati**
  

## <a name="how-to-use-apply-transformation"></a>Come utilizzare Applica trasformazione  
  
1. Aggiungere il modulo **Apply Transformation** alla pipeline. È possibile trovare questo modulo in **Machine Learning**, nella categoria **Punteggio** . 
  
2. Individuare una trasformazione esistente da utilizzare come input.  Le trasformazioni salvate in precedenza sono reperibili nel gruppo **trasformazioni** nel riquadro di spostamento a sinistra.  
  
   
  
3. Connettere il set di dati che si desidera trasformare. Il set di dati deve avere esattamente lo stesso schema (numero di colonne, nomi di colonna e tipi di dati) come set di dati per cui la trasformazione è stata progettata per la prima volta.  
  
4. Non è necessario impostare altri parametri, perché durante la definizione della trasformazione viene eseguita la personalizzazione.  
  
5. Per applicare una trasformazione al nuovo set di dati, eseguire la pipeline.  

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 