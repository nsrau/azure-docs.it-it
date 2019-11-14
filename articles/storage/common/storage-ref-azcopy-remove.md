---
title: azcopy rimuovere | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: abce1acb88e920c0de7bbb6447ec9d838f10486c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033989"
---
# <a name="azcopy-remove"></a>azcopy remove

Eliminare i BLOB o i file da un account di archiviazione di Azure.

## <a name="synopsis"></a>Sinossi

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire i dati con AzCopy e l'archiviazione BLOB](storage-use-azcopy-blobs.md)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>esempi

Rimuovere un singolo BLOB con SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Rimuovere un'intera directory virtuale con una firma di accesso condiviso:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Rimuovere solo i BLOB principali all'interno di una directory virtuale ma non le relative sottodirectory:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Rimuovere un subset di BLOB in una directory virtuale (ad esempio: solo file jpg e PDF o se il nome del BLOB è "exactname"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Rimuovere un'intera directory virtuale, ma escludere determinati BLOB dall'ambito, ad esempio: ogni BLOB che inizia con foo o termina con la barra:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Rimuovere i BLOB e le directory virtuali specifici inserendo i percorsi relativi (non codificati in URL) in un file:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Rimuovere un singolo file da un account di archiviazione BLOB con uno spazio dei nomi gerarchico (inclusione/esclusione non supportata).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Rimuovere una singola directory un account di archiviazione BLOB con uno spazio dei nomi gerarchico (includere/escludere non supportato):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opzioni

**--Exclude-percorso stringa**      Escludere questi percorsi durante la rimozione. Questa opzione non supporta i caratteri jolly (*). Controlla il prefisso del percorso relativo. Ad esempio: fileFolder; MyFile/subDirName/file. pdf.

**--Exclude-pattern** String esclude i file in cui il nome corrisponde all'elenco di modelli. Ad esempio: *. jpg;* . PDF; exactname

**-h,--** guida per la rimozione

**--include-percorso** stringa include solo questi percorsi durante la rimozione. Questa opzione non supporta i caratteri jolly (*). Controlla il prefisso del percorso relativo. Ad esempio: cartella fileFolder, cartella/subDirName/file. pdf

**--include-pattern** String include solo i file in cui il nome corrisponde all'elenco di modelli. Ad esempio: *. jpg;* . PDF; exactname

**--List-of-files** String definisce il percorso di un file che contiene l'elenco di file e directory da eliminare. I percorsi relativi devono essere delimitati da interruzioni di riga e i percorsi non devono essere codificati in URL.

**--** la stringa a livello di log definisce il livello di dettaglio del log per il file di log. I livelli disponibili includono: INFO (tutte le richieste/risposte), avviso (risposte lente), errore (solo richieste non riuscite) e nessuno (nessun log di output). (valore predefinito ' INFO ') (impostazione predefinita "INFO")

**--ricorsivo**                Esaminare in modo ricorsivo le sottodirectory durante la sincronizzazione tra le directory.

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|DESCRIZIONE|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
