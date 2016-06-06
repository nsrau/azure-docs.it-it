<properties
	pageTitle="Uso del servizio di importazione/esportazione per trasferire dati nell'archiviazione BLOB | Microsoft Azure"
	description="Informazioni su come creare ed esportare processi nel portale classico di Azure per trasferire dati all'archiviazione BLOB."
	authors="renashahmsft"
	manager="aungoo"
	editor="tysonn"
	services="storage"
	documentationCenter=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="renash"/>


# Usare il servizio di importazione/esportazione di Microsoft Azure per trasferire i dati nell'archivio Blob

## Panoramica

Il servizio Importazione/Esportazione di Azure consente di trasferire in modo sicuro grandi quantità di dati nell'archiviazione BLOB di Azure tramite la spedizione delle unità disco rigido a un data center di Azure. È anche possibile usare questo servizio per trasferire i dati dall'archiviazione BLOB di Azure a unità disco rigido e spedirle al sito locale. Questo servizio è adatto in situazioni in cui si desidera trasferire vari terabyte di dati da o verso Azure ma non è possibile eseguire il caricamento o il download sulla rete a causa della larghezza di banda limitata o degli elevati costi della rete.

Il servizio richiede che le unità disco rigido siano crittografate con crittografica bitlocker per la protezione dei dati. Il servizio supporta gli account di archiviazione classici presenti in tutte le aree di Azure pubblico. È necessario spedire l'unità disco rigido a una delle località supportate specificate più avanti in questo articolo. In questo articolo verranno fornite ulteriori informazioni sul servizio Importazione/Esportazione di Azure e su come spedire le unità per copiare i dati da e verso l'archiviazione BLOB di Azure.

> [AZURE.IMPORTANT] È possibile creare e gestire i processi di importazione ed esportare tramite il portale classico o l'API REST per gli account di archiviazione classici. Gli account di archiviazione ARM non sono attualmente supportati.

## Quando si deve usare il servizio Importazione/Esportazione di Azure?

È possibile usare il servizio Importazione/Esportazione di Azure quando il caricamento o il download dei dati attraverso la rete è troppo lento o l'acquisizione di maggiore larghezza di banda di rete comporta costi proibitivi.

È possibile usare il servizio in scenari come:

- Migrazione di dati al cloud: spostamento di grandi quantità di dati in Azure in modo veloce e a costi contenuti.
- Distribuzione del contenuto: invio rapido di dati ai siti dei clienti.
- Backup: esecuzione di backup dei dati locali da inserire nell'archiviazione BLOB di Azure.
- Ripristino di dati: ripristino di una grande quantità di dati inseriti nell'archiviazione BLOB in modo che siano recapitati al percorso locale.


## Prerequisiti

In questa sezione sono riportati i prerequisiti necessari per usare il servizio. Si consiglia di esaminarli attentamente prima di spedire le unità.

### Account di archiviazione

