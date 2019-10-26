---
title: copia di azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando di copia di azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 78482b5d7013ffa3bbb0a34dd04c8c48626dc77a
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72926661"
---
# <a name="azcopy-copy"></a>azcopy copy

Copia i dati di origine in un percorso di destinazione.

## <a name="synopsis"></a>Sinossi

Copia i dati di origine in un percorso di destinazione. Le direzioni supportate sono:

  - < locale-> BLOB di Azure (autenticazione SAS o OAuth)
  - File di Azure < locale-> (autenticazione SAS condivisione/directory)
  - < locale-> ADLS gen 2 (autenticazione SAS, OAuth o SharedKey)
  - BLOB di Azure (SAS o Public)-> BLOB di Azure (autenticazione SAS o OAuth)
  - BLOB di Azure (SAS o Public)-> File di Azure (SAS)
  - File di Azure (SAS)-> File di Azure (SAS)
  - File di Azure (SAS)-> BLOB di Azure (autenticazione SAS o OAuth)
  - AWS s3 (chiave di accesso)-> BLOB in blocchi di Azure (autenticazione SAS o OAuth)

Per ulteriori informazioni, fare riferimento agli esempi.

## <a name="advanced"></a>Funzionalità avanzate

AzCopy rileva automaticamente il tipo di contenuto dei file durante il caricamento dal disco locale, in base all'estensione o al contenuto del file (se non è specificata alcuna estensione).

La tabella di ricerca predefinita è di dimensioni ridotte, ma in UNIX viene aumentata in base ai file MIME. Types del sistema locale, se disponibili con uno o più dei nomi seguenti:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

In Windows i tipi MIME vengono estratti dal registro di sistema. Questa funzionalità può essere disattivata con l'ausilio di un flag. Vedere la sezione flag.

Se si imposta una variabile di ambiente tramite la riga di comando, tale variabile sarà leggibile nella cronologia della riga di comando. Si consiglia di cancellare le variabili che contengono credenziali dalla cronologia della riga di comando. Per evitare che le variabili vengano visualizzate nella cronologia, è possibile utilizzare uno script per richiedere le credenziali dell'utente e per impostare la variabile di ambiente.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>esempi

Caricare un singolo file usando l'autenticazione OAuth. Se non è ancora stato effettuato l'accesso a AzCopy, eseguire il comando AzCopy login prima di eseguire il comando seguente.

- azcopy CP "/Path/to/file.txt" "https:///[account]. blob. Core. Windows. NET/[contenitore]/[path/to/BLOB]"

Come sopra, ma questa volta calcola anche l'hash MD5 del contenuto del file e lo salva come proprietà Content-MD5 del BLOB:

- azcopy CP "/Path/to/file.txt" "https:///[account]. blob. Core. Windows. NET/[Container]/[path/to/BLOB]"--put-MD5

Caricare un singolo file usando un token SAS:

- azcopy CP "/Path/to/file.txt" "https:///[account]. blob. Core. Windows. NET/[Container]/[path/to/BLOB]? [SAS] "

Caricare un singolo file usando un token di firma di accesso condiviso e un piping (solo BLOB in blocchi):
  
- Cat "/Path/to/file.txt" | azcopy CP "https:///[account]. blob. Core. Windows. NET/[Container]/[path/to/BLOB]? [SAS] "

Caricare un'intera directory usando un token SAS:
  
- azcopy CP "/Path/to/dir" "https:///[account]. blob. Core. Windows. NET/[Container]/[path/to/directory]? [SAS] "--ricorsivo = true

Oppure

- azcopy CP "/Path/to/dir" "https:///[account]. blob. Core. Windows. NET/[Container]/[path/to/directory]? [SAS] "--ricorsivo = true--put-MD5

Caricare un set di file usando un token SAS e caratteri jolly (*):

- azcopy CP "/Path/*foo/* bar/*. pdf" "https://[account]. blob. Core. Windows. NET/[Container]/[path/to/directory]? [SAS] "

Caricare file e directory usando un token SAS e caratteri jolly (*):

- azcopy CP "/Path/*foo/* bar *" "https:///[account]. blob. Core. Windows. NET/[Container]/[path/to/directory]? [SAS] "--ricorsivo = true

Scaricare un singolo file usando l'autenticazione OAuth. Se non è ancora stato effettuato l'accesso a AzCopy, eseguire il comando AzCopy login prima di eseguire il comando seguente.

