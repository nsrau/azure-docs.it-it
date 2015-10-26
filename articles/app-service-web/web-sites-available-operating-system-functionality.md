<properties 
	pageTitle="Funzionalità del sistema operativo in App Web del servizio app di Azure" 
	description="Informazioni sulle funzionalità del sistema operativo disponibili per le applicazioni Web nel servizio app di Azure" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/29/2015" 
	ms.author="cephalin"/>

# Funzionalità del sistema operativo in App Web del servizio app di Azure #

Questo articolo descrive le funzionalità di base del sistema operativo disponibili in tutte le applicazioni in esecuzione su App Web del [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Queste funzionalità includono l'accesso a file, rete e registro, nonché log ed eventi di diagnostica.

<a id="tiers"></a>
## Livelli del piano del servizio app di Azure

Le app Web eseguono app di clienti in un ambiente host multi-tenant. Le app Web distribuite nei livelli **Gratuito** e **Condiviso** vengono eseguite in processi di lavoro su macchine virtuali condivise, mentre le app Web distribuite nei livelli **Standard** e **Premium** vengono eseguite su macchine virtuali dedicate in modo specifico per le app Web associate a un singolo cliente.

Poiché le app Web supportano una perfetta scalabilità tra i diversi livelli, la configurazione della sicurezza applicata per le app Web è la stessa. Questo garantisce un comportamento sempre costante delle applicazioni Web, senza eventi imprevisti, nel passaggio da un livello a un altro.

<a id="developmentframeworks"></a>
## Framework di sviluppo

I livelli delle app Web controllano la quantità di risorse di calcolo(CPU, archiviazione su disco, memoria e uscita di rete) disponibili per le app Web. Tuttavia, la gamma di funzionalità del framework disponibili per le applicazioni resta la stessa indipendentemente dai livelli di scalabilità.

Le app Web supportano diversi framework di sviluppo, tra cui ASP.NET, ASP classico, Node.js, PHP e Python, tutti eseguibili come estensioni in IIS. Per semplificare e normalizzare la configurazione della sicurezza, le app Web eseguono i vari framework di sviluppo con le relative impostazioni predefinite. Un approccio alla configurazione delle app Web avrebbe potuto comportare la personalizzazione delle funzionalità e della superficie di attacco delle API per ciascun framework di sviluppo. Le app Web invece usano un approccio più generico abilitando una linea di base comune delle funzionalità del sistema operativo indipendentemente dal framework di sviluppo di un'app Web.

Le seguenti sezioni riepilogano i tipi generali di funzionalità del sistema operativo disponibili per le app Web in Azure.

<a id="FileAccess"></a>
##Accesso ai file

Nelle app Web sono presenti varie unità, tra cui unità locali e unità di rete.

<a id="LocalDrives"></a>
### Unità locali

Le app Web sono essenzialmente un servizio in esecuzione sull'infrastruttura PaaS (Platform as a Service) di Azure. Di conseguenza, le unità locali collegate a una macchina virtuale sono dello stesso tipo di quelle disponibili in qualsiasi ruolo di lavoro in esecuzione in Azure. Sono incluse un'unità del sistema operativo (l'unità D:\\), un'unità delle applicazioni contenente i file pacchetto di Azure (cspkg) usati esclusivamente dalle app Web (e non accessibili ai clienti) e un'unità "utente" (l'unità C:\\), la cui dimensione varia in base alla dimensione della macchina virtuale.

<a id="NetworkDrives"></a>
### Unità di rete (note anche come condivisioni UNC)

Uno degli aspetti esclusivi delle app Web che rende immediata la distribuzione e la manutenzione delle applicazioni Web è rappresentato dal fatto che l'intero contenuto utente è archiviato in un set di condivisioni UNC. Questo modello corrisponde perfettamente al modello comune di archiviazione di contenuto usato dagli ambienti host Web locali che dispongono di più server con carico bilanciato.

Nelle app Web sono presenti numerose condivisioni UNC create in ciascun data center. A ciascuna condivisione UNC è allocata una percentuale del contenuto utente per tutti i clienti di ciascun data center. Inoltre, l'intero contenuto dei file per una sottoscrizione di un singolo cliente è sempre posizionato nella stessa condivisione UNC.

Tenere presente che per effetto della modalità di funzionamento dei servizi cloud, la macchina virtuale specifica che è responsabile dell'hosting di una condivisione UNC cambierà nel corso del tempo. Sicuramente le condivisioni UNC verranno montate da macchine virtuali diverse in quanto vengono avviate e arrestate durante la normale esecuzione delle operazioni cloud. Per questo motivo le applicazioni Web non devono mai essere hard-coded in base al presupposto che le informazioni sul computer in un percorso file UNC rimarranno stabili nel corso del tempo. Devono invece usare il pratico percorso assoluto *faux* **D:\\home\\site** fornito da App Web, che offre un metodo portatile, indipendente dall'app e dall'utente per fare riferimento alla propria app. Usando **D:\\home\\site** è possibile trasferire file condivisi da un'app all'altra senza dover configurare un nuovo percorso assoluto per ciascun trasferimento.

<a id="TypesOfFileAccess"></a>
### Tipi di accesso ai file concesso a un'applicazione Web

La sottoscrizione di ciascun cliente presenta una struttura di directory riservata su una condivisione UNC specifica all'interno di un data center. Un cliente può disporre di più app Web create all'interno di un data center specifico, pertanto tutte le directory che appartengono alla sottoscrizione di un singolo cliente vengono create nella stessa condivisione UNC. La condivisione può includere directory come quelle relative a log di contenuto, di errori e di diagnostica e versioni precedenti dell'app Web create dal controllo del codice sorgente. Come previsto, le directory dell'app Web di un cliente sono disponibili per l'accesso in lettura e in scrittura in fase di esecuzione dal codice dell'applicazione di un'app Web.

Le app Web riservano una porzione di spazio dell'unità C:\\ per l'archiviazione locale temporanea specifica delle app nelle unità locali collegate alla macchina virtuale in cui è in esecuzione un'app Web. Sebbene un'app Web disponga di accesso completo in lettura/scrittura alla propria risorsa di archiviazione locale temporanea, tale risorsa non è in realtà destinata all'uso diretto da parte del codice dell'applicazione. L'obiettivo è invece quello di fornire una risorsa di archiviazione di file temporanea per IIS e per i framework applicazione Web. Le app Web limitano inoltre la quantità di risorse di archiviazione locale temporanea disponibili per ogni app Web allo scopo di impedire un uso eccessivo di tali risorse da parte delle singole app.

Due esempi del modo in cui le app Web usano le risorse di archiviazione locale temporanea sono costituiti dalla directory per i file ASP.NET temporanei e dalla directory per i file IIS compressi. Il sistema di compilazione di ASP.NET usa la directory "Temporary ASP.NET Files" come posizione cache temporanea per la compilazione. IIS usa la directory "IIS Temporary Compressed Files" per archiviare l'output della risposta in formato compresso. Entrambi i tipi di uso di file (nonché altri) sono rimappati nelle app Web a una risorsa di archiviazione locale temporanea per singola app Web. Il remapping garantisce una funzionalità conforme alle aspettative.

Ogni app in App Web viene eseguita come identità esclusiva e casuale del processo di lavoro con privilegi limitati, definita come "identità del pool di applicazioni" e descritta più dettagliatamente al seguente indirizzo: [http://www.iis.net/learn/manage/configuring-security/application-pool-identities](http://www.iis.net/learn/manage/configuring-security/application-pool-identities). Il codice dell'applicazione usa questa identità per l'accesso di base in sola lettura all'unità del sistema operativo (l'unità D:\\). Questo significa che il codice dell'applicazione può elencare strutture di directory comuni e file comuni in lettura nell'unità del sistema operativo. Sebbene questo livello di accesso possa sembrare piuttosto esteso, è possibile accedere alle stesse directory e agli stessi file quando si esegue il provisioning di un ruolo di lavoro in un servizio ospitato Azure e la lettura dei contenuti dell'unità.

<a name="multipleinstances"></a>
### Accesso ai file tra più istanze

La home directory include il contenuto di un'app e le applicazioni Web possono scrivere in essa. Se un'app Web esegue più istanze, la home directory viene condivisa tra tutte le istanze e pertanto tutte le istanze visualizzano la stessa directory. Se pertanto un'app Web salva ad esempio i file caricati nella home directory, tali file sono immediatamente disponibili per tutte le istanze.

<a id="NetworkAccess"></a>
## Accesso alla rete
Il codice dell'applicazione può usare i protocolli basati su TCP/IP e UDP per effettuare connessioni di rete in uscita a endpoint accessibili tramite Internet che rendono visibili i servizi esterni. Le applicazioni possono usare gli stessi protocolli per eseguire la connessione ai servizi di Azure&#151; ad esempio stabilendo connessioni HTTPS a SQL Azure.

È inoltre presente una capacità limitata per le applicazioni di stabilire una connessione loopback locale e di disporre di un'applicazione in ascolto su tale socket loopback locale. Questa funzionalità esiste principalmente per abilitare le applicazioni che includono tra le proprie funzionalità l'ascolto su socket di loopback locali. Tenere presente che l'applicazione di ogni cliente visualizza una connessione di loopback "privata"; l'applicazione "A" non può essere in ascolto su un socket di loopback locale stabilito dall'applicazione "B".

Sono supportate anche le named pipe come meccanismo di comunicazione interprocesso (IPC) tra processi diversi che eseguono collettivamente un'app Web. Ad esempio, il modulo IIS FastCGI si basa su named pipe per coordinare i singoli processi che eseguono le pagine PHP.


<a id="Code"></a>
## Esecuzione del codice, processi e memoria
Come indicato in precedenza, le app Web vengono eseguite all'interno di processi di lavoro con privilegi limitati che usano un'identità del pool di applicazioni casuale. Il codice dell'applicazione ha accesso allo spazio di memoria associato al processo di lavoro, nonché a eventuali processi figlio che possono essere generati da processi CGI o da altre applicazioni. Tuttavia, l'app Web di un cliente non può accedere alla memoria o ai dati dell'app Web di un altro cliente anche se si trova nella stessa macchina virtuale.

Le applicazioni possono eseguire script o pagine scritte con framework di sviluppo di applicazioni Web supportati. Le app Web non configurano le impostazioni di framework di applicazioni Web su modalità con maggiori restrizioni. Ad esempio, le app ASP.NET in esecuzione nelle app Web vengono eseguite in una modalità di attendibilità "completa" e non in una modalità di attendibilità con maggiori restrizioni. I framework di applicazioni, tra cui ASP classico e ASP.NET, possono chiamare componenti COM in-process (ma non componenti COM out-of-process) come gli oggetti ADO (ActiveX Data Objects) che sono registrati per impostazione predefinita nel sistema operativo Windows.

Le applicazioni Web possono generare ed eseguire codice arbitrario. Un'applicazione Web può effettuare attività come la generazione di una shell dei comandi o l'esecuzione di uno script PowerShell. Tuttavia, mentre il codice arbitrario e i processi possano essere generati da un'applicazione Web, i programmi e gli script eseguibili sono ancora riservati al pool di applicazioni padre. Ad esempio, un'app Web può generare un eseguibile che effettua una chiamata HTTP in uscita, ma lo stesso eseguibile non può tentare di dissociare l'indirizzo IP di una macchina virtuale dalla relativa NIC. L'esecuzione di una chiamata di rete in uscita è consentita al codice con privilegi limitati, ma il tentativo di riconfigurare le impostazioni di rete su una macchina virtuale richiede privilegi amministrativi.


<a id="Diagnostics"></a>
## Log ed eventi di diagnostica
Le informazioni dei log costituiscono un altro set di dati al quale alcune applicazioni Web tentano di accedere. Le informazioni dei log disponibili per il codice in esecuzione nelle app Web includono informazioni di diagnostica e dei log generate da un'app Web che sono anche facilmente accessibili a un'app Web.

Ad esempio, i log HTTP W3C generati da un'app Web attiva sono disponibili in una directory log nel percorso di condivisione di rete creato per l'app Web oppure in una risorsa di archiviazione BLOB nel caso in cui un cliente abbia impostato la registrazione W3C nella risorsa di archiviazione. Quest'ultima opzione consente di raccogliere elevate quantità di log senza il rischio di superare i limiti di archiviazione dei file associati a una condivisione di rete.

Analogamente, le informazioni di diagnostica in tempo reale delle applicazioni .NET possono essere registrate usando l'infrastruttura delle funzionalità di traccia e diagnostica di .NET, con la possibilità di scrivere le informazioni di traccia nella condivisione di rete dell'app Web o, in alternativa, in un percorso di archiviazione BLOB.

Le aree delle funzionalità di registrazione diagnostica e traccia che non sono disponibili per le applicazioni Web in Azure sono eventi Windows ETW e log eventi Windows comuni (ad es. log eventi di sistema, di applicazioni e di sicurezza). Poiché le informazioni di traccia ETW sono potenzialmente visualizzabili a livello di intera macchina (con gli ACL appropriati), l'accesso in lettura e scrittura agli eventi ETW sono bloccati. Gli sviluppatori potrebbero notare chiamate API per la lettura e la scrittura di eventi ETW e di log eventi Windows comuni apparentemente funzionanti. Tuttavia, ciò accade perché le app Web "simulano" le chiamate in modo che risultino eseguite correttamente. In realtà, il codice dell'app Web non dispone dell'accesso a questi dati di eventi.

<a id="RegistryAccess"></a>
## Accesso al registro
Le applicazioni dispongono di accesso in sola lettura alla maggior parte del registro della macchina virtuale sulla quale sono in esecuzione. In pratica, questo significa che le chiavi di registro che consentono l'accesso in sola lettura al gruppo di utenti locali sono accessibili tramite applicazioni Web. Un'area del registro che non è attualmente supportata per l'accesso in lettura o scrittura è l'hive HKEY\_CURRENT\_USER.

L'accesso in scrittura al registro è bloccato, incluso l'accesso a qualsiasi chiave di registro per utente. Dal punto di vista delle applicazioni, l'accesso in scrittura al registro in un ambiente cloud non deve mai essere considerato affidabile poiché le applicazioni sono soggette a migrazione tra macchine virtuali diverse. L'unica risorsa di archiviazione scrivibile permanente che può essere considerata affidabile da un'applicazione Web è la struttura della directory dei contenuti per app Web archiviata nelle condivisioni UNC delle app Web.

>[AZURE.NOTE]Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 
 

<!---HONumber=Oct15_HO3-->