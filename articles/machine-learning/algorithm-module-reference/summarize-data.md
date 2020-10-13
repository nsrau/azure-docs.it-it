---
title: Riepilogare i dati
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo riepiloga dati in Azure Machine Learning per generare un report delle statistiche descrittive di base per le colonne in un set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: 5206565b85d1551e5e551f1dfe75d28c93bc53f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898209"
---
# <a name="summarize-data"></a>Riepilogare i dati

Questo articolo descrive un modulo di Azure Machine Learning Designer.

Usare il modulo riepiloga dati per creare un set di misure statistiche standard che descrivono ogni colonna della tabella di input.

Le statistiche di riepilogo sono utili quando si desidera comprendere le caratteristiche del set di dati completo. Ad esempio, potrebbe essere necessario sapere:

- Quanti valori mancanti sono presenti in ogni colonna?
- Quanti valori univoci sono presenti in una colonna della funzionalità?
- Qual è la deviazione media e standard per ogni colonna?

Il modulo calcola i punteggi importanti per ogni colonna e restituisce una riga di statistiche di riepilogo per ogni variabile (colonna di dati) fornita come input.

## <a name="how-to-configure-summarize-data"></a>Come configurare il riepilogo dei dati  

1. Aggiungere il modulo **riepiloga dati** alla pipeline. Questo modulo è reperibile nella categoria **funzioni statistiche** della finestra di progettazione.

1. Connettere il set di dati per il quale si desidera generare un report.

    Se si desidera creare un report solo per alcune colonne, utilizzare il modulo [Select Columns in DataSet](select-columns-in-dataset.md) per proiettare un subset di colonne da utilizzare.

1. Non è necessario alcun parametro aggiuntivo. Per impostazione predefinita, il modulo analizza tutte le colonne fornite come input e, a seconda del tipo di valori nelle colonne, restituisce un set di statistiche pertinente, come descritto nella sezione [risultati](#results) .

1. Inviare la pipeline.

## <a name="results"></a>Risultati

Il report del modulo può includere le statistiche seguenti. 

|Nome colonna|Descrizione|
|------|------|  
|**Funzionalità**|Nome della colonna|
|**Numero**|Count of all rows|
|**Conteggio valori univoci**|Numero di valori univoci nella colonna|
|**Conteggio valori mancanti**|Numero di valori univoci nella colonna|
|**Min**|Valore minimo nella colonna|  
|**Max**|Valore massimo nella colonna|
|**Media**|Media di tutti i valori di colonna|
|**Deviazione media**|Deviazione media dei valori di colonna|
|**1st Quartile**|Valore al primo quartile|
|**Mediana**|Valore della colonna mediana|
|**3rd Quartile**|Valore al terzo quartile|
|**Modalità**|Modalità dei valori di colonna|
|**Intervallo**|Integer che rappresenta il numero di valori compresi tra i valori minimo e massimo|
|**Varianza di esempio**|Varianza per la colonna; vedere la nota|
|**Deviazione standard di esempio**|Deviazione standard per la colonna; vedere la nota|
|**Asimmetria di esempio**|Asimmetria per la colonna; vedere la nota|
|**Curtosi di esempio**|Curtosi per la colonna; vedere la nota|
|**P 0,5**|0,5% percentile|
|**P1**|1% percentile|
|**P5**|5% percentile|
|**P95**|95% percentile|
|**P 99,5**|99,5% percentile |

## <a name="technical-notes"></a>Note tecniche

- Per le colonne non numeriche, vengono calcolati solo i valori di Count, Unique value count e Missing value count. Per altre statistiche, viene restituito un valore Null.

- Le colonne contenenti valori booleani vengono elaborate utilizzando le regole seguenti:

    - Per il calcolo di Min, viene applicato un AND logico.
    
    - Quando si calcola il valore Max, viene applicato un OR logico
    
    - Per il calcolo di Range, il modulo controlla innanzitutto se il numero di valori univoci nella colonna è uguale a 2.
    
    - Per il calcolo di qualsiasi statistica che richieda calcoli a virgola mobile, i valori True vengono considerati come 1,0 e i valori False come 0,0.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning.  
