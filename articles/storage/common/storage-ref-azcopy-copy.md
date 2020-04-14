---
title: copia di azcopy Documenti Microsoft
description: In questo articolo vengono fornite informazioni di riferimento per il comando azcopy copy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0325a71fb069f3d96f05d106afac1639fc38fe42
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253340"
---
# <a name="azcopy-copy"></a>azcopy copy

Copia i dati di origine in un percorso di destinazione.

## <a name="synopsis"></a>Riepilogo

Copia i dati di origine in un percorso di destinazione. Le indicazioni supportate sono:

  - BLOB di Azure < locale (SAS o autenticazione OAuth)local <-> Azure Blob (SAS or OAuth authentication)
  - File di Azure di < > locale (autenticazione sAS di condivisione/directory)local <-> Azure Files (Share/directory SAS authentication)
  - locale <-> ADLS Gen 2 (autenticazione SAS, OAuth o SharedKey)
  - BLOB di Azure (SAS o pubblico)-> Blob di Azure (autenticazione SAS o OAuth)Azure Blob (SAS or public) -> Azure Blob (SAS or OAuth authentication)
  - BLOB di Azure (SAS o pubblico) -> file di Azure (SAS)Azure Blob (SAS or public) -> Azure Files (SAS)
  - File di Azure (SAS) -> file di Azure (SAS)Azure Files (SAS) -> Azure Files (SAS)
  - File di Azure (SAS) -> BLOB di Azure (autenticazione SAS o OAuth)Azure Files (SAS) -> Azure Blob (SAS or OAuth authentication)
  - AWS S3 (chiave di accesso) -> BLOB blocchi di Azure (autenticazione SAS o OAuth)

Si prega di fare riferimento agli esempi per ulteriori informazioni.

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOBTransfer data with AzCopy and Blob storage](storage-use-azcopy-blobs.md)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Avanzate

AzCopy rileva automaticamente il tipo di contenuto dei file durante il caricamento dal disco locale, in base all'estensione del file o al contenuto (se non viene specificata alcuna estensione).

La tabella di ricerca incorporata è piccola, ma in Unix, è aumentata dai file mime.types del sistema locale, se disponibili con uno o più di questi nomi:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

In Windows, i tipi MIME vengono estratti dal Registro di sistema. Questa funzione può essere disattivata con l'aiuto di una bandiera. Si prega di fare riferimento alla sezione bandiera.

Se si imposta una variabile di ambiente tramite la riga di comando, tale variabile sarà leggibile nella cronologia della riga di comando. Prendere in considerazione la cancellazione delle variabili che contengono le credenziali dalla cronologia della riga di comando. Per impedire la visualizzazione delle variabili nella cronologia, è possibile utilizzare uno script per richiedere all'utente le credenziali e impostare la variabile di ambiente.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Esempi

Caricare un singolo file utilizzando l'autenticazione OAuth. Se non è ancora stato effettuato l'accesso ad AzCopy, eseguire il comando azcopy login prima di eseguire il comando seguente.

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"

Come sopra, ma questa volta anche calcolare l'hash MD5 del contenuto del file e salvarlo come proprietà Content-MD5 del BLOB:

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5

Caricare un singolo file usando un token di firma di accesso condiviso:Upload a single file by using a SAS token:

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Caricare un singolo file usando un token di firma di accesso condiviso e un piping (solo BLOB bloccati):Upload a single file by using a SAS token and piping (block blobs only):
  
- cat "/path/to/file.txt" azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Caricare un'intera directory usando un token di firma di accesso condiviso:Upload an entire directory by using a SAS token:
  
- azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --recursive-true

o

- azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --recursive-true --put-md5

Caricare un set di file usando un token di firma di accesso condiviso e caratteri jolly (-):

- azcopy cp "/path/*foo/* bar/'.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]"

Caricare file e directory utilizzando un token di firma di accesso condiviso e caratteri jolly (-):

- azcopy cp "/path/*foo/* bar" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --recursive-true

Scaricare un singolo file utilizzando l'autenticazione OAuth. Se non è ancora stato effettuato l'accesso ad AzCopy, eseguire il comando azcopy login prima di eseguire il comando seguente.

- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"

Scaricare un singolo file usando un token di firma di accesso condiviso:Download a single file by using a SAS token:

- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "/percorso/a/file.txt"

Scaricare un singolo file usando un token di firma di accesso condiviso e quindi eseguire il piping dell'output in un file (solo BLOB bloccati):Download a single file by using a SAS token and then piping the output to a file (block blobs only):
  
- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" > "/path/to/file.txt"

Scaricare un'intera directory usando un token di firma di accesso condiviso:Download an entire directory by using a SAS token:
  
- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" "/path/to/dir" --recursive

Nota sull'utilizzo di un carattere jolly (-) negli URL:

Esistono solo due modi supportati per utilizzare un carattere jolly in un URL. 

- È possibile utilizzarne uno subito dopo la barra finale (/) di un URL. In questo modo tutti i file di una directory vengono copiati direttamente nella destinazione senza inserirli in una sottodirectory.

- È anche possibile usarne uno nel nome di un contenitore, purché l'URL faccia riferimento solo a un contenitore e non a un BLOB. È possibile utilizzare questo approccio per ottenere file da un sottoinsieme di contenitori.

Scaricare il contenuto di una directory senza copiare la directory che lo contiene.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[percorso/cartella]/? [SAS]" "/percorso/a/dir"

Scaricare un intero account di archiviazione.

- azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive

Scaricare un sottoinsieme di contenitori all'interno di un account di archiviazione usando un simbolo con caratteri jolly (-) nel nome del contenitore.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container-name]" "/path/to/dir" --recursive

Copiare un singolo BLOB in un altro BLOB usando un token di firma di accesso condiviso.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Copiare un singolo BLOB in un altro BLOB usando un token di firma di accesso condiviso e un token OAuth. È necessario usare un token di firma di accesso condiviso alla fine dell'URL dell'account di origine, ma l'account di destinazione non ne ha bisogno se si accede ad AzCopy usando il comando azcopy login. 

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"

Copiare una directory virtuale BLOB in un'altra usando un token di firma di accesso condiviso:Copy one blob virtual directory to another by using a SAS token:

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --recursive-true

Copiare tutti i contenitori BLOB, le directory e i BLOB dall'account di archiviazione a un altro usando un token di firma di accesso condiviso:Copy all blob containers, directories, and blobs from storage account to another by using a SAS token:

- azcopy cp "https://[srcaccount].blob.core.windows.net? [SAS]" "https://[destaccount].blob.core.windows.net? [SAS]" --recursive-true

Copiare un singolo oggetto nell'archivio BLOB da Amazon Web Services (AWS) S3 utilizzando una chiave di accesso e un token di firma di accesso condiviso. Innanzitutto, imposta la variabile di ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY per l'origine AWS S3.
  
- azcopy cphttps://s3.amazonaws.com/" [bucket]/[oggetto]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Copia un'intera directory nell'archivio BLOB da AWS S3 utilizzando una chiave di accesso e un token di firma di accesso condiviso. Innanzitutto, imposta la variabile di ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY per l'origine AWS S3.

- azcopy cphttps://s3.amazonaws.com/" [bucket]/[cartella]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --recursive-true

Si prega https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html di fare riferimento a comprendere meglio il segnaposto [cartella].

Copiare tutti i bucket nell'archivio BLOB da Amazon Web Services (AWS) utilizzando una chiave di accesso e un token di firma di accesso condiviso. Innanzitutto, imposta la variabile di ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY per l'origine AWS S3.

- azcopy cphttps://s3.amazonaws.com/" " "" "https://[destaccount].blob.core.windows.net? [SAS]" --recursive-true

Copiare tutti i bucket nell'archivio BLOB da un'area Amazon Web Services (AWS) utilizzando una chiave di accesso e un token di firma di accesso condiviso. Innanzitutto, imposta la variabile di ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY per l'origine AWS S3.

- azcopy cphttps://s3-" [region].amazonaws.com/" "https://[destaccount].blob.core.windows.net? [SAS]" --recursive-true

Copiare un sottoinsieme di bucket utilizzando un simbolo di carattere jolly (-) nel nome del bucket. Come gli esempi precedenti, sono necessari una chiave di accesso e un token di firma di accesso condiviso. Assicurati di impostare la variabile di ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY per l'origine AWS S3.

- azcopy cphttps://s3.amazonaws.com/" [bucket:nome]/" "https://[destaccount].blob.core.windows.net? [SAS]" --recursive-true

## <a name="options"></a>Opzioni

**--backup (backup)**                               Attiva SeBackupPrivilege di Windows per i caricamenti o SeRestorePrivilege per i download, per consentire ad AzCopy di visualizzare tutti i file, indipendentemente dalle autorizzazioni del file system, e per ripristinare tutte le autorizzazioni. Richiede che l'account che esegue AzCopy disponga già di queste autorizzazioni (ad esempio, dispone dei diritti di amministratore o è un membro del gruppo 'Backup Operators'). Tutto questo flag fa è attivare i privilegi che l'account ha già.

