---
title: Origini dati supportate disponibili per la preparazione dei dati di Microsoft Azure Machine Learning | Microsoft Docs
description: Questo documento elenca tutte le origini dati supportate disponibili per la preparazione dei dati di Azure Machine Learning
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
ms.openlocfilehash: 1ef4c5c33d98cfeb566e8fe23bda9e0d3f041781
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="supported-data-sources-for-this-release"></a>Origini dati supportate per questa versione 
Il documento seguente illustra l'elenco di origini dati attualmente supportate nella preparazione dati di Microsoft Azure Machine Learning.

Le origini dati supportate per questa versione sono elencate di seguito.

## <a name="types"></a>Tipi 
### <a name="directory-versus-file"></a>Directory e file
*File/directory*: scegliere un singolo file e leggerlo nella preparazione dei dati. Il tipo di file viene analizzato per determinare i parametri predefiniti per la connessione file presente nella schermata successiva. Scegliere una directory o un set di file all'interno di una directory (il selettore file è a selezione multipla). Entrambi gli approcci determinano la lettura dei file come singolo flusso di dati grazie all'accodamento reciproco dei file (con l'eliminazione delle intestazioni, se necessario).

I tipi di file sono riportati di seguito:
- Testo delimitato (csv, tsv, txt e così via) 
- File a larghezza fissa
- Testo normale
- File JSON

### <a name="csv-file"></a>File CSV
Legge un file CSV dalla risorsa di archiviazione.

#### <a name="options"></a>Opzioni
- Separatore
- Commento
- Headers
- Separatore decimale
- Codifica file
- Righe da ignorare

### <a name="tsv-file"></a>File TSV
Legge un file TSV dalla risorsa di archiviazione.

#### <a name="options"></a>Opzioni
- Commento
- Headers
- Codifica file
- Righe da ignorare

### <a name="excel-xlsxlsx"></a>Excel (xls/xlsx)
Legge un file di Excel, un solo foglio alla volta, specificando il nome o il numero del foglio.

#### <a name="options"></a>Opzioni
- Nome/numero foglio
- Headers
- Righe da ignorare

### <a name="json-file"></a>File JSON
Legge un file JSON dalla risorsa di archiviazione. Si noti che il file è "flat" in lettura.

#### <a name="options"></a>Opzioni
Nessuno

### <a name="parquet"></a>Parquet
Legge un set di dati Parquet, un singolo file o una cartella.

Il formato Parquet può avere forme diverse nella risorsa di archiviazione. Per i set di dati di dimensioni ridotte, viene usato a volte un file con estensione parquet singolo. Diverse librerie Python supportano la lettura o la scrittura di un singolo file con estensione parquet. Al momento, Azure Machine Learning Workbench si basa sulla raccolta Python PyArrow per la lettura Parquet durante l'uso interattivo locale. Supporta singoli file con estensione parquet (se sono stati scritti come tali e non come parte di un set di dati più grande) e set di dati Parquet. 

Un set di dati Parquet è una raccolta di più file con estensione, ciascuno dei quali rappresenta una partizione più piccola di un set di dati più grande. I set di dati sono in genere contenuti in una cartella. Rappresentano il formato di output Parquet predefinito per le piattaforme comuni, ad esempio Spark e Hive.

>[!NOTE]
>Quando si leggono i dati Parquet presenti in una cartella con più file con estensione parquet, è più sicuro selezionare la directory per la lettura e selezionare l'opzione **Parquet Dataset** (Set di dati Parquet). In questo modo PyArrow legge l'intera cartella anziché i singoli file. Viene così garantito il supporto per la lettura di modi più complessi di archiviazione Parquet sul disco (ad esempio il partizionamento delle cartelle).

L'esecuzione della scalabilità orizzontale si basa sulle funzionalità di lettura Parquet di Spark e supporta singoli file e cartelle.

#### <a name="options"></a>Opzioni
*Parquet dataset* (Set di dati parquet): questa opzione determina se Azure Machine Learning Workbench usa la modalità senza tick o con tick. La modalità senza tick espande una determinata directory e tenta di leggerne ogni file singolarmente. La modalità con tick tratta la directory come un unico set di dati e lascia che PyArrow individui il modo migliore per interpretare i file.


## <a name="locations"></a>Località
### <a name="local"></a>Local
Posizione di archiviazione in rete mappata o in unità disco locale.

### <a name="azure-blob-storage"></a>Archivio BLOB di Azure
Richiede una sottoscrizione di Azure.

