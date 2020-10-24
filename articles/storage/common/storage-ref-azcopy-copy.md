---
title: copia di azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando di copia di azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f3d691692553a8201b3e3eccfaead82a2cbb9ca0
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92479698"
---
# <a name="azcopy-copy"></a>azcopy copy

Copia i dati di origine in un percorso di destinazione.

## <a name="synopsis"></a>Riepilogo

Copia i dati di origine in un percorso di destinazione. Le direzioni supportate sono:

  - < locale-> BLOB di Azure (autenticazione SAS o OAuth)
  - File di Azure < locale-> (autenticazione SAS condivisione/directory)
  - < locale-> Azure Data Lake Storage generazione 2 (firma di accesso condiviso, OAuth o chiave condivisa)
  - BLOB di Azure (SAS o Public)-> BLOB di Azure (autenticazione SAS o OAuth)
  - BLOB di Azure (SAS o Public)-> File di Azure (SAS)
  - File di Azure (SAS)-> File di Azure (SAS)
  - File di Azure (SAS)-> BLOB di Azure (autenticazione SAS o OAuth)
  - Amazon Web Services (AWS) S3 (chiave di accesso)-> BLOB in blocchi di Azure (autenticazione SAS o OAuth)

Per altre informazioni, vedere la sezione Esempi di questo articolo.

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire i dati con AzCopy e l'archiviazione BLOB](storage-use-azcopy-blobs.md)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Avanzato

AzCopy rileva automaticamente il tipo di contenuto dei file quando vengono caricati dal disco locale. AzCopy rileva il tipo di contenuto in base all'estensione o al contenuto del file (se non è specificata alcuna estensione).

La tabella di ricerca predefinita è di dimensioni ridotte, ma in UNIX viene aumentata in base ai file del sistema locale `mime.types` se sono disponibili con uno o più dei nomi seguenti:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

In Windows i tipi MIME vengono estratti dal registro di sistema. Questa funzionalità può essere disattivata con l'ausilio di un flag. Per altre informazioni, vedere la sezione flag di questo articolo.

Se si imposta una variabile di ambiente tramite la riga di comando, tale variabile sarà leggibile nella cronologia della riga di comando. Si consiglia di cancellare le variabili che contengono credenziali dalla cronologia della riga di comando. Per evitare che le variabili vengano visualizzate nella cronologia, è possibile utilizzare uno script per richiedere le credenziali dell'utente e per impostare la variabile di ambiente.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Esempi

Caricare un singolo file usando l'autenticazione OAuth. Se non è ancora stato effettuato l'accesso a AzCopy, eseguire il `azcopy login` comando prima di eseguire il comando seguente.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
Come sopra, ma questa volta, calcola anche l'hash MD5 del contenuto del file e lo salva come proprietà Content-MD5 del BLOB:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Caricare un singolo file usando un token SAS:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Caricare un singolo file usando un token di firma di accesso condiviso e un piping (solo BLOB in blocchi):
  
```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]
```

Caricare un'intera directory usando un token SAS:
  
```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

o

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive --put-md5
```

Caricare un set di file usando un token SAS e caratteri jolly (*):
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Caricare file e directory usando un token SAS e caratteri jolly (*):

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

Scaricare un singolo file usando l'autenticazione OAuth. Se non è ancora stato effettuato l'accesso a AzCopy, eseguire il `azcopy login` comando prima di eseguire il comando seguente.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Scaricare un singolo file usando un token di firma di accesso condiviso:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Scaricare un singolo file usando un token di firma di accesso condiviso e quindi inviare tramite pipe l'output a un file (solo BLOB in blocchi):
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

Scaricare un'intera directory usando un token di firma di accesso condiviso:
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

Nota sull'uso di un carattere jolly (*) negli URL:

Esistono solo due modi supportati per usare un carattere jolly in un URL. 

- È possibile utilizzarne uno immediatamente dopo la barra (/) finale di un URL. Questo utilizzo del carattere jolly copia tutti i file di una directory direttamente nella destinazione senza inserirli in una sottodirectory. 

- È anche possibile un carattere jolly nel nome di un contenitore, purché l'URL faccia riferimento solo a un contenitore e non a un BLOB. È possibile usare questo approccio per ottenere i file da un subset di contenitori. 

Scaricare il contenuto di una directory senza copiare la directory che lo contiene.
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

Scaricare un intero account di archiviazione.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

Scaricare un subset di contenitori in un account di archiviazione usando un carattere jolly (*) nel nome del contenitore.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