- azcopy CP "https:///[account]. blob. Core. Windows. NET/[Container]/[path/to/BLOB]" "/Path/to/file.txt"

Scaricare un singolo file usando un token di firma di accesso condiviso:

- azcopy CP "https:///[account]. blob. Core. Windows. NET/[Container]/[path/to/BLOB]? [SAS] ""/Path/to/file.txt "

Scaricare un singolo file usando un token di firma di accesso condiviso e quindi inviare tramite pipe l'output a un file (solo BLOB in blocchi):
  
- azcopy CP "https:///[account]. blob. Core. Windows. NET/[Container]/[path/to/BLOB]? [SAS] ">"/Path/to/file.txt "

Scaricare un'intera directory usando un token di firma di accesso condiviso:
  
- azcopy CP "https:///[account]. blob. Core. Windows. NET/[contenitore]/[percorso/a/directory]? [SAS] ""/Path/to/dir "--ricorsivo = true

Nota sull'uso di un carattere jolly (*) negli URL:

Esistono solo due modi supportati per usare un carattere jolly in un URL. 

- È possibile utilizzarne uno immediatamente dopo la barra (/) finale di un URL. In questo modo vengono copiati tutti i file di una directory direttamente nella destinazione senza inserirli in una sottodirectory.

- È anche possibile utilizzarne uno nel nome di un contenitore purché l'URL faccia riferimento solo a un contenitore e non a un BLOB. È possibile usare questo approccio per ottenere i file da un subset di contenitori.

Scaricare il contenuto di una directory senza copiare la directory che lo contiene.

- azcopy CP "https:///[srcaccount]. blob. Core. Windows. NET/[contenitore]/[percorso/a/cartella]/*? [SAS] ""/Path/to/dir "

Scaricare un intero account di archiviazione.

- azcopy CP "https:///[srcaccount]. blob. Core. Windows. NET/" "/Path/to/dir"--ricorsivo

Scaricare un subset di contenitori in un account di archiviazione usando un carattere jolly (*) nel nome del contenitore.

- azcopy CP "https:///[srcaccount]. blob. Core. Windows. NET/[Container * Name]" "/Path/to/dir"--ricorsivo

Copiare un singolo BLOB in un altro BLOB usando un token SAS.

- azcopy CP "https:///[srcaccount]. blob. Core. Windows. NET/[Container]/[path/to/BLOB]? [SAS] "" https:///[destaccount]. blob. Core. Windows. NET/[contenitore]/[path/to/BLOB]? [SAS] "

Copiare un singolo BLOB in un altro BLOB usando un token di firma di accesso condiviso e un token OAuth. È necessario usare un token di firma di accesso condiviso alla fine dell'URL dell'account di origine, ma non ne è necessario l'account di destinazione se si accede a AzCopy usando il comando AzCopy login. 

- azcopy CP "https:///[srcaccount]. blob. Core. Windows. NET/[Container]/[path/to/BLOB]? [SAS] "" https:///[destaccount]. blob. Core. Windows. NET/[contenitore]/[path/to/BLOB] "

Copiare una directory virtuale BLOB in un'altra usando un token SAS:

- azcopy CP "https:///[srcaccount]. blob. Core. Windows. NET/[contenitore]/[percorso/a/directory]? [SAS] "" https:///[destaccount]. blob. Core. Windows. NET/[contenitore]/[path/to/directory]? [SAS] "--ricorsivo = true

Copiare tutti i contenitori BLOB, le directory e i BLOB dall'account di archiviazione a un altro usando un token SAS:

- azcopy CP "https:///[srcaccount]. blob. Core. Windows. NET? [SAS] "" https:///[destaccount]. blob. Core. Windows. NET? [SAS] "--ricorsivo = true

Copiare un singolo oggetto nell'archivio BLOB da Amazon Web Services (AWS) S3 usando una chiave di accesso e un token di firma di accesso condiviso. Per prima cosa, impostare la variabile di ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY per l'origine AWS s3.
  
- azcopy CP "https://s3.amazonaws.com/ [bucket]/[oggetto]" "https://[destaccount]. blob. Core. Windows. NET/[contenitore]/[percorso/a/BLOB]? [SAS] "

