---
title: sincronizzazione azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy Sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: de97640ab462ac8ea7342d235d0fad802c232179
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783369"
---
# <a name="azcopy-sync"></a>azcopy sync

Replica il percorso di origine nel percorso di destinazione.

## <a name="synopsis"></a>Riepilogo

Per il confronto vengono usate le ore dell'Ultima modifica. Il file viene ignorato se l'ora dell'Ultima modifica della destinazione è più recente.

Le coppie supportate sono:

- BLOB di Azure locale < > (è possibile usare l'autenticazione SAS o OAuth)
- < BLOB di Azure: BLOB di Azure > (l'origine deve includere una firma di accesso condiviso o è accessibile pubblicamente, è possibile usare l'autenticazione SAS o OAuth per la destinazione)
- File < di Azure: > file di Azure (l'origine deve includere una firma di accesso condiviso o è accessibile pubblicamente; Usare l'autenticazione SAS per la destinazione

Il comando di sincronizzazione differisce dal comando copy in diversi modi:

1. Per impostazione predefinita, il flag ricorsivo è true e Sync copia tutte le sottodirectory. Sync copia solo i file di primo livello all'interno di una directory se il flag ricorsivo è false.
2. Quando si esegue la sincronizzazione tra directory virtuali, aggiungere una barra finale al percorso (vedere gli esempi) se è presente un BLOB con lo stesso nome di una delle directory virtuali.
3. Se il `deleteDestination` flag è impostato su true o prompt, Sync eliminerà i file e i BLOB nella destinazione che non sono presenti nell'origine.

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

## <a name="examples"></a>Esempi

Sincronizzare un singolo file:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Come sopra, ma anche per calcolare un hash MD5 del contenuto del file e quindi salvare tale hash MD5 come proprietà Content-MD5 del BLOB. 

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Sincronizzare un'intera directory, incluse le relative sottodirectory (si noti che per impostazione predefinita l'opzione è ricorsiva):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

oppure

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Sincronizzare solo i file all'interno di una directory, ma non le sottodirectory o i file all'interno delle sottodirectory:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Sincronizzare un subset di file in una directory (ad esempio, solo file jpg e PDF o se il nome del file è `exactName` ):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include-pattern="*.jpg;*.pdf;exactName"
```

Sincronizzare un'intera directory escludendo alcuni file dall'ambito (ad esempio, tutti i file che iniziano con foo o terminano con la barra):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude-pattern="foo*;*bar"
```

Sincronizzare un singolo BLOB:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Sincronizzare una directory virtuale:

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

**--block-size-MB** float usa questa dimensione del blocco (specificata in MIB) durante il caricamento in archiviazione di Azure o il download da archiviazione di Azure. Il valore predefinito viene calcolato automaticamente in base alle dimensioni del file. Sono consentite frazioni decimali, ad esempio `0.25` .

**--Check-MD5** stringa specifica il modo in cui devono essere convalidati gli hash MD5 durante il download. Questa opzione è disponibile solo durante il download. I valori disponibili sono: `NoCheck` ,, `LogOnly` `FailIfDifferent` , `FailIfDifferentOrMissing` . (impostazione predefinita `FailIfDifferent` ). (impostazione predefinita `FailIfDifferent` )

**--Delete-Destination** String definisce se eliminare i file aggiuntivi dalla destinazione che non sono presenti nell'origine. Potrebbe essere impostato su `true` , `false` o `prompt` . Se impostato su `prompt` , all'utente verrà inviata una domanda prima di pianificare i file e i BLOB per l'eliminazione. (impostazione predefinita `false` ). (impostazione predefinita `false` )

**--Exclude-Attributes** String (solo Windows) esclude i file i cui attributi corrispondono all'elenco di attributi. ad esempio `A;S;R`

**--Exclude-Path** String esclude questi percorsi quando si confronta l'origine con la destinazione. Questa opzione non supporta i caratteri jolly (*). Controlla il prefisso del percorso relativo (ad esempio: `myFolder;myFolder/subDirName/file.pdf` ).

**--Exclude-pattern** String esclude i file in cui il nome corrisponde all'elenco di modelli. ad esempio `*.jpg;*.pdf;exactName`

**--**    Guida alla sincronizzazione.

**--include-Attributes** String (solo Windows) include solo i file con attributi corrispondenti all'elenco di attributi. ad esempio `A;S;R`

**--include-pattern** String include solo i file in cui il nome corrisponde all'elenco di modelli. ad esempio `*.jpg;*.pdf;exactName`

**--** la stringa a livello di log definisce il livello di dettaglio del log per il file di log, i livelli disponibili: `INFO` (tutte le richieste e risposte), `WARNING` (risposte lente), `ERROR` (solo richieste non riuscite) e `NONE` (nessun log di output). (impostazione predefinita `INFO` ). 

**--Preserve-SMB-info**   False per impostazione predefinita. Conserva le informazioni sulle proprietà SMB (ora dell'ultima scrittura, ora di creazione, bit degli attributi) tra le risorse in grado di riconoscere SMB (Windows e File di Azure). Questo flag si applica sia a file che a cartelle, a meno che non sia specificato un filtro di solo file (ad esempio, include-pattern). Le informazioni trasferite per le cartelle sono identiche a quelle dei file, ad eccezione dell'ora dell'ultima scrittura non conservate per le cartelle.

**--Preserve-SMB-autorizzazioni**   False per impostazione predefinita. Conserva gli ACL SMB tra le risorse compatibili (Windows e File di Azure). Questo flag si applica a file e cartelle, a meno che non sia specificato un filtro di solo file (ad esempio, `include-pattern` ).

**--put-MD5**     Creare un hash MD5 di ogni file e salvare l'hash come proprietà Content-MD5 del BLOB o del file di destinazione. Per impostazione predefinita, l'hash non viene creato. Disponibile solo durante il caricamento.

**--ricorsivo** `True` per impostazione predefinita, esaminare le sottodirectory in modo ricorsivo durante la sincronizzazione tra le directory.     (impostazione predefinita `True` ). 

**--S2S-Preserve-livello di accesso**  Mantenere il livello di accesso durante la copia da servizio a servizio. Vedere [archiviazione BLOB di Azure: livelli di accesso ad accesso frequente, ad accesso sporadico e archivio](../blobs/storage-blob-storage-tiers.md) per assicurarsi che l'account di archiviazione di destinazione supporti l'impostazione del livello di accesso. Nei casi in cui l'impostazione del livello di accesso non è supportata, usare s2sPreserveAccessTier = false per ignorare la copia del livello di accesso. (impostazione predefinita `true` ). 

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|
|--trusted-Microsoft-suffissi stringa   |Specifica i suffissi di dominio aggiuntivi in cui è possibile inviare i token di accesso Azure Active Directory.  Il valore predefinito è' *. Core.Windows.NET;* . core.chinacloudapi.cn; *. Core.cloudapi.de;* . core.usgovcloudapi.net ". Tutti gli elencati qui vengono aggiunti al valore predefinito. Per la sicurezza, è consigliabile inserire qui solo Microsoft Azure domini. Separare più voci con un punto e virgola.|

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)