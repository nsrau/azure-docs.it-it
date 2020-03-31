---
title: Formati di dati supportati da Azure Data Explorer per l'inserimento.
description: Informazioni sui vari formati di dati e compressione supportati da Azure Data Explorer per l'inserimento.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 16ecdf7ac3f0062637e4bbea86d26e2560f38222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235291"
---
# <a name="data-formats-supported-by-azure-data-explorer-for-ingestion"></a>Formati di dati supportati da Azure Data Explorer per l'inserimentoData formats supported by Azure Data Explorer for ingestion

Data ingestion is the process by which data is added to a table and is made available for query in Azure Data Explorer. Per tutti i metodi di inserimento, ad esempio inserimento da query, i dati devono essere in uno dei formati supportati. Nella tabella seguente sono elencati e descritti i formati supportati da Azure Data Explorer per l'inserimento dei dati.

|Format   |Estensione   |Descrizione|
|---------|------------|-----------|
|Avro     |`.avro`     |[File contenitore Avro](https://avro.apache.org/docs/current/). I codici seguenti sono supportati: `null`, `deflate` (`snappy` non è al momento supportato).|
|CSV      |`.csv`      |File di testo con valori delimitati da virgole (`,`). Vedere [RFC 4180: Formato comune e tipo MIME per i _file CSV (Comma-Separated Values)._](https://www.ietf.org/rfc/rfc4180.txt)|
|JSON     |`.json`     |Un file di testo con oggetti JSON delimitati da `\n` o `\r\n`. Vedere [Righe JSON (JSONL)](http://jsonlines.org/).|
|MultiJSON|`.multijson`|File di testo con una matrice JSON di contenitori di proprietà (ognuno dei quali rappresenta un record) o un numero qualsiasi di contenitori di proprietà delimitati da spazi vuoti, `\n` o `\r\n`. Ogni contenitore di proprietà può essere distribuito su più righe. Questo formato è `JSON`preferito rispetto a , a meno che i dati non siano sacchetti di proprietà.|
|ORC      |`.orc`      |Un [file ORC](https://en.wikipedia.org/wiki/Apache_ORC).|
|Parquet  |`.parquet`  |Un [file Parquet](https://en.wikipedia.org/wiki/Apache_Parquet).|
|Psv      |`.psv`      |File di testo con valori delimitati da barre verticali (<code>&#124;</code>).|
|RAW      |`.raw`      |File di testo il cui intero contenuto è un singolo valore stringa.|
|Csv SCsv     |`.scsv`     |File di testo con valori delimitati da punti e virgola (`;`).|
|SOHsv    |`.sohsv`    |File di testo con valori delimitati da SOH. SOH è il punto di codice ASCII 1. Questo formato è usato da Hive in HDInsight.|
|Tsv      |`.tsv`      |File di testo con valori delimitati da tabulazioni (`\t`).|
|TSVE     |`.tsv`      |File di testo con valori delimitati da tabulazioni (`\t`). Per l'escape viene usato un carattere barra rovesciata (`\`).|
|TXT      |`.txt`      |File di testo con righe delimitate da `\n`. Le righe vuote vengono ignorate.|

## <a name="supported-data-compression-formats"></a>Formati di compressione dei dati supportati

BLOB e file possono essere compressi tramite uno dei seguenti algoritmi di compressione:

|Compressione|Estensione|
|-----------|---------|
|GZip       |gz      |
|Zip        |zip     |

Indicare la compressione aggiungendo l'estensione al nome del BLOB o del file.

Ad esempio:
* `MyData.csv.zip`indica un BLOB o un file formattato come CSV, compresso con zip (archivio o un singolo file)
* `MyData.csv.gz`indica un BLOB o un file formattato come CSV, compresso con

Blob o nomi di file che non includono le estensioni di formato, ma solo la compressione (ad esempio, ) è supportata. In questo caso, il formato di file deve essere specificato come proprietà di inserimento perché non può essere dedotto.

> [!NOTE]
> Alcuni formati di compressione tengono traccia dell'estensione di file originale come parte del flusso compresso. Questa estensione viene generalmente ignorata ai fini della determinazione del formato di file. Se non è possibile determinare il formato di file dal nome del file o del BLOB (compresso), deve essere specificato tramite la proprietà di inserimento `format`.

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni [sull'inserimento di dati](/azure/data-explorer/ingest-data-overview)
* Altre informazioni sulle proprietà di inserimento dati di Azure Data ExplorerLearn more about [Azure Data Explorer data ingestion properties](ingestion-properties.md)