Copiare un'intera directory nell'archivio BLOB da AWS S3 usando una chiave di accesso e un token di firma di accesso condiviso. Per prima cosa, impostare la variabile di ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY per l'origine AWS s3.

- azcopy CP "https://s3.amazonaws.com/ [bucket]/[cartella]" "https://[destaccount]. blob. Core. Windows. NET/[contenitore]/[percorso/a/directory]? [SAS] "--ricorsivo = true

Per comprendere meglio il segnaposto [cartella], vedere https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html.

Copiare tutti i bucket nell'archiviazione BLOB da Amazon Web Services (AWS) usando una chiave di accesso e un token di firma di accesso condiviso. Per prima cosa, impostare la variabile di ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY per l'origine AWS s3.

- azcopy CP "https://s3.amazonaws.com/ " "https:///[destaccount]. blob. Core. Windows. NET? [SAS] "--ricorsivo = true

Copiare tutti i bucket nell'archiviazione BLOB da un'area Amazon Web Services (AWS) usando una chiave di accesso e un token di firma di accesso condiviso. Per prima cosa, impostare la variabile di ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY per l'origine AWS s3.

- azcopy CP "https://s3- [Region]. amazonaws. com/" "https:///[destaccount]. blob. Core. Windows. NET? [SAS] "--ricorsivo = true

Copiare un subset di bucket usando un carattere jolly (*) nel nome del bucket. Come gli esempi precedenti, sono necessari una chiave di accesso e un token di firma di accesso condiviso. Assicurarsi di impostare la variabile di ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY per l'origine AWS s3.

- azcopy CP "https://s3.amazonaws.com/ [bucket * Name]/" "https://[destaccount]. blob. Core. Windows. NET? [SAS] "--ricorsivo = true

## <a name="options"></a>Opzioni

**--BLOB-type** String definisce il tipo di BLOB nella destinazione. Viene usato per il caricamento di BLOB e per la copia tra gli account (valore predefinito ' Detect '). I valori validi includono ' Detect ',' BlockBlob ',' PageBlob ' è AppendBlob '. Quando si esegue la copia tra account, un valore di ' Detect ' fa in modo che AzCopy usi il tipo di BLOB di origine per determinare il tipo di BLOB di destinazione. Quando si carica un file,' Detect ' determina se il file è un disco rigido virtuale o un file VHDX basato sull'estensione del file. Se il file è un file VHD o VHDX, AzCopy considera il file come un BLOB di pagine. (valore predefinito "rileva")

**--Block-BLOB:** consente di caricare un BLOB in blocchi in archiviazione di Azure usando questo livello BLOB. (valore predefinito "None")

**--block-size-MB** float usa questa dimensione del blocco (specificata in MIB) durante il caricamento in archiviazione di Azure e il download da archiviazione di Azure. Il valore predefinito viene calcolato automaticamente in base alle dimensioni del file. Sono consentite frazioni decimali, ad esempio: 0,25.

**--Cache-Control** stringa che imposta l'intestazione Cache-Control. Restituito durante il download.

**--Check-length**                         Controllare la lunghezza di un file nella destinazione dopo il trasferimento. In caso di mancata corrispondenza tra origine e destinazione, il trasferimento viene contrassegnato come non riuscito. (valore predefinito true)

**--Check-MD5** stringa specifica il modo in cui devono essere convalidati gli hash MD5 durante il download. Disponibile solo durante il download. Opzioni disponibili: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (valore predefinito "FailIfDifferent")

**--Content-Disposition** stringa ha impostato l'intestazione Content-Disposition. Restituito durante il download.

**--Content-Encoding** String imposta l'intestazione Content-Encoding. Restituito durante il download.

**--Content-Language** stringa ha impostato l'intestazione Content-Language. Restituito durante il download.

**--Content-Type** String specifica il tipo di contenuto del file. Implica no-gues-MIME-Type. Restituito durante il download.

**--Decomprimi**                           Decomprime automaticamente i file durante il download, se la codifica di contenuti indica che sono compressi. I valori supportati per la codifica del contenuto sono "gzip" e "deflate". Le estensioni di file di '. gz '/'. gzip ' o '. ZZ ' non sono necessarie, ma verranno rimosse se presenti.

**--Exclude-Attributes** String (solo Windows) escludere i file i cui attributi corrispondono all'elenco di attributi. Ad esempio: A; S R

