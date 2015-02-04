<properties urlDisplayName="AzCopy" pageTitle="Come usare AzCopy con Archiviazione di Microsoft Azure" metaKeywords="Get started Azure AzCopy   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure file   Azure file storage   Azure file share   AzCopy" description="Informazioni sull'uso dell'utilità AzCopy per caricare, scaricare e copiare il contenuto di BLOB e file." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="How to use AzCopy with Microsoft Azure Storage" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/17/2014" ms.author="tamram" />

# Introduzione all'utilità della riga di comando AzCopy

AzCopy è un'utilità della riga di comando progettata per il caricamento, il download e la copia a prestazioni elevate dei dati in e da servizi di archiviazione BLOB, file e tabelle di Microsoft Azure. In questa guida viene fornita una panoramica sull'uso di AzCopy.

> [WACOM.NOTE] In questa guida si presuppone che sia stato installato AzCopy 3.0.0 o versioni successive. AzCopy 3.x è ora disponibile per tutti.<br /> 
> Questa guida descrive anche l'uso di AzCopy 4.0.0, una versione di anteprima di AzCopy. In questa guida, le funzioni fornite solo nella versione di anteprima sono designate come *anteprima*.<br />
> Tenere presente che per AzCopy 4.x, le opzioni della riga di comando e le funzionalità potrebbero cambiare nelle versioni successive.

## Sommario

- [Scaricare e installare AzCopy](#install)
- [Informazioni sulla sintassi della riga di comando di AzCopy](#syntax)
- [Limitare le scritture simultanee durante la copia dei dati](#limit-writes)
- [Copiare i BLOB di Azure con AzCopy](#copy-blobs)
- [Copiare i file in una condivisione file di Azure con AzCopy solo &#40;versione di anteprima&#41;](#copy-files)
- [Copiare le entità di tabella di Azure con AzCopy &#40;solo versione di anteprima&#41;](#copy-entities)
- [Versioni di AzCopy](#versions)
- [Passaggi successivi](#next-steps)

## <a id="install"></a> Scaricare e installare AzCopy

1. Scaricare l'[ultima versione di AzCopy](http://aka.ms/downloadazcopy) o l'[ultima versione di anteprima](http://aka.ms/downloadazcopypr).
2. Eseguire l'installazione. Per impostazione predefinita, l'installazione di AzCopy crea una cartella denominata `AzCopy` in `%ProgramFiles(x86)%\Microsoft SDKs\Azure\` (n un computer che esegue Windows a 64 bit) o `%ProgramFiles%\Microsoft SDKs\Azure\` (n un computer che esegue Windows a 32 bit). È tuttavia possibile modificare il percorso di installazione nell'installazione guidata.
3. Se si vuole, è possibile aggiungere il percorso di installazione di AzCopy al percorso di sistema.

## <a id="syntax"></a> Informazioni sulla sintassi della riga di comando di AzCopy

Aprire una finestra di comando e passare alla directory di installazione di AzCopy contenente l'eseguibile `AzCopy.exe`. La sintassi di base per i comandi di AzCopy è la seguente:

	AzCopy /Source:<source> /Dest:<destination> /Pattern:<filepattern> [Options]

> [WACOM.NOTE] A partire da AzCopy versione 3.0.0, la sintassi della riga di comando di AzCopy richiede che ogni parametro venga specificato in modo da includere il nome di parametro, *ad esempio*, `/ParameterName:ParameterValue`.

I parametri per AzCopy sono descritti nella tabella seguente. È anche possibile digitare uno dei comandi seguenti dalla riga di comando per assistenza nell'uso di AzCopy:

- Per assistenza dettagliata della riga di comando per AzCopy: `AzCopy /?`
- Per assistenza dettagliata con un parametro di AzCopy: `AzCopy /?:SourceKey`
- Per esempi della riga di comando: `AzCopy /?:Samples` 


<table>
  <tr>
    <th>Nome opzione</th>
    <th>Descrizione</th>
    <th>Applicabile ad Archiviazione BLOB (S/N)</th>
    <th>Applicabile ad Archiviazione file (S/N) (solo versione di anteprima)</th>
    <th>Applicabile ad Archiviazione tabelle (S/N) (solo versione di anteprima)</th>
  </tr>
  <tr>
    <td><b>/Source:&lt;source&gt;</b></td>
    <td>Specifica i dati di origine da cui eseguire la copia. L'origine può essere una directory di file system, un contenitore BLOB, una directory virtuale BLOB, una condivisione file di archiviazione, una directory file di archiviazione o una tabella di Azure.</td>
    <td>S</td>
    <td>S<br /> (solo anteprima)</td>
    <td>S<br /> (solo anteprima)</td>
  </tr>
  <tr>
    <td><b>/Dest:&lt;destination&gt;</b></td>
    <td>Specifica la destinazione in cui eseguire la copia. La destinazione può essere una directory di file system, un contenitore BLOB, una directory virtuale BLOB, una condivisione file di archiviazione, una directory file di archiviazione o una tabella di Azure.</td>
    <td>S</td>
    <td>S<br /> (solo anteprima)</td>
    <td>S<br /> (solo anteprima)</td>
  </tr>
  <tr>
    <td><b>/Pattern:&lt;file-pattern&gt;</b></td>
      <td>
          Specifica un criterio file indicante i file da copiare. Il comportamento del parametro /Pattern è determinato dal percorso dei dati di origine e dalla presenza dell'opzione relativa alla modalità ricorsiva. È possibile specificare la modalità ricorsiva tramite l'opzione /S.
          <br />
          Se l'origine specificata è una directory nel file system, è possibile usare i caratteri jolly standard e viene cercata una corrispondenza del criterio del file specificato con i file inclusi nella directory. Se è specificata l'opzione /S, AzCopy cerca anche la corrispondenza del criterio specificato con tutti i file inclusi nelle sottocartelle della directory.
          <br />
          Se l'origine specificata è un contenitore o una directory virtuale BLOB, non è possibile applicare i caratteri jolly. Se è specificata l'opzione&nbsp;/S&nbsp;, AzCopy interpreta il criterio del file specificato come un prefisso BLOB. Se non è specificata l'opzione&nbsp;/S&nbsp;, AzCopy cerca una corrispondenza esatta del criterio del file con i nomi dei BLOB.
          <br />
          Se l'origine specificata è una condivisione file di Azure, è necessario specificare il nome file esatto (ad esempio, &nbsp;abc.txt) per copiare un solo file oppure specificare l'opzione&nbsp;/S&nbsp;per copiare tutti i file nella condivisione in modo ricorsivo. Se si specificano sia il criterio del file che l'opzione/S&nbsp;contemporaneamente, verrà generato un errore.
          <br />
          Il criterio file predefinito usato quando non viene specificato alcun criterio è *.* per un percorso del file system o un prefisso vuoto per un percorso di archiviazione di Azure. Non è consentito specificare più criteri file.</td>
    <td>S</td>
    <td>S<br /> (solo anteprima)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/DestKey:&lt;storage-key&gt;</b></td>
    <td>Specifica la chiave dell'account di archiviazione per la risorsa di destinazione.</td>
    <td>S</td>
    <td>S<br /> (solo anteprima)</td>
    <td>S<br /> (solo anteprima)</td>
  </tr>
  <tr>
    <td class="auto-style1"><b>/DestSAS:&lt;sas-token&gt;</b></td>
    <td class="auto-style1">Specifica una firma di accesso condiviso per il contenitore di destinazione (se applicabile). Racchiudere la firma di accesso condiviso tra virgolette doppie, in quanto potrebbe contenere caratteri della riga di comando speciali.<br />
        Se la risorsa di destinazione è una tabella o un contenitore BLOB, è possibile specificare questa opzione seguita dal token di accesso condiviso o specificare la firma di accesso condiviso come parte dell'URI del BLOB di destinazione, senza questa opzione.<br />
        Se l'origine e la destinazione sono entrambi BLOB, il BLOB di destinazione deve essere nello stesso account di archiviazione del BLOB di origine.</td>
    <td class="auto-style1">S</td>
    <td class="auto-style1">N</td>
    <td class="auto-style1">S<br /> (solo anteprima</td>
  </tr>
  <tr>
    <td><b>/SourceKey:&lt;storage-key&gt;</b></td>
    <td>Specifica la chiave dell'account di archiviazione per la risorsa di origine.</td>
    <td>S</td>
    <td>S<br /> (solo anteprima)</td>
    <td>S<br /> (solo anteprima)</td>
  </tr>
  <tr>
    <td><b>/SourceSAS:&lt;sas-token&gt;</b></td>
    <td>Specifica una firma di accesso condiviso per il contenitore di origine (se applicabile). Racchiudere la firma di accesso condiviso tra virgolette doppie, in quanto potrebbe contenere caratteri della riga di comando speciali.
        <br />
        Se la risorsa di origine è un contenitore BLOB e non viene fornita né una chiave né una firma di accesso condiviso, il contenitore BLOB verrà letto tramite accesso anonimo.
        <br />
        Se l'origine è una tabella, è necessario specificare una chiave o una firma di accesso condiviso.</td>
    <td>S</td>
    <td>N</td>
    <td>S<br /> (solo anteprima)</td>
  </tr>
  <tr>
    <td><b>/S</b></td>
    <td>Specifica la modalità ricorsiva per le operazioni di copia. Nella modalità ricorsiva, AzCopy copierà tutti i BLOB o file corrispondenti al criterio di file specificato, inclusi quelli nelle sottocartelle.</td>
    <td>S</td>
    <td>S<br /> (solo anteprima)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/BlobType:&lt;block | page&gt;</b></td>
    <td>Specifica se il BLOB di destinazione è un BLOB in blocchi o un BLOB di pagine. Questa opzione si applica solo quando si carica un BLOB; in caso contrario, viene generato un errore. Se la destinazione è un BLOB e questa opzione non è specificata, per impostazione predefinita, AzCopy creerà un BLOB in blocchi.</td>
    <td>S</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/CheckMD5</b></td>
    <td>Calcola un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato nella proprietà Content-MD5 del BLOB o del file corrisponda all'hash calcolato. Il controllo MD5 è disattivato per impostazione predefinita, quindi è necessario specificare questa opzione per eseguire il controllo MD5 al download dei dati.
	<br />
    Si noti che Archiviazione di Azure non garantisce che l'hash MD5 archiviato per il BLOB o il file sia aggiornato. È responsabilità del client aggiornare l'hash MD5 ogni volta che il BLOB o il file viene modificato.
	<br />
    AzCopy imposta sempre la proprietà Content-MD5 per un BLOB o un file di Azure dopo averlo caricato nel servizio.</td>
    <td>S</td>
    <td>S<br /> (solo anteprima)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/Snapshot</b></td>
    <td>Indica se trasferire gli snapshot o meno. Questa opzione è valida solo quando l'origine è un BLOB. 
        <br />
        Gli snapshot di BLOB trasferiti vengono rinominati nel formato seguente: [blob-name] (snapshot-time)[extension]. 
        <br />
        Per impostazione predefinita, gli snapshot non vengono copiati.</td>
    <td>S</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/V:[verbose log-file]</b></td>
    <td>Esegue l'output dei messaggi di stato dettagliati in un file di log. Per impostazione predefinita, al file di log dettagliato viene assegnato il nome <code>AzCopyVerbose.log</code> in <code>%LocalAppData%\Microsoft\Azure\AzCopy</code>. Se si specifica un percorso di file esistente per questa opzione, il log dettagliato verrà aggiunto al file indicato.</td>
    <td>S</td>
    <td>S<br /> (solo anteprima)</td>
    <td>S<br /> (solo anteprima)</td>
  </tr>
  <tr>
    <td><b>/Z:[journal-file-folder]</b></td>
    <td>Specifica la cartella del file journal per la ripresa di un'operazione.<br />
        AzCopy supporta sempre la ripresa di un'operazione interrotta.<br />
        Se questa opzione non è specificata o se è specificata senza un percorso di cartella, AzCopy creerà il file journal nel percorso predefinito, ovvero <code>%LocalAppData%\Microsoft\Azure\AzCopy</code>.<br />
        Ogni volta che si invia un comando ad AzCopy, l'utilità verifica se il file journal è presente nella cartella predefinita o in una cartella specificata dall'utente tramite questa opzione. Se il file journal non viene trovato in tali percorsi, AzCopy considera l'operazione come nuova e quindi crea un nuovo file journal.
        <br />
		Se il file journal esiste, AzCopy verifica se la riga di comando di input corrisponde alla riga di comando nel file journal. Se le righe di comando corrispondono, AzCopy avvia la ripresa dell'operazione incompleta. Se non corrispondono, verrà richiesto se si vuole sovrascrivere il file journal per avviare una nuova operazione o annullare l'operazione corrente. 
        <br />
        Se l'operazione viene completata senza errori, il file journal viene eliminato.
		<br />
		Si noti che la ripresa di un'operazione da un file journal creato da una versione precedente di AzCopy non è supportata.</td>
    <td>S</td>
    <td>S<br /> (solo anteprima)</td>
    <td>S<br /> (solo anteprima)</td>
  </tr>
  <tr>
    <td><b>/@:parameter-file</b></td>
    <td>Specifica un file contenente parametri. AzCopy elabora i parametri nel file come se fossero stati specificati nella riga di comando.<br /> 
		In un file di risposta è possibile specificare più parametri su una sola riga o specificare un parametro su ogni riga. Si noti che un singolo parametro non può estendersi su più righe. 
        <br />
		I file di risposta possono includere righe di commento che iniziano con il simbolo <code>#</code>. 
        <br />
        È possibile specificare più file di risposta. Tuttavia, tenere presente che AzCopy non supporta file di risposta annidati.</td>
    <td>S</td>
    <td>S<br /> (solo anteprima)</td>
    <td>S<br /> (solo anteprima)</td>
  </tr>
  <tr>
    <td><b>/Y</b></td>
    <td>Elimina tutte le richieste di conferma di AzCopy.</td>
    <td>S</td>
    <td>S<br /> (solo anteprima)</td>
    <td>S<br /> (solo anteprima)</td>
  </tr>
  <tr>
    <td><b>/L</b></td>
    <td>Specifica solo un'operazione di elenco, non viene copiato alcun dato.</td>
    <td>S</td>
    <td>S<br /> (solo anteprima)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/MT</b></td>
    <td>Imposta l'ora dell'ultima modifica di un file scaricato sulla stessa ora del file o del BLOB di origine.</td>
    <td>S</td>
    <td>S<br /> (solo anteprima)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XN</b></td>
    <td>Esclude una risorsa di origine più recente. La risorsa non verrà copiata se l'origine è più recente della destinazione.</td>
    <td>S</td>
    <td>S<br /> (solo anteprima)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XO</b></td>
    <td>Esclude una risorsa di origine meno recente. La risorsa non verrà copiata se la risorsa di origine è meno recente di quella di destinazione.</td>
    <td>S</td>
    <td>S<br /> (solo anteprima)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/A</b></td>
    <td>Carica solo i file per i quali è impostato l'attributo Archive.</td>
    <td>S</td>
    <td>S<br /> (solo anteprima)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/IA:[RASHCNETOI]</b></td>
    <td>Carica solo i file per i quali sono impostati gli attributi specificati.<br />
        Gli attributi disponibili sono:  
        <br />
        R&nbsp;&nbsp;&nbsp;File di sola lettura
        <br />
        A&nbsp;&nbsp;&nbsp;File pronti per l'archiviazione
        <br />
        S&nbsp;&nbsp;&nbsp;File di sistema
        <br />
        H&nbsp;&nbsp;&nbsp;File nascosti
        <br />
        C&nbsp;&nbsp;&nbsp;File compresso
        <br />
        N&nbsp;&nbsp;&nbsp;File normali
        <br />
        E&nbsp;&nbsp;&nbsp;File crittografati
        <br />
        T&nbsp;&nbsp;&nbsp;File temporanei
        <br />
        O&nbsp;&nbsp;&nbsp;File non in linea
        <br />
        I&nbsp;&nbsp;&nbsp;File non indicizzati</td>
    <td>S</td>
    <td>S<br /> (solo anteprima)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XA:[RASHCNETOI]</b></td>
    <td>Esclude i file per i quali sono impostati gli attributi specificati.<br />
        Gli attributi disponibili sono:  
        <br />
        R&nbsp;&nbsp;&nbsp;File di sola lettura  
        <br />
        A&nbsp;&nbsp;&nbsp;File pronti per l'archiviazione  
        <br />
        S&nbsp;&nbsp;&nbsp;File di sistema  
        <br />
        H&nbsp;&nbsp;&nbsp;File nascosti  
        <br />
        C&nbsp;&nbsp;&nbsp;File compresso  
        <br />
        N&nbsp;&nbsp;&nbsp;File normali  
        <br />
        E&nbsp;&nbsp;&nbsp;File crittografati  
        <br />
        T&nbsp;&nbsp;&nbsp;File temporanei  
        <br />
        O&nbsp;&nbsp;&nbsp;File non in linea  
        <br />
        I&nbsp;&nbsp;&nbsp;File non indicizzati</td>
    <td>S</td>
    <td>S<br /> (solo anteprima)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/Delimiter:&lt;delimiter&gt;</b></td>
    <td>Indica il delimitatore usato per delimitare le directory virtuali in un nome BLOB.<br />
        Per impostazione predefinita, AzCopy usa il carattere / come delimitatore. Tuttavia, a questo scopo supporta anche l'uso di caratteri comuni, ad esempio @, # o %. Se è necessario includere uno di questi caratteri speciali nella riga di comando, racchiudere il nome del file tra virgolette doppie. 
        <br />
        Questa opzione si applica solo per il download di BLOB.</td>
    <td>S</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/NC:&lt;number-of-concurrents&gt;</b></td>
    <td>Specifica il numero di operazioni simultanee.
        <br />
        Per impostazione predefinita, AzCopy avvia un determinato numero di operazioni simultanee per aumentare la velocità effettiva di trasferimento dei dati. Si noti che un numero elevato di operazioni simultanee in un ambiente con una larghezza di banda ridotta potrebbe sovraccaricare la connessione di rete e impedire il corretto completamento delle operazioni. Limitare le operazioni simultanee in base alla larghezza di banda di rete effettivamente disponibile.
        <br />
		Il limite massimo per le operazioni simultanee è 512.</td>
    <td>S</td>
    <td>S<br /> (solo anteprima)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/SourceType:Blob|Table</b></td>
    <td>Indica che la risorsa <code>source</code> è un BLOB disponibile nell'ambiente di sviluppo locale, in esecuzione nell'emulatore di archiviazione.</td>
    <td>S</td>
    <td>N</td>
    <td>S<br /> (solo anteprima)</td>
  </tr>
  <tr>
    <td><b>/DestType:Blob|Table</b></td>
    <td>Indica che la risorsa <code>destination</code> è un BLOB disponibile nell'ambiente di sviluppo locale, in esecuzione nell'emulatore di archiviazione.</td>
    <td>S</td>
    <td>N</td>
    <td>S<br /> (solo anteprima)</td>
  </tr>
  <tr>
    <td><strong>/PKRS:&lt;&quot;key1#key2#key3#...&quot;&gt;</strong></td>
    <td>Divide l'intervallo di chiavi di partizione per consentire l'esportazione dei dati della tabella in parallelo, aumentando la velocità dell'operazione di esportazione.
        <br />
        Se questa opzione non è specificata, AzCopy usa un solo thread per esportare le entità della tabella. Ad esempio, se l'utente specifica /PKRS:&quot;aa#bb&quot;, AzCopy avvia tre operazioni simultanee.
        <br />
        Ogni operazione esporta uno dei tre intervalli di chiavi di partizione, nel modo seguente: 
        <br />
        &nbsp;&nbsp;&nbsp;&#91;&lt;prima chiave partizione&gt;, aa&#41; 
        <br />
        &nbsp;&nbsp;&nbsp;&#91;aa, bb&#41;
        <br />
        &nbsp;&nbsp;&nbsp;&#91;bb, &lt;ultima chiave partizione&gt;&#93; </td>
    <td>N</td>
    <td>N</td>
    <td>S<br /> (solo anteprima)</td>
  </tr>
  <tr>
    <td><strong>/SplitSize:</strong><file-size><strong>&lt;file-size&gt;</strong></td>
    <td>Specifica le dimensioni di suddivisione del file esportato in MB.
        <br />
        Se questa opzione non è specificata, AzCopy esporterà i dati della tabella in un solo file.
        <br />
        Se i dati della tabella vengono esportati in un BLOB e le dimensioni del file esportato raggiungono il limite di 200 GB per le dimensioni del BLOB, AzCopy dividerà il file esportato, anche se questa opzione non viene specificata. </td>
    <td>N</td>
    <td>N</td>
    <td>S<br /> (solo anteprima)</td>
  </tr>
  <tr>
    <td><b>/EntityOperation:&lt;InsertOrSkip | InsertOrMerge | InsertOrReplace&gt;
</b>
</td>
    <td>Specifica il comportamento di importazione dei dati della tabella.
        <br />
        InsertOrSkip - Ignora un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.
        <br />
        InsertOrMerge - Unisce un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.
        <br />
        InsertOrReplace - Sostituisce un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella. </td>
    <td>N</td>
    <td>N</td>
    <td>S<br /> (solo anteprima)</td>
  </tr>
  <tr>
    <td><b>/Manifest:&lt;manifest-file&gt;</b></td>
    <td>Specifica il file manifesto per l'operazione di importazione. <br />
        Il file manifesto viene generato durante l'operazione di esportazione.</td>
    <td>N</td>
    <td>N</td>
    <td>S<br /> (solo anteprima)</td>
  </tr>
</table>

<br/>

## <a id="limit-writes"></a> Limitare le scritture simultanee durante la copia dei dati

Durante la copia di BLOB o file con AzCopy, tenere presente che altre applicazioni potrebbero modificare i dati mentre è in corso la copia. Se possibile, assicurarsi che i dati copiati non vengano modificati durante l'operazione di copia. Ad esempio, se si copia un file VHD associato a una macchina virtuale di Azure, verificare che altre applicazioni non stiano scrivendo nel file VHD durante la copia. In alternativa, è prima possibile creare uno snapshot del file VHD e quindi copiare lo snapshot.

Se non è possibile evitare che altre applicazioni scrivano nei BLOB o nei file durante l'operazione di copia, tenere presente che al termine del processo la parità delle risorse copiate con le risorse di origine potrebbe non essere completa.

## <a id="copy-blobs"></a> Copiare i BLOB di Azure con AzCopy

Gli esempi seguenti mostrano vari scenari per la copia di BLOB con AzCopy.

### Copiare un BLOB singolo

**Caricare un file dal file system all'archiviazione BLOB:**
	
	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt

**Scaricare un BLOB dall'archiviazione BLOB al file system:**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

### Copiare un BLOB tramite copia sul lato server

Quando si copia di un BLOB all'interno di un account di archiviazione o tra account di archiviazione diversi, viene eseguita un'operazione di copia sul lato server. Per altre informazioni sulle operazioni di copia sul lato server, vedere la pagina relativa all'[introduzione alla copia asincrona di BLOB tra account](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

**Copiare un BLOB all'interno di un account di archiviazione:**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt 

**Copiare un BLOB tra account di archiviazione diversi:**

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
 
### Copiare un BLOB dall'area secondaria 

Se per l'account di archiviazione è abilitata l'archiviazione con ridondanza geografica e accesso in lettura, è possibile copiare i dati dall'area secondaria. 

**Copiare un BLOB dall'area secondaria all'account primario:**

	AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

**Scaricare un BLOB dall'area secondaria a un file nel file system:**

	AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

### Caricare un file in un nuovo contenitore o in una nuova directory virtuale BLOB

**Caricare un file in un nuovo contenitore BLOB**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mynewcontainer /DestKey:key /Pattern:abc.txt

Si noti che se il contenitore di destinazione specificato non esiste, AzCopy ne creerà uno e vi caricherà i file.

**Caricare un file in una nuova directory virtuale BLOB**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

Si noti che se la directory virtuale specificata non esiste, AzCopy caricherà il file in modo da includere la directory virtuale nel relativo nome (in base all'esempio precedente: `vd/abc.txt`).

### Scaricare un BLOB in una nuova cartella

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Se la cartella `C:\myfolder` non esiste ancora, AzCopy la creerà nel file system e scaricherà `abc.txt ` nella nuova cartella.

### Caricare file e sottocartelle da una directory a un contenitore, in modo ricorsivo

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

Se si specifica l'opzione `/S`, il contenuto della directory specificata viene copiato nell'archiviazione BLOB in modo ricorsivo, ovvero includendo nella copia anche tutte le sottocartelle e i relativi file. Si supponga, ad esempio, che i file seguenti risiedano nella cartella `C:\myfolder`:

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

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key

Se non si specifica l'opzione `/S` nella riga di comando, AzCopy eseguirà la copia in modo non ricorsivo. Verranno copiati solo i file nella directory specificata, ma le sottocartelle e i relativi file NON verranno copiati. Si supponga, ad esempio, che i file seguenti risiedano nella cartella `C:\myfolder`:

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

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

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

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer/vd1/ /Dest:C:\myfolder /SourceKey:key /S

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

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

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

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

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

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

Dopo l'operazione di copia, il contenitore di destinazione includerà il BLOB e i relativi snapshot. Supponendo che il BLOB dell'esempio precedente contenga due snapshot, nel contenitore saranno presenti il BLOB e gli snapshot seguenti:

	abc.txt
	abc (2013-02-25 080757).txt
	abc (2014-02-21 150331).txt


### Usare un file di risposta per specificare i parametri della riga di comando

	AzCopy /@:"C:\myfolder\abc.txt"

È possibile includere i parametri della riga di comando di AzCopy in un file di risposta. AzCopy elabora i parametri nel file come se fossero stati specificati nella riga di comando, eseguendo una sostituzione diretta con i contenuti del file.

**Specificare uno o più file di risposta a riga singola**

Si supponga che siano presenti un file di risposta denominato `source.txt`, che specifica un contenitore di origine:

	/Source:http://myaccount.blob.core.windows.net/mycontainer

Un file di risposta denominato `dest.txt`, che specifica una cartella di destinazione nel file system:

	/Dest:C:\myfolder

E un file di risposta denominato options.txt`, che specifica le opzioni per AzCopy:

	/S /Y

Per chiamare AzCopy con questi file di risposta, che risiedono in una directory `C:\responsefiles`, usare il comando seguente:

	AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

AzCopy elabora il comando come se fossero stati inclusi tutti i singoli parametri nella riga di comando:

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

**Specificare un file di risposta a più righe**

Si supponga che sia presente un file di risposta denominato `copyoperation.txt`, che contiene le righe seguenti: Ogni parametro di AzCopy è specificato su una riga:

	/Source:http://myaccount.blob.core.windows.net/mycontainer
	/Dest:C:\myfolder
	/SourceKey:<sourcekey>
	/S 
	/Y

Per chiamare AzCopy con questo file di risposta, usare il comando seguente:

	AzCopy /@:"C:\responsefiles\copyoperation.txt"

AzCopy elabora il comando come se fossero stati inclusi tutti i singoli parametri nella riga di comando:	

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

Si noti che è necessario specificare ogni parametro di AzCopy su una sola riga. Se il parametro viene suddiviso su due righe, l'operazione di AzCopy avrà esito negativo, come mostrato di seguito per il parametro `/sourcekey`:

	http://myaccount.blob.core.windows.net/mycontainer
 	C:\myfolder
	/sourcekey:
	[sourcekey]
	/S 
	/Y

### Specificare una firma di accesso condiviso
	
**Specificare una firma di accesso condiviso per il contenitore di origine tramite l'opzione /sourceSAS**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /DestC:\myfolder /SourceSAS:SAS /S

**Specificare una firma di accesso condiviso per il contenitore di origine nell'URI del contenitore di origine**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

**Specificare una firma di accesso condiviso per il contenitore di destinazione tramite l'opzione /destSAS**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer1 /DestSAS:SAS /Pattern:abc.txt

**Specificare una firma di accesso condiviso per i contenitori di origine e di destinazione**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

### Specificare una cartella per il file journal

Ogni volta che si invia un comando ad AzCopy, l'utilità verifica se il file journal è presente nella cartella predefinita o in una cartella specificata dall'utente tramite questa opzione. Se il file journal non viene trovato in tali percorsi, AzCopy considera l'operazione come nuova e quindi crea un nuovo file journal.

Se il file journal esiste, AzCopy verifica se la riga di comando di input corrisponde alla riga di comando nel file journal. Se le righe di comando corrispondono, AzCopy avvia la ripresa dell'operazione incompleta. Se non corrispondono, verrà richiesto se si vuole sovrascrivere il file journal per avviare una nuova operazione o annullare l'operazione corrente. 

**Usare il percorso predefinito per il file journal**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

Se si omette l'opzione `/Z` o si specifica l'opzione senza il percorso `/Z` della cartella, come illustrato sopra, AzCopy creerà il file journal nel percorso predefinito, ovvero `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Se il file journal esiste già, AzCopy riprenderà l'operazione in base al file journal. 

**Specificare un percorso personalizzato per il file journal**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

In questo esempio viene creato il file journal, nel caso in cui non esista già: Se esiste, AzCopy riprenderà l'operazione in base al file journal.

**Riprendere un'operazione di AzCopy**

	AzCopy /Z:C:\journalfolder\

In questo esempio viene ripresa l'ultima operazione che potrebbe non essere stata completata a causa di errori.


### Generare un file di log

**Scrivere il file di log dettagliato nel percorso predefinito**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

Se si omette l'opzione `/V` senza specificare un percorso log dettagliato, quindi AzCopy crea il file di log nel percorso predefinito, ovvero `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

**Scrivere il file di log dettagliato in un percorso dettagliato**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

Si noti che se si specifica un percorso relativo per l'opzione `/V`, ad esempio `/V:test/azcopy1.log`, il log dettagliato verrà creato nella directory di lavoro corrente all'interno di una sottocartella denominata `test`.


### Impostare l'ora dell'ultima modifica dei file scaricati sulla stessa ora dei BLOB di origine

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

### Escludere BLOB dall'operazione di copia in base all'ora dell'ultima modifica

Specificare l'opzione `/MT` per confrontare l'ora dell'ultima modifica del BLOB di origine e del file di destinazione.

**Escludere i BLOB più recenti rispetto al file di destinazione**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

**Escludere i BLOB meno recenti rispetto al file di destinazione**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

### Specificare il numero di operazioni simultanee da avviare

L'opzione `/NC` specifica il numero di operazioni di copia simultanee. Per impostazione predefinita, AzCopy avvierà un numero di operazioni simultanee pari a otto volte il numero di processori core presenti. Se AzCopy è in esecuzione in una rete con larghezza di banda ridotta, è possibile specificare un numero inferiore per l'opzione in modo da evitare errori generati dalla competizione tra le risorse.


###	Eseguire AzCopy sulle risorse del BLOB nell'emulatore di archiviazione

	AzCopy /Source:https://127.0.0.1:10004/myaccount/myfileshare/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S


## <a id="copy-files"></a> Copiare i file in Archiviazione file di Azure con AzCopy (solo versione di anteprima)

Gli esempi seguenti mostrano vari scenari per la copia di file di Azure con AzCopy.

### Scaricare un file nel file system da una condivisione file di Azure

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Si noti che se l'origine specificata è una condivisione file di Azure, è necessario specificare il nome file esatto, (*ad esempio* `abc.txt`) per copiare un singolo file oppure specificare l'opzione `/S` per copiare tutti i file nella condivisione in modo ricorsivo. Se si specificano sia il criterio del file che l'opzione `/S` contemporaneamente, verrà generato un errore.

### Scaricare file e cartelle nel file system da una condivisione file di Azure, in modo ricorsivo

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

Le cartelle vuote non verranno copiate.


### Caricare file e cartelle dal file system a una condivisione file di Azure, in modo ricorsivo

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Le cartelle vuote non verranno copiate.


### Caricare i file corrispondenti al criterio di file specificato in una condivisione di Azure, in modo ricorsivo

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

## <a id="copy-entities"></a> Copiare le entità in una tabella di Azure con AzCopy (solo versione di anteprima)

Gli esempi seguenti mostrano vari scenari per la copia di file di entità di una tabella di Azure con AzCopy.

### Esportare le entità nel file system locale

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:D:\test\ /SourceKey:key

### Esportare le entità in un BLOB di Azure

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy genererà un file di dati JSON nella cartella locale o nel contenitore BLOB con la convenzione di denominazione seguente:

	<account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

Il file di dati JSON generato segue il formato di payload per i metadati minimi. Per i dettagli su questo formato di payload, vedere [Formato di payload per le operazioni del servizio tabelle](http://msdn.microsoft.com/library/azure/dn535600.aspx).

### Dividere i file di esportazione

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy usa un *indice di volumi* nei nomi file di dati divisi per distinguere più file. L'indice di volumi è costituito da due parti, un *indice di intervalli di chiavi di partizione* e un *indice di file divisi*. Entrambi gli indici sono in base zero.

L'indice di intervalli di chiavi di partizione sarà 0 se l'utente non specifica l'opzione `/PKRS` (descritta nella sezione successiva).

Si supponga, ad esempio, che AzCopy generi due file di dati dopo che l'utente ha specificato l'opzione `/SplitSize`. I nomi file di dati risultanti potrebbero essere:

	myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
	myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Tenere presente che il valore minimo possibile per l'opzione `/SplitSize` è di 32 MB. Se la destinazione specificata è l'archiviazione BLOB, AzCopy dividerà il file di dati quando le dimensioni raggiungeranno il limite delle dimensioni BLOB (200 GB), indipendentemente dal fatto che l'opzione `/SplitSize` sia stata specificata o meno dall'utente.

### Esportare le entità simultaneamente

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:D:\test\ /SourceKey:key /PKRS:"aa#bb"

AzCopy avvierà operazioni simultanee per esportare le entità quando l'utente specifica l'opzione `/PKRS`. Ogni operazione esporta un intervallo di chiavi di partizione.

Si noti che il numero di operazioni simultanee viene controllato anche dall'opzione `/NC`. AzCopy usa il numero di processori core come valore predefinito di `/NC` durante la copia di entità della tabella, anche se /NC` non è stato specificato. Quando l'utente specifica l'opzione `/PKRS`, AzCopy usa il valore più basso dei due (intervalli di chiavi di partizione e operazioni simultanee specificate in modo implicito o esplicito) per determinare il numero di operazioni simultanee da avviare. Per altri dettagli, digitare `AzCopy /?:NC` nella riga di comando.

### Importare le entità simultaneamente

	AzCopy /Source:D:\test\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace 

Quando si esportano le entità della tabella, AzCopy scrive un file manifesto nella cartella di destinazione o nel contenitore BLOB specificato. Il file manifesto viene usato dal processo di importazione per trovare i file di dati necessari ed eseguire la convalida di dati durante il processo di importazione. Il file manifesto usa la convenzione di denominazione seguente:

	<account name>_<table name>_<timestamp>.manifest

L'opzione `/EntityOperation` indica come inserire le entità nella tabella. I valori possibili sono:

- `InsertOrSkip`: ignora un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.
- `InsertOrMerge`: unisce un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.
- `InsertOrReplace`: sostituisce un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.

Si noti che non è possibile specificare l'opzione `/PKRS` nello scenario di importazione. Diversamente dallo scenario di esportazione, in cui è necessario specificare l'opzione `/PKRS` per avviare operazioni simultanee, per impostazione predefinita AzCopy avvierà le operazioni simultanee quando si importeranno le entità. Il numero predefinito di operazioni simultanee avviate è uguale al numero di processori core. Tuttavia, è possibile specificare un numero diverso di operazioni simultanee con l'opzione `/NC`. Per altri dettagli, digitare `AzCopy /?:NC` nella riga di comando.


## <a id="versions"></a> Versioni di AzCopy

| Versione | Novità                                                                                      				|
|---------|-----------------------------------------------------------------------------------------------------------------|
| **V4.0.0**  | **Versione di anteprima corrente. Include tutte le funzionalità da V3.0.0. Supporta anche la copia di file verso o dall'archiviazione file di Azure e la copia di entità verso o dall'archiviazione tabelle di Azure.**	
| **V3.0.0**  | **Versione di anteprima corrente. Modifica la sintassi della riga di comando di AzCopy per poter richiedere i nomi di parametro e riprogetta la Guida della riga di comando. Questa versione supporta solo la copia verso e dall'archiviazione BLOB di Azure.**	
| V2.5.1  | Ottimizza le prestazioni quando si usano le opzioni /xo e /xn. Corregge i bug relativi ai caratteri speciali nei nomi file di origine e al danneggiamento dei file journal dopo che l'utente ha inserito la sintassi della riga di comando errata.	
| V2.5.0  | Ottimizza le prestazioni per gli scenari di copia su larga scala e introduce numerosi miglioramenti importanti in termini di usabilità.	
| V2.4.1  | Consente di specificare la cartella di destinazione nell'installazione guidata.                     			
| V2.4.0  | Consente di caricare e scaricare file per l'archiviazione file di Azure.                       				                              
| V2.3.0  | Supporta gli account di archiviazione con ridondanza geografica e accesso in lettura.                                                  			|
| V2.2.2  | Versione aggiornata per l'uso della libreria client di Archiviazione di Azure versione 3.0.3.                                            				                    
| V2.2.1  | Include la correzione di un problema di prestazioni durante la copia di grandi quantità di file all'interno dello stesso account di archiviazione.            				                                                
| V2.2    | Supporta l'impostazione del delimitatore della directory virtuale per i nomi di BLOB. Consente di specificare il percorso del file journal.		|
| V2.1    | Offre più di 20 opzioni per supportare operazioni di caricamento, scaricamento e copia di BLOB in un modo efficiente.		|


## <a id="next-steps"></a> Passaggi successivi

Per altre informazioni su Archiviazione di Azure e AzCopy, vedere le risorse seguenti:

### Documentazione di Archiviazione di Azure

- [Introduzione ad Archiviazione di Azure](http://azure.microsoft.com/it-it/documentation/articles/storage-introduction/)
- [Archiviare i file nell'archiviazione BLOB](http://azure.microsoft.com/it-it/documentation/articles/storage-dotnet-how-to-use-blobs/)
- [Creare una condivisione file SMB in Azure con l'archiviazione file](http://azure.microsoft.com/it-it/documentation/articles/storage-dotnet-how-to-use-files/)

### Post del blog di Archiviazione di Azure:

- [AzCopy 3.0: annuncio della disponibilità per tutti di AzCopy 3.0 oltre alla versione di anteprima di AzCopy 4.0 con il supporto di tabelle e file](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy 2.5: ottimizzazione per gli scenari di copia su larga scala](http://go.microsoft.com/fwlink/?LinkId=507682)
- [Introduzione al servizio file di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [AzCopy: supporto per l'archiviazione con ridondanza geografica e accesso in lettura](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy: trasferimento di dati con modalità riavviabile e token di firma di accesso condiviso](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy: Uso del comando di copia dei BLOB tra account](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy: Caricamento e download di file per BLOB di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)


<!--HONumber=35.1-->
