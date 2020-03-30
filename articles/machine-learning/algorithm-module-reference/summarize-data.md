---
title: Riepilogare i dati
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Riepiloga dati in Azure Machine Learning per generare un report delle statistiche descrittive di base per le colonne in un set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: b0def12582dd3795e1b17334406e28d77c3c5656
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477443"
---
# <a name="summarize-data"></a>Riepilogare i dati

Questo articolo descrive un modulo della finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare il modulo Riepiloga dati per creare un set di misure statistiche standard che descrivono ogni colonna della tabella di input.

Le statistiche di riepilogo sono utili quando si desidera comprendere le caratteristiche del set di dati completo. Ad esempio, potrebbe essere necessario sapere:

- Quanti valori mancanti sono presenti in ogni colonna?
- Quanti valori univoci sono presenti in una colonna di entità geografiche?
- Qual è la media e la deviazione standard per ogni colonna?

Il modulo calcola i punteggi importanti per ogni colonna e restituisce una riga di statistiche di riepilogo per ogni variabile (colonna di dati) fornita come input.

## <a name="how-to-configure-summarize-data"></a>Come configurare Riepiloga dati  

1. Aggiungere il modulo **Riepiloga dati** alla pipeline. È possibile trovare questo modulo nella categoria **Funzioni statistiche** nella finestra di progettazione.

1. Connettere il set di dati per il quale si desidera generare un report.

    Se si desidera creare report solo su alcune colonne, utilizzare il modulo [Seleziona colonne nel set di dati](select-columns-in-dataset.md) per proiettare un sottoinsieme di colonne da utilizzare.

1. Non sono necessari parametri aggiuntivi. Per impostazione predefinita, il modulo analizza tutte le colonne fornite come input e, a seconda del tipo di valori nelle colonne, restituisce un set di statistiche pertinente, come descritto nella sezione [Risultati.](#results)

1. Inviare la pipeline.

## <a name="results"></a>Risultati

Il report del modulo può includere le seguenti statistiche. 

|Nome colonna|Descrizione|
|------|------|  
|**Funzionalità**|Nome della colonna|
|**Conteggio**|Count of all rows|
|**Conteggio valori univoci**|Numero di valori univoci nella colonna|
|**Conteggio valori mancanti**|Numero di valori univoci nella colonna|
|**Minimo**|Valore più basso nella colonna|  
|**Massimo**|Valore più alto nella colonna|
|**Media**|Media di tutti i valori di colonna|
|**Deviazione media**|Deviazione media dei valori di colonna|
|**1st Quartile**|Valore al primo quartile|
|**Median**|Valore della colonna mediana|
|**3rd Quartile**|Valore al terzo quartile|
|**Modalità**|Modalità dei valori delle colonne|
|**Gamma**|Intero che rappresenta il numero di valori compresi tra i valori massimo e minimo|
|**Scostamento campione**|Varianza per la colonna; vedere Nota|
|**Deviazione standard del campione**|Deviazione standard per la colonna; vedere Nota|
|**Esempio di asimmetria**|Asimmetria per colonna; vedere Nota|
|**Esempio di curtosi**|Curtosi per colonna; vedere Nota|
|**P0,5**|0,5% percentile|
|**P1 (in via p1)**|1% percentile|
|**P5**|5% percentile|
|**P95**|95% percentile|
|**P99,5**|99,5% percentile |

## <a name="technical-notes"></a>Note tecniche

- Per le colonne non numeriche, vengono calcolati solo i valori di Count, Unique value count e Missing value count. Per altre statistiche, viene restituito un valore Null.

- Le colonne che contengono valori booleani vengono elaborate utilizzando queste regole:Columns that contain Boolean values are processed using these rules:

    - Per il calcolo di Min, viene applicato un AND logico.
    
    - Quando si calcola Max, viene applicato un OR logico
    
    - Per il calcolo di Range, il modulo controlla innanzitutto se il numero di valori univoci nella colonna è uguale a 2.
    
    - Per il calcolo di qualsiasi statistica che richieda calcoli a virgola mobile, i valori True vengono considerati come 1,0 e i valori False come 0,0.

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning.  