Copiare un singolo BLOB in un altro BLOB usando un token SAS.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Copiare un singolo BLOB in un altro BLOB usando un token di firma di accesso condiviso e un token di autenticazione. È necessario usare un token di firma di accesso condiviso alla fine dell'URL dell'account di origine, ma non ne è necessario l'account di destinazione se si accede a AzCopy con il `azcopy login` comando. 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Copiare una directory virtuale BLOB in un'altra usando un token SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Copiare tutti i contenitori BLOB, le directory e i BLOB dall'account di archiviazione a un altro usando un token SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Copiare un singolo oggetto nell'archivio BLOB da Amazon Web Services (AWS) S3 usando una chiave di accesso e un token di firma di accesso condiviso. Per prima cosa, impostare la `AWS_ACCESS_KEY_ID` variabile `AWS_SECRET_ACCESS_KEY` di ambiente e per l'origine AWS s3.
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Copiare un'intera directory nell'archivio BLOB da AWS S3 usando una chiave di accesso e un token di firma di accesso condiviso. Per prima cosa, impostare la `AWS_ACCESS_KEY_ID` variabile `AWS_SECRET_ACCESS_KEY` di ambiente e per l'origine AWS s3.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.htmlPer comprendere meglio il segnaposto [cartella], fare riferimento a.

Copiare tutti i bucket nell'archiviazione BLOB da Amazon Web Services (AWS) usando una chiave di accesso e un token di firma di accesso condiviso. Per prima cosa, impostare la `AWS_ACCESS_KEY_ID` variabile `AWS_SECRET_ACCESS_KEY` di ambiente e per l'origine AWS s3.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Copiare tutti i bucket nell'archiviazione BLOB da un'area Amazon Web Services (AWS) usando una chiave di accesso e un token di firma di accesso condiviso. Per prima cosa, impostare la `AWS_ACCESS_KEY_ID` variabile `AWS_SECRET_ACCESS_KEY` di ambiente e per l'origine AWS s3.
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Copiare un subset di bucket usando un carattere jolly (*) nel nome del bucket. Come gli esempi precedenti, sono necessari una chiave di accesso e un token di firma di accesso condiviso. Assicurarsi di impostare la variabile di ambiente `AWS_ACCESS_KEY_ID` e l' `AWS_SECRET_ACCESS_KEY` origine di AWS s3.

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

## <a name="options"></a>Opzioni

**--backup** Attiva SeBackupPrivilege di Windows per i caricamenti o SeRestorePrivilege per i download, in modo da consentire a AzCopy di visualizzare e leggere tutti i file, indipendentemente dalle autorizzazioni file system e di ripristinare tutte le autorizzazioni. Richiede che l'account che esegue AzCopy disponga già di queste autorizzazioni (ad esempio, dispone di diritti di amministratore o è un membro del `Backup Operators` gruppo). Questo flag attiva i privilegi già presenti nell'account.

**--BLOB-type** String definisce il tipo di BLOB nella destinazione. Viene usato per il caricamento di BLOB e per la copia tra gli account (impostazione predefinita `Detect` ). I valori validi includono `Detect`, `BlockBlob`, `PageBlob`e `AppendBlob`. Quando si esegue la copia tra gli account, un valore `Detect` fa sì che AzCopy usi il tipo di BLOB di origine per determinare il tipo di BLOB di destinazione. Quando si carica un file, `Detect` determina se il file è un disco rigido virtuale o un file VHDX basato sull'estensione del file. Se il file è un file VHD o VHDX, AzCopy considera il file come un BLOB di pagine. (valore predefinito "rileva")

**--Block-BLOB:** consente di caricare un BLOB in blocchi in archiviazione di Azure usando questo livello BLOB. (valore predefinito "None")

**--block-size-MB** float usa questa dimensione del blocco (specificata in MIB) durante il caricamento in archiviazione di Azure e il download da archiviazione di Azure. Il valore predefinito viene calcolato automaticamente in base alle dimensioni del file. Sono consentite frazioni decimali, ad esempio: 0,25.

**--Cache-Control** stringa che imposta l'intestazione Cache-Control. Restituito durante il download.

**--Check-length** Controllare la lunghezza di un file nella destinazione dopo il trasferimento. In caso di mancata corrispondenza tra origine e destinazione, il trasferimento viene contrassegnato come non riuscito. (il valore predefinito è `true` )

**--Check-MD5** stringa specifica il modo in cui devono essere convalidati gli hash MD5 durante il download. Disponibile solo durante il download. Opzioni disponibili: `NoCheck` , `LogOnly` , `FailIfDifferent` , `FailIfDifferentOrMissing` . (impostazione predefinita `FailIfDifferent` ) (valore predefinito "FailIfDifferent")

