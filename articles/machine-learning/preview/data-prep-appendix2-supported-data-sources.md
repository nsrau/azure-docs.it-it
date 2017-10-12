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
ms.openlocfilehash: 0a77b31e07f118155094fab4d3393ffdb37c9c6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="supported-data-sources-for-this-release"></a>Origini dati supportate per questa versione 
Il documento seguente illustra l'elenco di origini dati supportate attualmente nella preparazione dati.

Le origini dati supportate per questa versione sono elencate di seguito.

## <a name="types"></a>Tipi 
### <a name="directory-vs-file"></a>Directory e file
File/Directory: scegliere un singolo file e leggerlo nella preparazione dei dati; il tipo di file viene analizzato per determinare i parametri predefiniti per la connessione file presente nella schermata successiva. Scegliere una directory o un set di file all'interno di una directory (la selezione file è di tipo multipla), usando i risultati dell'approccio dei file letti o come singolo flusso di dati con i file aggiunti l'uno all'altro (se necessario, con le intestazioni rimosse)

I tipi di file supportati sono:
- Testo delimitato (csv, tsv, txt e così via) 
- File a larghezza fissa
- Testo normale
- File JSON

### <a name="csv-file"></a>File CSV
Leggere un file con valori delimitati da virgole dalla risorsa di archiviazione

#### <a name="options"></a>Opzioni
- Separatore
- Commento
- Headers
- Separatore decimale
- Codifica file
- Righe da ignorare

### <a name="tsv-file"></a>File TSV
Leggere un file con valori delimitati da tabulazioni dalla risorsa di archiviazione

#### <a name="options"></a>Opzioni
- Commento
- Headers
- Codifica file
- Righe da ignorare

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
Leggere un file Excel, un solo foglio alla volta, specificando il nome o il numero del foglio

#### <a name="options"></a>Opzioni
- Nome/numero foglio
- Headers
- Righe da ignorare

### <a name="json-file"></a>File JSON
Leggere un file JSON dalla risorsa di archiviazione; si noti che il file è di tipo "flat" in lettura

#### <a name="options"></a>Opzioni
- Nessuno

### <a name="parquet"></a>Parquet
Leggere un set di dati Parquet: un singolo file o una cartella.

Il formato Parquet può avere forme diverse nella risorsa di archiviazione. Per i set di dati più piccoli viene usato talvolta un file ".parquet" singolo; svariate raccolte Python supportano la lettura/scrittura in singoli file ".parquet". Al momento AMLWB si basa sulla raccolta Python PyArrow per la lettura Parquet durante l'uso 'interattivo' locale. Supporta singoli file ".parquet" (se sono stati scritti come tali e non come parte di un set di dati più grande) e set di dati Parquet. Un set di dati Parquet è una raccolta di più file ".parquet", ciascuno dei quali rappresenta una partizione più piccola di un set di dati più grande. I set di dati sono in genere contenuti in una cartella e rappresentano il formato di output Parquet predefinito per piattaforme comuni come Spark e Hive.

>[!NOTE]
>Quando si leggono i dati Parquet presenti in una cartella con più file ".parquet", è più sicuro selezionare la directory per la lettura e quindi l'opzione relativa al set di dati Parquet. In questo modo PyArrow legge l'intera cartella anziché i singoli file, supportando la lettura di modalità più complesse di archiviazione Parquet sul disco, ad esempio nel caso del partizionamento di una cartella.**

L'esecuzione della scalabilità orizzontale si basa sulle funzionalità di lettura Parquet di Spark e supporta singoli file e cartelle, in modo analogo all'uso locale interattivo.

#### <a name="options"></a>Opzioni
- Set di dati Parquet
  - Questa opzione determina se AMLWB espanderà una determinata directory e tenterà di leggere singolarmente ogni file in essa contenuto (modalità non contrassegnata) o gestirà la directory come intero set di dati consentendo a PyArrow di individuare il modo migliore per interpretare i file (modalità contrassegnata).


## <a name="locations"></a>Località
### <a name="local"></a>Local
Unità disco rigido locale o posizione di archiviazione di rete mappata

### <a name="azure-blob"></a>BLOB Azure
Archiviazione di Azure (BLOB) richiede una sottoscrizione ad Azure

