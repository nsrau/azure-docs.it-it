<properties linkid="storage-use-azcopy" urlDisplayName="AzCopy" pageTitle="How to use AzCopy with Microsoft Azure Storage" metaKeywords="Get started Azure AzCopy   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure file   Azure file storage   Azure file share   AzCopy" description="Learn how to use the AzCopy utility to upload, download, and copy blob and file content." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="How to use AzCopy with Microsoft Azure Storage" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="tamram" />

# Introduzione all'utilità della riga di comando AzCopy

AzCopy è un'utilità della riga di comando progettata per il caricamento, il download e la copia a prestazioni elevate dei dati in e dai servizi di archiviazione BLOB e file di Microsoft Azure. In questa guida viene fornita una panoramica sull'utilizzo di AzCopy.

> [WACOM.NOTE] In questa guida si presuppone che sia stato installato AzCopy 2.5 o versioni successive. Si noti che AzCopy è disponibile in una versione provvisoria, quindi le opzioni della riga di comando e le relative funzioni potrebbero variare nelle versioni successive.

## Sommario

-   [Scaricare e installare AzCopy][Scaricare e installare AzCopy]
-   [Informazioni sulla sintassi della riga di comando di AzCopy][Informazioni sulla sintassi della riga di comando di AzCopy]
-   [Limitare le scritture simultanee durante la copia dei dati][Limitare le scritture simultanee durante la copia dei dati]
-   [Copiare i BLOB di Azure con AzCopy][Copiare i BLOB di Azure con AzCopy]
-   [Copiare i file in una condivisione file di Azure con AzCopy][Copiare i file in una condivisione file di Azure con AzCopy]
-   [Versioni di AzCopy][Versioni di AzCopy]
-   [Passaggi successivi][Passaggi successivi]

## <span id="install"></span></a> Scaricare e installare AzCopy

