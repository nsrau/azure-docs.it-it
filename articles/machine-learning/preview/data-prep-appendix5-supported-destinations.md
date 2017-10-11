---
title: Destinazioni/output supportati disponibili per la preparazione dei dati di Microsoft Azure Machine Learning | Microsoft Docs
description: Questo documento elenca tutte le destinazioni/output supportati disponibili per la preparazione dei dati di Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 415ceba02eb3c8da3de5ab3aa6980fbe5bae2db9
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="supported-data-exports-for-this-preview"></a>Esportazioni di dati supportate per questa anteprima 
È possibile eseguire l'esportazione in diversi formati. Per conservare i risultati intermedi della preparazione dei dati prima dell'integrazione nel restante flusso di lavoro di Machine Learning, è possibile usare questi formati.

## <a name="types"></a>Tipi 
### <a name="csv-file"></a>File CSV 
Scrivere un file con valori delimitati da virgole nella risorsa di archiviazione

#### <a name="options"></a>Opzioni
- Terminazioni riga
- Sostituisci valori null con
- Sostituisci errori con 
- Separatore


### <a name="parquet"></a>Parquet ###
Scrivere un set di dati in un archivio come Parquet.

Il formato Parquet può avere forme diverse nella risorsa di archiviazione. Per i set di dati più piccoli viene usato talvolta un file ".parquet" singolo; svariate raccolte Python supportano la lettura/scrittura in singoli file ".parquet". Al momento AMLWB si basa sulla raccolta Python PyArrow per la scrittura Parquet durante l'uso "interattivo" locale. Ciò significa che Parquet per singoli file è attualmente l'unico formato di output Parquet supportato durante l'uso interattivo locale.

Durante le esecuzioni della scalabilità orizzontale (in Spark), AMLWB fa affidamento sulle funzionalità di lettura/scrittura Parquet di Spark. Il formato di output predefinito di Spark per Parquet (attualmente l'unico supportato) ha una struttura analoga a un set di dati HIVE. Questo significa che una cartella contiene molti file ".parquet", ognuno dei quali è una partizione più piccola di un set di dati più grande. 

#### <a name="caveats"></a>Avvertenze ####
Parquet come formato è relativamente recente e con alcune incoerenze di implementazione tra le diverse raccolte. Ad esempio, Spark definisce restrizioni sui caratteri validi nei nomi di colonne quando si esegue la scrittura in Parquet, a differenza di quanto accade con PyArrow. Qualsiasi carattere nel set " ,;{}()\\n\\t=", , non può essere un nome di colonna.

>[!NOTE]
>Per garantire la compatibilità con Spark, ogni volta che i dati vengono scritti in Parquet, le occorrenze di questi caratteri nei nomi di colonne vengono sostituite con '_' (carattere di sottolineatura).**

>[!NOTE]
>Per garantire la coerenza tra locale e scalabilità orizzontale di tutti i dati scritti in Parquet, tramite l'app, Python o Spark, i nomi delle colonne vengono purificati per garantire la compatibilità con Spark. Per assicurare i nomi di colonna previsti quando si scrive in caratteri Parquet in Spark, il set non valido deve essere rimosso dalle colonne prima della scrittura.



## <a name="locations"></a>Località 
### <a name="local"></a>Local 
Unità disco rigido locale o posizione di archiviazione di rete mappata

### <a name="azure-blob"></a>BLOB Azure 
Archiviazione di Azure (BLOB) richiede una sottoscrizione ad Azure


