#Bus di servizio di Azure

Indipendentemente dal fatto che sia eseguito nel cloud o in locale, spesso è necessario che il software interagisca con altro software. Per semplificare questa operazione, in Azure è disponibile il bus di servizio. In questo articolo verrà illustrata questa tecnologia, verrà descritto di che cosa si tratta e perché potrebbe essere necessario usarla.

##Sommario      
- [Dati fondamentali del bus di servizio](#fundamentals)
- [Code](#queues)
- [Argomenti](#topics)
- [Inoltri](#relays)


## <a name="fundamentals"></a>Dati fondamentali del bus di servizio
A seconda delle situazioni, possono essere necessari stili di comunicazione diversi. Talvolta, consentire alle applicazioni di inviare e ricevere messaggi attraverso una semplice coda è la soluzione migliore. In altre situazioni, una coda ordinaria non è sufficiente e l'uso di una coda con un meccanismo di pubblicazione e sottoscrizione risulta la soluzione più adatta. In altri casi, invece, è sufficiente una connessione tra applicazioni e le code non sono necessarie. Il bus di servizio offre tutte e tre le opzioni e consente alle applicazioni in uso di interagire in diversi modi.

Il bus di servizio è un servizio cloud multi-tenant, il che significa che il servizio è condiviso da più utenti. Ogni utente, ad esempio uno sviluppatore di applicazioni, crea uno *namespace* e quindi definisce i meccanismi di comunicazione necessari all'interno di tale spazio dei nomi, [Nella Figura 1](#Fig1) viene illustrato questo scenario.

<a name="Fig1"></a>![Diagram of Azure Service Bus][svc-bus]
 
**Figura 1: il bus di servizio offre un servizio multi-tenant per la connessione di applicazioni attraverso il cloud.**

All'interno di uno spazio dei nomi è possibile usare una o più istanze di tre meccanismi di comunicazione diversi, ognuno dei quali consente di connettere le applicazioni in modo diverso. Le opzioni disponibili sono:

- *Queues*, che consentono la comunicazione unidirezionale. Ogni coda funge da intermediario (talvolta è infatti denominata *broker*) che archivia i messaggi inviati fino a quando non vengono ricevuti.
- *Topics*, che forniscono funzionalità di comunicazione unidirezionale tramite le *subscriptions*. Come la coda, un argomento funge da broker ma consente a ogni sottoscrizione di visualizzare solo messaggi corrispondenti a criteri specifici.
- *Relays*, che forniscono funzionalità di comunicazione bidirezionale. Diversamente da code e argomenti, un inoltro non archivia i messaggi in elaborazione, in quanto non si tratta di un broker. Al contrario, li passa semplicemente all'applicazione di destinazione.

Quando si crea una coda, un argomento o un inoltro, occorre assegnargli un nome. Questo nome, associato a quello assegnato allo spazio dei nomi, costituisce un identificatore univoco per l'oggetto. Le applicazioni possono fornire questo nome al bus di servizio e quindi usare tale coda, argomento o inoltro per comunicare tra loro. 

Le applicazioni Windows possono usare questi oggetti tramite Windows Communication Foundation (WCF). Per le code e gli argomenti, le applicazioni Windows possono inoltre usare un'API del sistema di messaggistica definita dal bus di servizio. È inoltre possibile accedere a code e argomenti tramite HTTP e, per facilitarne l'uso da parte di applicazioni non Windows, Microsoft fornisce appositi SDK per Java, Node.js e altri linguaggi.

È importante comprendere che, sebbene il bus di servizio stesso sia in esecuzione nel cloud, ovvero nei data center di Microsoft Azure, le applicazioni che lo usano possono essere eseguite ovunque. È possibile usare il bus di servizio per connettere applicazioni in esecuzione in Azure o all'interno del proprio data center. È inoltre possibile usarlo per connettere un'applicazione in esecuzione in Azure o in un'altra piattaforma cloud a un'applicazione locale o a tablet e telefoni. È altresì possibile connettere elettrodomestici, sensori e altri dispositivi a un'applicazione centrale o gli uni agli altri. Il bus di servizio è un meccanismo di comunicazione generico nel cloud accessibile praticamente ovunque. Il modo in cui lo si usa dipende dalle operazioni che le applicazioni devono eseguire.


## <a name="queues"></a>Code

Si supponga di dover connettere due applicazioni usando una coda del bus di servizio. [Nella Figura 2](#Fig2) è illustrato questo scenario.

<a name="Fig2"></a>![Diagram of Service Bus Queues][queues]
 
**Figura 2: le code del bus di servizio forniscono un servizio di accodamento asincrono unidirezionale.**

Il processo è semplice: un mittente invia un messaggio a una coda del bus di servizio e un ricevitore lo preleva un momento successivo. Una coda può avere un singolo ricevitore, come illustrato nella [Figura 2](#Fig2) oppure più applicazioni possono leggere dalla stessa coda. In quest'ultimo caso, ogni messaggio viene in genere letto da un solo ricevitore, in quanto le code non forniscono un servizio multicast.

Ogni messaggio è costituito da due parti: un set di proprietà, ognuno costituito da una coppia chiave-valore, e un corpo del messaggio binario. Il modo in cui vengono usati dipende dall'operazione che l'applicazione sta tentando di eseguire. Ad esempio, un'applicazione che invia un messaggio relativo a una vendita recente, potrebbe includere le proprietà *Seller="Ava"* e *Amount=10000*. Il corpo del messaggio potrebbe contenere un'immagine digitalizzata del contratto di vendita firmato, oppure, se questo non è disponibile, rimanere vuoto.

Un ricevitore può leggere un messaggio da una coda del bus di servizio in due modi. La prima opzione, denominata ReceiveAndDelete, rimuove un messaggio dalla coda e lo elimina immediatamente. Se, tuttavia, il ricevitore si arresta in modo anomalo prima di aver terminato l'elaborazione del messaggio, il messaggio andrà perso. Poiché è stato rimosso dalla coda, nessun altro ricevitore potrà accedervi. 

La seconda opzione, PeekLock, consente di risolvere il problema. Come ReceiveAndDelete, anche la modalità di lettura PeekLock rimuove un messaggio dalla coda, ma non lo elimina. Il messaggio viene invece bloccato e quindi reso invisibile agli altri utenti e rimane in attesa di uno dei tre eventi seguenti:

- Se il ricevitore elabora correttamente il messaggio, chiama il metodo Complete e la coda elimina il messaggio. 
- Se il ricevitore stabilisce che non è possibile elaborare il messaggio, chiama il metodo Abandon. La coda rimuove quindi il blocco dal messaggio e lo rende disponibile per gli altri ricevitori.
- Se il ricevitore non chiama uno di questi metodi entro un periodo di tempo configurabile (per impostazione predefinita, 60 secondi) la coda presuppone che si sia verificato un errore nel ricevitore. In questo caso, si comporta come se il ricevitore avesse chiamato il metodo Abandon, rendendo così il messaggio disponibile per altri ricevitori.

Possibili risultati: Lo stesso messaggio potrebbe essere recapitato due volte, anche a due ricevitori diversi. Le applicazioni che usano le code del bus di servizio devono prevedere questa possibilità. Per semplificare il rilevamento dei duplicati, ogni messaggio presenta una proprietà MessageID che per impostazione predefinita rimane invariata indipendentemente dal numero di letture del messaggio da una coda. 

Le code risultano utili in un numero limitato di situazioni. Consentono alle applicazioni di comunicare anche se non sono in esecuzione contemporaneamente, pertanto sono ideali per l'uso con applicazioni batch e mobili. Una coda con più ricevitori garantisce inoltre il bilanciamento del carico automatico, in quanto i messaggi vengono distribuiti tra i vari ricevitori.


## <a name="topics"></a>Argomenti

Sebbene siano utili, non sempre le code rappresentano la soluzione più appropriata. Talvolta, sono più indicati gli argomenti del bus di servizio. [Nella Figura 3](#Fig3) viene illustrato questo concetto.

<a name="Fig3"></a>![Diagram of Service Bus Topics and Subscriptions][topics-subs]
 
**Figura 3: in base al filtro specificato dall'applicazione di sottoscrizione, è possibile che vengano ricevuti alcuni o tutti i messaggi inviati a un argomento del bus di servizio.**

Un argomento e una coda presentano caratteristiche simili. I mittenti inviano messaggi a un argomento nello stesso modo in cui li inviano a una coda e tali messaggi hanno lo stesso aspetto di quelli nelle code. La differenza principale sta nel fatto che gli argomenti consentono a ogni applicazione ricevente di creare la propria sottoscrizione tramite la creazione di un *filter*. Un sottoscrittore potrà quindi visualizzare solo i messaggi corrispondenti al filtro definito. Nella [Figura 3],(#Fig3) ad esempio, sono mostrati un mittente e un argomento con tre sottoscrittori, ognuno con il relativo filtro:

- Il sottoscrittore 1 riceve solo i messaggi che contengono la proprietà *Seller="Ava"*.
- Il sottoscrittore 2 riceve i messaggi che contengono la proprietà *Seller="Ruby"* e/o che contengono una proprietà *Amount* il cui valore è maggiore di 100.000. Ruby potrebbe essere una responsabile vendite che desidera visualizzare sia le proprie vendite che le vendite di importo elevato indipendentemente da chi le ha concluse.
- Il sottoscrittore 3 presenta un filtro impostato su *True* e pertanto riceve tutti i messaggi. Questa applicazione potrebbe ad esempio essere responsabile del mantenimento di un audit trail e pertanto è necessario che possa visualizzare tutto.

Come con le code, i sottoscrittori di un argomento possono leggere i messaggi usando la modalità di ricezione ReceiveAndDelete o PeekLock. Diversamente dalle code, tuttavia, un singolo messaggio inviato a un argomento può essere ricevuto da più sottoscrittori. Questo approccio, comunemente denominato di *publish and subscribe*, risulta utile qualora più applicazioni siano interessate agli stessi messaggi. Con la definizione del filtro corretto, ogni sottoscrittore può accedere solo alla parte del flusso dei messaggi che gli interessa.


## <a name="relays"></a>Inoltri

Le code e gli argomenti consentono la comunicazione asincrona unidirezionale tramite un broker. Il traffico scorre in una sola direzione e non esiste una connessione diretta tra mittenti e ricevitori. Talvolta questo potrebbe non essere sufficiente, ad esempio se è necessario che le applicazioni possano inviare e ricevere o se occorre un collegamento diretto tra esse e non serve che i messaggi vengano archiviati nel frattempo. Per risolvere questi tipi di problemi, nel bus di servizio sono disponibili gli inoltri, come illustrato nella [Figura 4](#Fig4) .

<a name="Fig4"></a>![Diagram of Service Bus Relay][relay]
 
**Figura 4: l'inoltro del bus di servizio garantisce la comunicazione sincrona bidirezionale tra applicazioni.**

Di seguito sono riportate alcune domande ovvie relative all'uso degli inoltri: perché è necessario usarli? Anche se le code non sono necessarie, perché le applicazioni dovrebbero comunicare tramite un servizio cloud anziché interagire direttamente? La risposta è che la comunicazione diretta tra le applicazioni potrebbe risultare più complessa di quanto si pensi.

Ad esempio, potrebbe essere necessario connettere due applicazioni locali, entrambe in esecuzione all'interno di data center aziendali. Ognuna di queste applicazioni è protetta da firewall ed è probabile che ogni data center usi il processo NAT (Network Address Translation). Il firewall blocca i dati in ingresso su tutte le porte tranne alcune e il processo NAT implica che è probabile che il computer in cui è in esecuzione ogni applicazione non disponga di un indirizzo IP fisso. Senza un ulteriore supporto, la connessione di queste applicazioni sulla rete Internet pubblica risulta problematica.

L'inoltro del bus di servizio garantisce questo supporto. Per comunicare in modalità bidirezionale tramite un inoltro, ogni applicazione stabilisce una connessione TCP in uscita con il bus di servizio e la mantiene aperta. Tutte le comunicazioni tra le due applicazioni avvengono su tali connessioni. Poiché ogni connessione è stata stabilita dall'interno del data center, il firewall consentirà il traffico in ingresso a ogni applicazione "dati inviati tramite l'inoltro" senza aprire nuove porte. Questo approccio consente inoltre di risolvere il problema relativo al processo NAT, in quanto ogni applicazione presenta un endpoint coerente nel corso della comunicazione. Lo scambio di dati tramite l'inoltro consente alle applicazioni di evitare i problemi che potrebbero rendere difficoltosa la comunicazione. 

Per usare l'inoltro del bus di servizio, le applicazioni usano Windows Communication Foundation (WCF). Il bus di servizio fornisce le associazioni WCF che semplificano l'interazione delle applicazioni Windows tramite inoltro. Le applicazioni che usano già WCF possono in genere specificare una di queste associazioni e quindi comunicare tra loro tramite un inoltro. Diversamente da code e argomenti, l'uso degli inoltri da applicazioni non Windows, sebbene possibile, richiede alcune operazioni di programmazione, in quanto non sono disponibili librerie standard.

Diversamente da code e argomenti, le applicazioni non creano inoltri in modo esplicito. Al contrario, quando un'applicazione che desidera ricevere messaggi stabilisce una connessione TCP con il bus di servizio, l'inoltro viene creato automaticamente. Quando la connessione viene chiusa, l'inoltro viene eliminato. Per consentire a un'applicazione di trovare l'inoltro creato da un listener specifico, il bus di servizio fornisce un registro che permette l'individuazione di un inoltro specifico in base al nome.

Gli inoltri rappresentano la soluzione ottimale nei casi in cui è necessaria la comunicazione diretta, come ad esempio un sistema di prenotazione di una compagnia aerea in esecuzione in un data center locale al quale devono poter accedere banchi del check-in, dispositivi mobili e altri computer. Le applicazioni in esecuzione in questi sistemi possono usare l'inoltro del bus di servizio nel cloud per comunicare, indipendentemente dalla posizione in cui risiedono.

La connessione di applicazioni fa parte dello sviluppo di soluzioni complete e i problemi ad essa associati sono di difficile risoluzione. Il bus di servizio include tecnologie basate sul cloud, tramite code, argomenti e inoltri, che consentono di semplificare questa funzionalità essenziale e disponibile a livello più ampio.

[svc-bus]: ./media/hybrid-solutions/SvcBus_01_architecture.png
[queues]: ./media/hybrid-solutions/SvcBus_02_queues.png
[topics-subs]: ./media/hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[relay]: ./media/hybrid-solutions/SvcBus_04_relay.png
<!--HONumber=42-->
