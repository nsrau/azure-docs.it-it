---
title: copia di azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando di copia di azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c15d188e333bea5e74fa65d2bbdf38ae7fadc246
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195737"
---
# <a name="azcopy-copy"></a>copia di azcopy

Copia i dati di origine in un percorso di destinazione.

## <a name="synopsis"></a>Sinossi

Le direzioni supportate sono:

- < locale-> BLOB di Azure (autenticazione SAS o OAuth)
- < locale-> file di Azure (autenticazione SAS condivisione/directory)
- < locale-> ADLS gen 2 (autenticazione SAS, OAuth o SharedKey)
- BLOB di Azure (SAS o Public) < > BLOB di Azure (autenticazione SAS o OAuth)
- File di Azure (SAS)-> BLOB in blocchi di Azure (autenticazione SAS o OAuth)
- AWS s3 (chiave di accesso)-> BLOB in blocchi di Azure (autenticazione SAS o OAuth)

Per ulteriori informazioni, fare riferimento agli esempi.

### <a name="advanced"></a>Avanzate

AzCopy rileva automaticamente il tipo di contenuto dei file durante il caricamento dal disco locale, in base all'estensione o al contenuto del file (se non è specificata alcuna estensione).

La tabella di ricerca predefinita è di dimensioni ridotte, ma in UNIX viene aumentata in base ai file MIME. Types del sistema locale, se disponibili con uno o più dei nomi seguenti:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

In Windows i tipi MIME vengono estratti dal registro di sistema. Questa funzionalità può essere disattivata con l'ausilio di un flag. Vedere la sezione flag.

> [!IMPORTANT]
> Se si imposta una variabile di ambiente tramite la riga di comando, tale variabile sarà leggibile nella cronologia della riga di comando. Si consiglia di cancellare le variabili che contengono credenziali dalla cronologia della riga di comando. Per evitare che le variabili vengano visualizzate nella cronologia, è possibile utilizzare uno script per richiedere le credenziali dell'utente e per impostare la variabile di ambiente.

```azcopy
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Esempi

Caricare un singolo file usando l'autenticazione OAuth.

Se non è ancora stato effettuato l'accesso a AzCopy, `azcopy login` usare il comando prima di eseguire il comando seguente.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Come sopra, ma questa volta calcola anche l'hash MD5 del contenuto del file e lo salva come proprietà Content-MD5 del BLOB:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Caricare un singolo file con una firma di accesso condiviso:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Caricare un singolo file con una firma di accesso condiviso usando il piping (solo BLOB in blocchi):

```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Caricare un'intera directory con una firma di accesso condiviso:

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

oppure

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --put-md5
```

Caricare un set di file con una firma di accesso condiviso usando caratteri jolly:

```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Caricare file e directory con una firma di accesso condiviso usando caratteri jolly:

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Scaricare un singolo file usando l'autenticazione OAuth.

Se non è ancora stato effettuato l'accesso a AzCopy, `azcopy login` usare il comando prima di eseguire il comando seguente.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Scaricare un singolo file con una firma di accesso condiviso:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Scaricare un singolo file con una firma di accesso condiviso usando il piping (solo BLOB in blocchi):

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
```

Scaricare un'intera directory con una firma di accesso condiviso:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true
```

Scaricare un set di file con una firma di accesso condiviso usando caratteri jolly:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir"
```

Scaricare file e directory con una firma di accesso condiviso usando caratteri jolly:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir" --recursive=true
```

Copiare un singolo BLOB con SAS in un altro BLOB con SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Copiare un singolo BLOB con firma di accesso condiviso in un altro BLOB con token OAuth.

Se non è ancora stato effettuato l'accesso a AzCopy, `azcopy login` usare il comando prima di eseguire il comando seguente. Il token OAuth viene usato per accedere all'account di archiviazione di destinazione.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Copiare un'intera directory dalla directory virtuale BLOB con SAS a un'altra directory virtuale BLOB con SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Copiare tutti i dati dell'account da un account BLOB con firma di accesso condiviso a un altro account BLOB con SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Copiare un singolo oggetto da S3 con una chiave di accesso a un BLOB con SAS:

Impostare la variabile di ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY per l'origine S3.

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Copiare un'intera directory da S3 con la chiave di accesso in una directory virtuale BLOB con SAS:

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Vedere https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html per informazioni su cosa significa [cartella] per S3. Impostare la variabile di ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY per l'origine S3.

Copiare tutti i bucket nel servizio S3 con la chiave di accesso all'account BLOB con SAS:

Impostare la variabile di ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY per l'origine S3.

```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Copiare tutti i bucket in un'area S3 con una chiave di accesso all'account BLOB con SAS:

```azcopy
azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Impostare la variabile di ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY per l'origine S3.

## <a name="options"></a>Opzioni