**stringa di tipo --blob** Definisce il tipo di BLOB nella destinazione. Viene usato per il caricamento di BLOB e durante la copia tra account (impostazione predefinita 'Rileva'). I valori validi includono 'Detect', 'BlockBlob', 'PageBlob' e 'AppendBlob'. Durante la copia tra account, il valore 'Detect' fa sì che AzCopy usi il tipo di BLOB di origine per determinare il tipo di BLOB di destinazione. Quando si carica un file, 'Rileva' determina se il file è un disco rigido virtuale o un file VHDX in base all'estensione del file. Se il file è un file VHD o VHDX, AzCopy considera il file come BLOB di pagine. (predefinito "Rileva")

**--block-blob-tier** string Carica i BLOB direttamente nel livello di [accesso](../blobs/storage-blob-storage-tiers.md) di tua scelta. (impostazione predefinita 'Nessuno'). I valori validi includono 'None', 'Hot', 'Cool' e 'Archive'. Se viene passato 'Nessuno' o nessun livello, il BLOB erediterà il livello dell'account di archiviazione.

**--block-size-mb** float Usare questa dimensione del blocco (specificata in MiB) durante il caricamento in Archiviazione di Azure e il download da Archiviazione di Azure.--block-size-mb float Use this block size (specified in MiB) when uploading to Azure Storage, and downloading from Azure Storage. Il valore predefinito viene calcolato automaticamente in base alle dimensioni del file. Sono consentite frazioni decimali (ad esempio: 0,25).

**--cache-control** string Imposta l'intestazione del controllo della cache. Restituito al download.

**--check-lunghezza**                         Controllare la lunghezza di un file nella destinazione dopo il trasferimento. Se si verifica una mancata corrispondenza tra origine e destinazione, il trasferimento viene contrassegnato come non riuscito. (impostazione predefinita true)

**--check-md5** string Specifica in che modo gli heheM5 devono essere convalidati durante il download. Disponibile solo durante il download. Opzioni disponibili: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (impostazione predefinita "FailIfDifferent")

**--content-disposition** string Imposta l'intestazione content-disposition. Restituito al download.

**--content-encoding** string Imposta l'intestazione content-encoding. Restituito al download.

**--content-language** string Imposta l'intestazione content-language. Restituito al download.

**--content-type** string Specifica il tipo di contenuto del file. Implica no-guess-mime-type. Restituito al download.

**--decomprimere**                           Decomprimi automaticamente i file durante il download, se la loro codifica del contenuto indica che sono compressi. I valori di codifica del contenuto supportati sono 'gzip' e 'deflate'. Le estensioni di file di '.gz'/'.gzip' o '.zz' non sono necessarie, ma verranno rimosse se presenti.

**--exclude-attributes** string (solo Windows) Esclude i file i cui attributi corrispondono all'elenco di attributi. Ad esempio: A; S; R

**--exclude-blob-type** string Specifica facoltativamente il tipo di BLOB (BlockBlob/ PageBlob/ AppendBlob) da escludere durante la copia dei BLOB dal contenitore o dall'account. L'utilizzo di questo flag non è applicabile per la copia di dati da non azure-service a service. Più BLOB devono essere separati da ';'.

**--exclude-path** stringa Escludi questi percorsi durante la copia. Questa opzione non supporta i caratteri jolly (-). Controlla il prefisso del percorso relativo(Ad esempio: myFolder;myFolder/subDirName/file.pdf). Se utilizzati in combinazione con l'attraversamento dell'account, i percorsi non includono il nome del contenitore.

**--exclude-pattern** stringa Escludere questi file durante la copia. Questa opzione supporta i caratteri jolly

**--follow-symlinks**                      Seguire i collegamenti simbolici durante il caricamento dal file system locale.

**--from-to-a** string Specifica facoltativamente la combinazione di destinazione di origine. Ad esempio: LocalBlob, BlobLocal, LocalBlobFS.For Example: LocalBlob, BlobLocalLocal, LocalBlobFS.

**-h, --aiuto** aiuto per la copia

**--include-attributes** string (solo Windows) Include i file i cui attributi corrispondono all'elenco di attributi. Ad esempio: A; S; R

**--include-path** stringa Include solo questi percorsi durante la copia. Questa opzione non supporta i caratteri jolly (-). Controlla il prefisso del percorso relativo (ad esempio: myFolder;myFolder/subDirName/file.pdf).

