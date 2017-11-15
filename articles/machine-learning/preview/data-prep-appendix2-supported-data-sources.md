---
title: Origini dati supportate disponibili per la preparazione dei dati di Microsoft Azure Machine Learning | Microsoft Docs
description: Questo documento elenca tutte le origini dati supportate disponibili per la preparazione dei dati di Microsoft Azure Machine Learning.
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
ms.date: 09/12/2017
ms.openlocfilehash: db4774de28a17e022de111986f72a1f15ec32beb
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="supported-data-sources-for-azure-machine-learning-data-preparation"></a>Origini dati supportate disponibili per la preparazione dei dati di Microsoft Azure Machine Learning 
Questo articolo illustra le origini dati attualmente supportate nella preparazione dei dati di Microsoft Azure Machine Learning.

Le origini dati supportate per questa versione sono elencate di seguito.

## <a name="types"></a>Tipi 
### <a name="directory-vs-file"></a>Directory e file
Scegliere un singolo file e leggerlo nella preparazione dei dati. Il tipo di file viene analizzato per determinare i parametri predefiniti per la connessione file mostrata nella schermata successiva.

Scegliere una directory o un set di file all'interno di una directory (il selettore file è a selezione multipla). Entrambi gli approcci determinano la lettura dei file come singolo flusso di dati grazie all'accodamento reciproco dei file (con l'eliminazione delle intestazioni, se necessario).

Di seguito sono elencati i tipi di file supportati:
- Testo delimitato (csv, tsv, txt e così via)
- File a larghezza fissa
- Testo normale
- File JSON

### <a name="csv-file"></a>File CSV
Legge un file con valori delimitati da virgole dalla risorsa di archiviazione.

#### <a name="options"></a>Opzioni
- Separatore
- Commento
- Headers
- Separatore decimale
- Codifica file
- Righe da ignorare

### <a name="tsv-file"></a>File TSV
Legge un file con valori delimitati da tabulazioni dalla risorsa di archiviazione.

#### <a name="options"></a>Opzioni
- Commento
- Headers
- Codifica file
- Righe da ignorare

### <a name="excel-xlsxlsx"></a>Excel (xls/xlsx)
Legge un file Excel, un foglio alla volta, specificando il nome o il numero del foglio.

#### <a name="options"></a>Opzioni
- Nome o numero foglio
- Headers
- Righe da ignorare

### <a name="json-file"></a>File JSON
Legge un file JSON dalla risorsa di archiviazione. Si noti che il file è "flat" in lettura.

#### <a name="options"></a>Opzioni
- Nessuno

### <a name="parquet"></a>Parquet
Legge un set di dati Parquet, un singolo file o una cartella.

Il formato Parquet può avere forme diverse nella risorsa di archiviazione. Per i set di dati di dimensioni ridotte viene usato a volte un singolo file con estensione parquet. Diverse librerie Python supportano la lettura o la scrittura di un singolo file con estensione parquet. La preparazione dei dati di Microsoft Azure Machine Learning al momento si basa sulla raccolta PyArrow Python per la lettura di Parquet durante l'uso interattivo locale. Supporta singoli file con estensione parquet (se sono stati scritti come tali e non come parte di un set di dati più grande), nonché set di dati Parquet.

Un set di dati Parquet è una raccolta di più file con estensione parquet, ciascuno dei quali rappresenta una partizione più piccola di un set di dati più grande. I set di dati sono in genere contenuti in una cartella e rappresentano il formato di output Parquet predefinito per piattaforme come Spark e Hive.

>[!NOTE]
>Quando si leggono i dati Parquet presenti in una cartella con più file con estensione parquet, è più sicuro selezionare la directory per la lettura e l'opzione **Parquet data set** (Set di dati Parquet). In questo modo PyArrow legge l'intera cartella anziché i singoli file. Viene così garantito il supporto per la lettura di modi più complessi di archiviazione Parquet sul disco, ad esempio il partizionamento delle cartelle.

L'esecuzione della scalabilità orizzontale (scale-out) si basa sulle funzionalità di lettura Parquet di Spark e supporta singoli file e cartelle, in modo analogo all'uso locale interattivo.

#### <a name="options"></a>Opzioni
- Parquet data set (Set di dati Parquet) Questa opzione determina se la preparazione dei dati di Microsoft Azure Machine Learning espande una determinata directory e tenta di leggere ogni file singolarmente (la modalità non selezionata) o se considera la directory come intero set di dati (la modalità selezionata). Con la modalità selezionata, PyArrow sceglie il modo migliore per interpretare i file.


## <a name="locations"></a>Località
### <a name="local"></a>Local
Posizione di archiviazione in rete mappata o in unità disco locale.

### <a name="azure-blob-storage"></a>Archivio BLOB di Azure
Archivio BLOB di Azure, che richiede una sottoscrizione di Azure.