1.  Scaricare la [versione più recente di AzCopy][versione più recente di AzCopy].
2.  Eseguire l'installazione. Per impostazione predefinita, l'installazione di AzCopy crea una cartella denominata `AzCopy` in `%ProgramFiles(x86)%\Microsoft SDKs\Azure\` (in un computer che esegue Windows a 64 bit) o `%ProgramFiles%\Microsoft SDKs\Azure\` (in un computer che esegue Windows a 32 bit). È tuttavia possibile modificare il percorso di installazione nell'installazione guidata.
3.  Se si vuole, è possibile aggiungere il percorso di installazione di AzCopy al percorso di sistema.

## <span id="syntax"></span></a> Informazioni sulla sintassi della riga di comando di AzCopy

Aprire una finestra di comando e passare alla directory di installazione di AzCopy contenente l'eseguibile `AzCopy.exe`. La sintassi di base per i comandi di AzCopy è la seguente:

    AzCopy <source> <destination> [filepattern] [options]

-   Il parametro `<source>` specifica i dati di origine da cui eseguire la copia. L'origine può essere una directory di file system, un contenitore BLOB, una directory virtuale BLOB o una condivisione file di archiviazione.

-   Il parametro `<destination>` specifica la destinazione in cui eseguire la copia. La destinazione può essere una directory di file system, un contenitore BLOB, una directory virtuale BLOB o una condivisione file di archiviazione.

-   Il comportamento del parametro `filepattern` facoltativo è determinato dal percorso dei dati di origine e dalla presenza dell'opzione relativa alla modalità ricorsiva. È possibile specificare la modalità ricorsiva tramite l'opzione `/S`.

    Se l'origine specificata è una directory nel file system, è possibile usare i caratteri jolly standard e viene cercata una corrispondenza del criterio del file specificato con i file inclusi nella directory. Se è specificata l'opzione `/S`, AzCopy cerca anche la corrispondenza del criterio specificato con tutti i file inclusi nelle sottocartelle della directory.

    Se l'origine specificata è un contenitore o una directory virtuale BLOB, non è possibile applicare i caratteri jolly. Se è specificata l'opzione `/S`, AzCopy interpreta il criterio del file come un prefisso BLOB. Se non è specificata l'opzione `/S`, AzCopy cerca una corrispondenza esatta del criterio con i nomi dei BLOB.

    Se l'origine specificata è una condivisione file di Azure, è necessario specificare il nome file esatto, (*ad esempio* `abc.txt`) per copiare un singolo file oppure specificare l'opzione `/S` per copiare tutti i file nella condivisione in modo ricorsivo. Se si specificano sia il criterio del file che l'opzione `/S` contemporaneamente, verrà generato un errore.

    Il criterio file predefinito usato quando non viene specificato alcun criterio è `*.*` per una directory del file system o un prefisso vuoto per una risorsa di archiviazione BLOB o file di Azure.

    > [WACOM.NOTE] Per evitare problemi di prestazioni, in AzCopy versione 2.5 non sono più supportati più criteri file in un unico comando. Per gli scenari che prevedono più criteri di file, è necessario inviare più comandi, ognuno con un singolo criterio di file.

-   Le opzioni disponibili per il parametro `options` sono descritte nella tabella seguente. Per informazioni sulle opzioni, è anche possibile digitare `AzCopy /?` nella riga di comando.

| Nome opzione                     | Descrizione                                                                                                                                                                                                                                                                                                                                                                 | Applicabile ad Archiviazione BLOB (S/N) | Applicabile ad Archiviazione file (S/N) |
|----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|-----------------------------------------|
| **/DestKey:\<storage-key\>**     | Specifica la chiave dell'account di archiviazione per la risorsa di destinazione.                                                                                                                                                                                                                                                                                           | S                                       | S                                       |
| **/DestSAS:\<container-SAS\>**   | Specifica una firma di accesso condiviso per il contenitore di destinazione (se applicabile). Racchiudere la firma di accesso condiviso tra virgolette doppie, in quanto potrebbe contenere caratteri della riga di comando speciali.                                                                                                                                       
                                     Se la risorsa di destinazione è un BLOB, è possibile specificare questa opzione seguita dal token di accesso condiviso o specificare la firma di accesso condiviso come parte dell'URI del BLOB di destinazione, senza questa opzione.                                                                                                                                      
                                     Questa opzione si applica solo all'archiviazione BLOB ed è valida solo per il caricamento o la copia di BLOB all'interno dello stesso account di archiviazione.                                                                                                                                                                                                             | S                                       | N                                       |
| **/SourceKey:\<storage-key\>**   | Specifica la chiave dell'account di archiviazione per la risorsa di origine.                                                                                                                                                                                                                                                                                                | S                                       | S                                       |
| **/SourceSAS:\<container-SAS\>** | Specifica una firma di accesso condiviso per il contenitore di origine (se applicabile). Racchiudere la firma di accesso condiviso tra virgolette doppie, in quanto potrebbe contenere caratteri della riga di comando speciali.                                                                                                                                            
                                     Se non viene fornita né una chiave né una firma di accesso condiviso, il contenitore verrà letto tramite accesso anonimo.                                                                                                                                                                                                                                                   
                                     Questa opzione si applica solo per l'archiviazione BLOB.                                                                                                                                                                                                                                                                                                                    | S                                       | N                                       |
| **/S**                           | Specifica la modalità ricorsiva per le operazioni di copia. Nella modalità ricorsiva, AzCopy copierà tutti i BLOB o file corrispondenti al criterio di file specificato, inclusi quelli nelle sottocartelle.                                                                                                                                                                | S                                       | S                                       |
| **/BlobType:\<block | page\>**   | Indica che la destinazione è un BLOB in blocchi o un BLOB di pagine. Questa opzione si applica solo se la destinazione è un BLOB; in caso contrario, viene generato un errore. Se la destinazione è un BLOB e questa opzione non è specificata, per impostazione predefinita, AZCopy presupporrà che l'opzione desiderata è un BLOB in blocchi.                             | S                                       | N                                       |
| **/CheckMd5**                    | Calcola un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato nella proprietà Content-MD5 del BLOB o del file corrisponda all'hash calcolato. Il controllo MD5 è disattivato per impostazione predefinita, quindi è necessario specificare questa opzione per eseguire il controllo MD5 al download dei dati.                                               
                                     Si noti che Archiviazione di Azure non garantisce che l'hash MD5 archiviato per il BLOB o il file sia aggiornato. È responsabilità del client aggiornare l'hash MD5 ogni volta che il BLOB o il file viene modificato.                                                                                                                                                      
                                     AzCopy imposta sempre la proprietà Content-MD5 per un BLOB o un file di Azure dopo averlo caricato nel servizio.                                                                                                                                                                                                                                                            | S                                       | S                                       |
| **/Snapshot**                    | Indica se trasferire gli snapshot o meno. Questa opzione è valida solo quando l'origine è un BLOB.                                                                                                                                                                                                                                                                          
                                     Gli snapshot di BLOB trasferiti vengono rinominati nel formato seguente: [nome-BLOB] (ora-snapshot)[estensione].                                                                                                                                                                                                                                                            
                                     Per impostazione predefinita, gli snapshot non vengono copiati.                                                                                                                                                                                                                                                                                                             | S                                       | N                                       |
| **/V:[verbose log-file]**        | Esegue l'output dei messaggi di stato dettagliati in un file di log. Per impostazione predefinita, al file di log dettagliato viene assegnato il nome `AzCopyVerbose.log` in `%LocalAppData%\Microsoft\Azure\AzCopy`. Se si specifica un percorso di file esistente per questa opzione, il log dettagliato verrà aggiunto al file indicato.                                 | S                                       | S                                       |
| **/Z:[journal-file-folder]**     | Specifica la cartella del file journal per la ripresa di un'operazione.                                                                                                                                                                                                                                                                                                     
                                     AzCopy supporta sempre la ripresa di un'operazione interrotta.                                                                                                                                                                                                                                                                                                              
                                     Se questa opzione non è specificata o se è specificata senza un percorso di cartella, AzCopy creerà il file journal nel percorso predefinito, ovvero `%LocalAppData%\Microsoft\Azure\AzCopy`.                                                                                                                                                                               
                                     Ogni volta che si invia un comando ad AzCopy, l'utilità verifica se il file journal è presente nella cartella predefinita o in una cartella specificata dall'utente tramite questa opzione. Se il file journal non viene trovato in tali percorsi, AzCopy considera l'operazione come nuova e quindi crea un nuovo file journal.                                            
                                     Se il file journal esiste, AzCopy verifica se la riga di comando di input corrisponde alla riga di comando nel file journal. Se le righe di comando corrispondono, AzCopy avvia la ripresa dell'operazione incompleta. Se non corrispondono, verrà richiesto se si vuole sovrascrivere il file journal per avviare una nuova operazione o annullare l'operazione corrente.  
                                     Se l'operazione viene completata senza errori, il file journal viene eliminato.                                                                                                                                                                                                                                                                                             
                                     Si noti che la ripresa di un'operazione da un file journal creato da una versione precedente di AzCopy non è supportata.                                                                                                                                                                                                                                                    | S                                       | S                                       |
| **/@:response-file**             | Specifica un file contenente parametri. AzCopy elabora i parametri nel file come se fossero stati specificati nella riga di comando.                                                                                                                                                                                                                                        
                                     In un file di risposta è possibile specificare più parametri su una sola riga o specificare un parametro su ogni riga. Si noti che un singolo parametro non può estendersi su più righe.                                                                                                                                                                                    
                                     I file di risposta possono includere righe di commento che iniziano con il simbolo `#`.                                                                                                                                                                                                                                                                                     
                                     È possibile specificare più file di risposta. Tuttavia, tenere presente che AzCopy non supporta file di risposta annidati.                                                                                                                                                                                                                                                  | S                                       | S                                       |