**--include-pattern** stringa Includere solo questi file durante la copia. Questa opzione supporta i caratteri jolly (-). Separare i file utilizzando un ';'.

**--log-level** string Definire il livello di dettaglio del log per il file di log, i livelli disponibili: INFO(tutte le richieste/risposte), AVVISO(risposte lente), ERRORE (solo richieste non riuscite) e NONE(nessun log di output). (impostazione predefinita "INFO")

**--stringa dei metadati** Carica in Archiviazione di Azure con queste coppie chiave-valore come metadati.

**--no-guess-mime-tipo**                   Impedisce ad AzCopy di rilevare il tipo di contenuto in base all'estensione o al contenuto del file.

**--overwrite** string Sovrascrivendo i file e i BLOB in conflitto nella destinazione se questo flag è impostato su true. I valori possibili includono 'true', 'false', 'ifSourceNewer' e 'prompt'. (predefinito "true")

**--page-blob-tier** stringa Caricare il BLOB di pagine in Archiviazione di Azure usando questo livello BLOB. (impostazione predefinita "Nessuno")

**--preserve-last-modified-time**          Disponibile solo quando la destinazione è il file system.

**--preserve-smb-permissions** stringa False per impostazione predefinita. Mantiene gli ACL SMB tra risorse in grado di riconoscere (Windows e File di Azure). Per i download, è inoltre `--backup` necessario utilizzare il flag per ripristinare le autorizzazioni in cui il nuovo proprietario non sarà l'utente che esegue AzCopy. Questo flag si applica sia ai file che alle cartelle, `include-pattern`a meno che non venga specificato un filtro di solo file (ad esempio ).

**--preserve-smb-info** stringa False per impostazione predefinita. Mantiene le informazioni sulle proprietà SMB (ora dell'ultima scrittura, ora di creazione, bit di attributo) tra le risorse in grado di riconoscere SMB (Windows e file di Azure). Verranno trasferiti solo i bit di attributo supportati da File di Azure. tutti gli altri verranno ignorati. Questo flag si applica sia ai file che alle cartelle, a meno che non venga specificato un filtro di solo file (ad esempio, include-pattern). Le informazioni trasferite per le cartelle sono le stesse di quella dei file, ad eccezione dell'ora dell'ultima scrittura che non viene mai conservata per le cartelle.

**--preserve-owner**                       Ha effetto solo quando si scaricano i `--preserve-smb-permissions` dati e solo quando si utilizza l'oggetto . Se true (impostazione predefinita), il proprietario e il gruppo del file vengono conservati nei download. Se questo flag è `--preserve-smb-permissions` impostato su false, manterrà comunque gli ACL, ma Il proprietario e il gruppo saranno basati sull'utente che esegue AzCopy.

**--put-md5**                             Creare un hash MD5 di ogni file e salvare l'hash come proprietà Content-MD5 del BLOB o del file di destinazione. (Per impostazione predefinita l'hash NON viene creato.) Disponibile solo durante il caricamento.

**--ricorsivo**                            Esaminare le sottodirectory in modo ricorsivo durante il caricamento dal file system locale.

**--s2s-detect-source-changed**           Controllare se l'origine è stata modificata dopo l'enumerazione.

**--s2s-handle-invalid-metadata** string Specifica la modalità di gestione delle chiavi di metadati non valide. Opzioni disponibili: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (impostazione predefinita "ExcludeIfInvalid")

**--s2s-preserve-access-tier**             Mantenere il livello di accesso durante la copia del servizio. Fare riferimento all'archiviazione BLOB di Azure: livelli di [accesso a caldo, ad accesso a freddo e ad archivio](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) per assicurarsi che l'account di archiviazione di destinazione supporti l'impostazione del livello di accesso. Nei casi in cui l'impostazione del livello di accesso non è supportata, utilizzare s2sPreserveAccessTier-false per ignorare la copia del livello di accesso. (impostazione predefinita true)

**--s2s-preserve-properties**              Mantenere le proprietà complete durante la copia del servizio. Per AWS S3 e Azure File origine file non singola, l'operazione di elenco non restituisce le proprietà complete di oggetti e file. Per mantenere le proprietà complete, AzCopy deve inviare una richiesta aggiuntiva per ogni oggetto o file. (impostazione predefinita true)

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

**--cap-mbps uint32**      Limita la velocità di trasferimento, in megabit al secondo. La velocità effettiva momento per momento potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.

**--output-type** string Format dell'output del comando. Le opzioni disponibili includono: text, json. Il valore predefinito è 'text'. (predefinito "testo")

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
