---
title: sincronizzazione azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy Sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 9bfe88c34c78d18f2f5aefb8ae6946b9786030ad
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023505"
---
# <a name="azcopy-sync"></a>azcopy sync

Replica il percorso di origine nel percorso di destinazione.

## <a name="synopsis"></a>Sinossi

Per il confronto vengono usate le ore dell'Ultima modifica. Il file viene ignorato se l'ora dell'Ultima modifica della destinazione è più recente.

Le coppie supportate sono:

- BLOB di Azure locale < > (è possibile usare l'autenticazione SAS o OAuth)
- < BLOB di Azure: BLOB di Azure > (l'origine deve includere una firma di accesso condiviso o è accessibile pubblicamente, è possibile usare l'autenticazione SAS o OAuth per la destinazione)
- File < di Azure: > file di Azure (l'origine deve includere una firma di accesso condiviso o è accessibile pubblicamente; Usare l'autenticazione SAS per la destinazione

Il comando di sincronizzazione differisce dal comando copy in diversi modi:

1. Per impostazione predefinita, il flag ricorsivo è true e Sync copia tutte le sottodirectory. Sync copia solo i file di primo livello all'interno di una directory se il flag ricorsivo è false.
2. Quando si esegue la sincronizzazione tra directory virtuali, aggiungere una barra finale al percorso (vedere gli esempi) se è presente un BLOB con lo stesso nome di una delle directory virtuali.
3. Se il flag ' deleteDestination ' è impostato su true o prompt, Sync eliminerà i file e i BLOB nella destinazione che non sono presenti nell'origine.

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire i dati con AzCopy e l'archiviazione BLOB](storage-use-azcopy-blobs.md)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Avanzate

Se non si specifica un'estensione di file, AzCopy rileva automaticamente il tipo di contenuto dei file durante il caricamento dal disco locale, in base all'estensione o al contenuto del file (se non è specificata alcuna estensione).

La tabella di ricerca incorporata è di dimensioni ridotte, ma in UNIX è aumentata dal file MIME. Types del sistema locale, se disponibile con uno o più dei nomi seguenti:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

In Windows i tipi MIME vengono estratti dal registro di sistema.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>esempi

Sincronizzare un singolo file:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Come sopra, ma questa volta, calcola anche l'hash MD5 del contenuto del file e lo salva come proprietà Content-MD5 del BLOB:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Sincronizzare un'intera directory, incluse le relative sottodirectory (si noti che ricorsivo è attiva per impostazione predefinita):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

oppure

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Sincronizzare solo i primi file all'interno di una directory, ma non le relative sottodirectory:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Sincronizzare un subset di file in una directory (ad esempio, solo file jpg e PDF o se il nome del file è "exactname"):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

Sincronizzare un'intera directory, escludendo alcuni file dall'ambito (ad esempio, tutti i file che iniziano con foo o terminano con la barra):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

Sincronizzare un singolo BLOB:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"

Sync a virtual directory:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Sincronizzare una directory virtuale con lo stesso nome di un BLOB (aggiungere una barra finale al percorso per evitare ambiguità):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Sincronizzare una directory di file di Azure (stessa sintassi del BLOB):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Se i flag di inclusione/esclusione vengono utilizzati insieme, verranno esaminati solo i file che corrispondono ai modelli di inclusione, ma quelli corrispondenti ai modelli di esclusione verrebbero sempre ignorati.

## <a name="options"></a>Opzioni

**--block-size-MB** float usa questa dimensione del blocco (specificata in MIB) durante il caricamento in archiviazione di Azure o il download da archiviazione di Azure. Il valore predefinito viene calcolato automaticamente in base alle dimensioni del file. Sono consentite frazioni decimali, ad esempio: 0,25.

**--Check-MD5** stringa specifica il modo in cui devono essere convalidati gli hash MD5 durante il download. Questa opzione è disponibile solo durante il download. I valori disponibili includono: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (valore predefinito ' FailIfDifferent '). (valore predefinito "FailIfDifferent")

**--Delete-Destination** String definisce se eliminare i file aggiuntivi dalla destinazione che non sono presenti nell'origine. Potrebbe essere impostato su true, false o prompt. Se viene impostata la richiesta, all'utente verrà inviata una domanda prima di pianificare i file e i BLOB per l'eliminazione. (valore predefinito ' false '). (valore predefinito "false")

**--Exclude-Attributes** String (solo Windows) escludere i file i cui attributi corrispondono all'elenco di attributi. Ad esempio: A; S R

**--Exclude-pattern** String esclude i file in cui il nome corrisponde all'elenco di modelli. Ad esempio: *. jpg;* . PDF; exactname

**-h,--** Guida alla sincronizzazione

**--include-gli attributi** stringa (solo Windows) includono solo i file i cui attributi corrispondono all'elenco di attributi. Ad esempio: A; S R

**--include-pattern** String include solo i file in cui il nome corrisponde all'elenco di modelli. Ad esempio: *. jpg;* . PDF; exactname

**--** la stringa a livello di log definisce il livello di dettaglio del log per il file di log, i livelli disponibili: info (tutte le richieste e risposte), avviso (risposte lente), errore (solo richieste non riuscite) e nessuno (nessun log di output). (informazioni predefinite). (impostazione predefinita "INFO")

**--put-MD5**                     Creare un hash MD5 di ogni file e salvare l'hash come proprietà Content-MD5 del BLOB o del file di destinazione. Per impostazione predefinita, l'hash non viene creato. Disponibile solo durante il caricamento.

**--ricorsivo**                   True per impostazione predefinita, esaminare le sottodirectory in modo ricorsivo durante la sincronizzazione tra le directory. (valore predefinito true). (valore predefinito true)

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|DESCRIZIONE|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
