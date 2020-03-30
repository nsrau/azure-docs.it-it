---
title: azcopy remove Documenti Microsoft
description: In questo articolo vengono fornite informazioni di riferimento per il comando azcopy remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: abce1acb88e920c0de7bbb6447ec9d838f10486c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033989"
---
# <a name="azcopy-remove"></a>azcopy remove

Eliminare BLOB o file da un account di archiviazione di Azure.Delete blobs or files from an Azure storage account.

## <a name="synopsis"></a>Riepilogo

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOBTransfer data with AzCopy and Blob storage](storage-use-azcopy-blobs.md)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Esempi

Rimuovere un singolo BLOB con la creazione di accesso sAS:Remove a single blob with SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Rimuovere un'intera directory virtuale con una server di accesso sAS:Remove an entire virtual directory with a SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Rimuovere solo i BLOB principali all'interno di una directory virtuale ma non le relative sottodirectory:Remove only the top blobs inside a virtual directory but not its sub-directories:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Rimuovere un sottoinsieme di BLOB in una directory virtuale (ad esempio: solo file jpg e pdf o se il nome del BLOB è "exactName"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Rimuovere un'intera directory virtuale, ma escludere determinati BLOB dall'ambito (ad esempio: ogni BLOB che inizia con foo o termina con barra):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Rimuovere BLOB e directory virtuali specifici inserendo i relativi percorsi (non con codifica URL) in un file:Remove specific blobs and virtual directories by putting their relative paths (NOT-url-encoded) in a file:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Rimuovere un singolo file da un account di archiviazione BLOB con uno spazio dei nomi gerarchico (includere/escludere non supportato).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Rimuovere una singola directory un account di archiviazione BLOB con uno spazio dei nomi gerarchico (includere/escludere non supportato):Remove a single directory a Blob Storage account that has a hierarchical namespace (include/exclude not supported):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opzioni

**stringa --exclude-path**      Escludere questi percorsi durante la rimozione. Questa opzione non supporta i caratteri jolly (-). Controlla il prefisso del percorso relativo. Ad esempio: myFolder;myFolder/subDirName/file.pdf.

**--exclude-pattern** stringa Escludi i file in cui il nome corrisponde all'elenco dei modelli. Ad esempio: *.jpg;*. pdf;nomeesatto

**-h, --aiuto** aiuto per rimuovere

**--include-path** stringa Include solo questi percorsi durante la rimozione. Questa opzione non supporta i caratteri jolly (-). Controlla il prefisso del percorso relativo. Ad esempio: cartella;cartellaPersonale/NomeDir/file.pdf

**--include-pattern** stringa Includere solo i file in cui il nome corrisponde all'elenco dei modelli. Ad esempio: *.jpg;*. pdf;nomeesatto

**--list-of-files** string Definisce la posizione di un file che contiene l'elenco di file e directory da eliminare. I percorsi relativi devono essere delimitati da interruzioni di riga e i percorsi NON devono essere codificati in URL.

**--log-level** string Definire il livello di dettaglio del log per il file di log. I livelli disponibili includono: INFO(tutte le richieste/risposte), AVVISO(risposte lente), ERRORE (solo richieste non riuscite) e NONE (nessun log di output). (predefinito 'INFO') (impostazione predefinita "INFO")

**--ricorsivo**                Esaminare le sottodirectory in modo ricorsivo durante la sincronizzazione tra le directory.

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--cap-mbps uint32|Limita la velocità di trasferimento, in megabit al secondo. La velocità effettiva momento per momento potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le opzioni disponibili includono: text, json. Il valore predefinito è "text".|

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
