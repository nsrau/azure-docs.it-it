---
title: Riepiloga dati
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo riepiloga dati nel servizio Azure Machine Learning per generare un report delle statistiche descrittive di base per le colonne in un set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 503eb533b83cbeedcc16b73a9c1dbe821a4d4d94
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492577"
---
# <a name="summarize-data"></a>Riepiloga dati

Questo articolo descrive un modulo di Azure Machine Learning Designer (anteprima).

Usare il modulo riepiloga dati per creare un set di misure statistiche standard che descrivono ogni colonna della tabella di input.

Le statistiche di riepilogo sono utili quando si desidera comprendere le caratteristiche del set di dati completo. Ad esempio, potrebbe essere necessario tenere presente quanto segue:

- Quanti valori mancanti sono presenti in ogni colonna?
- Quanti valori univoci sono presenti in una colonna della funzionalità?
- Qual è la deviazione media e standard per ogni colonna?

Il modulo calcola i punteggi importanti per ogni colonna e restituisce una riga di statistiche di riepilogo per ogni variabile (colonna di dati) fornita come input.

## <a name="how-to-configure-summarize-data"></a>Come configurare il riepilogo dei dati  

1. Aggiungere il modulo **riepiloga dati** alla pipeline. Questo modulo è reperibile nella categoria **funzioni statistiche** della finestra di progettazione.

1. Connettere il set di dati per il quale si desidera generare un report.

    Se si desidera creare un report solo per alcune colonne, utilizzare il modulo [Select Columns in DataSet](select-columns-in-dataset.md) per proiettare un subset di colonne da utilizzare.

1. Non è necessario alcun parametro aggiuntivo. Per impostazione predefinita, il modulo analizza tutte le colonne fornite come input e, a seconda del tipo di valori nelle colonne, restituisce un set di statistiche pertinente, come descritto nella sezione [risultati](#results) .

1. Eseguire la pipeline oppure fare clic con il pulsante destro del mouse sul modulo e scegliere **Esegui selezione**.

## <a name="results"></a>Risultati

Il report del modulo può includere le statistiche seguenti. 

|Nome colonna|Descrizione|
|------|------|  
|**Funzionalità**|Nome della colonna|
|**Numero**|Conteggio di tutte le righe|
|**Conteggio valori univoci**|Numero di valori univoci nella colonna|
|**Conteggio valori mancanti**|Numero di valori univoci nella colonna|
|**Min**|Valore minimo nella colonna|  
|**Max**|Valore massimo nella colonna|
|**Significa**|Media di tutti i valori di colonna|
|**Deviazione media**|Deviazione media dei valori di colonna|
|**primo quartile**|Valore al primo quartile|
|**Mediana**|Valore della colonna mediana|
|**terzo quartile**|Valore al terzo quartile|
|**Modalità**|Modalità dei valori di colonna|
|**Range**|Integer che rappresenta il numero di valori compresi tra i valori minimo e massimo|
|**Varianza di esempio**|Varianza per la colonna; vedere la nota|
|**Deviazione standard di esempio**|Deviazione standard per la colonna; vedere la nota|
|**Asimmetria di esempio**|Asimmetria per la colonna; vedere la nota|
|**Curtosi di esempio**|Curtosi per la colonna; vedere la nota|
|**P 0,5**|0,5% percentile|
|**P1**|1% percentile|
|**P5**|5% percentile|
|**P95**|95% percentile|
|**P 99,5**|99,5% percentile |

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio.  