| **/Y**                           | Elimina tutte le richieste di conferma di AzCopy.                                                                                                                                                                                                                                                                                                                           | S                                       | S                                       |
| **/L**                           | Specifica solo un'operazione di elenco, non viene copiato alcun dato.                                                                                                                                                                                                                                                                                                       | S                                       | S                                       |
| **/MT**                          | Imposta l'ora dell'ultima modifica di un file scaricato sulla stessa ora del file o del BLOB di origine.                                                                                                                                                                                                                                                                    | S                                       | S                                       |
| **/XN**                          | Esclude una risorsa di origine più recente. La risorsa non verrà copiata se l'origine è più recente della destinazione.                                                                                                                                                                                                                                                     | S                                       | S                                       |
| **/XO**                          | Esclude una risorsa di origine meno recente. La risorsa non verrà copiata se la risorsa di origine è meno recente di quella di destinazione.                                                                                                                                                                                                                                | S                                       | S                                       |
| **/A**                           | Carica solo i file per i quali è impostato l'attributo Archive.                                                                                                                                                                                                                                                                                                             | S                                       | S                                       |
| **/IA:[RASHCNETOI]**             | Carica solo i file per i quali sono impostati gli attributi specificati.                                                                                                                                                                                                                                                                                                    
                                     Gli attributi disponibili sono:                                                                                                                                                                                                                                                                                                                                             
                                     R   File di sola lettura                                                                                                                                                                                                                                                                                                                                                    
                                     A   File pronti per l'archiviazione                                                                                                                                                                                                                                                                                                                                         
                                     S   File di sistema                                                                                                                                                                                                                                                                                                                                                         
                                     H   File nascosti                                                                                                                                                                                                                                                                                                                                                           
                                     C   File compressi                                                                                                                                                                                                                                                                                                                                                          
                                     N   File normali                                                                                                                                                                                                                                                                                                                                                            
                                     E   File crittografati                                                                                                                                                                                                                                                                                                                                                      
                                     T   File temporanei                                                                                                                                                                                                                                                                                                                                                         
                                     O   File offline                                                                                                                                                                                                                                                                                                                                                            
                                     I   File non indicizzati                                                                                                                                                                                                                                                                                                                                                    | S                                       | S                                       |
