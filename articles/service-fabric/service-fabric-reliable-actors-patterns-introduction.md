<properties
   pageTitle="Modelli e anti-modelli di Reliable Actors | Microsoft Azure"
   description="Fornisce una panoramica del modello di programmazione degli attori, dei modelli di progettazione compatibili con Service Fabric Reliable Actors e di alcuni anti-modelli da evitare."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2015"
   ms.author="vturecek"/>

# Introduzione ai modelli di progettazione Reliable Actors

Il modello di programmazione Reliable Actors di Azure Service Fabric è una piattaforma basata sul modello degli attori per risolvere problemi reali a livello di cloud. Service Fabric costituisce invece una piattaforma per la creazione di applicazioni altamente affidabili e scalabili, di facile sviluppo e gestione sia in locale che nel cloud.

Questo articolo è stato realizzato allo scopo di affrontare alcuni problemi pratici. Dopo aver acquisito informazioni sui vari modelli, sarà possibile capire come poter usare il modello Reliable Actors per creare soluzioni "enterprise" e "cloud".

## Modelli

Questa sezione elenca una serie di modelli, con i relativi scenari, adottati per soddisfare le esigenze di clienti reali. Questi modelli rappresentano classi di problemi applicabili a un'ampia gamma di soluzioni messe a punto dai clienti grazie a Microsoft Azure. Da questi scenari basati su casi reali sono stati estrapolati molti dei problemi specifici dei domini per rendere i modelli di più facile comprensione. È probabile che la maggior parte del codice di esempio appaia semplice o ovvio. Si è scelto di includerlo a scopo di completezza espositiva e non perché sia particolarmente complesso o ingegnoso.

I modelli presentati in questo documento non intendono essere completi o canonici: è possibile che alcuni sviluppatori siano in grado di risolvere gli stessi problemi in modo diverso rispetto a quello presentato.

[Modello: Smart Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Modello: Reti distribuite e grafici](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Modello: Governance delle risorse](service-fabric-reliable-actors-pattern-resource-governance.md)

[Modello: Composizione dei servizi con stato](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Modello: Internet delle cose](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Modello: Calcolo distribuito](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alcuni anti-modelli](service-fabric-reliable-actors-anti-patterns.md)

### Breve storia degli attori

Sebbene l'[articolo](http://dl.acm.org/citation.cfm?id=1624804) redatto da Carl Hewitt e i suoi coautori, da cui prende origine il modello degli attori, sia stato pubblicato nel 1973, solo in anni più recenti si è iniziato a considerare il modello degli attori come strumento per risolvere i problemi di simultaneità e complessità nei sistemi distribuiti.

Il modello degli attori supporta singoli oggetti con granularità fine (gli attori), isolati l'uno dall'altro e in grado di comunicare tramite l'interfaccia MPI (Message Passing Interface) asincrona, che consente comunicazioni dirette tra gli attori. Gli attori, inoltre, eseguono mediante una semantica a thread singolo. Combinato con l'incapsulamento dello stato dell'attore e l'isolamento dagli altri attori, questo aspetto semplifica la scrittura di sistemi altamente paralleli evitando problemi di simultaneità nel codice dell'attore. Gli attori vengono creati dinamicamente nel pool delle risorse hardware disponibili.

