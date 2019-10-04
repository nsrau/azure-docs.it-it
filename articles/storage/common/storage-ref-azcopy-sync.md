---
title: sincronizzazione azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy Sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: fb6c3b711a89ae7e4ef403a75927c4c6172523d0
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195763"
---
# <a name="azcopy-sync"></a>sincronizzazione azcopy

Replica il percorso di origine nel percorso di destinazione.

## <a name="synopsis"></a>Sinossi

Per il confronto vengono usate le ore dell'Ultima modifica. Il file viene ignorato se l'ora dell'Ultima modifica della destinazione è più recente.

Le coppie supportate sono:

- BLOB di Azure locale < > (è possibile usare l'autenticazione SAS o OAuth)

Il comando di sincronizzazione differisce dal comando copy in diversi modi:

  1. Il flag ricorsivo è on per impostazione predefinita.
  2. L'origine e la destinazione non devono contenere modelli, ad esempio * o?.
  3. I flag di inclusione ed esclusione possono essere un elenco di criteri che corrispondono ai nomi dei file. Per illustrazione, vedere la sezione di esempio.
  4. Se nella destinazione sono presenti file o BLOB che non sono presenti nell'origine, all'utente verrà richiesto di eliminarli.

     Questa richiesta può essere silenziata usando i flag corrispondenti per rispondere automaticamente alla domanda di eliminazione.

### <a name="advanced"></a>Avanzate

AzCopy rileva automaticamente il tipo di contenuto dei file durante il caricamento dal disco locale, in base all'estensione o al contenuto del file (se non è specificata alcuna estensione).

La tabella di ricerca incorporata è di dimensioni ridotte, ma in UNIX è aumentata dal file MIME. Types del sistema locale, se disponibile con uno o più dei nomi seguenti:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

In Windows i tipi MIME vengono estratti dal registro di sistema.

```azcopy
azcopy sync [flags]
```

## <a name="examples"></a>Esempi

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

> [!NOTE]
> Se i flag di inclusione/esclusione vengono utilizzati insieme, verranno esaminati solo i file che corrispondono ai modelli di inclusione, ma quelli corrispondenti ai modelli di esclusione verrebbero sempre ignorati.

## <a name="options"></a>Opzioni

|Opzione|Descrizione|
|--|--|
|--block-size-MB float|Usare questa dimensione del blocco (specificata in MiB) durante il caricamento in archiviazione di Azure o il download da archiviazione di Azure. Il valore predefinito viene calcolato automaticamente in base alle dimensioni del file. Sono consentite frazioni decimali, ad esempio: 0,25).|
|--Check-MD5 stringa|Specifica il modo in cui devono essere convalidati gli hash MD5 durante il download. Questa opzione è disponibile solo durante il download. I valori disponibili includono: NOCHECK, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (valore predefinito "FailIfDifferent").|
|--Delete-stringa di destinazione|definisce se eliminare i file aggiuntivi dalla destinazione che non sono presenti nell'origine. Potrebbe essere impostato su true, false o prompt. Se viene impostata la richiesta, all'utente verrà inviata una domanda prima di pianificare i file e i BLOB per l'eliminazione. (valore predefinito "false").|
|--Escludi stringa|Escludere i file in cui il nome corrisponde all'elenco di modelli. Ad esempio: *. jpg;* . PDF; exactname.|
|-h, --help|Mostra il contenuto della Guida per il comando di sincronizzazione.|
|--Includi stringa|Includere solo i file in cui il nome corrisponde all'elenco di modelli. Ad esempio: *. jpg;* . PDF; exactname.|
|--stringa a livello di log|Definire il livello di dettaglio del log per il file di log, livelli disponibili: INFO (tutte le richieste/risposte), avviso (risposte lente), errore (solo richieste non riuscite) e nessuno (nessun log di output). (impostazione predefinita "INFO").|
|--Put-MD5|Creare un hash MD5 di ogni file e salvare l'hash come proprietà Content-MD5 del BLOB o del file di destinazione. Per impostazione predefinita, l'hash non viene creato. Disponibile solo durante il caricamento.|
|--ricorsivo|True per impostazione predefinita, esaminare le sottodirectory in modo ricorsivo durante la sincronizzazione tra le directory. (valore predefinito true).|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|DESCRIZIONE|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