**--Content-Disposition** stringa ha impostato l'intestazione Content-Disposition. Restituito durante il download.

**--Content-Encoding** String imposta l'intestazione Content-Encoding. Restituito durante il download.

**--Content-Language** stringa ha impostato l'intestazione Content-Language. Restituito durante il download.

**--Content-Type** String specifica il tipo di contenuto del file. Implica no-gues-MIME-Type. Restituito durante il download.

**--Decomprimi** Decomprime automaticamente i file durante il download, se la codifica di contenuti indica che sono compressi. I valori supportati per la codifica del contenuto sono `gzip` e `deflate` . Le estensioni di file di `.gz` / `.gzip` o `.zz` non sono necessarie, ma verranno rimosse se presenti.

**--Exclude-Attributes** String (solo Windows) esclude i file i cui attributi corrispondono all'elenco di attributi. Ad esempio: A; S R

**--Exclude-BLOB-type** stringa consente di specificare il tipo di BLOB ( `BlockBlob` /  `PageBlob` /  `AppendBlob` ) da escludere durante la copia di BLOB dal contenitore o dall'account. L'uso di questo flag non è applicabile per la copia dei dati dal servizio non Azure al servizio. Più di un BLOB deve essere separato da `;` . 

**--Exclude-Path** String escludere questi percorsi durante la copia. Questa opzione non supporta i caratteri jolly (*). Controlla il prefisso del percorso relativo (ad esempio: `myFolder;myFolder/subDirName/file.pdf` ). Se usato in combinazione con l'attraversamento dell'account, i percorsi non includono il nome del contenitore.

**--Exclude-pattern** String escludere questi file durante la copia. Questa opzione supporta i caratteri jolly (*).

**--follow-collegamenti simbolici**  Seguire i collegamenti simbolici durante il caricamento da file system locali.

**--Force-if-sola lettura** Quando si sovrascrive un file esistente in Windows o File di Azure, forzare il funzionamento della sovrascrittura anche se per il file esistente è impostato l'attributo di sola lettura.

**--** la stringa from-to specifica facoltativamente la combinazione di destinazione di origine. Ad esempio: `LocalBlob` , `BlobLocal` , `LocalBlobFS` .

**--**  guida per la copia.

le stringhe **--include-after** includono solo i file modificati in o dopo la data/ora specificata. Il valore deve essere in formato ISO8601. Se non viene specificato alcun fuso orario, si presuppone che il valore si trovi nel fuso orario locale del computer che esegue AzCopy. ad esempio, `2020-08-19T15:04:00Z` per un'ora UTC o `2020-08-19` per la mezzanotte (00:00) nel fuso orario locale. Come in AzCopy 10,5, questo flag è valido solo per i file, non per le cartelle, quindi le proprietà delle cartelle non verranno copiate quando si usa questo flag con `--preserve-smb-info` o `--preserve-smb-permissions` .

**--include-Attributes** String (solo Windows) include i file i cui attributi corrispondono all'elenco di attributi. Ad esempio: A; S R

**--include-percorso** stringa include solo questi percorsi durante la copia. Questa opzione non supporta i caratteri jolly (*). Controlla il prefisso del percorso relativo (ad esempio: `myFolder;myFolder/subDirName/file.pdf` ).

**--include-pattern** String include solo questi file durante la copia. Questa opzione supporta i caratteri jolly (*). Separare i file usando `;` .

**--List-of-Versions** stringa specifica un file in cui ogni ID versione è elencato in una riga separata. Verificare che l'origine debba puntare a un singolo BLOB e che tutti gli ID versione specificati nel file con questo flag appartengano solo al BLOB di origine. AzCopy scaricherà le versioni specificate nella cartella di destinazione specificata. Per altre informazioni, vedere [scaricare le versioni precedenti di un BLOB](storage-use-azcopy-blobs.md#download-previous-versions-of-a-blob).

**--** la stringa a livello di log definisce il livello di dettaglio del log per il file di log, i livelli disponibili: info (tutte le richieste/risposte), avviso (risposte lente), errore (solo richieste non riuscite) e nessuno (nessun log di output). (impostazione predefinita `INFO` ). 

**--** la stringa di metadati carica in archiviazione di Azure con queste coppie chiave-valore come metadati.

**--No-gues-MIME-Type**  Impedisce a AzCopy di rilevare il tipo di contenuto in base all'estensione o al contenuto del file.

