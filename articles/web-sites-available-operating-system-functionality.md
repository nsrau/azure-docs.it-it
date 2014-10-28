<properties title="Operating System Functionality Available to Applications on Azure Websites" pageTitle="Operating System Functionality Available to Applications on Azure Websites" description="Learn about the OS functionality available to web applications on Azure Websites" metaKeywords="Azure,Web Sites,web applications,operating system functionality" services="web-sites" solutions="web" documentationCenter="" authors="cephalin" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Funzionalità del sistema operativo disponibili per le applicazioni su Siti web di Azure

In questo articolo vengono descritte le funzionalità di base del sistema operativo disponibili in tutte le applicazioni in esecuzione su Siti Web di Azure. Queste funzionalità includono l'accesso a file, rete e registro, nonché log ed eventi di diagnostica.

## Sommario

-   [Modalità dei siti Web][Modalità dei siti Web]
-   [Framework di sviluppo][Framework di sviluppo]
-   [Accesso ai file][Accesso ai file]
    -   [Unità locali][Unità locali]
    -   [Unità di rete (note anche come condivisioni UNC)][Unità di rete (note anche come condivisioni UNC)]
    -   [Accesso a file tra più istanze][Accesso a file tra più istanze]
    -   [Tipi di accesso ai file concesso a un'applicazione Web][Tipi di accesso ai file concesso a un'applicazione Web]
-   [Accesso alla rete][Accesso alla rete]
-   [Esecuzione del codice, processi e memoria][Esecuzione del codice, processi e memoria]
-   [Log ed eventi di diagnostica][Log ed eventi di diagnostica]
-   [Accesso al registro][Accesso al registro]

<span id="websitemodes"></span></a>

## Modalità dei siti Web

Siti Web di Azure esegue i siti Web dei clienti in un ambiente host multi-tenant. I siti Web distribuiti nelle modalità di scala dei siti Web Gratuito e Condiviso vengono eseguiti in processi di lavoro su macchine virtuali condivise, mentre i siti Web distribuiti nella modalità di scala dei siti Web Standard vengono eseguiti su una o più macchine virtuali specificatamente dedicate ai siti Web associati a un solo cliente.

Poiché Siti Web di Azure supporta una perfetta scalabilità tra le varie modalità, ai siti Web viene applicata la stessa configurazione di sicurezza. Questo garantisce un comportamento sempre costante delle applicazioni Web, senza eventi imprevisti, nel passaggio di un sito Web tra le varie modalità.

<span id="developmentframeworks"></span></a>

## Framework di sviluppo

Le modalità dei siti Web controllano la quantità di risorse di calcolo (CPU, archiviazione su disco, memoria e uscita di rete) disponibili per i siti Web. Tuttavia, la gamma di funzionalità del framework disponibile per le applicazioni resta la stessa indipendentemente dalla modalità del sito Web.

Siti Web di Azure supporta diversi framework di sviluppo, tra cui ASP.NET, ASP classico, Node.js, PHP e Python, tutti eseguibili come estensioni in IIS. Per semplificare e normalizzare la configurazione di sicurezza, Siti Web di Azure esegue i vari framework di sviluppo con le relative impostazioni predefinite. Un approccio alla configurazione di Siti Web di Azure avrebbe potuto comportare la personalizzazione della superficie e delle funzionalità API per ciascun framework di sviluppo. Siti Web di Azure, invece, usa un approccio più generico abilitando una linea di base comune delle funzionalità del sistema operativo indipendentemente dal framework di sviluppo delle applicazioni di un sito Web.

Nelle sezioni seguenti sono riepilogati i tipi generali di funzionalità del sistema operativo disponibili per i siti Web in Azure.

<span id="FileAccess"></span></a>

## Accesso ai file

In Siti Web di Azure sono presenti varie unità, tra cui unità locali e unità di rete.

<span id="LocalDrives"></span></a>

### Unità locali