| **/XA:[RASHCNETOI]**             | Esclude i file per i quali sono impostati gli attributi specificati.                                                                                                                                                                                                                                                                                                        
                                     Gli attributi disponibili sono:                                                                                                                                                                                                                                                                                                                                             
                                     R   File di sola lettura                                                                                                                                                                                                                                                                                                                                                    
                                     A   File pronti per l'archiviazione                                                                                                                                                                                                                                                                                                                                         
                                     S   File di sistema                                                                                                                                                                                                                                                                                                                                                         
                                     H   File nascosti                                                                                                                                                                                                                                                                                                                                                           
                                     C   File compressi                                                                                                                                                                                                                                                                                                                                                          
                                     N   File normali                                                                                                                                                                                                                                                                                                                                                            
                                     E   File crittografati                                                                                                                                                                                                                                                                                                                                                      
                                     T   File temporanei                                                                                                                                                                                                                                                                                                                                                         
                                     O   File offline                                                                                                                                                                                                                                                                                                                                                            
                                     I   File non indicizzati                                                                                                                                                                                                                                                                                                                                                    | S                                       | S                                       |
| **/Delimiter:\<delimiter\>**     | Indica il delimitatore usato per delimitare le directory virtuali in un nome BLOB.                                                                                                                                                                                                                                                                                          
                                     Per impostazione predefinita, AzCopy usa il carattere / come delimitatore. Tuttavia, a questo scopo supporta anche l'uso di caratteri comuni, ad esempio @, \# o %. Se è necessario includere uno di questi caratteri speciali nella riga di comando, racchiudere il nome del file tra virgolette doppie.                                                                   
                                     Questa opzione si applica solo per il download di BLOB.                                                                                                                                                                                                                                                                                                                     | S                                       | N                                       |
| **/NC**                          | Specifica il numero di operazioni simultanee.                                                                                                                                                                                                                                                                                                                               
                                     Il numero predefinito è pari a 8 volte il numero di processori Core in esecuzione. Di conseguenza, se nel computer sono presenti 8 core, per impostazione predefinita AzCopy avvierà 64 operazioni simultanee.                                                                                                                                                              
                                     Si noti che un numero elevato di operazioni simultanee in un ambiente con una larghezza di banda ridotta potrebbe sovraccaricare la connessione di rete e impedire il corretto completamento delle operazioni. Limitare le operazioni simultanee in base alla larghezza di banda di rete effettivamente disponibile.                                                        | S                                       | S                                       |
| **/SourceType:Blob**             | Indica che la risorsa `source` è un BLOB disponibile nell'ambiente di sviluppo locale, in esecuzione nell'emulatore di archiviazione.                                                                                                                                                                                                                                       | S                                       | N                                       |
| **/DestType:Blob**               | Indica che la risorsa `destination` è un BLOB disponibile nell'ambiente di sviluppo locale, in esecuzione nell'emulatore di archiviazione.                                                                                                                                                                                                                                  | S                                       | N                                       |

## <span id="limit-writes"></span></a> Limitare le scritture simultanee durante la copia dei dati

Durante la copia di BLOB o file con AzCopy, tenere presente che altre applicazioni potrebbero modificare i dati mentre è in corso la copia. Se possibile, assicurarsi che i dati copiati non vengano modificati durante l'operazione di copia. Ad esempio, se si copia un file VHD associato a una macchina virtuale di Azure, verificare che altre applicazioni non stiano scrivendo nel file VHD durante la copia. In alternativa, è prima possibile creare uno snapshot del file VHD e quindi copiare lo snapshot.

