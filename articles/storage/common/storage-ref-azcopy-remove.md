---
title: azcopy rimuovere | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 1cdb49f6865afa4101468dc35b4e416d999b63f5
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285221"
---
# <a name="azcopy-remove"></a>azcopy remove

Eliminare i BLOB o i file da un account di archiviazione di Azure.

## <a name="synopsis"></a>Riepilogo

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire i dati con AzCopy e l'archiviazione BLOB](storage-use-azcopy-blobs.md)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Esempi

Rimuovere un singolo BLOB usando un token SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Rimuovere un'intera directory virtuale usando un token SAS:
 
```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Rimuovere solo i BLOB all'interno di una directory virtuale, ma non rimuovere le sottodirectory o i BLOB all'interno di tali sottodirectory:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Rimuovere un subset di BLOB in una directory virtuale (ad esempio: rimuovere solo i file jpg e PDF o se il nome del BLOB è `exactName` ):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

Rimuovere un'intera directory virtuale ma escludere determinati BLOB dall'ambito (ad esempio, ogni BLOB che inizia con foo o termina con la barra):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

Rimuovere i BLOB e le directory virtuali specifici inserendo i percorsi relativi (non codificati in URL) in un file:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
- file content:
    dir1/dir2
    blob1
    blob2
```

Rimuovere un singolo file da un account di archiviazione BLOB con uno spazio dei nomi gerarchico (includere/escludere non supportato):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Rimuovere una singola directory da un account di archiviazione BLOB con uno spazio dei nomi gerarchico (includere/escludere non supportato):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opzioni

**--Delete-Snapshots** stringa per impostazione predefinita, l'operazione di eliminazione ha esito negativo se un BLOB contiene snapshot. Specificare `include` per rimuovere il BLOB radice e tutti i relativi snapshot; in alternativa, specificare `only` per rimuovere solo gli snapshot, mantenendo però il BLOB radice.

**--Exclude-Path** stringa escludere questi percorsi durante la rimozione. Questa opzione non supporta i caratteri jolly (*). Controlla il prefisso del percorso relativo. ad esempio `myFolder;myFolder/subDirName/file.pdf`

**--Exclude-pattern** String esclude i file in cui il nome corrisponde all'elenco di modelli. Ad esempio: `*.jpg` ; `*.pdf` ;`exactName`

**--Force-if-sola lettura**   Quando si elimina un file o una cartella File di Azure, forzare l'eliminazione a funzionare anche se per l'oggetto esistente è impostato l'attributo di sola lettura.

**--** guida per la rimozione.

**--include-percorso** stringa include solo questi percorsi durante la rimozione. Questa opzione non supporta i caratteri jolly (*). Controlla il prefisso del percorso relativo. ad esempio `myFolder;myFolder/subDirName/file.pdf`

**--include-pattern** String include solo i file in cui il nome corrisponde all'elenco di modelli. Ad esempio: * `.jpg` ;* `.pdf` ;`exactName`

**--List-of-files** String definisce il percorso di un file che contiene l'elenco di file e directory da eliminare. I percorsi relativi devono essere delimitati da interruzioni di riga e i percorsi non devono essere codificati in URL.

**--** la stringa a livello di log definisce il livello di dettaglio del log per il file di log. I livelli disponibili includono: `INFO` (tutte le richieste/risposte), `WARNING` (risposte lente), `ERROR` (solo richieste non riuscite) e `NONE` (nessun log di output). (impostazione predefinita `INFO` ) (impostazione predefinita `INFO` )

**--ricorsivo**    Esaminare le sottodirectory in modo ricorsivo durante la sincronizzazione tra le directory.

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--Cap-Mbps float|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|
|--trusted-Microsoft-suffissi stringa   |Specifica i suffissi di dominio aggiuntivi in cui è possibile inviare i token di accesso Azure Active Directory.  Il valore predefinito è'*. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net ". Tutti gli elencati qui vengono aggiunti al valore predefinito. Per la sicurezza, è consigliabile inserire qui solo Microsoft Azure domini. Separare più voci con un punto e virgola.|

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