[Erlang](http://www.erlang.org/) è l'implementazione più diffusa del modello Actor. La riscoperta da parte degli sviluppatori del modello degli attori ha stimolato un rinnovato interesse in Erlang e incentivato la creazione di nuove soluzioni simili a Erlang, come gli attori [Scala](http://www.scala-lang.org/), [Akka](http://akka.io), [Akka.net](http://getakka.net/) e [DCell](http://research.microsoft.com/pubs/75988/dcell.pdf).

## Vantaggi di Service Fabric Reliable Actors

Il modello di programmazione Reliable Actors è un'implementazione del modello degli attori che combina alcuni aspetti di Erlang con sistemi a oggetti distribuiti, aggiunge ad essi un livello di riferimento indiretto all'attore e li espone in un modello di programmazione integrato che sfrutta la piattaforma Service Fabric

I principali vantaggi offerti da Reliable Actors sono la **produttività degli sviluppatori**, anche per i programmatori meno esperti, e la **scalabilità trasparente per impostazione predefinita**, che non richiede speciali interventi da parte dei programmatori. Basato su una libreria .NET in esecuzione su Service Fabric, il modello di programmazione Reliable Actors fornisce strumenti che semplificano notevolmente lo sviluppo di applicazioni distribuite complesse e rendono facilmente scalabile la progettazione delle applicazioni risultanti. Di seguito viene illustrato nel dettaglio ciascuno di questi vantaggi. Il modello di programmazione contribuisce ad aumentare la produttività dei programmatori, esperti e non, fornendo le astrazioni chiave, le garanzie e i servizi di sistema seguenti.

* *Familiarità del paradigma di programmazione orientata agli oggetti*. Gli attori sono classi .NET che implementano interfacce di attore .NET dichiarate con proprietà e metodi asincroni. Gli attori appaiono quindi ai programmatori come oggetti remoti con proprietà e metodi che possono essere richiamati direttamente. Questo consente loro di usare il paradigma di programmazione orientata agli oggetti, con cui hanno familiarità, trasformando in messaggi le chiamate ai metodi, indirizzandoli all'endpoint corretto, richiamando i metodi dell'attore di destinazione e gestendo errori e casi limite in modo completamente trasparente.

* *Esecuzione di attori a thread singolo*. Il runtime di Reliable Actors garantisce che un attore non esegua mai operazioni su più thread contemporaneamente. Questo vantaggio, combinato con l'isolamento degli attori, consente ai programmatori di non dover mai affrontare problemi di simultaneità a livello di attore e, quindi, di non dover mai usare blocchi o altri meccanismi di sincronizzazione per controllare l'accesso ai dati condivisi. Questa funzione, inoltre, consente anche a programmatori non esperti di sviluppare applicazioni distribuite.

* *Attivazione trasparente*. Il runtime attiva un attore in base alle necessità, ovvero solo quando è presente un messaggio da elaborare. Questo consente di separare chiaramente il concetto di creazione logica di un attore, visibile e controllata dal codice dell'applicazione, dall'attivazione fisica dell'attore in memoria, trasparente per l'applicazione. L'approccio Reliable Actors è simile alla memoria virtuale in quanto stabilisce quando attivare o disattivare un attore; l'applicazione ha inoltre accesso ininterrotto all'intero "spazio di memoria" degli attori creati logicamente, che si trovino o meno nella memoria fisica in un momento specifico. L'attivazione trasparente consente infine il bilanciamento adattivo e dinamico del carico tramite il posizionamento e la migrazione degli attori all'interno del pool di risorse hardware.

* *Trasparenza della posizione.* Un riferimento attore (oggetto proxy) che un programmatore usa per richiamare i metodi di un attore o passarli ad altri componenti, contenente solo l'identità logica dell'attore. La conversione dell'identità logica dell'attore in una posizione fisica e il corrispondente routing dei messaggi vengono eseguiti in modo trasparente dal runtime di Reliable Actors. Il codice dell'applicazione comunica con gli attori indipendentemente dalla loro posizione fisica, che può cambiare nel tempo a causa di errori o di interventi nella gestione delle risorse o perché un attore risulta disattivato nel momento in cui viene chiamato.

* *Integrazione trasparente con archivio permanente*. Il modello di programmazione Reliable Actors consente il mapping dichiarativo dello stato in memoria degli attori all'archivio permanente. Sincronizza gli aggiornamenti, garantendo in modo trasparente che i chiamanti ricevano i risultati solo dopo il completo aggiornamento dello stato persistente.

* *Disponibilità elevata, supporto per il failover e gestione del ciclo di vita delle applicazioni*. Lo stato di un attore viene gestito dalla piattaforma e replicato in modo che possa essere ripristinato se, ad esempio, si verifica un errore in un nodo del cluster. Service Fabric gestisce inoltre il ciclo di vita delle applicazioni e consente di aggiornarle senza tempi di inattività. Il modello di programmazione Reliable Actors è pensato per consentire ai programmatori di riuscire a scalare correttamente le proprie applicazioni e i propri servizi attraverso vari ordini di grandezza. Questo obiettivo può essere raggiunto solo incorporando procedure consigliate e modelli collaudati e conseguendo un'implementazione efficiente della funzionalità dei sistemi di livello inferiore. Questi sono alcuni dei fattori chiave che consentono la scalabilità e le prestazioni delle applicazioni di Service Fabric.

  * *Partizionamento con granularità fine dello stato dell'applicazione implicito*. Usando gli attori come entità indirizzabili in modo diretto, i programmatori suddividono implicitamente lo stato complessivo delle proprie applicazioni. Anche se il modello di programmazione Reliable Actors non prescrive quanto deve essere grande un attore, nella maggior parte dei casi è consigliabile avere un numero relativamente grande di attori (milioni o persino di più), ciascuno costituente un'entità naturale dell'applicazione, ad esempio un account utente o un ordine di acquisto. La possibilità di indirizzare direttamente gli attori e di astrarne la posizione fisica attraverso il runtime consente di disporre di una flessibilità straordinaria nel bilanciamento del carico e nella capacità di gestire gli hot spot in modo trasparente e generico, senza alcun intervento da parte dello sviluppatore dell'applicazione.

  * *Gestione adattiva delle risorse.* Gli attori non formulano alcuna ipotesi sulla località degli attori con cui interagiscono. A causa della trasparenza del percorso, tuttavia, il runtime può gestire e modificare dinamicamente l'allocazione delle risorse hardware disponibili adottando decisioni specifiche sul posizionamento e la migrazione degli attori nel cluster di elaborazione, in funzione dei modelli di caricamento e comunicazione e senza generare errori nelle richieste in ingresso. Creando più repliche di un determinato attore, il runtime può aumentare la velocità effettiva dell'attore, senza apportare modifiche al codice dell'applicazione.

  * *Comunicazione multiplex*. Nell'infrastruttura di servizi gli attori presentano endpoint logici e i messaggi scambiati tra di essi vengono sottoposti a multiplex tramite un set fisso di connessioni fisiche "all-to-all" (socket TCP). In questo modo, il runtime può ospitare un numero molto grande (milioni) di entità indirizzabili, senza alcun sovraccarico sul sistema operativo per ogni attore. L'attivazione o disattivazione di un attore, inoltre, non influisce sul costo di registrazione o annullamento della registrazione di un endpoint fisico, ad esempio una porta TCP o un URL HTTP.

  * *Programmazione efficiente*. Il runtime di Reliable Actors pianifica l'esecuzione di un elevato numero di attori a thread singolo all'interno di un pool di thread personalizzato usando un thread per ogni core di processore fisico. Con il codice dell'attore scritto in uno stile non di blocco basato sulla continuazione (un requisito del modello di programmazione), il codice dell'applicazione viene eseguito in una modalità multi-thread "cooperativa" ad alta efficienza, senza contese. Questa caratteristica consente al sistema di raggiungere un'elevata velocità effettiva e di essere eseguito con un impiego molto elevato della CPU (fino a oltre il 90%), che garantisce grande stabilità. Un aumento del numero di attori nel sistema e nel carico non comporta thread aggiuntivi o altre primitive del sistema operativo, favorendo la scalabilità dei singoli nodi e dell'intero sistema.

  * *Modalità asincrona esplicita*. Il modello di programmazione Reliable Actors rende esplicita la natura asincrona di un'applicazione distribuita e guida i programmatori nella scrittura di codice asincrono non di blocco. Consente inoltre un alto grado di parallelismo distribuito e un'elevata velocità effettiva globale, senza l'uso esplicito del multi-threading.

<!---HONumber=AcomDC_0128_2016-->