Se non è possibile evitare che altre applicazioni scrivano nei BLOB o nei file durante l'operazione di copia, tenere presente che al termine del processo la parità delle risorse copiate con le risorse di origine potrebbe non essere completa.

## <span id="copy-blobs"></span></a> Copiare i BLOB di Azure con AzCopy

Gli esempi seguenti mostrano vari scenari per la copia di BLOB con AzCopy.

### Copiare un BLOB singolo

**Caricare un file dal file system all'archiviazione BLOB:**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key abc.txt

**Scaricare un BLOB dall'archiviazione BLOB al file system:**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key abc.txt

### Copiare un BLOB tramite copia sul lato server

Quando si copia di un BLOB all'interno di un account di archiviazione o tra account di archiviazione diversi, viene eseguita un'operazione di copia sul lato server. Per altre informazioni sulle operazioni di copia sul lato server, vedere la pagina relativa all'[introduzione alla copia asincrona di BLOB tra account][introduzione alla copia asincrona di BLOB tra account].

**Copiare un BLOB all'interno di un account di archiviazione:**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer/mycontainer1 https://myaccount.blob.core.windows.net/mycontainer2 /sourcekey:key /destkey:key abc.txt 

**Copiare un BLOB tra account di archiviazione diversi:**

    AzCopy https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 https://destaccount.blob.core.windows.net/mycontainer2 /sourcekey:key1 /destkey:key2 abc.txt

### Copiare un BLOB dall'area secondaria

Se per l'account di archiviazione è abilitata l'archiviazione con ridondanza geografica e accesso in lettura, è possibile copiare i dati dall'area secondaria.

**Copiare un BLOB dall'area secondaria all'account primario:**

    AzCopy https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 https://myaccount2.blob.core.windows.net/mynewcontainer2 /sourcekey:key1 /destkey:key2 abc.txt

**Scaricare un BLOB dall'area secondaria a un file nel file system:**

    AzCopy https://myaccount-secondary.blob.core.windows.net/mynewcontainer C:\myfolder /sourcekey:key abc.txt

### Caricare un file in un nuovo contenitore o in una nuova directory virtuale BLOB

**Caricare un file in un nuovo contenitore BLOB**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mynewcontainer /destkey:key abc.txt

Si noti che se il contenitore di destinazione specificato non esiste, AzCopy ne creerà uno e vi caricherà i file.

**Caricare un file in una nuova directory virtuale BLOB**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer/vd /destkey:key abc.txt