**--Sovrascrivi** stringa sovrascrive i file e i BLOB in conflitto nella destinazione se questo flag è impostato su true. (impostazione predefinita `true` ) I valori possibili sono `true` ,, `false` `prompt` e `ifSourceNewer` . Per le destinazioni che supportano le cartelle, le proprietà a livello di cartella in conflitto verranno sovrascritte questo flag è `true` o se viene fornita una risposta positiva al prompt. (valore predefinito "true")

**--page-blob** caricare il BLOB di pagine in archiviazione di Azure usando questo livello BLOB. (impostazione predefinita `None` ). (valore predefinito "None")

**--Preserve-Ultima modifica-ora**  Disponibile solo quando la destinazione è file system.

**--Preserve-Owner**    Ha un effetto solo nei download e solo quando `--preserve-smb-permissions` viene usato. Se true (impostazione predefinita), il proprietario e il gruppo del file vengono conservati nei download. Se è impostato su false, `--preserve-smb-permissions` manterrà gli ACL, ma Owner e Group saranno basati sull'utente che esegue AzCopy (impostazione predefinita: true)

**--Preserve-SMB-info**   False per impostazione predefinita. Conserva le informazioni sulle proprietà SMB (ora dell'ultima scrittura, ora di creazione, bit degli attributi) tra le risorse in grado di riconoscere SMB (Windows e File di Azure). Verranno trasferiti solo i bit di attributo supportati da File di Azure; tutti gli altri verranno ignorati. Questo flag si applica sia a file che a cartelle, a meno che non sia specificato un filtro di solo file (ad esempio, include-pattern). Le informazioni trasferite per le cartelle sono identiche a quelle per i file, ad eccezione dell'ora dell'ultima scrittura che non viene mai conservata per le cartelle.

**--Preserve-SMB-autorizzazioni**   False per impostazione predefinita. Conserva gli ACL SMB tra le risorse compatibili (Windows e File di Azure). Per i download, sarà necessario anche il `--backup` flag per ripristinare le autorizzazioni in cui il nuovo proprietario non sarà l'utente che esegue AzCopy. Questo flag si applica a file e cartelle, a meno che non sia specificato un filtro di solo file (ad esempio, `include-pattern` ).

**--put-MD5**    Creare un hash MD5 di ogni file e salvare l'hash come proprietà Content-MD5 del BLOB o del file di destinazione. Per impostazione predefinita, l'hash non viene creato. Disponibile solo durante il caricamento.

**--ricorsivo**    Esaminare le sottodirectory in modo ricorsivo durante il caricamento da file system locali.

**--S2S-Detect-source-modificato**   Rilevare se il BLOB o il file di origine viene modificato durante la lettura. Questo parametro è valido solo per le copie da servizio a servizio, perché il controllo corrispondente è abilitato in modo permanente per il caricamento e il download.

**--S2S-handle-non valido-la stringa di metadati** specifica il modo in cui vengono gestite le chiavi di metadati non valide. Opzioni disponibili: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (impostazione predefinita `ExcludeIfInvalid` ). (valore predefinito "ExcludeIfInvalid")

**--S2S-Preserve-livello di accesso**   Mantenere il livello di accesso durante la copia da servizio a servizio. Vedere [archiviazione BLOB di Azure: livelli di accesso ad accesso frequente, ad accesso sporadico e archivio](/azure/storage/blobs/storage-blob-storage-tiers) per assicurarsi che l'account di archiviazione di destinazione supporti l'impostazione del livello di accesso. Nei casi in cui l'impostazione del livello di accesso non è supportata, usare s2sPreserveAccessTier = false per ignorare la copia del livello di accesso. (impostazione predefinita `true` ).  (valore predefinito "true")

**--S2S-Preserve-proprietà**   Mantieni le proprietà complete durante la copia da servizio a servizio. Per AWS S3 e l'origine file non singolo file di Azure, l'operazione list non restituisce proprietà complete di oggetti e file. Per mantenere le proprietà complete, AzCopy deve inviare un'altra richiesta per oggetto o file. (valore predefinito true)

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

**--Cap-Mbps float**   Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.

**--output-** formato stringa di tipo dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è `text`. (impostazione predefinita "testo")

**--trusted-Microsoft-suffissi** stringa specifica i suffissi di dominio aggiuntivi in cui è possibile inviare i token di accesso Azure Active Directory.  Il valore predefinito è `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`. Tutti gli elencati qui vengono aggiunti al valore predefinito. Per la sicurezza, è consigliabile inserire qui solo Microsoft Azure domini. Separare più voci con un punto e virgola.

## <a name="see-also"></a>Vedi anche

- [azcopy](storage-ref-azcopy.md)
