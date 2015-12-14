<properties 
	pageTitle="Come usare AzCopy con Archiviazione di Microsoft Azure" 
	description="Informazioni sull'uso dell'utilità AzCopy per caricare, scaricare e copiare il contenuto di BLOB e file." 
	services="storage" 
	documentationCenter="" 
	authors="micurd" 
	manager="jahogg" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="micurd"/>

# Introduzione all'utilità della riga di comando AzCopy

## Panoramica

AzCopy è un'utilità della riga di comando progettata per il caricamento, il download e la copia a prestazioni elevate dei dati in e da servizi di archiviazione BLOB, file e tabelle di Microsoft Azure. In questa guida viene fornita una panoramica sull'uso di AzCopy.

> [AZURE.NOTE]In questa guida si presuppone che sia stato installato AzCopy 5.0 o versioni successive.

È inoltre stata rilasciata una raccolta open source basata sul framework di spostamento di Core Data alla base di AzCopy. Altre informazioni sono disponibili in [Introduzione alla versione di anteprima della libreria per lo spostamento dei dati di Archiviazione di Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)

## Scaricare e installare AzCopy

1. Scaricare la [versione più recente di AzCopy](http://aka.ms/downloadazcopy)
2. Eseguire l'installazione. Per impostazione predefinita, AzCopy viene installato in `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy\AzCopy.exe` (in un computer che esegue Windows a 64 bit) o in `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy\AzCopy.exe` (in un computer che esegue Windows a 32 bit). È tuttavia possibile modificare il percorso di installazione nell'installazione guidata.
3. Se si vuole, è possibile aggiungere il percorso di installazione di AzCopy al percorso di sistema.

## Informazioni sulla sintassi della riga di comando di AzCopy

Aprire una finestra di comando e passare alla directory di installazione di AzCopy `AzCopy.exe` contenente l'eseguibile. La sintassi di base per i comandi di AzCopy è la seguente:

	AzCopy /Source:<source> /Dest:<destination> /Pattern:<filepattern> [Options]

> [AZURE.NOTE]A partire da AzCopy versione 3.0.0, la sintassi della riga di comando di AzCopy richiede che ogni parametro venga specificato in modo da includere il nome di parametro,*ad esempio *, `/ParameterName:ParameterValue`.

## Scrivere il primo comando AzCopy

**Caricare un file dal file system all'archiviazione BLOB:**
	
	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt

Si noti che quando si copiano file singoli, specificare l'opzione /Pattern con il nome del file. È possibile trovare ulteriori esempi nella sezione successiva di questo articolo.

## Introduzione ai parametri

I parametri per AzCopy sono descritti nella tabella seguente. È anche possibile digitare uno dei comandi seguenti dalla riga di comando per assistenza nell'uso di AzCopy:

- Per assistenza dettagliata della riga di comando per AzCopy:`AzCopy /?`
- Per assistenza dettagliata con un parametro di AzCopy:`AzCopy /?:SourceKey`
- Per esempi della riga di comando:`AzCopy /?:Samples` 

<table>
  <tr>
    <th>Nome opzione</th>
    <th>Descrizione</th>
    <th>Applicabile ad Archiviazione BLOB (S/N)</th>
    <th>Applicabile ad Archiviazione file (S/N)</th>
    <th>Applicabile ad archiviazione tabelle (S/N)</th>
  </tr>
  <tr>
    <td><b>/Source:&lt;source></b></td>
    <td>Specifica i dati di origine da cui eseguire la copia. L'origine può essere una directory di file system, un contenitore BLOB, una directory virtuale BLOB, una condivisione file di archiviazione, una directory file di archiviazione o una tabella di Azure.</td>
    <td>S</td>
    <td>S<br /></td>
    <td>S<br /></td>
  </tr>
  <tr>
    <td><b>/Dest:&lt;destination></b></td>
    <td>Specifica la destinazione in cui eseguire la copia. La destinazione può essere una directory di file system, un contenitore BLOB, una directory virtuale BLOB, una condivisione file di archiviazione, una directory file di archiviazione o una tabella di Azure.</td>
    <td>S</td>
    <td>S<br /></td>
    <td>S<br /></td>
  </tr>
  <tr>
    <td><b>/Pattern:&lt;file-pattern></b></td>
      <td>
          Specifica un criterio file indicante i file da copiare. Il comportamento del parametro /Pattern è determinato dal percorso dei dati di origine e dalla presenza dell'opzione relativa alla modalità ricorsiva. È possibile specificare la modalità ricorsiva tramite l'opzione /S.
          <br />
          Se l'origine specificata è una directory nel file system, è possibile usare i caratteri jolly standard e viene cercata una corrispondenza del criterio del file specificato con i file inclusi nella directory. Se è specificata l'opzione /S, AzCopy cerca anche la corrispondenza del criterio specificato con tutti i file inclusi nelle sottocartelle della directory.
          <br />
          Se l'origine specificata è un contenitore o una directory virtuale BLOB, non è possibile applicare i caratteri jolly. Se è specificata l'opzione /S, AzCopy interpreta il criterio del file come un prefisso BLOB. Se non è specificata l'opzione /S, AzCopy cerca una corrispondenza esatta del criterio con i nomi dei BLOB.
          <br />
          Se l'origine specificata è una condivisione file di Azure, è necessario specificare il nome file esatto (ad esempio, abc.txt) per copiare un solo file oppure specificare l'opzione&#160;/S&#160;per copiare tutti i file nella condivisione in modo ricorsivo. Se si specificano sia il criterio del file che l'opzione /S contemporaneamente, verrà generato un errore.
          <br />
          AzCopy utilizza la corrispondenza tra maiuscole e minuscole quando l’opzione /Source è un contenitore BLOB o una directory virtuale di BLOB e non utilizza la corrispondenza tra maiuscole e minuscole in tutti gli altri casi.
          <br/>
          Il criterio file predefinito usato quando non viene specificato alcun criterio è *.* per un percorso del file system o un prefisso vuoto per un percorso di archiviazione di Azure. Non è consentito specificare più criteri file.</td>
    <td>S</td>
    <td>S<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/DestKey:&lt;storage-key></b></td>
    <td>Specifica la chiave dell'account di archiviazione per la risorsa di destinazione.</td>
    <td>S</td>
    <td>S<br /></td>
    <td>S<br /></td>
  </tr>
  <tr>
    <td class="auto-style1"><b>/DestSAS:&lt;sas-token></b></td>
    <td class="auto-style1">Specifica una firma di accesso condiviso con autorizzazioni di LETTURA e SCRITTURA per la destinazione (se applicabile). Racchiudere la firma di accesso condiviso tra virgolette doppie, in quanto potrebbe contenere caratteri della riga di comando speciali.<br />
        Se la risorsa di destinazione è una tabella, una condivisione file o un contenitore BLOB, è possibile specificare questa opzione seguita dal token di accesso condiviso o specificare la firma di accesso condiviso come parte dell'URI della tabella, della condivisione file o del contenitore BLOB, senza questa opzione.<br />
        Se l'origine e la destinazione sono entrambi BLOB, il BLOB di destinazione deve essere nello stesso account di archiviazione del BLOB di origine.</td>
    <td class="auto-style1">S</td>
    <td class="auto-style1">S<br /></td>
    <td class="auto-style1">S<br /></td>
  </tr>
  <tr>
    <td><b>/SourceKey:&lt;storage-key></b></td>
    <td>Specifica la chiave dell'account di archiviazione per la risorsa di origine.</td>
    <td>S</td>
    <td>S<br /></td>
    <td>S<br /></td>
  </tr>
  <tr>
    <td><b>/SourceSAS:&lt;sas-token></b></td>
    <td>Specifica una firma di accesso condiviso con autorizzazioni di LETTURA ed ELENCO per l’origine (se applicabile). Racchiudere la firma di accesso condiviso tra virgolette doppie, in quanto potrebbe contenere caratteri della riga di comando speciali.
        <br />
        Se la risorsa di origine è un contenitore BLOB e non viene fornita né una chiave né una firma di accesso condiviso, il contenitore BLOB verrà letto tramite accesso anonimo.
        <br />
        Se l'origine è una tabella o una condivisione file, è necessario specificare una chiave o una firma di accesso condiviso.</td>
    <td>S</td>
    <td>S<br /></td>
    <td>S<br /></td>
  </tr>
  <tr>
    <td><b>/S</b></td>
    <td>Specifica la modalità ricorsiva per le operazioni di copia. Nella modalità ricorsiva, AzCopy copierà tutti i BLOB o file corrispondenti al criterio di file specificato, inclusi quelli nelle sottocartelle.</td>
    <td>S</td>
    <td>S<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/BlobType:&lt;block | page | append></b></td>
    <td>Specifica se il BLOB di destinazione è un BLOB in blocch, un BLOB di pagine o un BLOB di accodamento. Questa opzione si applica solo quando si carica un BLOB; in caso contrario, viene generato un errore. Se la destinazione è un BLOB e questa opzione non è specificata, per impostazione predefinita, AzCopy creerà un BLOB in blocchi.</td>
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
    <td>S<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/Snapshot</b></td>
    <td>Indica se trasferire gli snapshot o meno. Questa opzione è valida solo quando l'origine è un BLOB. 
        <br />
        Gli snapshot di blob trasferiti vengono rinominati nel formato seguente: [nome-blob] (ora-snapshot) [estensione]. 
        <br />
        Per impostazione predefinita, gli snapshot non vengono copiati.</td>
    <td>S</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/V:verbose log-file</b></td>
    <td>Esegue l'output dei messaggi di stato dettagliati in un file di log. Per impostazione predefinita, al file di log dettagliato viene assegnato il nome <code>AzCopyVerbose.log</code> in <code>%LocalAppData%\Microsoft\Azure\AzCopy</code>. Se si specifica un percorso di file esistente per questa opzione, il log dettagliato verrà aggiunto al file indicato.</td>
    <td>S</td>
    <td>S<br /></td>
    <td>S<br /></td>
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
    <td>S<br /></td>
    <td>S<br /></td>
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
    <td>S<br /></td>
    <td>S<br /></td>
  </tr>
  <tr>
    <td><b>/Y</b></td>
    <td>Elimina tutte le richieste di conferma di AzCopy.</td>
    <td>S</td>
    <td>S<br /></td>
    <td>S<br /></td>
  </tr>
  <tr>
    <td><b>/L</b></td>
    <td>Specifica solo un'operazione di elenco, non viene copiato alcun dato.
    <br />
    AzCopy interpreta l'utilizzo di questa opzione come una simulazione per l'esecuzione della riga di comando senza questa opzione /L e conta il numero di oggetti che vengono copiati. È possibile specificare l'opzione /V contemporaneamente per controllare quali oggetti vengono copiati nel log dettagliato.
    <br />
    Il comportamento di questa opzione è determinato dal percorso dei dati di origine e dalla presenza dell'opzione relativa alla modalità ricorsiva /S e dell’opzione di modello file /Pattern.
    <br />
    AzCopy richiede l'autorizzazione ELENCO e LETTURA per questo percorso di origine quando si utilizza questa opzione.</td>
    <td>S</td>
    <td>S<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/MT</b></td>
    <td>Imposta l'ora dell'ultima modifica di un file scaricato sulla stessa ora del file o del BLOB di origine.</td>
    <td>S</td>
    <td>S<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XN</b></td>
    <td>Esclude una risorsa di origine più recente. La risorsa non verrà copiata se l'ora dell'ultima modifica dell'origine è uguale alla destinazione o più recente.</td>
    <td>S</td>
    <td>S<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XO</b></td>
    <td>Esclude una risorsa di origine meno recente. La risorsa non verrà copiata se l'ora dell'ultima modifica dell'origine è uguale alla destinazione o meno recente.</td>
    <td>S</td>
    <td>S<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/A</b></td>
    <td>Carica solo i file per i quali è impostato l'attributo Archive.</td>
    <td>S</td>
    <td>S<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/IA:[RASHCNETOI]</b></td>
    <td>Carica solo i file per i quali sono impostati gli attributi specificati.<br />
        Gli attributi disponibili sono:  
        <br />
        R&#160;&#160;&#160;File di sola lettura
        <br />
        A&#160;&#160;&#160;File pronti per l'archiviazione
        <br />
        S&#160;&#160;&#160;File di sistema
        <br />
        H&#160;&#160;&#160;File nascosti
        <br />
        C&#160;&#160;&#160;File compressi
        <br />
        N&#160;&#160;&#160;File normali
        <br />
        E&#160;&#160;&#160;File crittografati
        <br />
        T&#160;&#160;&#160;File temporanei
        <br />
        O&#160;&#160;&#160;File offline
        <br />
        I&#160;&#160;&#160;File non indicizzati</td>
    <td>S</td>
    <td>S<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XA:[RASHCNETOI]</b></td>
    <td>Esclude i file per i quali sono impostati gli attributi specificati.<br />
        Gli attributi disponibili sono:  
        <br />
        R&#160;&#160;&#160;File di sola lettura  
        <br />
        A&#160;&#160;&#160;File pronti per l'archiviazione  
        <br />
        S&#160;&#160;&#160;File di sistema  
        <br />
        H&#160;&#160;&#160;File nascosti  
        <br />
        C&#160;&#160;&#160;File compressi  
        <br />
        N&#160;&#160;&#160;File normali  
        <br />
        E&#160;&#160;&#160;File crittografati  
        <br />
        T&#160;&#160;&#160;File temporanei  
        <br />
        O&#160;&#160;&#160;File offline  
        <br />
        I&#160;&#160;&#160;File non indicizzati</td>
    <td>S</td>
    <td>S<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/Delimiter:&lt;delimiter></b></td>
    <td>Indica il delimitatore usato per delimitare le directory virtuali in un nome BLOB.<br />
        Per impostazione predefinita, AzCopy usa il carattere / come delimitatore. Tuttavia, a questo scopo supporta anche l'uso di caratteri comuni, ad esempio @, # o %. Se è necessario includere uno di questi caratteri speciali nella riga di comando, racchiudere il nome del file tra virgolette doppie. 
        <br />
        Questa opzione si applica solo per il download di BLOB.</td>
    <td>S</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/NC:&lt;number-of-concurrents></b></td>
    <td>Specifica il numero di operazioni simultanee.
        <br />
        Per impostazione predefinita, AzCopy avvia un determinato numero di operazioni simultanee per aumentare la velocità effettiva di trasferimento dei dati. Si noti che un numero elevato di operazioni simultanee in un ambiente con una larghezza di banda ridotta potrebbe sovraccaricare la connessione di rete e impedire il corretto completamento delle operazioni. Limitare le operazioni simultanee in base alla larghezza di banda di rete effettivamente disponibile.
        <br />
		Il limite massimo per le operazioni simultanee è 512.</td>
    <td>S</td>
    <td>S<br /></td>
    <td>S<br /></td>
  </tr>
  <tr>
    <td><b>/SourceType:Blob|Tabella</b></td>
    <td>Indica che la risorsa <code>source</code> è un BLOB disponibile nell'ambiente di sviluppo locale, in esecuzione nell'emulatore di archiviazione.</td>
    <td>S</td>
    <td>N</td>
    <td>S<br /></td>
  </tr>
  <tr>
    <td><b>/DestType:Blob|Tabella</b></td>
    <td>Indica che la risorsa <code>destination</code>  è un BLOB disponibile nell'ambiente di sviluppo locale, in esecuzione nell'emulatore di archiviazione.</td>
    <td>S</td>
    <td>N</td>
    <td>S<br /></td>
  </tr>
  <tr>
    <td><strong>/PKRS:&lt;"key1#key2#key3#..."></strong></td>
    <td>Divide l'intervallo di chiavi di partizione per consentire l'esportazione dei dati della tabella in parallelo, aumentando la velocità dell'operazione di esportazione.
        <br />
        Se questa opzione non è specificata, AzCopy usa un solo thread per esportare le entità della tabella. Ad esempio, se l'utente specifica /PKRS:"aa#bb", AzCopy avvia tre operazioni simultanee.
        <br />
        Ogni operazione esporta uno dei tre intervalli di chiavi di partizione, nel modo seguente: 
        <br />
        &#160;&#160;&#160;[&lt;prima chiave partizione>, aa) 
        <br />
        &#160;&#160;&#160;[aa, bb)
        <br />
        &#160;&#160;&#160;[bb, &lt;ultima chiave partizione>] </td>
    <td>N</td>
    <td>N</td>
    <td>S<br /></td>
  </tr>
  <tr>
    <td><strong>/SplitSize:</strong><file-size><strong>&lt;file-size></strong></td>
    <td>Specifica la dimensione del file esportato diviso in MB, il valore minimo consentito è 32.
        <br />
        Se questa opzione non è specificata, AzCopy esporterà i dati della tabella in un solo file.
        <br />
        Se i dati della tabella vengono esportati in un BLOB e le dimensioni del file esportato raggiungono il limite di 200 GB per le dimensioni del BLOB, AzCopy dividerà il file esportato, anche se questa opzione non viene specificata. </td>
    <td>N</td>
    <td>N</td>
    <td>S<br /></td>
  </tr>
  <tr>
    <td><b>/EntityOperation:InsertOrSkip | InsertOrMerge | InsertOrReplace</b>
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
    <td>S<br /></td>
  </tr>
  <tr>
    <td><b>/Manifest:&lt;manifest-file></b></td>
    <td>Specifica il file manifesto per l'operazione di importazione ed esportazione della tabella. <br />
    Questa opzione è facoltativa durante l'operazione di esportazione. AzCopy genera un file manifesto con il nome predefinito se non si specifica questa opzione.
    <br />
    Questa opzione è necessaria durante l'operazione di importazione per individuare i file di dati.</td>
    <td>N</td>
    <td>N</td>
    <td>S<br /></td>
  </tr>
  <tr>
    <td><b>/SyncCopy</b></td>
    <td>Indica se si desidera copiare BLOB o file in modo sincrono da un endpoint di Archiviazione di Azure a un altro. <br />
		Per impostazione predefinita, AzCopy esegue la copia in modo asincrono sul lato server. Specificare questa opzione per eseguire una copia sincrona, che scarica BLOB o file nella memoria locale e quindi li carica in Archiviazione di Azure. È possibile utilizzare questa opzione quando si copiano file all'interno dell'archiviazione BLOB o dell'archiviazione file oppure quando i file vengono copiati dall'archiviazione BLOB all'archiviazione file o viceversa.</td>
    <td>S</td>
    <td>S<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/SetContentType:&lt;content-type></b></td>
    <td>Specifica il tipo di contenuto MIME per i BLOB o i file di destinazione. <br />
		Per impostazione predefinita, AzCopy imposta il tipo di contenuto per i BLOB o per i file su <code>application/octet-stream</code>. Se si desidera impostare il tipo di contenuto per tutti i BLOB o i file, è possibile specificare in modo esplicito un valore per questa opzione. Se si specifica questa opzione senza fornire un valore, AzCopy imposterà il tipo di contenuto di ogni BLOB o file in base all'estensione di ciascuno di questi elementi.</td>
    <td>S</td>
    <td>S<br /></td>
    <td>N</td>
  </tr>
    <tr>
    <td><b>/PayloadFormat:&lt;JSON | CSV></b></td>
    <td>Specifica il formato del file di dati tabella esportato.<br />
    Se questa opzione non è specificata, per impostazione predefinita AzCopy esporta il file di dati tabella nel formato JSON.</td>
    <td>N</td>
    <td>N</td>
    <td>S<br /></td>
  </tr>
</table>
<br/>

## Limitare le scritture simultanee durante la copia dei dati

Durante la copia di BLOB o file con AzCopy, tenere presente che altre applicazioni potrebbero modificare i dati mentre è in corso la copia. Se possibile, assicurarsi che i dati copiati non vengano modificati durante l'operazione di copia. Ad esempio, se si copia un file VHD associato a una macchina virtuale di Azure, verificare che altre applicazioni non stiano scrivendo nel file VHD durante la copia. In alternativa, è prima possibile creare uno snapshot del file VHD e quindi copiare lo snapshot.

Se non è possibile evitare che altre applicazioni scrivano nei BLOB o nei file durante l'operazione di copia, tenere presente che al termine del processo la parità delle risorse copiate con le risorse di origine potrebbe non essere completa.

## Copiare i BLOB di Azure con AzCopy

Gli esempi seguenti mostrano vari scenari per la copia di BLOB con AzCopy.

### Copiare un BLOB singolo

**Caricare un file dal file system all'archiviazione BLOB:**
	
	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt

**Scaricare un BLOB dall'archiviazione BLOB al file system:**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Per ulteriori informazioni sull'uso delle chiavi di accesso alle risorse di archiviazione, vedere [Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](../storage-create-storage-account/#regeneratestoragekeys).

### Copiare un BLOB tramite copia sul lato server

Quando si copia di un BLOB all'interno di un account di archiviazione o tra account di archiviazione diversi, viene eseguita un'operazione di copia sul lato server. Per altre informazioni sulle operazioni di copia sul lato server, vedere la pagina relativa all'[introduzione alla copia asincrona di BLOB tra account](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

**Copiare un BLOB all'interno di un account di archiviazione:**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt 

**Copiare un BLOB tra account di archiviazione diversi:**

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
 
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

Tenere presente che se la directory virtuale specificata non esiste, AzCopy caricherà il file in modo da includere la directory virtuale nel relativo nome (*ossia*, `vd/abc.txt` in base all'esempio precedente).

### Scaricare un BLOB in una nuova cartella

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Se la cartella `C:\myfolder`non esiste ancora, AzCopy la creerà nel file system e la scaricherà `abc.txt `nella nuova cartella.

### Caricare file e sottocartelle da una directory a un contenitore, in modo ricorsivo

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

Se si specifica l'opzione`/S` , il contenuto della directory specificata viene copiato nell'archiviazione BLOB in modo ricorsivo, ovvero includendo nella copia anche tutte le sottocartelle e i relativi file. Si supponga ad esempio che i file seguenti risiedano nella cartella `C:\myfolder`:

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

Se non si specifica l'opzione `/S`nella riga di comando, AzCopy eseguirà la copia in modo non ricorsivo. Verranno copiati solo i file nella directory specificata, ma le sottocartelle e i relativi file NON verranno copiati. Si supponga ad esempio che i file seguenti risiedano nella cartella `C:\myfolder`:

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

Dopo l'operazione di copia, la directory `C:\myfolder`includerà i file seguenti: Si noti che vengono copiati solo i BLOB della directory virtuale:

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

Si supponga che i BLOB seguenti risiedano nel contenitore specificato. Verranno copiati tutti i BLOB che iniziano con il prefisso `a` :

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

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

Dopo l'operazione di copia, il contenitore di destinazione includerà il BLOB e i relativi snapshot. Supponendo che il BLOB dell'esempio precedente contenga due snapshot, nel contenitore saranno presenti il BLOB e gli snapshot seguenti:

	abc.txt
	abc (2013-02-25 080757).txt
	abc (2014-02-21 150331).txt


### Usare un file di risposta per specificare i parametri della riga di comando

	AzCopy /@:"C:\myfolder\abc.txt"

È possibile includere i parametri della riga di comando di AzCopy in un file di risposta. AzCopy elabora i parametri nel file come se fossero stati specificati nella riga di comando, eseguendo una sostituzione diretta con i contenuti del file.

**Specificare uno o più file di risposta a riga singola**

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

**Specificare un file di risposta a più righe**

Si supponga che sia presente un file di risposta denominato `copyoperation.txt` , che contiene le righe seguenti. Ogni parametro di AzCopy è specificato su una riga:

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

Se si omette l'opzione`/Z` o si specifica l'opzione `/Z` senza il percorso della cartella, come illustrato sopra, AzCopy creerà il file journal nel percorso predefinito, ovvero `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Se il file journal esiste già, AzCopy riprenderà l'operazione in base al file journal.

**Specificare un percorso personalizzato per il file journal**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

In questo esempio viene creato il file journal, nel caso in cui non esista già: Se esiste, AzCopy riprenderà l'operazione in base al file journal.

**Riprendere un'operazione di AzCopy**

	AzCopy /Z:C:\journalfolder\

In questo esempio viene ripresa l'ultima operazione che potrebbe non essere stata completata a causa di errori.


### Generare un file di log

**Scrivere il file di log dettagliato nel percorso predefinito**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

Se si specifica l'opzione `/V`senza fornire il percorso del file di log dettagliato, AzCopy creerà il file journal nel percorso predefinito, ovvero`%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

**Scrivere il file di log dettagliato in un percorso dettagliato**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

Si noti che se si specifica un percorso relativo per l'opzione`/V` , ad esempio`/V:test/azcopy1.log`, il log dettagliato verrà creato nella directory di lavoro corrente all'interno di una sottocartella denominata `test`.


### Impostare l'ora dell'ultima modifica dei file scaricati sulla stessa ora dei BLOB di origine

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

### Escludere BLOB dall'operazione di copia in base all'ora dell'ultima modifica

Specificare l'opzione `/MT`per confrontare l'ora dell'ultima modifica del BLOB di origine e del file di destinazione.

**Escludere BLOB in cui l'ora dell'ultima modifica è uguale al file di destinazione o più recente**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

**Escludere BLOB in cui l'ora dell'ultima modifica è uguale al file di destinazione o meno recente**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

### Specificare il numero di operazioni simultanee da avviare

L'opzione `/NC`specifica il numero di operazioni di copia simultanee. Per impostazione predefinita, AzCopy avvierà un numero di operazioni simultanee pari a otto volte il numero di processori core presenti. Se AzCopy è in esecuzione in una rete con larghezza di banda ridotta, è possibile specificare un numero inferiore per l'opzione in modo da evitare errori generati dalla competizione tra le risorse.


### 	Eseguire AzCopy sulle risorse del BLOB nell'emulatore di archiviazione

	AzCopy /Source:https://127.0.0.1:10004/myaccount/myfileshare/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S

### Copiare i BLOB tra due endpoint di Archiviazione di Azure in modo sincrono

Per impostazione predefinita, AzCopy copia i dati tra due endpoint di archiviazione in modo asincrono. L'operazione di copia verrà pertanto eseguita in background sfruttando la capacità di larghezza di banda disponibile non limitata da alcun contratto di servizio relativo alla velocità di copia di un BLOB. AzCopy controllerà periodicamente lo stato della copia fino a quando questa non verrà completata o risulterà non riuscita.

L'opzione `/SyncCopy` garantisce che l'operazione di copia ottenga una velocità coerente. AzCopy esegue la copia sincrona scaricando i BLOB da copiare dall'origine specificata nella memoria locale, per poi caricarli nella destinazione dell'archiviazione BLOB.

	AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy

Notare che `/SyncCopy` potrebbe generare ulteriori costi in uscita rispetto alla copia asincrona. Per evitare costi di uscita, è dunque consigliabile usare questa opzione nella VM di Azure che si trova nella stessa area dell'account di archiviazione di origine.

### Specificare il tipo di contenuto MIME di un BLOB di destinazione

Per impostazione predefinita, AzCopy imposta il tipo di contenuto di un BLOB di destinazione su`application/octet-stream`. A partire dalla versione 3.1.0, è possibile specificare il tipo di contenuto in modo esplicito tramite l'opzione `/SetContentType:[content-type]`. Questa sintassi imposta il tipo di contenuto per tutti i BLOB in un'operazione di copia.

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4

Se si specifica `/SetContentType`senza fornire un valore, AzCopy imposterà il tipo di contenuto di ogni BLOB o file in base all'estensione di ciascuno di questi elementi.

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType

## Copiare i file in un’archiviazione file di Azure con AzCopy

Gli esempi seguenti mostrano vari scenari per la copia di file di Azure con AzCopy.

### Scaricare un file nel file system da una condivisione file di Azure

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Notare che se l'origine specificata è una condivisione file di Azure, è necessario specificare il nome file esatto (*ad esempio* `abc.txt`) per copiare un solo file oppure specificare l'opzione `/S` per copiare tutti i file nella condivisione in modo ricorsivo. Se si specificano sia il criterio del file che l'opzione `/S`contemporaneamente, verrà generato un errore.

### Scaricare file e cartelle nel file system da una condivisione file di Azure, in modo ricorsivo, specificare la firma di accesso condiviso

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceSAS:SAS /S

Le cartelle vuote non verranno copiate.


### Caricare file e cartelle dal file system a una condivisione file di Azure, in modo ricorsivo

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Le cartelle vuote non verranno copiate.


### Caricare i file corrispondenti al criterio di file specificato in una condivisione di Azure, in modo ricorsivo

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

### Copiare i file in modo asincrono in Archiviazione file di Azure

Archiviazione file di Azure supporta la copia asincrona sul lato server.

Copia asincrona da Archiviazione file ad Archiviazione file:

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S

Copia asincrona da Archiviazione file a BLOB in blocchi:
  
	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S

Copia asincrona da Archiviazione BLOB di pagine/in blocchi ad Archiviazione file:

	AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S

Si noti che la copia asincrona da Archiviazione file a BLOB di pagine non è supportata.

### Copiare i file in modo sincrono in Archiviazione file di Azure

Oltre alla copia asincrona, è possibile anche specificare l'opzione `/SyncCopy` per copiare i dati da Archiviazione file ad Archiviazione file, da Archiviazione file ad Archiviazione BLOB e da Archiviazione BLOB ad Archiviazione file in modo sincrono. AzCopy esegue questa operazione scaricando i dati di origine nella memoria locale e caricandoli nuovamente nella destinazione.

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy
	
	AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

Durante la copia da Archiviazione file ad Archiviazione BLOB, il tipo di BLOB predefinito è il BLOB in blocchi. Per modificare il tipo di BLOB di destinazione, è possibile specificare l'opzione `/BlobType:page`.

Notare che `/SyncCopy` potrebbe generare ulteriori costi in uscita rispetto alla copia asincrona. Per evitare costi di uscita, è dunque consigliabile usare questa opzione nella VM di Azure che si trova nella stessa area dell'account di archiviazione di origine.


## Copiare le entità in una tabella di Azure con AzCopy

Gli esempi seguenti mostrano vari scenari per la copia di file di entità di una tabella di Azure con AzCopy.

### Esportare le entità nel file system locale

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key

AzCopy scrive un file manifesto nella cartella di destinazione o nel contenitore BLOB specificato. Il file manifesto viene usato dal processo di importazione per trovare i file di dati necessari ed eseguire la convalida di dati durante il processo di importazione. Il file manifesto usa per impostazione predefinita la convenzione di denominazione seguente:

	<account name>_<table name>_<timestamp>.manifest

È possibile anche specificare l'opzione `/Manifest:<manifest file name>` per impostare il nome del file manifesto.

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest


### Esportare entità nel formato di file di dati JSON e CSV

AzCopy per impostazione predefinita esporta le entità tabella in file JSON. È possibile specificare l'opzione `/PayloadFormat:JSON|CSV` per decidere il tipo di file di dati esportati.

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV

Quando si specifica il formato di payload CSV, oltre ai file di dati con l'estensione `.csv` che verranno creati nella posizione specificata dal parametro `/Dest`, AzCopy genererà un file di schema con estensione `.schema.csv` per ogni file di dati. Si noti che AzCopy non include il supporto per "importare" il file di dati CSV. È possibile utilizzare il formato JSON per esportare e importare i dati della tabella.

### Esportare le entità in un BLOB di Azure

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy genererà un file di dati JSON nella cartella locale o nel contenitore BLOB con la convenzione di denominazione seguente:

	<account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

Il file di dati JSON generato segue il formato di payload per i metadati minimi. Per i dettagli su questo formato di payload, vedere [Formato di payload per le operazioni del servizio tabelle](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Tenere presente che quando si esportano entità di tabella di archiviazione nella risorsa di archiviazione BLOB, AzCopy esporterà in primo luogo le entità della tabella in file di dati temporanei locali e quindi li caricherà in un BLOB, questi file di dati temporanei vengono inseriti nella cartella di file journal con il percorso predefinito "<code>%LocalAppData%\\Microsoft\\Azure\\AzCopy</code>", è possibile specificare l'opzione/z: [cartella di file journal] per modificare il percorso della cartella di file journal e quindi modificare il percorso di file di dati temporanei. Le dimensioni dei file di dati temporanei vengono stabilite dalla dimensione delle entità della tabella e dalla dimensione specificata tramite l’opzione /SplitSize, anche se il file di dati temporanei nel disco locale verrà eliminato immediatamente dopo averlo caricato nel BLOB, verificare che si disponga di sufficiente spazio su disco locale per archiviare i file di dati temporanei prima di essere eliminati,

### Dividere i file di esportazione

	AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy usa un *indice di volumi* nei nomi file di dati divisi per distinguere più file. L'indice di volumi è costituito da due parti, un *indice di intervalli di chiavi di partizione* e un *indice di file divisi*. Entrambi gli indici sono in base zero.

L'indice di intervalli di chiavi di partizione sarà 0 se l'utente non specifica l'opzione `/PKRS` (descritta nella sezione successiva).

Si supponga, ad esempio, che AzCopy generi due file di dati dopo che l'utente ha specificato l'opzione.`/SplitSize`. I nomi file di dati risultanti potrebbero essere:

	myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
	myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Tenere presente che il valore minimo possibile per l'opzione `/SplitSize` è di 32 MB. Se la destinazione specificata è l'archiviazione BLOB, AzCopy dividerà il file di dati quando le dimensioni raggiungeranno il limite delle dimensioni BLOB (200 GB), indipendentemente dal fatto che l'opzione `/SplitSize`sia stata specificata o meno dall'utente.

### Esportare le entità simultaneamente

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"

AzCopy avvierà operazioni simultanee per esportare le entità quando l'utente specifica l'opzione.`/PKRS`. Ogni operazione esporta un intervallo di chiavi di partizione.

Si noti che il numero di operazioni simultanee viene controllato anche dall'opzione `/NC`. AzCopy usa il numero di processori core come valore predefinito di `/NC`durante la copia di entità della tabella, anche se `/NC` non è stato specificato. Quando l'utente specifica l'opzione `/PKRS`, AzCopy usa il valore più basso dei due (intervalli di chiavi di partizione e operazioni simultanee specificate in modo implicito o esplicito) per determinare il numero di operazioni simultanee da avviare. Per ulteriori dettagli, digitare `AzCopy /?:NC`nella riga di comando.

### Importare le entità simultaneamente

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace 

L'opzione `/EntityOperation` indica come inserire le entità nella tabella. I valori possibili sono:

- `InsertOrSkip`: ignora un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.
- `InsertOrMerge`: unisce un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.
- `InsertOrReplace`: sostituisce un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.

Si noti che non è possibile specificare l'opzione `/PKRS`nello scenario di importazione. Diversamente dallo scenario di esportazione, in cui è necessario specificare l'opzione `/PKRS`per avviare operazioni simultanee, per impostazione predefinita AzCopy avvierà le operazioni simultanee quando si importeranno le entità. Il numero predefinito di operazioni simultanee avviate è uguale al numero di processori core. Tuttavia, è possibile specificare un numero diverso di operazioni simultanee con l'opzione`/NC`. Per ulteriori dettagli, digitare `AzCopy /?:NC`nella riga di comando.


## Problemi noti e procedure consigliate

#### Eseguire un'istanza di AzCopy su un computer.
AzCopy è progettato per massimizzare l'utilizzo della risorsa del computer per accelerare il trasferimento dei dati, è consigliabile eseguire solo un'istanza di AzCopy in un computer e specificare l'opzione `/NC` se sono necessarie più operazioni simultanee. Per ulteriori dettagli, digitare `AzCopy /?:NC` nella riga di comando.

#### Abilitare gli algoritmi MD5 conformi a FIPS per AzCopy quando si utilizzano gli algoritmi FIPS compatibili per crittografia, hash e firma.
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

## Versioni di AzCopy

> [AZURE.NOTE]È consigliabile installare la versione più recente di AzCopy per ottenere nuove funzionalità e prestazioni migliori.

| Versione | Novità | Versione libreria client .NET a cui viene fatto riferimento | Versione API REST di archiviazione di destinazione |
|---------|-----------------------------------------------------------------------------------------------------------------|--------|----------|
| [**V5.0.0**](http://xdmrelease.blob.core.windows.net/azcopy-5-0-0/MicrosoftAzureStorageTools.msi) | **Versione finale corrente. Include tutte le funzionalità dalla V4.2.0. Tutte le funzionalità per l'archiviazione di file e tabelle sono ora GA.** | **V6.0.0** | **2015-04-05**
| [V4.2.0](http://xdmrelease.blob.core.windows.net/azcopy-4-2-0-preview/MicrosoftAzureStorageTools.msi) | Include tutte le funzionalità della V3.2.0. Supporta inoltre la firma di accesso condiviso della condivisione di archiviazione file, la copia asincrona di archiviazione file, l’esportazione di entità tabella in formato CSV e la specifica del nome del manifesto quando si esportano le entità tabella | V5.0.0 | 20150221
| [V3.2.0](http://xdmrelease.blob.core.windows.net/azcopy-3-2-0/MicrosoftAzureStorageTools.msi) | Supporta il BLOB di accodamento e l'impostazione MD5 conforme a FIPS | V5.0.0 | 20150221
| [V4.1.0](http://xdmrelease.blob.core.windows.net/azcopy-4-1-0-preview/MicrosoftAzureStorageTools.msi) | Include tutte le funzionalità da V3.1.0. Supporta la copia sincrona di BLOB e file e la possibilità di specificare il tipo di contenuto dei file e dei BLOB di destinazione. | V4.3.0 | 14-02-2014
| [V3.1.0](http://xdmrelease.blob.core.windows.net/azcopy-3-1-0/MicrosoftAzureStorageTools.msi) | Supporta la copia sincrona di BLOB e la possibilità di specificare il tipo di contenuto dei BLOB di destinazione| V4.3.0 | 14-02-2014
| [V4.0.0](http://xdmrelease.blob.core.windows.net/azcopy-4-0-0-preview/MicrosoftAzureStorageTools.msi) | Include tutte le funzionalità da V3.0.0. Supporta anche la copia di file verso o dall'archiviazione file di Azure e la copia di entità verso o dall'archiviazione tabelle di Azure.| V4.2.1 | 14-02-2014
| [V3.0.0](http://xdmrelease.blob.core.windows.net/azcopy-3-0-0/MicrosoftAzureStorageTools.msi) | Modifica la sintassi della riga di comando di AzCopy per poter richiedere i nomi di parametro e riprogetta la Guida della riga di comando. Questa versione supporta solo la copia verso e dall'archiviazione BLOB di Azure.| V4.2.1 | 14-02-2014
| V2.5.1 | Ottimizza le prestazioni quando si usano le opzioni /xo e /xn. Corregge i bug relativi ai caratteri speciali nei nomi file di origine e al danneggiamento dei file journal dopo che l'utente ha inserito la sintassi della riga di comando errata.| V4.1.0 | 14-02-2014
| V2.5.0 | Ottimizza le prestazioni per gli scenari di copia su larga scala e introduce numerosi miglioramenti importanti in termini di usabilità.| V4.1.0 | 14-02-2014
| V2.4.1 | Consente di specificare la cartella di destinazione nell'installazione guidata.| V4.0.0 | 14-02-2014
| V2.4.0 | Consente di caricare e scaricare file per l'archiviazione file di Azure.| V4.0.0 | 14-02-2014
| V2.3.0 | Supporta gli account di archiviazione con ridondanza geografica e accesso in lettura| V3.0.3 | 2013-08-15
| V2.2.2 | Versione aggiornata per l'uso della libreria client di archiviazione di Azure versione 3.0.3.| V3.0.3 | 2013-08-15
| V2.2.1 | Include la correzione di un problema di prestazioni durante la copia di grandi quantità di file all'interno dello stesso account di archiviazione.| V2.1.0 |
| V2.2 | Supporta l'impostazione del delimitatore della directory virtuale per i nomi di BLOB. Consente di specificare il percorso del file journal.| V2.1.0 |
| V2.1 | Offre più di 20 opzioni per supportare operazioni di caricamento, scaricamento e copia di BLOB in un modo efficiente.| V2.0.5 |


## Passaggi successivi

Per altre informazioni su Archiviazione di Azure e AzCopy, vedere le risorse seguenti:

### Documentazione di Archiviazione di Azure

- [Introduzione ad Archiviazione di Azure](storage-introduction.md)
- [Archiviare i file nell'archiviazione BLOB](storage-dotnet-how-to-use-blobs.md)
- [Creare una condivisione file SMB in Azure con l'archiviazione file](storage-dotnet-how-to-use-files.md)

### Post del blog di Archiviazione di Azure:
- [DML: Introduzione alla versione di anteprima della libreria per lo spostamento dei dati di Archiviazione di Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
- [AzCopy: introduzione alla copia sincrona e al tipo di contenuto personalizzato](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
- [AzCopy: annuncio della disponibilità per tutti di AzCopy 3.0 oltre alla versione di anteprima di AzCopy 4.0 con il supporto di tabelle e file](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy: ottimizzazione per gli scenari di copia su larga scala](http://go.microsoft.com/fwlink/?LinkId=507682)
- [Introduzione al servizio File di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [AzCopy:supporto per l'archiviazione con ridondanza geografica e accesso in lettura](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy:trasferimento di dati con modalità riavviabile e token di firma di accesso condiviso](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy: uso del comando di copia dei BLOB tra account](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy: Caricamento e download di file per BLOB di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)

<!---HONumber=AcomDC_1203_2015-->