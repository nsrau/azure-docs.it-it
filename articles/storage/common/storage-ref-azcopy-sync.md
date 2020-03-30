---
title: sincronizzazione azcopy Documenti Microsoft
description: Questo articolo fornisce informazioni di riferimento per il comando azcopy sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 1bff46c8584934ab8bcffce74763edc8363533d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988244"
---
# <a name="azcopy-sync"></a>azcopy sync

Replica il percorso di origine nel percorso di destinazione.

## <a name="synopsis"></a>Riepilogo

Gli ultimi tempi modificati vengono utilizzati per il confronto. Il file viene ignorato se l'ora dell'ultima modifica nella destinazione è più recente.

Le coppie supportate sono:

- BLOB di Azure <-> locale (è possibile usare l'autenticazione SAS o OAuth)local <-> Azure Blob (either SAS or OAuth authentication can be used)
- BLOB di Azure <-> BLOB di Azure (l'origine deve includere una sAS o è accessibile pubblicamente; è possibile usare l'autenticazione SAS o OAuth per la destinazione)Azure Blob <-> Azure Blob (Source must include a SAS or is publicly accessible; either SAS or OAuth authentication can be used for destination)
- File di Azure < > file di Azure (l'origine deve includere una configurazione di accesso utente o è accessibile pubblicamente. L'autenticazione SAS deve essere usata per la destinazione)SAS authentication should be used for destination)

Il comando sync è diverso dal comando copy in diversi modi:

1. Per impostazione predefinita, il flag ricorsivo è true e la sincronizzazione copia tutte le sottodirectory. La sincronizzazione copia i file di primo livello all'interno di una directory solo se il flag ricorsivo è false.
2. Durante la sincronizzazione tra directory virtuali, aggiungere una barra finale al percorso (fare riferimento a esempi) se è presente un BLOB con lo stesso nome di una delle directory virtuali.
3. Se il flag 'deleteDestination' è impostato su true o prompt, la sincronizzazione eliminerà i file e i BLOB nella destinazione che non sono presenti nell'origine.

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOBTransfer data with AzCopy and Blob storage](storage-use-azcopy-blobs.md)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Avanzate

Se non specifichi un'estensione di file, AzCopy rileva automaticamente il tipo di contenuto dei file durante il caricamento dal disco locale, in base all'estensione o al contenuto del file (se non viene specificata alcuna estensione).

La tabella di ricerca incorporata è piccola, ma in Unix, è aumentata dai file mime.types del sistema locale, se disponibili con uno o più di questi nomi:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

In Windows, i tipi MIME vengono estratti dal Registro di sistema.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Esempi

Sincronizzare un singolo file:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Come sopra, ma questa volta, anche calcolare l'hash MD5 del contenuto del file e salvarlo come proprietà Content-MD5 del BLOB:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Sincronizzare un'intera directory, incluse le relative sottodirectory (si noti che ricorsivo è attivato per impostazione predefinita):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

o

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Sincronizzare solo i file principali all'interno di una directory ma non le relative sottodirectory:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Sincronizzare un sottoinsieme di file in una directory (ad esempio: solo file jpg e pdf, o se il nome del file è "exactName"):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

Sincronizzare un'intera directory, ma escludere alcuni file dall'ambito (ad esempio: ogni file che inizia con foo o termina con barra):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

Sincronizzare un singolo BLOB:Sync a single blob:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Sincronizzare una directory virtuale:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Sincronizzare una directory virtuale con lo stesso nome di un BLOB (aggiungere una barra finale al percorso per evitare ambiguità):Sync a virtual directory that has the same name as a blob (add a trailing slash to the path in order to disambiguate):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Sincronizzare una directory File di Azure (stessa sintassi del BLOB):Sync an Azure File directory (same syntax as Blob):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Se i flag include/exclude vengono utilizzati insieme, verranno esaminati solo i file che corrispondono ai modelli di inclusione, ma quelli corrispondenti ai modelli di esclusione verranno sempre ignorati.

## <a name="options"></a>Opzioni

**--block-size-mb** float Usare questa dimensione del blocco (specificata in MiB) durante il caricamento in Archiviazione di Azure o il download da Archiviazione di Azure.--block-size-mb float Use this block size (specified in MiB) when uploading to Azure Storage or downloading from Azure Storage. Il valore predefinito viene calcolato automaticamente in base alle dimensioni del file. Sono consentite frazioni decimali (ad esempio: 0,25).

**--check-md5** string Specifica in che modo gli heheM5 devono essere convalidati durante il download. Questa opzione è disponibile solo durante il download. I valori disponibili includono: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (impostazione predefinita 'FailIfDifferent'). (impostazione predefinita "FailIfDifferent")

**--delete-destination** string Definisce se eliminare file aggiuntivi dalla destinazione che non sono presenti nell'origine. Potrebbe essere impostato su true, false o prompt. Se impostato su prompt, all'utente verrà posta una domanda prima di pianificare file e BLOB per l'eliminazione. (predefinito 'false'). (predefinito "false")

**--exclude-attributes** string (solo Windows) Esclude i file i cui attributi corrispondono all'elenco di attributi. Ad esempio: A; S; R

**--exclude-path** stringa Escludi questi percorsi durante la copia. Questa opzione non supporta i caratteri jolly (-). Controlla il prefisso del percorso relativo(Ad esempio: myFolder;myFolder/subDirName/file.pdf). Se utilizzati in combinazione con l'attraversamento dell'account, i percorsi non includono il nome del contenitore.

**--exclude-pattern** stringa Escludi i file in cui il nome corrisponde all'elenco dei modelli. Ad esempio: \*.jpg; \*.pdf;nomeesatto

**-h, --aiuto** aiuto per la sincronizzazione

**--include-attributes** string (solo Windows) Include solo i file i cui attributi corrispondono all'elenco di attributi. Ad esempio: A; S; R

**--include-pattern** stringa Includere solo i file in cui il nome corrisponde all'elenco dei modelli. Ad esempio: \*.jpg; \*.pdf;nomeesatto

**--log-level** string Definire il livello di dettaglio del log per il file di log, i livelli disponibili: INFO(tutte le richieste e le risposte), AVVISO(risposte lente), ERRORE (solo richieste non riuscite) e NONE(nessun log di output). (INFO predefinito). (impostazione predefinita "INFO")

**--put-md5**                     Creare un hash MD5 di ogni file e salvare l'hash come proprietà Content-MD5 del BLOB o del file di destinazione. (Per impostazione predefinita l'hash NON viene creato.) Disponibile solo durante il caricamento.

**--ricorsivo**                   True per impostazione predefinita, esaminare le sottodirectory in modo ricorsivo durante la sincronizzazione tra le directory. (impostazione predefinita true). (impostazione predefinita true)

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--cap-mbps uint32|Limita la velocità di trasferimento, in megabit al secondo. La velocità effettiva momento per momento potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le opzioni disponibili includono: text, json. Il valore predefinito è "text".|

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
