---
title: 'Applica trasformazione: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Apply Transformation nel servizio Azure Machine Learning per modificare un set di dati di input basato su una trasformazione calcolata in precedenza.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 810f375642af49814049589cb83ad17fea578b13
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028711"
---
# <a name="apply-transformation-module"></a>Modulo Apply Transformation

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per modificare un set di dati di input basato su una trasformazione calcolata in precedenza.  
  
Ad esempio, se punteggi z è stato usato per normalizzare i dati di training usando il **Normalize Data** modulo, si potrebbe voler usare il valore z-score calcolato per il training durante la fase di assegnazione dei punteggi anche. In Azure Machine Learning, è possibile salvare il metodo di normalizzazione come una trasformazione e viene quindi utilizzato **Apply Transformation** per applicare il punteggio z per i dati di input prima dell'assegnazione dei punteggi.
  
Azure Machine Learning fornisce il supporto per la creazione e quindi l'applicazione di molti tipi diversi di trasformazioni personalizzate. Potrebbe, ad esempio, si desidera salvare e quindi riutilizzarlo trasformazioni:  
  
- Rimuovere o sostituire i valori mancanti, usando **Clean Missing Data**
- Normalizzare i dati mediante **Normalize Data**
  

## <a name="how-to-use-apply-transformation"></a>Come usare Apply Transformation  
  
1. Aggiungere il **Apply Transformation** modulo nell'esperimento. È possibile trovare questo modulo sotto **Machine Learning**, nella **punteggio** categoria. 
  
2. Individuare una trasformazione esistente da utilizzare come input.  Le trasformazioni salvate in precedenza sono reperibile nella **trasforma** gruppo nel riquadro di spostamento a sinistra.  
  
   
  
3. Connettere il set di dati che si desidera trasformare. Il set di dati deve avere esattamente lo stesso schema (numero di colonne, i nomi delle colonne, tipi di dati) del set di dati per cui è stata progettata inizialmente la trasformazione.  
  
4. Nessun altro parametro necessario essere impostata perché l'intera personalizzazione viene eseguita durante la definizione della trasformazione.  
  
5. Per applicare una trasformazione al nuovo set di dati, eseguire l'esperimento.  

## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 