Siti Web di Azure è essenzialmente un servizio che viene eseguito sull'infrastruttura PaaS (Platform as a Service) di Azure. Di conseguenza, le unità locali collegate a una macchina virtuale sono dello stesso tipo di quelle disponibili in qualsiasi ruolo di lavoro in esecuzione in Azure. Sono inclusi un'unità del sistema operativo (l'unità D:\\), un'unità delle applicazioni che contiene i file Pacchetti di Windows Azure (cspkg) usati esclusivamente da Siti Web di Azure (e che non sono accessibili ai clienti) e un'unità "utente" (l'unità C:\\), la cui dimensione varia in base alla dimensione della macchina virtuale.

<span id="NetworkDrives"></span></a>

### Unità di rete (note anche come condivisioni UNC)

Uno degli aspetti esclusivi di Siti Web di Azure che rende immediata la distribuzione e la manutenzione delle applicazioni Web è rappresentato dal fatto che l'intero contenuto utente è archiviato in un set di condivisioni UNC. Questo modello è perfettamente associato al modello comune di archiviazione di contenuto utilizzato dagli ambienti di hosting Web locali che dispongono di più server con carico bilanciato.

In Siti Web di Azure sono presenti numerose condivisioni UNC create in ciascun data center. A ciascuna condivisione UNC è allocata una percentuale del contenuto utente per tutti i clienti di ciascun data center. Inoltre, l'intero contenuto dei file per una sottoscrizione di un singolo cliente è sempre posizionato nella stessa condivisione UNC.

Tenere presente che per effetto della modalità di funzionamento dei servizi cloud, la macchina virtuale specifica che è responsabile dell'hosting di una condivisione UNC cambierà nel corso del tempo. Sicuramente le condivisioni UNC verranno montate da macchine virtuali diverse in quanto vengono avviate e arrestate durante la normale esecuzione delle operazioni cloud. Per questo motivo le applicazioni Web non devono mai essere hard-coded in base al presupposto che le informazioni sul computer in un percorso file UNC rimarranno stabili nel corso del tempo. Devono, invece, usare il pratico percorso assoluto *faux* **D:\\home\\site** fornito da Siti Web di Azure, che fornisce un metodo portatile, indipendente dal sito e dall'utente, di fare riferimento al proprio sito. Utilizzando **D:\\home\\site** è possibile trasferire file condivisi tra siti senza dover configurare un nuovo percorso assoluto per ciascun trasferimento.

<span id="TypesOfFileAccess"></span></a>

### Tipi di accesso ai file concesso a un'applicazione Web

La sottoscrizione di ciascun cliente presenta una struttura di directory riservata su una condivisione UNC specifica all'interno di un data center. Un cliente può avere più siti Web creati all'interno di un data center specifico, pertanto tutte le directory che appartengono alla sottoscrizione di un singolo cliente vengono create nella stessa condivisione UNC. La condivisione può includere directory come quelle relative a log di contenuto, di errori e di diagnostica e versioni precedenti del sito Web creato dal controllo del codice sorgente. Come previsto, le directory del sito Web di un cliente sono disponibili per l'accesso in lettura e in scrittura in fase di esecuzione dal codice dell'applicazione di un sito Web.

Nelle unità locali collegate a una macchina virtuale sulla quale è in esecuzione un sito Web, Siti Web di Azure riserva una porzione di spazio nell'unità C:\\ per l'archiviazione locale temporanea specifica del sito Web. Sebbene un sito Web abbia accesso completo in lettura/scrittura alla propria risorsa di archiviazione locale temporanea, tale risorsa non è in realtà destinata all'uso diretto da parte del codice dell'applicazione. L'obiettivo è invece quello di fornire una risorsa di archiviazione di file temporanea per IIS e per i framework applicazione Web. Siti Web di Azure limita inoltre la quantità di risorse di archiviazione locale temporanea disponibili per ciascun sito Web allo scopo di impedire un uso eccessivo di tali risorse da parte dei singoli siti.

Due esempi del modo in cui Siti Web di Azure usa le risorse di archiviazione locale temporanea sono la directory per i file ASP.NET temporanei e la directory per i file IIS compressi. Il sistema di compilazione di ASP.NET utilizza la directory "Temporary ASP.NET Files" come posizione cache temporanea per la compilazione. IIS utilizza la directory "IIS Temporary Compressed Files" per archiviare l'output della risposta in formato compresso. Entrambi i tipi di uso di file (nonché altri) sono rimappati in Siti Web di Azure a una risorsa di archiviazione locale temporanea per singolo sito Web. Il remapping garantisce una funzionalità conforme alle aspettative.