Si noti che se la directory virtuale specificata non esiste, AzCopy caricherà il file in modo da includere la directory virtuale nel relativo nome (*in base all'esempio precedente:* `vd/abc.txt`).

### Scaricare un BLOB in una nuova cartella

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key abc.txt

Se la cartella `C:\myfolder` non esiste ancora, AzCopy la creerà nel file system e scaricherà `abc.txt` nella nuova cartella.

### Caricare file e sottocartelle da una directory a un contenitore, in modo ricorsivo

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /S

Se si specifica l'opzione `/S`, il contenuto della directory specificata viene copiato nell'archiviazione BLOB in modo ricorsivo, ovvero includendo nella copia anche tutte le sottocartelle e i relativi file. Si supponga ad esempio che i file seguenti risiedano nella cartella `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Dopo l'operazione di copia, il contenitore includerà i file seguenti:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

### Caricare file da una directory a un contenitore, in modo non ricorsivo

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key

Se non si specifica l'opzione `/S` nella riga di comando, AzCopy eseguirà la copia in modo non ricorsivo. Verranno copiati solo i file nella directory specificata, ma le sottocartelle e i relativi file NON verranno copiati. Si supponga ad esempio che i file seguenti risiedano nella cartella `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Dopo l'operazione di copia, il contenitore includerà i file seguenti:

    abc.txt
    abc1.txt
    abc2.txt

### Scaricare tutti i BLOB di un contenitore in una directory del file system, in modo ricorsivo

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /S

Si supponga che i BLOB seguenti risiedano nel contenitore specificato:

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Dopo l'operazione di copia, la directory `C:\myfolder` includerà i file seguenti:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

### Scaricare i BLOB di una directory virtuale del BLOB in una directory del file system, in modo ricorsivo

    AzCopy https://myaccount.blob.core.windows.net/mycontainer/vd1/ C:\myfolder /sourcekey:key /S

Si supponga che i BLOB seguenti risiedano nel contenitore specificato:

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Dopo l'operazione di copia, la directory `C:\myfolder` includerà i file seguenti: Si noti che vengono copiati solo i BLOB della directory virtuale:

    C:\myfolder\a.txt
    C:\myfolder\abcd.txt

### Caricare i file corrispondenti al criterio di file specificato in un contenitore, in modo ricorsivo

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key a* /S

Si supponga che i file seguenti risiedano nella cartella `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Dopo l'operazione di copia, il contenitore includerà i file seguenti:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

### Scaricare i BLOB con il prefisso specificato nel file system, in modo ricorsivo

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key a /S

Si supponga che i BLOB seguenti risiedano nel contenitore specificato. Verranno copiati tutti i BLOB che iniziano con il prefisso `a`:

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Dopo l'operazione di copia, la cartella `C:\myfolder` includerà i file seguenti:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

Si noti che il prefisso si applica alla directory virtuale, che forma la prima parte del nome del BLOB. Nell'esempio precedente, la directory virtuale non corrisponde al prefisso specificato, quindi non viene copiata.

### Copiare un BLOB e i relativi snapshot in un altro account di archiviazione

    AzCopy https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 https://destaccount.blob.core.windows.net/mycontainer2 /sourcekey:key1 /destkey:key2 abc.txt /snapshot

Dopo l'operazione di copia, il contenitore di destinazione includerà il BLOB e i relativi snapshot. Supponendo che il BLOB dell'esempio precedente contenga due snapshot, nel contenitore saranno presenti il BLOB e gli snapshot seguenti:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### Usare un file di risposta per specificare i parametri della riga di comando

    AzCopy /@:"C:\myfolder\abc.txt"

È possibile includere i parametri della riga di comando di AzCopy in un file di risposta. AzCopy elabora i parametri nel file come se fossero stati specificati nella riga di comando, eseguendo una sostituzione diretta con i contenuti del file.

**Specificare uno o più file di risposta a riga singola**

Si supponga che siano presenti un file di risposta denominato `source.txt`, che specifica un contenitore di origine:

    http://myaccount.blob.core.windows.net/mycontainer

Un file di risposta denominato `dest.txt`, che specifica una cartella di destinazione nel file system:

    C:\myfolder

E un file di risposta denominato `options.txt`, che specifica le opzioni per AzCopy:

    /S /Y

Per chiamare AzCopy con questi file di risposta, che risiedono in una directory `C:\responsefiles`, usare il comando seguente:

    AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /sourcekey:[sourcekey] /@:"C:\responsefiles\options.txt"   

AzCopy elabora il comando come se fossero stati inclusi tutti i singoli parametri nella riga di comando:

    AzCopy http://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:[sourcekey] /S /Y

**Specificare un file di risposta a più righe**

Si supponga che sia presente un file di risposta denominato `copyoperation.txt`, che contiene le righe seguenti: Ogni parametro di AzCopy è specificato su una riga:

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:[sourcekey]
    /S 
    /Y

Per chiamare AzCopy con questo file di risposta, usare il comando seguente:

    AzCopy /@:"C:\responsefiles\copyoperation.txt"

AzCopy elabora il comando come se fossero stati inclusi tutti i singoli parametri nella riga di comando:

    AzCopy http://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:[sourcekey] /S /Y

Si noti che è necessario specificare ogni parametro di AzCopy su una sola riga. Se il parametro viene suddiviso su due righe, l'operazione di AzCopy avrà esito negativo, come mostrato di seguito per il parametro `/sourcekey`:

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:
    [sourcekey]
    /S 
    /Y

### Specificare una firma di accesso condiviso

**Specificare una firma di accesso condiviso per il contenitore di origine tramite l'opzione /sourceSAS**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1 C:\myfolder /sourceSAS:SAS /S

**Specificare una firma di accesso condiviso per il contenitore di origine nell'URI del contenitore di origine**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken C:\myfolder /S

**Specificare una firma di accesso condiviso per il contenitore di destinazione tramite l'opzione /destSAS**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer1 /destSAS:SAS abc.txt

**Specificare una firma di accesso condiviso per i contenitori di origine e di destinazione**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1 https://myaccount.blob.core.windows.net/mycontainer2 /sourceSAS:SAS1 /destSAS:SAS2 abc.txt

### Specificare una cartella per il file journal

Ogni volta che si invia un comando ad AzCopy, l'utilità verifica se il file journal è presente nella cartella predefinita o in una cartella specificata dall'utente tramite questa opzione. Se il file journal non viene trovato in tali percorsi, AzCopy considera l'operazione come nuova e quindi crea un nuovo file journal.

Se il file journal esiste, AzCopy verifica se la riga di comando di input corrisponde alla riga di comando nel file journal. Se le righe di comando corrispondono, AzCopy avvia la ripresa dell'operazione incompleta. Se non corrispondono, verrà richiesto se si vuole sovrascrivere il file journal per avviare una nuova operazione o annullare l'operazione corrente.

**Usare il percorso predefinito per il file journal**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /Z

Se si omette l'opzione `/Z` o si specifica l'opzione `/Z` senza il percorso della cartella, come illustrato sopra, AzCopy creerà il file journal nel percorso predefinito, ovvero `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Se il file journal esiste già, AzCopy riprenderà l'operazione in base al file journal.

**Specificare un percorso personalizzato per il file journal**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /Z:C:\journalfolder\

In questo esempio viene creato il file journal, nel caso in cui non esista già: Se esiste, AzCopy riprenderà l'operazione in base al file journal.

**Riprendere un'operazione di AzCopy**

    AzCopy /Z:C:\journalfolder\

In questo esempio viene ripresa l'ultima operazione che potrebbe non essere stata completata a causa di errori.

### Generare un file di log

**Scrivere il file di log dettagliato nel percorso predefinito**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /V

Se si specifica l'opzione `/V` senza fornire il percorso del file di log dettagliato, AzCopy creerà il file journal nel percorso predefinito, ovvero `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

**Scrivere il file di log dettagliato in un percorso dettagliato**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /V:C:\myfolder\azcopy1.log

Si noti che se si specifica un percorso relativo per l'opzione `/V`, ad esempio `/V:test/azcopy1.log`, il log dettagliato verrà creato nella directory di lavoro corrente all'interno di una sottocartella denominata `test`.

### Impostare l'ora dell'ultima modifica dei file scaricati sulla stessa ora dei BLOB di origine

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT

### Escludere BLOB dall'operazione di copia in base all'ora dell'ultima modifica

Specificare l'opzione `/MT` per confrontare l'ora dell'ultima modifica del BLOB di origine e del file di destinazione.

**Escludere i BLOB più recenti rispetto al file di destinazione**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT /XN

**Escludere i BLOB meno recenti rispetto al file di destinazione**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT /XO

### Specificare il numero di operazioni simultanee da avviare

L'opzione `/NC` specifica il numero di operazioni di copia simultanee. Per impostazione predefinita, AzCopy avvierà un numero di operazioni simultanee pari a otto volte il numero di processori core presenti. Se AzCopy è in esecuzione in una rete con larghezza di banda ridotta, è possibile specificare un numero inferiore per l'opzione in modo da evitare errori generati dalla competizione tra le risorse.

### Eseguire AzCopy sulle risorse del BLOB nell'emulatore di archiviazione

    AzCopy https://127.0.0.1:10004/myaccount/myfileshare/ C:\myfolder /SourceKey:key /SourceType:Blob /S

## <span id="copy-files"></span></a> Copiare i file in una condivisione file di Azure con AzCopy

Gli esempi seguenti mostrano vari scenari per la copia di file di Azure con AzCopy.

### Scaricare un file nel file system da una condivisione file di Azure

    AzCopy https://myaccount.file.core.windows.net/myfileshare/myfolder1/ C:\myfolder /SourceKey:key abc.txt

Si noti che se l'origine specificata è una condivisione file di Azure, è necessario specificare il nome file esatto, (*ad esempio* `abc.txt`) per copiare un singolo file oppure specificare l'opzione `/S` per copiare tutti i file nella condivisione in modo ricorsivo. Se si specificano sia il criterio del file che l'opzione `/S` contemporaneamente, verrà generato un errore.

### Scaricare file e cartelle nel file system da una condivisione file di Azure, in modo ricorsivo

    AzCopy https://myaccount.file.core.windows.net/myfileshare/ C:\myfolder /SourceKey:key /S

Le cartelle vuote non verranno copiate.

### Caricare file e cartelle dal file system a una condivisione file di Azure, in modo ricorsivo

    AzCopy C:\myfolder https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Le cartelle vuote non verranno copiate.

### Caricare i file corrispondenti al criterio di file specificato in una condivisione di Azure, in modo ricorsivo

    AzCopy C:\myfolder https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key ab* /S

## <span id="versions"></span></a> Versioni di AzCopy

| Versione   | Novità                                                                                                                                                        |
|------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **V2.5.0** | **Versione corrente Ottimizza le prestazioni per gli scenari di copia su larga scala e introduce numerosi miglioramenti importanti in termini di usabilità.** |
| V2.4.1     | Consente di specificare la cartella di destinazione nell'installazione guidata.                                                                               |
| V2.4.0     | Consente di caricare e scaricare file per l'archiviazione file di Azure.                                                                                      |
| V2.3.0     | Supporta gli account di archiviazione con ridondanza geografica e accesso in lettura                                                                          |
| V2.2.2     | Versione aggiornata per l'uso della libreria client di archiviazione di Azure versione 3.0.3.                                                                 |
| V2.2.1     | Include la correzione di un problema di prestazioni durante la copia di grandi quantità di file all'interno dello stesso account di archiviazione.            |
| V2.2       | Supporta l'impostazione del delimitatore della directory virtuale per i nomi di BLOB. Consente di specificare il percorso del file journal.                   |
| V2.1       | Offre più di 20 opzioni per supportare operazioni di caricamento, scaricamento e copia di BLOB in un modo efficiente.                                         |

## <span id="next-steps"></span></a>Passaggi successivi

Per altre informazioni su Archiviazione di Azure e AzCopy, vedere le risorse seguenti:

### Documentazione di Archiviazione di Azure

-   [Introduzione ad Archiviazione di Azure][Introduzione ad Archiviazione di Azure]
-   [Archiviare i file nell'archiviazione BLOB][Archiviare i file nell'archiviazione BLOB]
-   [Creare una condivisione file SMB in Azure con l'archiviazione file][Creare una condivisione file SMB in Azure con l'archiviazione file]

### Post del blog di Archiviazione di Azure:

-   [Introduzione al servizio file di Microsoft Azure][Introduzione al servizio file di Microsoft Azure]
-   [AzCopy 2.5: Ottimizzazione per gli scenari di copia su larga scala][AzCopy 2.5: Ottimizzazione per gli scenari di copia su larga scala]
-   [AzCopy: Supporto per l'archiviazione con ridondanza geografica e accesso in lettura][AzCopy: Supporto per l'archiviazione con ridondanza geografica e accesso in lettura]
-   [AzCopy: Trasferimento di dati con modalità riavviabile e token di firma di accesso condiviso][AzCopy: Trasferimento di dati con modalità riavviabile e token di firma di accesso condiviso]
-   [AzCopy: Uso del comando di copia dei BLOB tra account][AzCopy: Uso del comando di copia dei BLOB tra account]
-   [AzCopy: Caricamento e download di file per BLOB di Microsoft Azure][AzCopy: Caricamento e download di file per BLOB di Microsoft Azure]

  [Scaricare e installare AzCopy]: #install
  [Informazioni sulla sintassi della riga di comando di AzCopy]: #syntax
  [Limitare le scritture simultanee durante la copia dei dati]: #limit-writes
  [Copiare i BLOB di Azure con AzCopy]: #copy-blobs
  [Copiare i file in una condivisione file di Azure con AzCopy]: #copy-files
  [Versioni di AzCopy]: #versions
  [Passaggi successivi]: #next-steps
  [versione più recente di AzCopy]: http://aka.ms/downloadazcopy
  [introduzione alla copia asincrona di BLOB tra account]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx
  [Introduzione ad Archiviazione di Azure]: http://azure.microsoft.com/it-it/documentation/articles/storage-introduction/
  [Archiviare i file nell'archiviazione BLOB]: http://azure.microsoft.com/it-it/documentation/articles/storage-dotnet-how-to-use-blobs/
  [Creare una condivisione file SMB in Azure con l'archiviazione file]: http://azure.microsoft.com/it-it/documentation/articles/storage-dotnet-how-to-use-files/
  [Introduzione al servizio file di Microsoft Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx
  [AzCopy 2.5: Ottimizzazione per gli scenari di copia su larga scala]: http://go.microsoft.com/fwlink/?LinkId=507682
  [AzCopy: Supporto per l'archiviazione con ridondanza geografica e accesso in lettura]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx
  [AzCopy: Trasferimento di dati con modalità riavviabile e token di firma di accesso condiviso]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx
  [AzCopy: Uso del comando di copia dei BLOB tra account]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx
  [AzCopy: Caricamento e download di file per BLOB di Microsoft Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx
