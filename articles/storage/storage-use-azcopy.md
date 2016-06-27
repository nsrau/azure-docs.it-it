<properties
	pageTitle="Copiare o spostare i dati in un archivio con AzCopy | Microsoft Azure"
	description="Utilizzare l'utilità AzCopy per spostare o copiare dati da o verso BLOB, tabelle e contenuto del file. Copiare i dati in archiviazione di Azure da file locali o copiare i dati all'interno o tra account di archiviazione. Migrare facilmente i dati in archiviazione di Azure."
	services="storage"
	documentationCenter=""
	authors="micurd"
	manager="jahogg"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="micurd"/>

# Trasferire dati con l'utilità della riga di comando AzCopy

## Panoramica

AzCopy è un'utilità della riga di comando di Windows progettata la copia dei dati in e da servizi di archiviazione BLOB, file e tabelle di Microsoft Azure usando semplici comandi con prestazioni ottimali. È possibile copiare dati da un oggetto a un altro all'interno dell'account di archiviazione o tra account di archiviazione.

> [AZURE.NOTE] Questa guida presuppone che si abbia già familiarità con [Archiviazione di Azure](https://azure.microsoft.com/services/storage/). In caso contrario, potrà essere utile leggere l'articolo [Introduzione ad Archiviazione di Microsoft Azure](storage-introduction.md). Ancora più importante, sarà necessario [creare un account di archiviazione](storage-create-storage-account.md#create-a-storage-account) per iniziare a usare AzCopy.

## Scaricare e installare AzCopy

### Windows

Scaricare la [versione più recente di AzCopy](http://aka.ms/downloadazcopy).

### Mac/Linux

AzCopy non è disponibile per i sistemi operativi Mac/Linux. Tuttavia, l'interfaccia della riga di comando di Azure è un'alternativa adatta per la copia di dati da e in Archiviazione di Azure. Per altre informazioni, vedere [Uso dell'interfaccia della riga di comando di Azure con archiviazione di Azure](storage-azure-cli.md).

## Scrittura del primo comando AzCopy

La sintassi di base per i comandi di AzCopy è la seguente:

	AzCopy /Source:<source> /Dest:<destination> [Options]

Aprire una finestra di comando e passare alla directory di installazione di AzCopy nel computer locale, dove si trova l'eseguibile `AzCopy.exe`. Se si vuole, è possibile aggiungere il percorso di installazione di AzCopy al percorso di sistema. Per impostazione predefinita, AzCopy viene installato in `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` (Windows a 64 bit) o in `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy` (Windows a 32 bit).

Gli esempi seguenti illustrano vari scenari per la copia dei dati da e in e da BLOB, file e tabelle di Microsoft Azure. Per una spiegazione dettagliata dei parametri usati in ogni esempio, vedere la sezione [Parametri di AzCopy](#azcopy-parameters).

## BLOB: scaricare

### Scaricare un singolo BLOB

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"

Se la cartella `C:\myfolder` non esiste ancora, AzCopy la creerà e scaricherà `abc.txt ` nella nuova cartella.

### Scaricare un singolo BLOB da un'area secondaria

	AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

È necessario che sia abilitata l'archiviazione con ridondanza geografica con accesso in lettura.

### Scaricare tutti BLOB

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

Si supponga che i BLOB seguenti risiedano nel contenitore specificato:

	abc.txt
	abc1.txt
	abc2.txt
	vd1\a.txt
	vd1\abcd.txt

Dopo l'operazione di download, la directory `C:\myfolder` includerà i file seguenti:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\vd1\a.txt
	C:\myfolder\vd1\abcd.txt

Se non si specifica l'opzione `/S`, non verrà scaricato alcun BLOB.

### Scaricare i BLOB con il prefisso specificato

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

Si supponga che i BLOB seguenti risiedano nel contenitore specificato. Verranno scaricati tutti i BLOB che iniziano con il prefisso `a`:

	abc.txt
	abc1.txt
	abc2.txt
	xyz.txt
	vd1\a.txt
	vd1\abcd.txt

Dopo l'operazione di download, la cartella `C:\myfolder` includerà i file seguenti:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt

Il prefisso si applica alla directory virtuale che forma la prima parte del nome del BLOB. Nell'esempio precedente, la directory virtuale non corrisponde al prefisso specificato, quindi non viene scaricata. Se inoltre l'opzione `\S` non è specificata, AzCopy non scaricherà alcun BLOB.

### Impostare l'ora dell'ultima modifica dei file esportati sulla stessa ora dei BLOB di origine

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

È anche possibile escludere BLOB dall'operazione di download in base all'ora dell'ultima modifica. Se ad esempio si vogliono escludere i BLOB in cui l'ora dell'ultima modifica è uguale o successiva a quella del file di destinazione, aggiungere l'opzione `/XN`.

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

In alternativa, se si vogliono escludere i BLOB in cui l'ora dell'ultima modifica è uguale o precedente a quella del file di destinazione, aggiungere l'opzione `/XO`.

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

## BLOB: caricare

### Caricare un singolo file

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"

Se il contenitore di destinazione specificato non esiste, AzCopy ne creerà uno e vi caricherà i file.

### Caricare un singolo file nella directory virtuale

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

Se la directory virtuale specificata non esiste, AzCopy caricherà il file in modo da includere la directory virtuale nel relativo nome, *ovvero* `vd/abc.txt` nell'esempio precedente.

### Caricare tutti i file

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

Se si specifica l'opzione `/S`, il contenuto della directory specificata viene caricato nell'archivio BLOB in modo ricorsivo, ovvero saranno caricati anche tutte le sottocartelle e i relativi file. Si supponga ad esempio che i file seguenti risiedano nella cartella `C:\myfolder`:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\subfolder\a.txt
	C:\myfolder\subfolder\abcd.txt

Dopo l'operazione di caricamento, il contenitore includerà i file seguenti:

  	abc.txt
	abc1.txt
	abc2.txt
	subfolder\a.txt
	subfolder\abcd.txt

Se non si specifica l'opzione `/S`, AzCopy non caricherà i file in modo ricorsivo. Dopo l'operazione di caricamento, il contenitore includerà i file seguenti:

	abc.txt
	abc1.txt
	abc2.txt

### Caricare i file corrispondenti ai criteri specificati

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

Si supponga che i file seguenti risiedano nella cartella `C:\myfolder`:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\xyz.txt
	C:\myfolder\subfolder\a.txt
	C:\myfolder\subfolder\abcd.txt

Dopo l'operazione di caricamento, il contenitore includerà i file seguenti:

	abc.txt
	abc1.txt
	abc2.txt
	subfolder\a.txt
	subfolder\abcd.txt

Se non si specifica l'opzione `/S`, AzCopy caricherà solo i BLOB che non si trovano in una directory virtuale:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt

### Specificare il tipo di contenuto MIME di un BLOB di destinazione

Per impostazione predefinita, AzCopy imposta il tipo di contenuto di un BLOB di destinazione su`application/octet-stream`. A partire dalla versione 3.1.0, è possibile specificare il tipo di contenuto in modo esplicito tramite l'opzione `/SetContentType:[content-type]`. Questa sintassi imposta il tipo di contenuto per tutti i BLOB in un'operazione di caricamento.

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4

Se si specifica `/SetContentType`senza fornire un valore, AzCopy imposterà il tipo di contenuto di ogni BLOB o file in base all'estensione di ciascuno di questi elementi.

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType

## BLOB: copiare

### Copiare un BLOB all'interno di un account di archiviazione

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt

Quando si copia un BLOB all'interno di un account di archiviazione, viene eseguita un'operazione di [copia sul lato server](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### Copiare un singolo BLOB tra account di archiviazione

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

Quando si copia un BLOB tra account di archiviazione, viene eseguita un'operazione di [copia sul lato server](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### Copiare in BLOB singolo da un'area secondaria all'area primaria

	AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

È necessario che sia abilitata l'archiviazione con ridondanza geografica con accesso in lettura.

### Copiare un singolo BLOB e i relativi snapshot tra account di archiviazione

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

Dopo l'operazione di copia, il contenitore di destinazione includerà il BLOB e i relativi snapshot. Supponendo che il BLOB dell'esempio precedente contenga due snapshot, nel contenitore saranno presenti il BLOB e gli snapshot seguenti:

	abc.txt
	abc (2013-02-25 080757).txt
	abc (2014-02-21 150331).txt

### Copiare in modo sincrono i BLOB tra account di archiviazione

Per impostazione predefinita, AzCopy copia i dati tra due endpoint di archiviazione in modo asincrono. L'operazione di copia verrà pertanto eseguita in background sfruttando la capacità di larghezza di banda disponibile non limitata da alcun contratto di servizio relativo alla velocità di copia di un BLOB. AzCopy controllerà periodicamente lo stato della copia fino a quando questa non verrà completata o risulterà non riuscita.

L'opzione `/SyncCopy` garantisce che l'operazione di copia ottenga una velocità coerente. AzCopy esegue la copia sincrona scaricando i BLOB da copiare dall'origine specificata nella memoria locale, per poi caricarli nella destinazione dell'archiviazione BLOB.

	AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy

`/SyncCopy` potrebbe generare costi di uscita aggiuntivi rispetto alla copia asincrona. Per evitare costi di uscita, è quindi consigliabile usare questa opzione in una VM di Azure che si trova nella stessa area dell'account di archiviazione di origine.

## File: scaricare

### Scaricare un singolo file

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Se l'origine specificata è una condivisione file di Azure, è necessario specificare il nome file esatto, *ad esempio* `abc.txt`, per copiare un solo file oppure specificare l'opzione `/S` per copiare tutti i file nella condivisione in modo ricorsivo. Se si specificano sia il criterio del file che l'opzione `/S`contemporaneamente, verrà generato un errore.

### Scaricare tutti i file

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

Le cartelle vuote non verranno scaricate.

## File: caricare

### Caricare un singolo file

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt

### Caricare tutti i file

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Le cartelle vuote non verranno caricate.

### Caricare i file corrispondenti ai criteri specificati

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

## File: copia

### Copiare tra condivisioni file

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S

### Copiare dalla condivisione file al BLOB

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S

Si noti che la copia asincrona da Archiviazione file a BLOB di pagine non è supportata.

### Copy dal BLOB alla condivisione file

	AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S

### Copiare file in modo sincrono

È possibile specificare l'opzione `/SyncCopy` per copiare i dati da archiviazione file ad archiviazione file, da archiviazione file ad archivio BLOB e da archivio BLOB ad archiviazione file in modo sincrono. AzCopy esegue questa operazione scaricando i dati di origine nella memoria locale e caricandoli di nuovo nella destinazione.

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

Durante la copia da Archiviazione file ad Archiviazione BLOB, il tipo di BLOB predefinito è il BLOB in blocchi. Per modificare il tipo di BLOB di destinazione, è possibile specificare l'opzione `/BlobType:page`.

Notare che `/SyncCopy` potrebbe generare ulteriori costi in uscita rispetto alla copia asincrona. Per evitare costi di uscita, è dunque consigliabile usare questa opzione nella macchina virtuale di Azure che si trova nella stessa area dell'account di archiviazione di origine.

## Tabella: esportare

### Esportare una tabella

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key

AzCopy scrive un file manifesto nella cartella di destinazione specificata. Il file manifesto viene usato dal processo di importazione per trovare i file di dati necessari ed eseguire la convalida di dati. Il file manifesto usa per impostazione predefinita la convenzione di denominazione seguente:

	<account name>_<table name>_<timestamp>.manifest

È possibile anche specificare l'opzione `/Manifest:<manifest file name>` per impostare il nome del file manifesto.

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest

### Dividere l'esportazione in più file

	AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy usa un *indice di volumi* nei nomi file di dati divisi per distinguere più file. L'indice di volumi è costituito da due parti, un *indice di intervalli di chiavi di partizione* e un *indice di file divisi*. Entrambi gli indici sono in base zero.

L'indice dell'intervallo di chiavi di partizione sarà 0 se l'utente non specifica l'opzione `/PKRS`.

Si supponga, ad esempio, che AzCopy generi due file di dati dopo che l'utente ha specificato l'opzione.`/SplitSize`. I nomi file di dati risultanti potrebbero essere:

	myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
	myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Tenere presente che il valore minimo possibile per l'opzione `/SplitSize` è di 32 MB. Se la destinazione specificata è l'archiviazione BLOB, AzCopy dividerà il file di dati quando le dimensioni raggiungeranno il limite delle dimensioni BLOB (200 GB), indipendentemente dal fatto che l'opzione `/SplitSize`sia stata specificata o meno dall'utente.

### Esportare la tabella nel formato di file di dati JSON o CSV

Per impostazione predefinita, AzCopy esporta le tabelle in file di dati JSON. È possibile specificare l'opzione `/PayloadFormat:JSON|CSV` per esportare le tabelle come JSON o CSV.

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV

Quando si specifica il formato di payload CSV, AzCopy genera anche un file di schema con estensione `.schema.csv` per ogni file di dati.

### Esportare entità tabella simultaneamente

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"

AzCopy avvierà operazioni simultanee per esportare le entità quando l'utente specifica l'opzione.`/PKRS`. Ogni operazione esporta un intervallo di chiavi di partizione.

Si noti che il numero di operazioni simultanee viene controllato anche dall'opzione `/NC`. AzCopy usa il numero di processori core come valore predefinito di `/NC`durante la copia di entità della tabella, anche se `/NC` non è stato specificato. Quando l'utente specifica l'opzione `/PKRS`, AzCopy usa il valore più basso dei due (intervalli di chiavi di partizione e operazioni simultanee specificate in modo implicito o esplicito) per determinare il numero di operazioni simultanee da avviare. Per ulteriori dettagli, digitare `AzCopy /?:NC`nella riga di comando.

### Esportare una tabella in un BLOB

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy genera un file di dati JSON nel contenitore BLOB con la convenzione di denominazione seguente:

	<account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

Il file di dati JSON generato segue il formato di payload per i metadati minimi. Per i dettagli su questo formato di payload, vedere [Formato di payload per le operazioni del servizio tabelle](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Si noti che quando si esportano tabelle nei BLOB, AzCopy scarica le entità tabella in file di dati temporanei locali e quindi carica tali entità nel BLOB. Questi file di dati temporanei vengono inseriti nella cartella di file journal con il percorso predefinito "<code>%LocalAppData%\\Microsoft\\Azure\\AzCopy</code>". È possibile specificare l'opzione/Z:[journal-file-folder] per modificare il percorso della cartella di file journal e quindi modificare il percorso dei file di dati temporanei. Le dimensioni dei file di dati temporanei vengono stabilite dalle dimensioni delle entità tabella e dalle dimensioni specificate con l'opzione /SplitSize, anche se il file di dati temporanei sul disco locale verrà eliminato immediatamente dopo averlo caricato nel BLOB. Verificare che lo spazio disponibile sul disco locale sia sufficiente per archiviare i file di dati temporanei prima di essere eliminati.

## Tabella: importare

### Importare una tabella

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace

L'opzione `/EntityOperation` indica come inserire le entità nella tabella. I valori possibili sono:

- `InsertOrSkip`: ignora un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.
- `InsertOrMerge`: unisce un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.
- `InsertOrReplace`: sostituisce un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.

Si noti che non è possibile specificare l'opzione `/PKRS`nello scenario di importazione. Diversamente dallo scenario di esportazione, in cui è necessario specificare l'opzione `/PKRS` per avviare operazioni simultanee, quando si importa una tabella AzCopy avvia l'esecuzione di operazioni simultanee per impostazione predefinita. Il numero predefinito di operazioni simultanee avviate è uguale al numero di processori core. Tuttavia, è possibile specificare un numero diverso di operazioni simultanee con l'opzione`/NC`. Per ulteriori dettagli, digitare `AzCopy /?:NC`nella riga di comando.

Si noti che AzCopy supporta solo l'importazione per JSON, non per CSV. AzCopy non supporta l'importazione di tabelle da file JSON e file manifesto creati dall'utente. Entrambi questi file devono provenire da un'esportazione di tabella di AzCopy. Per evitare errori, non modificare il file JSON o il file manifesto esportato.

### Importare entità nella tabella usando i BLOB

Si supponga che in un contenitore BLOB siano presenti: un file JSON che rappresenta una tabella di Azure e il relativo file manifesto associato.

	myaccount_mytable_20140103T112020.manifest
	myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

È possibile eseguire il comando seguente per importare le entità in una tabella usando il file manifesto disponibile nel contenitore BLOB:

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"

## Altre funzionalità di AzCopy

### Copiare solo i dati che non esistono nella destinazione

I parametri `/XO` e `/XN` consentono di escludere dalla copia, rispettivamente, le risorse di origine precedenti o più recenti. Per copiare solo e risorse di origine che non esistono nella destinazione, è possibile specificare entrambi i parametri nel comando AzCopy:

	/Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

	/Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

	/Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Nota: questi parametri non sono supportati quando l'origine o la destinazione è una tabella.

### Usare un file di risposta per specificare i parametri della riga di comando

	AzCopy /@:"C:\responsefiles\copyoperation.txt"

È possibile includere i parametri della riga di comando di AzCopy in un file di risposta. AzCopy elabora i parametri nel file come se fossero stati specificati nella riga di comando, eseguendo una sostituzione diretta con i contenuti del file.

Si supponga che sia presente un file di risposta denominato `copyoperation.txt` , che contiene le righe seguenti. Ogni parametro di AzCopy può essere specificato su una singola riga

	/Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

o su righe separate:

	/Source:http://myaccount.blob.core.windows.net/mycontainer
	/Dest:C:\myfolder
	/SourceKey:<sourcekey>
	/S
	/Y

Se il parametro viene suddiviso su due righe, l'operazione di AzCopy avrà esito negativo, come mostrato di seguito per il parametro `/sourcekey`:

	http://myaccount.blob.core.windows.net/mycontainer
 	C:\myfolder
	/sourcekey:
	<sourcekey>
	/S
	/Y

### Usare più file di risposta per specificare i parametri della riga di comando

Si supponga che siano presenti un file di risposta denominato `source.txt` , che specifica un contenitore di origine:

	/Source:http://myaccount.blob.core.windows.net/mycontainer

Un file di risposta denominato `dest.txt` , che specifica una cartella di destinazione nel file system:

	/Dest:C:\myfolder

E un file di risposta denominato `options.txt` , che specifica le opzioni per AzCopy:

	/S /Y

Per chiamare AzCopy con questi file di risposta, che risiedono in una directory `C:\responsefiles`, usare il comando seguente:

	AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

AzCopy elabora il comando come se fossero stati inclusi tutti i singoli parametri nella riga di comando:

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

### Specificare una firma di accesso condiviso

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

È anche possibile specificare una firma di accesso condiviso per il contenitore URI:

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

### Cartella del file journal

Ogni volta che si invia un comando ad AzCopy, l'utilità verifica se il file journal è presente nella cartella predefinita o in una cartella specificata dall'utente tramite questa opzione. Se il file journal non viene trovato in tali percorsi, AzCopy considera l'operazione come nuova e quindi crea un nuovo file journal.

Se il file journal esiste, AzCopy verifica se la riga di comando di input corrisponde alla riga di comando nel file journal. Se le righe di comando corrispondono, AzCopy avvia la ripresa dell'operazione incompleta. Se non corrispondono, verrà richiesto se si vuole sovrascrivere il file journal per avviare una nuova operazione o annullare l'operazione corrente.

Se si vuole usare il percorso predefinito per il file journal:

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

Se si omette l'opzione`/Z` o si specifica l'opzione `/Z` senza il percorso della cartella, come illustrato sopra, AzCopy creerà il file journal nel percorso predefinito, ovvero `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Se il file journal esiste già, AzCopy riprenderà l'operazione in base al file journal.

Se si vuole specificare un percorso personalizzato per il file journal:

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

In questo esempio viene creato il file journal, nel caso in cui non esista già: Se esiste, AzCopy riprenderà l'operazione in base al file journal.

Se si vuole riprendere un'operazione di AzCopy:

	AzCopy /Z:C:\journalfolder\

In questo esempio viene ripresa l'ultima operazione che potrebbe non essere stata completata a causa di errori.

### Generare un file di log

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

Se si specifica l'opzione `/V`senza fornire il percorso del file di log dettagliato, AzCopy creerà il file journal nel percorso predefinito, ovvero`%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

In caso contrario, è possibile creare un file di log in un percorso personalizzato:

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

Si noti che se si specifica un percorso relativo per l'opzione`/V` , ad esempio`/V:test/azcopy1.log`, il log dettagliato verrà creato nella directory di lavoro corrente all'interno di una sottocartella denominata `test`.

### Specificare il numero di operazioni simultanee da avviare

L'opzione `/NC`specifica il numero di operazioni di copia simultanee. Per impostazione predefinita, AzCopy avvia un determinato numero di operazioni simultanee per aumentare la velocità effettiva di trasferimento dei dati. Per le operazioni su tabella il numero di operazioni simultanee è uguale al numero di processori disponibili. Per le operazioni su BLOB e file il numero di operazioni simultanee è pari a 8 volte il numero di processori disponibili. Se AzCopy è in esecuzione in una rete con larghezza di banda ridotta, è possibile specificare un numero inferiore per il parametro /NC in modo da evitare errori generati dalla competizione tra le risorse.

### Eseguire AzCopy nell'Emulatore di archiviazione di Azure

È possibile eseguire AzCopy nell'[Emulatore di archiviazione di Azure](storage-use-emulator.md) per i BLOB:

	AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S

e le tabelle:

	AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table

## Parametri di AzCopy

I parametri per AzCopy sono descritti nella tabella seguente. È anche possibile digitare uno dei comandi seguenti dalla riga di comando per assistenza nell'uso di AzCopy:

- Per assistenza dettagliata della riga di comando per AzCopy:`AzCopy /?`
- Per assistenza dettagliata con un parametro di AzCopy:`AzCopy /?:SourceKey`
- Per esempi della riga di comando:`AzCopy /?:Samples`

### /Source: "source"

Specifica i dati di origine da cui eseguire la copia. L'origine può essere una directory di file system, un contenitore BLOB, una directory virtuale BLOB, una condivisione file di archiviazione, una directory file di archiviazione o una tabella di Azure.

**Applicabile a:** BLOB, file, tabelle

### /Dest:"destination"

Specifica la destinazione in cui eseguire la copia. La destinazione può essere una directory di file system, un contenitore BLOB, una directory virtuale BLOB, una condivisione file di archiviazione, una directory file di archiviazione o una tabella di Azure.

**Applicabile a:** BLOB, file, tabelle

### /Pattern:"file-pattern"

Specifica un criterio file indicante i file da copiare. Il comportamento del parametro /Pattern è determinato dal percorso dei dati di origine e dalla presenza dell'opzione relativa alla modalità ricorsiva. È possibile specificare la modalità ricorsiva tramite l'opzione /S.

Se l'origine specificata è una directory nel file system, è possibile usare i caratteri jolly standard e viene cercata una corrispondenza del criterio del file specificato con i file inclusi nella directory. Se è specificata l'opzione /S, AzCopy cerca anche la corrispondenza del criterio specificato con tutti i file inclusi nelle sottocartelle della directory.

Se l'origine specificata è un contenitore o una directory virtuale BLOB, non è possibile applicare i caratteri jolly. Se è specificata l'opzione /S, AzCopy interpreta i criteri del file specificato come prefisso del BLOB. Se non è specificata l'opzione /S, AzCopy cerca una corrispondenza esatta dei criteri con i nomi dei BLOB.

Se l'origine specificata è una condivisione file di Azure, è necessario specificare il nome file esatto (ad esempio, abc.txt) per copiare un solo file oppure specificare l'opzione /S per copiare tutti i file nella condivisione in modo ricorsivo. Se si specificano sia i criteri del file che l'opzione /S contemporaneamente, verrà generato un errore.

AzCopy utilizza la corrispondenza tra maiuscole e minuscole quando l’opzione /Source è un contenitore BLOB o una directory virtuale di BLOB e non utilizza la corrispondenza tra maiuscole e minuscole in tutti gli altri casi.

Il criterio del file predefinito usato quando non viene specificato alcun criterio è *.* per un percorso del file system o un prefisso vuoto per un percorso di Archiviazione di Azure. Non è consentito specificare più criteri file.

**Applicabile a:** BLOB, file

### /DestKey:"storage-key"

Specifica la chiave dell'account di archiviazione per la risorsa di destinazione.

**Applicabile a:** BLOB, file, tabelle

### /DestSAS:"sas-token"

Specifica una firma di accesso condiviso con autorizzazioni di LETTURA e SCRITTURA per la destinazione (se applicabile). Racchiudere la firma di accesso condiviso tra virgolette doppie, in quanto potrebbe contenere caratteri della riga di comando speciali.

Se la risorsa di destinazione è una tabella, una condivisione file o un contenitore BLOB, è possibile specificare questa opzione seguita dal token di accesso condiviso o specificare la firma di accesso condiviso come parte dell'URI della tabella, della condivisione file o del contenitore BLOB, senza questa opzione.

Se l'origine e la destinazione sono entrambi BLOB, il BLOB di destinazione deve essere nello stesso account di archiviazione del BLOB di origine.

**Applicabile a:** BLOB, file, tabelle

### /SourceKey:"storage-key"

Specifica la chiave dell'account di archiviazione per la risorsa di origine.

**Applicabile a:** BLOB, file, tabelle

### /SourceSAS:"sas-token"

Specifica una firma di accesso condiviso con autorizzazioni di LETTURA ed ELENCO per l’origine (se applicabile). Racchiudere la firma di accesso condiviso tra virgolette doppie, in quanto potrebbe contenere caratteri della riga di comando speciali.

Se la risorsa di origine è un contenitore BLOB e non viene fornita né una chiave né una firma di accesso condiviso, il contenitore BLOB verrà letto tramite accesso anonimo.

Se l'origine è una tabella o una condivisione file, è necessario specificare una chiave o una firma di accesso condiviso.

**Applicabile a:** BLOB, file, tabelle

### /S

Specifica la modalità ricorsiva per le operazioni di copia. Nella modalità ricorsiva, AzCopy copierà tutti i BLOB o file corrispondenti al criterio di file specificato, inclusi quelli nelle sottocartelle.

**Applicabile a:** BLOB, file

### /BlobType:"block" | "page" | "append"

Specifica se il BLOB di destinazione è un BLOB in blocchi, un BLOB di pagine o un BLOB di accodamento. Questa opzione è applicabile solo per l'upload di un BLOB. In caso contrario, viene generato un errore. Se la destinazione è un BLOB e questa opzione non è specificata, per impostazione predefinita AzCopy crea un BLOB in blocchi.

**Applicabile a:** BLOB

### /CheckMD5

Calcola un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato nella proprietà Content-MD5 del BLOB o del file corrisponda all'hash calcolato. Il controllo MD5 è disattivato per impostazione predefinita, quindi è necessario specificare questa opzione per eseguire il controllo MD5 al download dei dati.

Si noti che Archiviazione di Azure non garantisce che l'hash MD5 archiviato per il BLOB o il file sia aggiornato. È responsabilità del client aggiornare l'hash MD5 ogni volta che il BLOB o il file viene modificato.

AzCopy imposta sempre la proprietà Content-MD5 per un BLOB o un file di Azure dopo averlo caricato nel servizio.

**Applicabile a:** BLOB, file

### /Snapshot

Indica se trasferire gli snapshot o meno. Questa opzione è valida solo quando l'origine è un BLOB.

Gli snapshot di BLOB trasferiti vengono rinominati nel formato seguente: nome-BLOB (ora-snapshot).estensione.

Per impostazione predefinita, gli snapshot non vengono copiati.

**Applicabile a:** BLOB

### /V:[verbose-log-file]

Esegue l'output dei messaggi di stato dettagliati in un file di log.

Per impostazione predefinita, al file di log dettagliato viene assegnato il nome AzCopyVerbose.log in `%LocalAppData%\Microsoft\Azure\AzCopy`. Se si specifica un percorso di file esistente per questa opzione, il log dettagliato verrà aggiunto al file indicato.

**Applicabile a:** BLOB, file, tabelle

### /Z:[journal-file-folder]

Specifica la cartella del file journal per la ripresa di un'operazione.

AzCopy supporta sempre la ripresa di un'operazione interrotta.

Se questa opzione non è specificata o se è specificata senza un percorso di cartella, AzCopy creerà il file journal nel percorso predefinito, ovvero %LocalAppData%\\Microsoft\\Azure\\AzCopy.

Ogni volta che si invia un comando ad AzCopy, l'utilità verifica se il file journal è presente nella cartella predefinita o in una cartella specificata dall'utente tramite questa opzione. Se il file journal non viene trovato in tali percorsi, AzCopy considera l'operazione come nuova e quindi crea un nuovo file journal.

Se il file journal esiste, AzCopy verifica se la riga di comando di input corrisponde alla riga di comando nel file journal. Se le righe di comando corrispondono, AzCopy avvia la ripresa dell'operazione incompleta. Se non corrispondono, verrà richiesto se si vuole sovrascrivere il file journal per avviare una nuova operazione o annullare l'operazione corrente.

Se l'operazione viene completata senza errori, il file journal viene eliminato.

Si noti che la ripresa di un'operazione da un file journal creato da una versione precedente di AzCopy non è supportata.

**Applicabile a:** BLOB, file, tabelle

### /@:"parameter-file"

Specifica un file contenente parametri. AzCopy elabora i parametri nel file come se fossero stati specificati nella riga di comando.

In un file di risposta è possibile specificare più parametri su una sola riga o specificare un parametro su ogni riga. Si noti che un singolo parametro non può estendersi su più righe.

I file di risposta possono includere righe di commento che iniziano con il simbolo #.

È possibile specificare più file di risposta. Tuttavia, tenere presente che AzCopy non supporta file di risposta annidati.

**Applicabile a:** BLOB, file, tabelle

### /Y

Elimina tutte le richieste di conferma di AzCopy.

**Applicabile a:** BLOB, file, tabelle

### /L

Specifica solo un'operazione di elenco, non viene copiato alcun dato.

AzCopy interpreta l'uso di questa opzione come una simulazione per l'esecuzione della riga di comando senza questa opzione /L e conta il numero di oggetti copiati. È possibile specificare l'opzione /V contemporaneamente per controllare quali oggetti vengono copiati nel log dettagliato.

Il comportamento di questa opzione è determinato dal percorso dei dati di origine e dalla presenza dell'opzione relativa alla modalità ricorsiva /S e dell’opzione di modello file /Pattern.

AzCopy richiede l'autorizzazione ELENCO e LETTURA per questo percorso di origine quando si utilizza questa opzione.

**Applicabile a:** BLOB, file

### /MT

Imposta l'ora dell'ultima modifica di un file scaricato sulla stessa ora del file o del BLOB di origine.

**Applicabile a:** BLOB, file

### /XN

Esclude una risorsa di origine più recente. La risorsa non verrà copiata se l'ora dell'ultima modifica dell'origine è uguale alla destinazione o più recente.

**Applicabile a:** BLOB, file

### /XO

Esclude una risorsa di origine meno recente. La risorsa non verrà copiata se l'ora dell'ultima modifica dell'origine è uguale alla destinazione o meno recente.

**Applicabile a:** BLOB, file

### /A

Carica solo i file per i quali è impostato l'attributo Archive.

**Applicabile a:** BLOB, file

### /IA:[RASHCNETOI]

Carica solo i file per i quali sono impostati gli attributi specificati.

Gli attributi disponibili sono:

- R = File di sola lettura
- A = File pronti per l'archiviazione
- S = File di sistema
- H = File nascosti
- C = File compressi
- N = File normali
- E = File crittografati
- T = File temporanei
- O = File offline
- I = File non indicizzati

**Applicabile a:** BLOB, file

### /XA:[RASHCNETOI]

Esclude i file per i quali sono impostati gli attributi specificati.

Gli attributi disponibili sono:

- R = File di sola lettura
- A = File pronti per l'archiviazione
- S = File di sistema
- H = File nascosti
- C = File compressi
- N = File normali
- E = File crittografati
- T = File temporanei
- O = File offline
- I = File non indicizzati

**Applicabile a:** BLOB, file

### /Delimiter:"delimiter"

Indica il delimitatore usato per delimitare le directory virtuali in un nome BLOB.

Per impostazione predefinita, AzCopy usa il carattere / come delimitatore. Tuttavia, a questo scopo supporta anche l'uso di caratteri comuni, ad esempio @, # o %. Se è necessario includere uno di questi caratteri speciali nella riga di comando, racchiudere il nome del file tra virgolette doppie.

Questa opzione si applica solo per il download di BLOB.

**Applicabile a:** BLOB

### /NC:"number-of-concurrent-operations"

Specifica il numero di operazioni simultanee.

Per impostazione predefinita, AzCopy avvia un determinato numero di operazioni simultanee per aumentare la velocità effettiva di trasferimento dei dati. Si noti che un numero elevato di operazioni simultanee in un ambiente con una larghezza di banda ridotta potrebbe sovraccaricare la connessione di rete e impedire il corretto completamento delle operazioni. Limitare le operazioni simultanee in base alla larghezza di banda di rete effettivamente disponibile.

Il limite massimo per le operazioni simultanee è 512.

**Applicabile a:** BLOB, file, tabelle

### /SourceType:"Blob" | "Table"

Indica che la risorsa `source` è un BLOB disponibile nell'ambiente di sviluppo locale, in esecuzione nell'emulatore di archiviazione.

**Applicabile a:** BLOB, tabelle

### /DestType:"Blob" | "Table"

Indica che la risorsa `destination` è un BLOB disponibile nell'ambiente di sviluppo locale, in esecuzione nell'emulatore di archiviazione.

**Applicabile a:** BLOB, tabelle

### /PKRS:"key1#key2#key3#..."

Divide l'intervallo di chiavi di partizione per consentire l'esportazione dei dati della tabella in parallelo, aumentando la velocità dell'operazione di esportazione.

Se questa opzione non è specificata, AzCopy usa un solo thread per esportare le entità della tabella. Ad esempio, se l'utente specifica /PKRS:"aa#bb", AzCopy avvia tre operazioni simultanee.

Ogni operazione esporta uno dei tre intervalli di chiavi di partizione, nel modo seguente:

  [first-partition-key, aa)

  [aa, bb)

  [bb, last-partition-key]

**Applicabile a:** tabelle

### /SplitSize:"file-size"

Specifica la dimensione del file esportato diviso in MB, il valore minimo consentito è 32.

Se questa opzione non è specificata, AzCopy esporterà i dati della tabella in un solo file.

Se i dati della tabella vengono esportati in un BLOB e le dimensioni del file esportato raggiungono il limite di 200 GB per le dimensioni del BLOB, AzCopy dividerà il file esportato, anche se questa opzione non viene specificata.

**Applicabile a:** tabelle

### /EntityOperation:"InsertOrSkip" | "InsertOrMerge" | "InsertOrReplace"

Specifica il comportamento di importazione dei dati della tabella.

- InsertOrSkip - Ignora un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.

- InsertOrMerge - Unisce un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.

- InsertOrReplace - Sostituisce un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.

**Applicabile a:** tabelle

### /Manifest:"manifest-file"

Specifica il file manifesto per l'operazione di importazione ed esportazione della tabella.

Questa opzione è facoltativa durante l'operazione di esportazione. AzCopy genera un file manifesto con il nome predefinito se non si specifica questa opzione.

Questa opzione è necessaria durante l'operazione di importazione per individuare i file di dati.

**Applicabile a:** tabelle

### /SyncCopy

Indica se si desidera copiare BLOB o file in modo sincrono da un endpoint di Archiviazione di Azure a un altro.

Per impostazione predefinita, AzCopy esegue la copia in modo asincrono sul lato server. Specificare questa opzione per eseguire una copia sincrona, che scarica BLOB o file nella memoria locale e quindi li carica in Archiviazione di Azure.

È possibile utilizzare questa opzione quando si copiano file all'interno dell'archiviazione BLOB o dell'archiviazione file oppure quando i file vengono copiati dall'archiviazione BLOB all'archiviazione file o viceversa.

**Applicabile a:** BLOB, file

### /SetContentType:"content-type"

Specifica il tipo di contenuto MIME per i BLOB o i file di destinazione.

Per impostazione predefinita, AzCopy imposta il tipo di contenuto per un BLOB o un file su application/octet-stream. Se si desidera impostare il tipo di contenuto per tutti i BLOB o i file, è possibile specificare in modo esplicito un valore per questa opzione.

Se si specifica questa opzione senza fornire un valore, AzCopy imposterà il tipo di contenuto di ogni BLOB o file in base all'estensione di ciascuno di questi elementi.

**Applicabile a:** BLOB, file

### /PayloadFormat:"JSON" | "CSV"

Specifica il formato del file di dati tabella esportato.

Se questa opzione non è specificata, per impostazione predefinita AzCopy esporta il file di dati tabella nel formato JSON.

**Applicabile a:** tabelle

## Problemi noti e procedure consigliate

### Limitare le scritture simultanee durante la copia dei dati

Durante la copia di BLOB o file con AzCopy, tenere presente che altre applicazioni potrebbero modificare i dati mentre è in corso la copia. Se possibile, assicurarsi che i dati copiati non vengano modificati durante l'operazione di copia. Ad esempio, se si copia un file VHD associato a una macchina virtuale di Azure, verificare che altre applicazioni non stiano scrivendo nel file VHD durante la copia. Un buon metodo per eseguire questa operazione è tramite leasing della risorsa da copiare. In alternativa, è prima possibile creare uno snapshot del file VHD e quindi copiare lo snapshot.

Se non è possibile evitare che altre applicazioni scrivano nei BLOB o nei file durante l'operazione di copia, tenere presente che al termine del processo la parità delle risorse copiate con le risorse di origine potrebbe non essere completa.

### Eseguire un'istanza di AzCopy su un computer.
AzCopy è progettato per massimizzare l'utilizzo della risorsa del computer per accelerare il trasferimento dei dati, è consigliabile eseguire solo un'istanza di AzCopy in un computer e specificare l'opzione `/NC` se sono necessarie più operazioni simultanee. Per ulteriori dettagli, digitare `AzCopy /?:NC` nella riga di comando.

### Abilitare gli algoritmi MD5 conformi a FIPS per AzCopy quando si utilizzano gli algoritmi FIPS compatibili per crittografia, hash e firma.
AzCopy per impostazione predefinita utilizza l'implementazione .NET MD5 per calcolare l’algoritmo MD5 durante la copia di oggetti, ma esistono alcuni requisiti di sicurezza per cui è necessario abilitare in AzCopy l'impostazione MD5 conforme a FIPS.

È possibile creare un file app.config `AzCopy.exe.config` con la proprietà `AzureStorageUseV1MD5` e conservarlo con AzCopy.exe.

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	  <appSettings>
	    <add key="AzureStorageUseV1MD5" value="false"/>
	  </appSettings>
	</configuration>

Per la proprietà "AzureStorageUseV1MD5" • True: valore predefinito, AzCopy utilizzerà l’implementazione .NET MD5. • False: AzCopy utilizzerà l'algoritmo MD5 conforme a FIPS.

Si noti che gli algoritmi conformi a FIPS sono disabilitati per impostazione predefinita nel computer Windows. Digitare secpol.msc nella finestra Esegui e selezionare questa opzione in Impostazione di sicurezza -> Criteri locali -> Opzioni di sicurezza -> Crittografia di sistema: utilizza algoritmi FIPS compatibili per crittografia, hash e firma.

## Passaggi successivi

Per altre informazioni su Archiviazione di Azure e AzCopy, vedere le risorse seguenti:

### Documentazione di Archiviazione di Azure

- [Introduzione ad Archiviazione di Azure](storage-introduction.md)
- [Come usare l'archiviazione BLOB da .NET](storage-dotnet-how-to-use-blobs.md)
- [Come usare l'archiviazione file da .NET](storage-dotnet-how-to-use-files.md)
- [Come usare l'archiviazione tabelle da .NET](storage-dotnet-how-to-use-tables.md)
- [Come creare, gestire o eliminare un account di archiviazione](storage-create-storage-account.md)

### Post del blog di Archiviazione di Azure:
- [Introduzione alla versione di anteprima della libreria per lo spostamento dei dati di Archiviazione di Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
- [AzCopy: introduzione alla copia sincrona e al tipo di contenuto personalizzato](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
- [AzCopy: annuncio della disponibilità per tutti di AzCopy 3.0 oltre alla versione di anteprima di AzCopy 4.0 con il supporto di tabelle e file](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy: ottimizzazione per gli scenari di copia su larga scala](http://go.microsoft.com/fwlink/?LinkId=507682)
- [AzCopy:supporto per l'archiviazione con ridondanza geografica e accesso in lettura](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy:trasferimento di dati con modalità riavviabile e token di firma di accesso condiviso](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy: uso del comando di copia dei BLOB tra account](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy: Caricamento e download di file per BLOB di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)

<!---HONumber=AcomDC_0615_2016-->