**--Exclude-BLOB-type** stringa consente di specificare il tipo di BLOB (BlockBlob/PageBlob/AppendBlob) da escludere durante la copia dei BLOB dal contenitore o dall'account. L'uso di questo flag non è applicabile per la copia dei dati dal servizio non Azure al servizio. Più di un BLOB deve essere separato da';'.

**--Exclude-Path** String escludere questi percorsi durante la copia. Questa opzione non supporta i caratteri jolly (*). Verifica il prefisso del percorso relativo (ad esempio: cartella, cartella/subDirName/file. pdf). Se usato in combinazione con l'attraversamento dell'account, i percorsi non includono il nome del contenitore.

**--Exclude-pattern** String escludere questi file durante la copia. Questa opzione supporta i caratteri jolly (*)

**--follow-collegamenti simbolici**                      Seguire i collegamenti simbolici durante il caricamento da file system locali.

**--** la stringa from-to specifica facoltativamente la combinazione di destinazione di origine. Ad esempio: LocalBlob, BlobLocal, LocalBlobFS.

**-h,--** Guida alla guida per la copia

**--include-Attributes** String (solo Windows) include i file i cui attributi corrispondono all'elenco di attributi. Ad esempio: A; S R

**--include-percorso** stringa include solo questi percorsi durante la copia. Questa opzione non supporta i caratteri jolly (*). Verifica il prefisso del percorso relativo (ad esempio: cartella, cartella/subDirName/file. pdf).

**--include-pattern** String include solo questi file durante la copia. Questa opzione supporta i caratteri jolly (*). Separare i file usando ';'.

**--** la stringa a livello di log definisce il livello di dettaglio del log per il file di log, i livelli disponibili: info (tutte le richieste/risposte), avviso (risposte lente), errore (solo richieste non riuscite) e nessuno (nessun log di output). (impostazione predefinita "INFO")

**--** la stringa di metadati carica in archiviazione di Azure con queste coppie chiave-valore come metadati.

**--No-gues-MIME-Type**                   Impedisce a AzCopy di rilevare il tipo di contenuto in base all'estensione o al contenuto del file.

**--Sovrascrivi** stringa sovrascrive i file e i BLOB in conflitto nella destinazione se questo flag è impostato su true. I valori possibili sono ' true ',' false ' è prompt '. (valore predefinito "true")

**--page-blob** caricare il BLOB di pagine in archiviazione di Azure usando questo livello BLOB. (valore predefinito "None")

**--Preserve-Ultima modifica-ora**          Disponibile solo quando la destinazione è file system.

**--put-MD5**                             Creare un hash MD5 di ogni file e salvare l'hash come proprietà Content-MD5 del BLOB o del file di destinazione. Per impostazione predefinita, l'hash non viene creato. Disponibile solo durante il caricamento.

**--ricorsivo**                            Esaminare in modo ricorsivo le sottodirectory durante il caricamento da file system locali.

**--S2S-Detect-source-modificato**           Controllare se l'origine è stata modificata dopo l'enumerazione.

**--S2S-handle-non valido-la stringa di metadati** specifica il modo in cui vengono gestite le chiavi di metadati non valide. Opzioni disponibili: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (valore predefinito "ExcludeIfInvalid")

**--S2S-Preserve-livello di accesso**             Mantenere il livello di accesso durante la copia da servizio a servizio. Per verificare che l'account di archiviazione di destinazione supporti l'impostazione del livello di accesso, vedere [archiviazione BLOB di Azure: livelli di accesso ad accesso frequente, ad accesso sporadico e archivio](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) . Nei casi in cui l'impostazione del livello di accesso non è supportata, usare s2sPreserveAccessTier = false per ignorare la copia del livello di accesso. (valore predefinito true)

**--S2S-Preserve-proprietà**              Mantieni le proprietà complete durante la copia da servizio a servizio. Per AWS S3 e l'origine file non singolo file di Azure, l'operazione list non restituisce proprietà complete di oggetti e file. Per mantenere le proprietà complete, AzCopy deve inviare un'altra richiesta per oggetto o file. (valore predefinito true)

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

**--Cap-Mbps UInt32**      Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.

**--output-** formato stringa di tipo dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text". (impostazione predefinita "testo")

## <a name="see-also"></a>Vedi anche

- [azcopy](storage-ref-azcopy.md)