Per usare il servizio Importazione/Esportazione di Azure, è necessario disporre di una sottoscrizione di Azure esistente e di uno o più account di archiviazione **classici**. Ogni processo può essere usato per trasferire dati da o verso un solo account di archiviazione classico. In altre parole, un processo non può estendersi tra più account di archiviazione. Per informazioni sulla creazione di un nuovo account di archiviazione, vedere [Come creare un account di archiviazione](storage-create-storage-account.md#create-a-storage-account).

### Tipi di BLOB

È possibile usare il servizio Importazione/Esportazione di Azure per copiare dati su BLOB **in blocchi** o BLOB **di pagine**. Al contrario, usando questo servizio è possibile esportare solo BLOB **in blocchi**, BLOB **di pagine** o BLOB **di accodamento** dall'archiviazione di Azure.

### Job

Per iniziare il processo di importazione o di esportazione dall'archiviazione BLOB, creare innanzitutto un processo, che potrà essere un processo di importazione o un processo di esportazione:

- Creare un processo di importazione quando si desidera trasferire i dati locali nei BLOB nell'account di archiviazione di Azure.
- Creare un processo di esportazione quando si desidera trasferire su dischi rigidi spediti all'utente i dati attualmente archiviati come BLOB nell'account di archiviazione.

Quando si crea un processo, si notifica al servizio Importazione/Esportazione che si spedirà uno o più dischi rigidi a un data center di Azure.
- Per un processo di importazione, si spediranno dischi rigidi contenenti i dati. 
- Per un processo di esportazione, si spediranno dischi rigidi vuoti. 
- È possibile spedire fino a 10 unità disco rigido per ogni processo.

È possibile creare un processo di importazione o di esportazione mediante il [portale classico](https://manage.windowsazure.com/) o l'[API REST Importazione/Esportazione di Archiviazione di Azure](http://go.microsoft.com/fwlink/?LinkID=329099)

### Strumento client

Il primo passaggio della creazione di un processo di **importazione** consiste nel preparare l'unità che verrà inviata per l'importazione. Per preparare l'unità, è necessario connetterla a un server locale ed eseguire lo strumento client di Importazione/Esportazione di Azure nel server locale. Questo strumento client facilita la copia dei dati sull'unità, crittografando i dati sull'unità con BitLocker e generando i file journal dell'unità.

I file journal contengono le informazioni di base sul processo e l'unità, come il numero di serie dell'unità e il nome dell'account di archiviazione. Questo file journal non è archiviato nell'unità. Viene usato durante la creazione del processo di importazione. La procedura dettagliata sulla creazione dei processi è riportata più avanti in questo articolo.

Lo strumento client è compatibile solo con il sistema operativo Windows a 64 bit. Vedere la sezione [Sistema operativo](#operating-system) per le specifiche versioni del sistema operativo supportate.

Scaricare la versione più recente dello strumento client da [Strumento client di importazione/esportazione di Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409). Per altri dettagli sull'uso dello strumento Importazione/Esportazione di Azure, vedere il [materiale di riferimento dello strumento Importazione/Esportazione di Azure](http://go.microsoft.com/fwlink/?LinkId=329032).

### Unità disco rigido

Con il servizio Importazione/Esportazione sono supportati solo i dischi rigidi interni SATA II/III da 3,5 pollici. È possibile usare dischi rigidi fino a 8 TB. Per i processi di importazione, verrà elaborato solo il primo volume di dati sull'unità. Il volume di dati deve essere formattato con NTFS. Quando si copiano dati sul disco rigido, è possibile collegarlo direttamente tramite un connettore SATA oppure collegarlo esternamente tramite un adattatore USB SATA II/III esterno. Si consiglia di usare uno degli adattatori USB SATA II/III esterni seguenti:

- Anker 68UPSATAA-02BU
- Anker 68UPSHHDS-BU
- Startech SATADOCK22UE
- Sharkoon QuickPort XT HC

Se si ha un convertitore non incluso nell'elenco precedente, prima di acquistare un convertitore supportato è possibile provare a eseguire lo strumento Importazione/Esportazione di Azure con il convertitore per preparare l'unità e vedere se funziona.

> [AZURE.IMPORTANT] I dischi rigidi esterni forniti con un adattatore USB incorporato non sono supportati da questo servizio. Non inviare un'unità disco rigido esterna. Inoltre non è possibile usare il disco all'interno del casing di un'unità disco rigido esterna. Non sono supportate neppure le unità USB.

### Crittografia

I dati sull'unità devono essere crittografati mediante Crittografia unità BitLocker, che protegge i dati mentre sono in transito.

Per i processi di importazione è necessaria la crittografia bitlocker delle unità. Ovvero, è possibile usare il parametro /encrypt quando si esegue lo strumento client durante la preparazione dell'unità. In alternativa, è possibile abilitare la crittografia bitlocker manualmente nell'unità e specificare la chiave di crittografia nella riga di comando dello strumento client durante la preparazione dell'unità.

Per i processi di esportazione, dopo aver copiato i dati nelle unità, il servizio applicherà la crittografia bitlocker all'unità prima di rispedirla indietro all'utente. La chiave di crittografia verrà fornita all'utente tramite il portale classico.

### Sistema operativo

Per preparare il disco rigido mediante lo strumento Importazione/Esportazione di Azure prima della spedizione ad Azure, è possibile usare uno dei seguenti sistemi operativi a 64 bit:

Windows 7 Enterprise, Windows 7 Ultimate, Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise, Windows 10*, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Tutti questi sistemi operativi supportano la funzionalità Crittografia unità BitLocker.

> [AZURE.IMPORTANT] *Se per preparare il disco rigido si usa un computer con Windows 10, scaricare la versione più recente dello strumento Importazione/Esportazione di Azure.

### Località

Il servizio Importazione/Esportazione di Azure supporta la copia dei dati da e verso tutti gli account di archiviazione di Azure pubblici. È possibile spedire le unità disco rigido a una delle seguenti posizioni. Se l'account di archiviazione si trova in un percorso di Azure pubblico che non è specificato qui, sarà fornita una località di spedizione alternativa quando si crea il processo nel portale classico o tramite l'API REST.

Località di spedizione supportate:

- Stati Uniti Orientali

- Stati Uniti occidentali

- Stati Uniti orientali 2

- Stati Uniti centrali

- Stati Uniti centro-settentrionali

- Stati Uniti centro-meridionali

- Europa settentrionale

- Europa occidentale

- Asia orientale

- Asia sudorientale

- Australia orientale

- Australia sudorientale

- Giappone occidentale

- Giappone orientale

- India centrale


### Spedizione

**Spedizione di unità al data center:**

Quando si crea un processo di importazione o esportazione, viene fornito un indirizzo di spedizione di una delle località supportate per spedire le unità. L'indirizzo di spedizione fornito dipende dalla posizione dell'account di archiviazione, ma potrebbe non corrispondere alla posizione dell'account di archiviazione.

Per spedire le unità all'indirizzo di destinazione è possibile usare vettori come FedEx, DHL, UPS o il servizio postale.

**Spedizione di restituzione delle unità dal data center:**

Quando si crea un processo di importazione o esportazione, è necessario fornire un indirizzo di restituzione a cui le unità andranno rispedite al termine del processo. Assicurarsi di fornire un indirizzo di restituzione valido per evitare ritardi nell'elaborazione.

È necessario fornire anche un numero di account vettore valido FedEx o DHL in base alla località, per la spedizione delle unità da restituire. Per la spedizione di unità da restituire da località negli Stati Uniti e in Europa è richiesto un numero di account FedEx. Per la spedizione di unità da restituire da località in Asia e in Australia è richiesto un numero di account DHL. È possibile creare un account vettore [FedEx](http://www.fedex.com/us/oadr/) (per Stati Uniti ed Europa) o [DHL](http://www.dhl.com/) (per Asia e Australia) se non è già disponibile. Se si dispone già di un numero di account vettore, verificare che sia valido.

Durante la spedizione dei pacchetti, seguire le condizioni di [Condizioni per l’Uso dei Servizi di Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

> [AZURE.IMPORTANT] È possibile che i supporti fisici spediti debbano passare i confini internazionali. È responsabilità del cliente assicurarsi che i supporti fisici e i dati siano importati e/o esportati conformemente alle leggi vigenti. Prima di spedire i supporti fisici, rivolgersi ai consulenti per verificare che i supporti e i dati possano essere spediti ai data center specificati in modo conforme alle normative. Ciò assicura che la spedizione raggiunga Microsoft in modo tempestivo.

## Come funziona il servizio Importazione/Esportazione di Azure

È possibile trasferire dati tra il sito locale e l'archiviazione BLOB di Azure tramite il servizio Importazione/Esportazione di Azure creando "processi" e spedendo unità disco rigido a un data center di Azure. Ogni unità disco rigido che si spedisce è associata a un singolo processo. Ogni processo è associato a un singolo account di archiviazione. Leggere attentamente la sezione dedicata ai prerequisiti per informazioni sulle specifiche di questo servizio, ad esempio i tipi di BLOB supportati, i tipi di disco, le località e la spedizione.

In questa sezione presenteremo una descrizione generale dei passaggi necessari per importare ed esportare processi. Più avanti, nella sezione dedicata all'avvio rapido, forniremo istruzioni dettagliate per la creazione di un processo di importazione ed esportazione.

### Analisi di un processo di importazione

In generale, un processo di importazione prevede i passaggi seguenti:

- Determinare i dati da importare e il numero di unità necessarie.
- Identificare i BLOB di destinazione per i dati nell'archiviazione BLOB.
- Usare lo strumento Importazione/Esportazione di Azure per copiare i dati in uno o più dischi rigidi e crittografarli con Bitlocker.  
- Creare un processo di importazione nel portale classico o mediante l'API REST nell'account di archiviazione classico di destinazione. Se si usa il portale classico, caricare i file journal dell'unità.
- Specificare l'indirizzo di restituzione e il numero di account vettore per la spedizione di ritorno.
- Spedire le unità disco rigido all'indirizzo di spedizione fornito durante la creazione del processo.
- Aggiornare il numero di tracciabilità della consegna nei dettagli del processo di importazione e inviare il processo di importazione.
- Le unità vengono ricevute ed elaborate nel data center di Azure. 
- Le unità vengono restituite usando l'account vettore all'indirizzo di restituzione fornito nel processo di importazione.

### Analisi di un processo di esportazione

In generale, un processo di esortazione prevede i passaggi seguenti:

- Determinare i dati da esportare e il numero di unità necessarie.
- Identificare i BLOB di origine o i percorsi dei contenitori dei dati nell'archiviazione BLOB.
- Creare un processo di esportazione nel portale classico o mediante l'API REST nell'account di archiviazione di origine.
- Specificare i BLOB di origine o i percorsi dei contenitori dei dati nel processo di esportazione.
- Specificare l'indirizzo di restituzione e il numero di account vettore per la spedizione di ritorno.
- Spedire le unità disco rigido all'indirizzo di spedizione fornito durante la creazione del processo.
- Aggiornare il numero di tracciabilità della consegna nei dettagli del processo di esportazione e inviare il processo di esportazione.
- Le unità vengono ricevute ed elaborate nel data center di Azure. 
- Le unità sono crittografate con bitlocker e le chiavi sono disponibili tramite il portale classico.  
- Le unità vengono restituite usando l'account vettore all'indirizzo di restituzione fornito nel processo di importazione.

### Visualizzazione dello stato dei processi

È possibile tenere traccia dello stato dei processo di importazione o esportazione dal portale classico. Passare all'account di archiviazione nel portale classico e fare clic sulla scheda **Importazione/Esportazione**. Nella pagina verrà visualizzato un elenco dei processi. È possibile filtrare l'elenco per stato processo, nome processo, tipo di processo o numero di spedizione.

Stato processo|Descrizione
---|---
Creating|Il processo è stato creato, ma non sono ancora state specificate le informazioni per la spedizione.
Shipping|Il processo è stato creato e sono state specificate le informazioni per la spedizione. **Nota**: quando l'unità viene recapitata al data center di Azure, lo stato potrebbe continuare a essere "Spedizione" per un certo tempo. Quando il servizio inizia a copiare i dati, lo stato diventa "Trasferimento". Se si desidera visualizzare uno stato più specifico dell'unità, è possibile usare l'API REST. 
Transferring|È in corso il trasferimento dei dati dal disco rigido (per un processo di importazione) o al disco rigido (per un processo di esportazione).
Packaging|Il trasferimento dei vostri dati è stato completato ed è in corso la preparazione del disco rigido per la spedizione all'utente.
Complete|Il disco rigido è stato spedito all'utente.

### Tempo di elaborazione del processo 

Il tempo impiegato per elaborare un processo di importazione/esportazione varia in base a diversi fattori, ad esempio i tempi di spedizione, il tipo di processo, il tipo e la dimensione dei dati da copiare e la dimensione dei dischi forniti. Questa funzionalità non prevede un Contratto di servizio. È possibile usare l'API REST per tenere traccia più da vicino dell'avanzamento del processo. È disponibile un parametro indicante la percentuale di completamento nell'operazione Elencare i processi che fornisce un'indicazione dell'avanzamento della copia. In caso di processi di importazione/esportazione che richiedono una precisa tempistica, invitiamo gli utenti a contattarci per avere una stima dei tempi di completamento.

### Prezzi 

**Tariffa di gestione delle unità**

Esiste una tariffa di gestione per ogni unità elaborata come parte del processo di importazione o esportazione. Vedere i dettagli sui [prezzi di importazione/esportazione di Azure](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Costi di spedizione**

Quando si spediscono unità ad Azure, si paga il costo di spedizione al vettore. Quando Azure restituisce le unità all'utente, il costo della spedizione di restituzione viene addebitato sull'account del vettore fornito al momento della creazione del processo.

**Costi di transazione**

Non ci sono costi di transazione quando si importano dati nell'archiviazione BLOB. Sono applicabili spese di uscita standard quando si esportano dati dall'archiviazione BLOB. Per ulteriori informazioni sui costi di transazione, vedere i [prezzi per il trasferimento dei dati.](https://azure.microsoft.com/pricing/details/data-transfers/)

## Avvio rapido

## Come creare un processo di importazione

È possibile creare un processo di importazione per copiare dati nel proprio account di archiviazione di Azure inviando uno o più unità contenenti i dati al data center specificato. Il processo di importazione fornisce informazioni dettagliate sulle unità disco rigido, i dati da copiare, l'account di archiviazione di destinazione e le informazioni di spedizione al servizio Importazione/Esportazione di Azure. La creazione di un processo di importazione è un processo in tre fasi. In primo luogo si preparano le unità mediante lo strumento client di Importazione/Esportazione di Azure. In secondo luogo si invia un processo di importazione al portale classico. In terzo luogo si spediscono le unità all'indirizzo di spedizione fornito durante la creazione del processo e si aggiornano le informazioni di spedizione nei dettagli del processo.

> [AZURE.IMPORTANT] È possibile inviare un solo processo per ogni account di archiviazione. Ogni unità che si spedisce può essere importata in un account di archiviazione. Si immagini ad esempio di voler importare dati in due account di archiviazione. È necessario usare dischi rigidi separati per ogni account di archiviazione e creare processi separati per ogni account di archiviazione.

### Preparare le unità	

Il primo passaggio per l'importazione di dati tramite il servizio Importazione/Esportazione di Azure consiste nel preparare le unità usando lo strumento client di Importazione/Esportazione di Azure. Per preparare le unità, seguire i passaggi seguenti:

1.	Identificare i dati da importare. Potrebbe trattarsi di directory e file autonomi nel server locale o in una condivisione di rete.  

2.	Determinare il numero di unità che serviranno in base alla dimensione totale dei dati. Procurare il numero necessario di dischi rigidi SATA II/III da 3,5 pollici.

3.	Identificare l'account di archiviazione, il contenitore, le directory virtuali e i BLOB di destinazione.

4.	Determinare le directory e/o i file autonomi che verranno copiati in ciascuna unità disco rigido.

5.	Usare lo strumento [Importazione/Esportazione di Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) per copiare i dati in uno o più dischi rigidi.
	
	- Lo strumento Importazione/Esportazione di Azure consente di creare sessioni di copia per copiare i dati dall'origine alle unità disco rigido. In una sessione di copia, lo strumento può copiare una singola directory con le relative sottodirectory o un singolo file. 

	- Potrebbero essere necessarie più sessioni di copia se i dati di origine occupano molte directory.

	- Ogni unità disco rigido che si prepara richiederà almeno una sessione di copia.

6.	È possibile specificare il parametro /encrypt per abilitare la crittografia Bitlocker nell'unità disco rigido. In alternativa è possibile anche abilitare la crittografia Bitlocker manualmente nell'unità disco rigido e fornire la chiave durante l'esecuzione dello strumento.

7.	Lo strumento Importazione/Esportazione di Azure genera un file journal dell'unità per ogni unità preparata. Il file journal dell'unità viene archiviato nel computer locale, non nell'unità stessa. Il file journal verrà caricato quando si creerà il processo di importazione. Un file journal di unità include l'ID unità e la chiave BitLocker, nonché altre informazioni sull'unità. **Importante**: ogni unità disco rigido preparata darà luogo a un file journal. Quando si crea il processo di importazione nel portale classico, è necessario caricare tutti i file journal delle unità che fanno parte del processo di importazione. Le unità senza i file journal non saranno elaborate.

8.	Non modificare i dati sulle unità disco rigido o sul file journal dopo aver completato la preparazione del disco.

Di seguito sono riportati i comandi e gli esempi per preparare l'unità disco rigido usando lo strumento client Importazione/Esportazione di Azure.

Il comando PrepImport dello strumento client Importazione/Esportazione di Azure per la prima sessione di copia in un'operazione di copia di una directory:

	WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Esempio:**

Nell'esempio seguente si copiano tutti i file e sottodirectory da "H:\\Video" all'unità disco rigido montata su "x:". I dati saranno importati nell'account di archiviazione di destinazione specificato dalla chiave dell'account di archiviazione e nel contenitore di archiviazione denominato "video/". Se il contenitore di archiviazione non è presente, verrà creato. Questo comando inoltre formatterà e crittograferà l'unità disco rigido di destinazione.

	WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:storageaccountkey /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

Il comando PrepImport dello strumento client Importazione/Esportazione di Azure per le sessioni di copia successive in un'operazione di copia di una directory:

	WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

Per le sessioni di copia successive verso la stessa unità disco rigido è necessario specificare lo stesso nome di file journal, fornire un nuovo ID di sessione, non è necessario fornire la chiave dell'account di archiviazione e l'unità di destinazione o formattare o crittografare l'unità. In questo esempio stiamo copiando la cartella "H:\\Photo" e le sottodirectory nella stessa unità di destinazione, nel contenitore di archiviazione denominato "foto".

	WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

Il comando PrepImport dello strumento client Importazione/Esportazione di Azure per la prima sessione di copia in un'operazione di copia di un file:

	WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

Il comando PrepImport dello strumento client Importazione/Esportazione di Azure per le sessioni di copia successive in un'operazione di copia di un file:

	WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Da ricordare**: per impostazione predefinita, i dati verranno importati come BLOB in blocchi. È possibile usare il parametro /BlobType per importare i dati come un BLOB di pagine. Ad esempio, se si importano file di dischi rigidi virtuali che verranno montati come dischi in una VM di Azure, è necessario importarli come BLOB di pagine. Se non si è certi di quale tipo di BLOB usare, è possibile specificare /blobType:auto per ricevere aiuto nella determinazione del tipo corretto. In questo caso verranno importati tutti i file vhd e vhdx come BLOB di pagine e il resto verrà importato come BLOB in blocchi.

Vedere altri dettagli sull'uso dello strumento client Importazione/Esportazione di Azure in [Preparazione delle unità disco rigido per l'importazione](https://msdn.microsoft.com/library/dn529089.aspx).

Vedere anche [Flusso di lavoro di esempio per la preparazione dell'unità disco rigido per un processo di importazione](https://msdn.microsoft.com/library/dn529097.aspx) per le istruzioni passo passo.

### Creare il processo di importazione

1.	Dopo aver preparato l'unità, passare all'account di archiviazione nel [portale classico](https://manage.windowsazure.com) e visualizzare il dashboard. In **Quick Glance** fare clic su **Create an Import Job**. Rivedere i passaggi e selezionare la casella di controllo per indicare di aver preparato l'unità e che il file journal dell'unità è disponibile.

2.	Nel passaggio 1 fornire le informazioni di contatto per la persona responsabile di questo processo di importazione e un indirizzo di restituzione valido. Per salvare i dati del log dettagliato per il processo di importazione, selezionare l'opzione **Salva log dettagliato nel contenitore BLOB 'waimportexport'**.

3.	Nel passaggio 2 caricare i file journal dell'unità ottenuti durante il passaggio di preparazione dell'unità. Sarà necessario caricare un file per ogni unità preparata.

	![Creare il processo di importazione - Passaggio 3][import-job-03]

4.	Nel passaggio 3 immettere un nome descrittivo per il processo di importazione. Il nome immesso può contenere solo lettere minuscole, numeri, trattini e caratteri di sottolineatura, deve iniziare con una lettera e non può contenere spazi. Il nome scelto sarà usato per tenere traccia dei processi mentre sono in corso e dopo che sono stati completati.

	Selezionare quindi l'area geografica del data center dall'elenco. L'area geografica del data center indica il data center e l'indirizzo per la spedizione del pacchetto. Per altre informazioni, vedere le domande frequenti più avanti.

5. 	Nel passaggio 4 selezionare il vettore di ritorno dall'elenco, quindi immettere il numero di account del vettore. Microsoft usa questo account per inviare le unità al cliente al completamento del processo di importazione.

	Se si dispone del numero di spedizione, selezionare il vettore di consegna dall'elenco, quindi immettere il numero di spedizione.

	Se non si dispone ancora di un numero di spedizione, scegliere **I will provide my shipping information for this import job once I have shipped my package**, quindi completare il processo di importazione.

6. Per immettere il numero di spedizione dopo aver spedito il pacchetto, tornare nella pagina **Importazione/Esportazione** dell'account di archiviazione nel portale classico, selezionare il processo dall'elenco e scegliere **Informazioni sulla spedizione**. Nella procedura guidata, immettere il numero di spedizione nel passaggio 2.

	Se il numero di tracciabilità non viene aggiornato entro due settimane dalla creazione del processo, il processo scadrà.

	Se il processo si trova in stato di creazione, spedizione o trasferimento, è anche possibile aggiornare il numero di account del vettore nel passaggio 2 della procedura guidata. Quando il processo è in stato Packaging, non è possibile aggiornare il numero di account del vettore del processo.

7. È possibile monitorare l'avanzamento del processo nel dashboard del portale. Vedere il significato di ogni stato del processo nella sezione precedente [Visualizzazione dello stato dei processi](#viewing-your-job-status).

## Come creare un processo di esportazione

Creare un processo di esportazione per notificare al servizio Importazione/Esportazione che si spedirà una o più unità vuote al data center, in modo che i dati possano essere esportati dall'account di archiviazione alle unità e le unità possano quindi essere spedite all'utente.

### Preparare le unità

Per preparare le unità per un processo di esportazione si consiglia di eseguire i controlli preliminari seguenti:

1. Verificare il numero di dischi richiesti tramite il comando PreviewExport dello strumento Importazione/Esportazione di Azure. Per altre informazioni, vedere [Anteprima dell'utilizzo dell'unità per un processo di esportazione](https://msdn.microsoft.com/library/azure/dn722414.aspx). Consente di visualizzare l'anteprima dell'utilizzo di unità per il Blob è selezionato, in base alla dimensione delle unità che si desidera utilizzare. 

2. Controllare che sia possibile leggere/scrivere sul disco rigido che verrà inviato per il processo di esportazione.

### Creare il processo di esportazione

1. 	Per creare un processo di esportazione, passare all'account di archiviazione nel [portale classico](https://manage.windowsazure.com) e visualizzare il dashboard. In **Quick Glance**, fare clic su **Crea un processo di esportazione** e continuare la procedura guidata.

2. 	Nel passaggio 2 fornire le informazioni di contatto per la persona responsabile di questo processo di esportazione. Per salvare i dati del log dettagliato per il processo di esportazione, selezionare l'opzione **Salva log dettagliato nel contenitore BLOB 'waimportexport'**.

3.	Nel passaggio 3 specificare i dati BLOB da esportare dall'account di archiviazione a una o più unità vuote. È possibile scegliere di esportare tutti i dati Blob nell'account di archiviazione o specificare singoli Blob o set di Blob.

	- Per specificare un BLOB da esportare, utilizzare il selettore **Equal To** e specificare il percorso relativo del BLOB, iniziando con il nome del contenitore. Utilizzare *$root* per specificare il contenitore radice.
	- Per specificare tutti i BLOB che iniziano con un prefisso, utilizzare il selettore **Starts With** e specificare il prefisso, iniziando con una barra "/". Il prefisso può essere il prefisso del nome del contenitore, il nome del contenitore completo o il nome del contenitore completo seguito dal prefisso del nome BLOB.

	Nella tabella vengono indicati esempi di percorsi BLOB validi:

	Selettore|Percorso BLOB|Descrizione
	---|---|---
	Starts With|/|Esporta tutti i BLOB nell'account di archiviazione
	Starts With|/$root/|Esporta tutti i BLOB nel contenitore radice
	Starts With|/book|Esporta tutti i BLOB in qualsiasi contenitore che inizia con il prefisso**book**
	Starts With|/music/|Esporta tutti i BLOB nel contenitore **music**
	Starts With|/music/love|Esporta nel contenitore **music** tutti i BLOB che iniziano con il prefisso **love**
	Equal To|$root/logo.bmp|Esporta il BLOB **logo.bmp** nel contenitore radice
	Equal To|videos/story.mp4|Esporta il BLOB **story.mp4** nel contenitore **video**

	È necessario specificare i percorsi BLOB in formati validi per evitare errori durante l'elaborazione. Ecco un esempio:

	![Creare il processo di esportazione - Passaggio 3][export-job-03]

4.	Nel passaggio 4 immettere un nome descrittivo per il processo di esportazione. Il nome immesso può contenere solo lettere minuscole, numeri, trattini e caratteri di sottolineatura, deve iniziare con una lettera e non può contenere spazi.

	L'area del data center indicherà il data center a cui è necessario spedire il pacchetto. Per altre informazioni, vedere le domande frequenti più avanti.

5. 	Nel passaggio 5 selezionare il vettore di ritorno dall'elenco, quindi immettere il numero di account del vettore. Microsoft usa questo account per inviare le unità al cliente al completamento del processo di esportazione.

	Se si dispone del numero di spedizione, selezionare il vettore di consegna dall'elenco, quindi immettere il numero di spedizione.

	Se non si dispone ancora di un numero di spedizione, scegliere **Fornirò le informazioni sulla spedizione per questo processo di esportazione dopo aver spedito il pacchetto**, quindi completare il processo di esportazione.

6. Per immettere il numero di spedizione dopo aver spedito il pacchetto, tornare nella pagina **Importazione/Esportazione** dell'account di archiviazione nel portale classico, selezionare il processo dall'elenco e scegliere **Informazioni sulla spedizione**. Nella procedura guidata, immettere il numero di spedizione nel passaggio 2.

	Se il numero di tracciabilità non viene aggiornato entro due settimane dalla creazione del processo, il processo scadrà.

	Se il processo si trova in stato di creazione, spedizione o trasferimento, è anche possibile aggiornare il numero di account del vettore nel passaggio 2 della procedura guidata. Quando il processo è in stato Packaging, non è possibile aggiornare il numero di account del vettore del processo.

	> [AZURE.NOTE] Se il blob da esportare è in uso al momento della copia sul disco rigido, servizio di Importazione/Esportazione di Azure creare uno snapshot del blob e copiare lo snapshot.

7.	È possibile monitorare l'avanzamento del processo nel dashboard del portale. Vedere il significato di ogni stato del processo nella sezione precedente "Visualizzazione dello stato dei processi".

8.	Dopo aver ricevuto le unità con i dati esportati, è possibile visualizzare e copiare le chiavi BitLocker generate dal servizio per l'unità. Passare all'account di archiviazione nel portale classico e fare clic sulla scheda Importazione/Esportazione. Selezionare il processo di esportazione nell'elenco e fare clic sul pulsante Visualizza chiavi. Le chiavi BitLocker vengono visualizzate come illustrato:

	![Visualizzare le chiavi BitLocker per il processo di esportazione][export-job-bitlocker-keys]

Vedere la sezione delle domande frequenti che riporta le domande più comuni relative all'uso del servizio.

## Domande frequenti ##

**Quanto tempo sarà necessario per la copia dei dati dopo che le unità saranno giunte al data center?**

Il tempo impiegato per la copia varia in base a diversi fattori, come il tipo di processo, il tipo e la dimensione dei dati da copiare, la dimensione dei dischi forniti e il carico di lavoro esistente, e va da un paio di giorni a un paio di settimane. È difficile fornire una stima generale. Il servizio tenta di ottimizzare il processo tramite la copia di più unità in parallelo quando possibile. Per i processi di importazione/esportazione che richiedono una precisa tempistica invitiamo gli utenti a contattarci per avere una stima.

**È possibile usare il servizio Importazione/Esportazione di Azure con un account di archiviazione ARM?**

No, non è possibile copiare dati su un account di archiviazione ARM direttamente mediante il servizio Importazione/Esportazione di Azure. Il servizio supporta solo gli account di archiviazione classici. Il supporto per gli account di archiviazione ARM sarà presto disponibile. In alternativa è possibile importare i dati in un account di archiviazione classico ed eseguirne la migrazione in un account di archiviazione ARM mediante AzCopy o CopyBlob.

**È possibile copiare i file di Azure mediante il servizio Importazione/Esportazione di Azure?**

No, il servizio Importazione/Esportazione di Azure supporta solo i BLOB in blocchi e i BLOB di pagine. Tutti gli altri tipi di archiviazione non sono supportati, inclusi i file, le tabelle e le code di Azure.

**Il servizio Importazione/Esportazione di Azure è disponibile per le sottoscrizioni CSP?**

No, il servizio Importazione/Esportazione di Azure non supporta le sottoscrizioni CSP. Il supporto verrà aggiunto in futuro.

**È possibile saltare il passaggio di preparazione dell'unità per un processo di importazione o preparare un disco senza copiare?**

Qualsiasi unità che si desidera spedire per l'importazione di dati deve essere preparata mediante lo strumento client Importazione/Esportazione di Azure. È necessario usare lo strumento client per copiare i dati nell'unità.

**È necessario eseguire la preparazione del disco durante la creazione di un processo di esportazione?**

No, ma alcuni controlli preliminari sono consigliati Verificare il numero di dischi richiesti tramite il comando PreviewExport dello strumento Importazione/Esportazione di Azure. Per altre informazioni, vedere [Anteprima dell'utilizzo dell'unità per un processo di esportazione](https://msdn.microsoft.com/library/azure/dn722414.aspx). Consente di visualizzare l'anteprima dell'utilizzo di unità per il Blob è selezionato, in base alla dimensione delle unità che si desidera utilizzare. Controllare inoltre che possono leggere/ scrivere sul disco rigido che verranno inviati per il processo di esportazione.

**Cosa accade se si invia per errore un disco rigido non conforme ai requisiti supportati?**

Il data center di Azure restituirà all'utente l'unità non conforme ai requisiti supportati. Se solo alcune delle unità nel pacchetto soddisfano i requisiti per il supporto, tali unità verranno elaborate e le unità che non soddisfano i requisiti verranno restituite all'utente.

**È possibile annullare il processo?**

È possibile annullare un processo quando lo stato è Creating o Shipping.

**Per quanto tempo è possibile visualizzare lo stato dei processi completati nel portale classico?**

È possibile visualizzare lo stato per i processi completati fino a 90 giorni. Tutti i processi completi vengono eliminati dopo 90 giorni.

**Che cosa si deve fare, se si desidera importare o esportare più di 10 unità?**

Un unico processo di importazione o esportazione può fare riferimento solo a 10 unità in un singolo processo del servizio di importazione/esportazione. Se si desidera spedire più di 10 unità, è possibile creare più processi. Le unità associate con lo stesso processo devono essere spedite insieme nello stesso pacco.

**È possibile usare un adattatore USB SATA non incluso nell'elenco consigliato?**

Se si ha un convertitore non incluso nell'elenco precedente, prima di acquistare un convertitore supportato è possibile provare a eseguire lo strumento Importazione/Esportazione di Azure con il convertitore per preparare l'unità e vedere se funziona.

**Il servizio formatta le unità prima di restituirle?**

No. Tutte le unità vengono crittografati con BitLocker.

**È possibile acquistare da Microsoft unità per i processi di importazione/esportazione?**

No. Sarà necessario spedire le unità per i processi sia di importazione che di esportazione.

**Dopo il completamento del processo di importazione, come si presenteranno i dati nell'account di archiviazione? Verrà mantenuta la gerarchia delle directory?**

Quando si prepara un disco rigido per un processo di importazione, la destinazione viene specificata mediante il parametro "/dstdir:". Questo è il contenitore di destinazione nell'account di archiviazione in cui vengono copiati i dati dal disco rigido. In questo contenitore di destinazione vengono create directory "virtuali" per le cartelle dal disco rigido e "BLOB" per i file.

**Se l'unità contiene file già presenti nell'account di archiviazione, il servizio sovrascriverà i BLOB esistenti nell'account di archiviazione?**

Quando si prepara l'unità, è possibile specificare se i file di destinazione possono essere sovrascritti o ignorati mediante il parametro denominato /Disposition:<rename|no-overwrite|overwrite>. Per impostazione predefinita, il servizio non sovrascriverà alcun BLOB e rinominerà i nuovi file.

**Lo strumento client Importazione/Esportazione di Azure è compatibile con il sistema operativo a 32 bit?** No. Lo strumento client è compatibile solo con il sistema operativo Windows a 64 bit. Vedere la sezione [Prerequisiti](#pre-requisites) in Sistema operativo per un elenco completo delle versioni supportate del sistema operativo.

**È consigliabile includere qualcos'altro oltre all'unità disco rigido nel pacco?**

Spedire solo i dischi rigidi. Non inserire oggetti come cavi di alimentazione o cavi USB.

**È necessario spedire le unità tramite FedEx o DHL?**

È possibile spedire le unità al data center mediante qualsiasi vettore noto come FedEx, DHL, UPS o il servizio postale. Per la spedizione delle unità restituite dal data center, invece, è necessario fornire un numero di account FedEx negli Stati Uniti e nell'Unione Europea e un numero di account DHL nelle aree geografiche di Asia e Australia.

**Vi sono restrizioni in merito alla spedizione internazionale delle unità?**

È possibile che i supporti fisici spediti debbano passare i confini internazionali. È responsabilità del cliente assicurarsi che i supporti fisici e i dati siano importati e/o esportati conformemente alle leggi vigenti. Prima di spedire i supporti fisici, rivolgersi ai consulenti per verificare che i supporti e i dati possano essere spediti ai data center specificati in modo conforme alle normative. Ciò assicura che la spedizione raggiunga Microsoft in modo tempestivo.

**Quando si crea un processo, l'indirizzo di spedizione è una località diversa rispetto alla posizione dell'account di archiviazione. Cosa devo fare?**

Alcuni percorsi dell'account di archiviazione sono mappati a indirizzi di spedizione alternativi. Le località di spedizione disponibili in precedenza possono essere temporaneamente mappate a posizioni alternative. Controllare sempre l'indirizzo di spedizione fornito durante la creazione del processo prima di spedire le unità.

**Perché lo stato del processo nel portale classico risulta essere "Spedizione" mentre il sito Web del vettore indica che il pacco è stato consegnato?**

Lo stato indicato dal portale passa da "Spedizione" a "Trasferimento" quando inizia l'elaborazione dell'unità. Se l'unità ha raggiunto la struttura, ma l'elaborazione non è iniziata, lo stato del processo mostrato sarà "Spedizione".

**Quando si spedisce l'unità, il vettore richiede il nome e il numero di telefono di contatto del data center. Cosa devo fornire?**

Il numero di telefono viene fornito all'utente durante la creazione del processo. Se è richiesto un nome di contatto, contattare waimportexport@microsoft.com per ottenere le informazioni.

**È possibile usare il servizio Importazione/Esportazione di Azure per copiare cassette postali PST e dati di SharePoint in Office 365?**

Vedere [Importare file PST o dati di SharePoint in Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**È possibile usare il servizio Importazione/Esportazione di Azure per copiare i backup offline nel servizio Backup di Azure?**

Vedere [Flusso di lavoro di backup offline in Backup di Azure](../backup/backup-azure-backup-import-export.md).

## Vedere anche

[Configurazione dello strumento client Importazione/Esportazione di Azure](https://msdn.microsoft.com/library/dn529112.aspx)

[Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)


[import-job-03]: ./media/storage-import-export-service/import-job-03.png
[export-job-03]: ./media/storage-import-export-service/export-job-03.png
[export-job-bitlocker-keys]: ./media/storage-import-export-service/export-job-bitlocker-keys.png
[importjobflow]: ./media/storage-import-export-service/importjob.png
[exportjobflow]: ./media/storage-import-export-service/exportjob.png

<!---HONumber=AcomDC_0525_2016-->