Ogni sito Web di Siti Web di Azure viene eseguito come identità esclusiva e casuale del processo di lavoro con privilegi limitati definita "Identità pool di applicazioni", descritta più dettagliatamente all'indirizzo seguente: [][]<http://www.iis.net/learn/manage/configuring-security/application-pool-identities></a>. Il codice dell'applicazione utilizza questa identità per l'accesso di base in sola lettura all'unità del sistema operativo (l'unità D:\\). Questo significa che il codice dell'applicazione può elencare strutture di directory comuni e file comuni in lettura nell'unità del sistema operativo. Sebbene questo livello di accesso possa sembrare piuttosto esteso, è possibile accedere alle stesse directory e agli stessi file quando si esegue il provisioning di un ruolo di lavoro in un servizio ospitato Azure e la lettura dei contenuti dell'unità.

<a name="multipleinstances"></a>

### Accesso ai file tra più istanze

La home directory contiene il contenuto di un sito e le applicazioni Web possono scrivere su di essa. Se un sito Web esegue più istanze, la home directory viene condivisa tra tutte le istanze e pertanto tutte le istanze visualizzano la stessa directory. Quindi, ad esempio, se un sito Web salva i file caricati nella home directory, tali file sono immediatamente disponibili per tutte le istanze.

<span id="NetworkAccess"></span></a>

## Accesso alla rete

Il codice dell'applicazione può usare i protocolli basati su TCP/IP e UDP per effettuare connessioni di rete in uscita a endpoint accessibili tramite Internet che rendono visibili i servizi esterni. Le applicazioni possono usare gli stessi protocolli per eseguire la connessione ai servizi di Azure, ad esempio stabilendo connessioni HTTPS a SQL Azure.

È inoltre presente una capacità limitata per le applicazioni di stabilire una connessione loopback locale e di disporre di un'applicazione in ascolto su tale socket loopback locale. Questa funzionalità esiste principalmente per abilitare le applicazioni che includono tra le proprie funzionalità l'ascolto su socket di loopback locali. Tenere presente che l'applicazione di ogni cliente visualizza una connessione di loopback "privata"; l'applicazione "A" non può essere in ascolto su un socket di loopback locale stabilito dall'applicazione "B".

Anche i named pipe sono supportati come meccanismo di comunicazione interprocesso (IPC) tra processi diversi che eseguono collettivamente un sito Web. Ad esempio, il modulo IIS FastCGI si basa su named pipe per coordinare i singoli processi che eseguono le pagine PHP.

<span id="Code"></span></a>

## Esecuzione del codice, processi e memoria

Come indicato in precedenza, i siti Web vengono eseguiti all'interno di processi di lavoro con privilegi limitati che usano un'identità pool di applicazioni casuale. Il codice dell'applicazione ha accesso allo spazio di memoria associato al processo di lavoro, nonché a eventuali processi figlio che possono essere generati da processi CGI o da altre applicazioni. Tuttavia, le applicazioni di un sito Web non possono accedere alla memoria o ai dati del sito Web di un altro cliente anche se il sito si trova nella stessa macchina virtuale.

Le applicazioni possono eseguire script o pagine scritte con framework di sviluppo di applicazioni Web supportati. Siti Web di Azure non configura le impostazioni di framework applicazione Web su modalità con maggiori restrizioni. Ad esempio, i siti ASP.NET in esecuzione su Siti Web di Azure vengono eseguiti in una modalità di attendibilità "completa" e non in una modalità di attendibilità con maggiori restrizioni. I framework di applicazioni, tra cui ASP classico e ASP.NET, possono chiamare componenti COM in-process (ma non componenti COM out-of-process) come gli oggetti ADO (ActiveX Data Objects) che sono registrati per impostazione predefinita nel sistema operativo Windows.

Le applicazioni Web possono generare ed eseguire codice arbitrario. Un'applicazione Web può effettuare attività come la generazione di una shell dei comandi o l'esecuzione di uno script PowerShell. Tuttavia, mentre il codice arbitrario e i processi possano essere generati da un'applicazione Web, i programmi e gli script eseguibili sono ancora riservati al pool di applicazioni padre. Ad esempio, un sito Web può generare un eseguibile che effettua una chiamata HTTP in uscita, ma lo stesso eseguibile non può tentare di dissociare l'indirizzo IP di una macchina virtuale dal relativo NIC. L'esecuzione di una chiamata di rete in uscita è consentita al codice con privilegi limitati, ma il tentativo di riconfigurare le impostazioni di rete su una macchina virtuale richiede privilegi amministrativi.

<span id="Diagnostics"></span></a>

## Log ed eventi di diagnostica

Le informazioni dei log costituiscono un altro set di dati al quale alcune applicazioni Web provano ad accedere. I tipi di informazioni dei log disponibili per il codice in esecuzione in Siti Web di Azure includono le informazioni di diagnostica e dei log generate da un sito Web che sono anche facilmente accessibili a un sito Web.

Ad esempio, i log HTTP W3C generati da un sito Web attivo sono disponibili anche in una directory log nella posizione di condivisione di rete creata per il sito Web oppure in un archivio BLOB nel caso in cui un cliente abbia configurato la registrazione W3C nella risorsa di archiviazione. Quest'ultima opzione consente di raccogliere elevate quantità di log senza il rischio di superare i limiti di archiviazione dei file associati a una condivisione di rete.

Analogamente, le informazioni di diagnostica in tempo reale delle applicazioni .NET possono essere registrate usando l'infrastruttura delle funzionalità di traccia e diagnostica di .NET, con la possibilità di scrivere le informazioni di traccia nella condivisione di rete del sito Web o, in alternativa, in una posizione di archiviazione BLOB.

Le aree delle funzionalità di registrazione diagnostica e traccia che non sono disponibili per le applicazioni Web in Azure sono eventi Windows ETW e log eventi Windows comuni (ad es. log eventi di sistema, di applicazioni e di sicurezza). Poiché le informazioni di traccia ETW sono potenzialmente visualizzabili a livello di intera macchina (con gli ACL appropriati), l'accesso in lettura e scrittura agli eventi ETW sono bloccati. Gli sviluppatori potrebbero notare chiamate API per la lettura e la scrittura di eventi ETW e di log eventi Windows comuni apparentemente funzionanti; tuttavia, ciò accade perché Siti Web di Azure "simula" le chiamate in modo che risultino eseguite correttamente. In realtà, il codice dell'applicazione del sito Web non è autorizzato ad accedere a questi dati di eventi.

<span id="RegistryAccess"></span></a>

## Accesso al registro

Le applicazioni possono accedere in sola lettura alla maggior parte del registro della macchina virtuale sulla quale sono in esecuzione. In pratica, questo significa che le chiavi di registro che consentono l'accesso in sola lettura al gruppo di utenti locali sono accessibili tramite applicazioni Web. Un'area del registro che non è attualmente supportata per l'accesso in lettura o scrittura è l'hive HKEY\_CURRENT\_USER.

L'accesso in scrittura al registro è bloccato, incluso l'accesso a qualsiasi chiave di registro per utente. Dal punto di vista delle applicazioni, l'accesso in scrittura al registro in un ambiente cloud non deve mai essere considerato affidabile poiché le applicazioni sono soggette a migrazione tra macchine virtuali diverse. L'unica risorsa di archiviazione scrivibile permanente che può essere considerata affidabile da un'applicazione Web è la struttura della directory dei contenuti per sito Web archiviata nelle condivisioni UNC di Siti Web di Azure.

  [Modalità dei siti Web]: #websitemodes
  [Framework di sviluppo]: #developmentframeworks
  [Accesso ai file]: #FileAccess
  [Unità locali]: #LocalDrives
  [Unità di rete (note anche come condivisioni UNC)]: #NetworkDrives
  [Accesso a file tra più istanze]: #multipleinstances
  [Tipi di accesso ai file concesso a un'applicazione Web]: #TypesOfFileAccess
  [Accesso alla rete]: #NetworkAccess
  [Esecuzione del codice, processi e memoria]: #Code
  [Log ed eventi di diagnostica]: #Diagnostics
  [Accesso al registro]: #RegistryAccess
  []: http://www.iis.net/learn/manage/configuring-security/application-pool-identities