|Opzione|Descrizione|
|--|--|
|--BLOB-tipo stringa|Definisce il tipo di BLOB nella destinazione. Viene usato per il caricamento di BLOB e per la copia tra gli account (valore predefinito "None").|
|--Block-BLOB-livello stringa|Caricare un BLOB in blocchi in archiviazione di Azure usando questo livello BLOB. (valore predefinito "None").|
|--block-size-MB float |Usare questa dimensione del blocco (specificata in MiB) durante il caricamento in archiviazione di Azure e il download da archiviazione di Azure. Il valore predefinito viene calcolato automaticamente in base alle dimensioni del file. Sono consentite frazioni decimali, ad esempio: 0,25).|
|--cache-Control stringa|Impostare l'intestazione Cache-Control. Restituito durante il download.|
|--Check-MD5 stringa|Specifica il modo in cui devono essere convalidati gli hash MD5 durante il download. Disponibile solo durante il download. Opzioni disponibili: NOCHECK, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (valore predefinito "FailIfDifferent")|
|--Content-Disposition stringa|Impostare l'intestazione Content-Disposition. Restituito durante il download.|
|--Content-Encoding stringa|Impostare l'intestazione Content-Encoding. Restituito durante il download.|
|--Content-Language stringa|Impostare l'intestazione Content-Language. Restituito durante il download.|
|--Content-Type stringa |Specifica il tipo di contenuto del file. Implica no-gues-MIME-Type. Restituito durante il download.|
|--Escludi stringa|Escludere questi file durante la copia. Supporto dell'utilizzo di *.|
|--Exclude-BLOB-type stringa|Facoltativamente specifica il tipo di BLOB (BlockBlob/PageBlob/AppendBlob) da escludere durante la copia di BLOB dal contenitore o dall'account. L'uso di questo flag non è applicabile per la copia dei dati dal servizio non Azure al servizio. Più di un BLOB deve essere separato da';'.|
|--follow-collegamenti simbolici|Seguire i collegamenti simbolici durante il caricamento da file system locali.|
|--da a stringa|Specifica facoltativamente la combinazione di destinazione di origine. Di seguito è riportato un esempio: LocalBlob, BlobLocal, LocalBlobFS.|
|-h, --help|Visualizza il contenuto della Guida per il comando copia. |
|--stringa a livello di log|Definire il livello di dettaglio del log per il file di log, livelli disponibili: INFO (tutte le richieste/risposte), avviso (risposte lente), errore (solo richieste non riuscite) e nessuno (nessun log di output). (impostazione predefinita "INFO").|
|--stringa di metadati|Caricare in archiviazione di Azure con queste coppie chiave-valore come metadati.|
|--No-gues-MIME-Type|Impedisce a AzCopy di rilevare il tipo di contenuto in base all'estensione o al contenuto del file.|
|--Sovrascrivi|Sovrascrivere i BLOB o i file in conflitto nella destinazione se questo flag è impostato su true. (valore predefinito true).|
|--Page-stringa di livello BLOB |Caricare il BLOB di pagine in archiviazione di Azure usando questo livello BLOB. (valore predefinito "None").|
|--Preserve-Ultima modifica-ora|Disponibile solo quando la destinazione è file system.|
|--Put-MD5|creare un hash MD5 di ogni file e salvare l'hash come proprietà Content-MD5 del BLOB o del file di destinazione. Per impostazione predefinita, l'hash non viene creato. Disponibile solo durante il caricamento.|
|--ricorsivo|Esaminare in modo ricorsivo le sottodirectory durante il caricamento da file system locali.|
|--S2S-Detect-source-modificato|Controllare se l'origine è stata modificata dopo l'enumerazione. Per la copia S2S, come origine è una risorsa remota, per verificare se l'origine è stata modificata, è necessario richiedere ulteriori costi di richiesta.|
|--S2S-handle-non valido-stringa di metadati |Specifica il modo in cui vengono gestite le chiavi di metadati non valide. Opzioni disponibili: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (valore predefinito "ExcludeIfInvalid").|
|--S2S-Preserve-livello di accesso|Mantenere il livello di accesso durante la copia da servizio a servizio. Per verificare che l'account di archiviazione di destinazione supporti l'impostazione del livello di accesso, vedere [archiviazione BLOB di Azure: livelli di accesso ad accesso frequente, ad accesso sporadico e archivio](../blobs/storage-blob-storage-tiers.md) . Nei casi in cui l'impostazione del livello di accesso non è supportata, usare s2sPreserveAccessTier = false per ignorare la copia del livello di accesso.  (valore predefinito true).|
|--S2S-Preserve-proprietà|Mantieni le proprietà complete durante la copia da servizio a servizio. Per la S3 e l'origine file non singolo file di Azure, poiché l'operazione list non restituisce proprietà complete di oggetti e file, per mantenere le proprietà complete AzCopy deve inviare un'altra richiesta per oggetto e file. (valore predefinito true).|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|DESCRIZIONE|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
