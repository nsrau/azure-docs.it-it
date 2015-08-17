<properties
   pageTitle="Service Fabric Reliable Actors - Introduzione ai modelli e agli anti-modelli"
   description="Informazioni sul modello di programmazione Reliable Actors di Service Fabric e modelli di progettazione che funzionano bene con Actors."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2015"
   ms.author="claudioc"/>

# Introduzione ai modelli di progettazione di Reliable Actors
Il modello Reliable Actors di Service Fabric è una piattaforma basata sul modello Actor per risolvere problemi reali a livello di cloud. Azure Service Fabric costituisce invece una piattaforma per la realizzazione di applicazioni altamente affidabili e scalabili, da implementare sia in locale sia nel cloud, di facile sviluppo e gestione. Questo articolo è da intendere come una guida pratica a problemi pratici. Dopo aver acquisito informazioni sui vari modelli, sarà possibile capire come poter usare il modello Actor di Service Fabric per creare soluzioni "enterprise" o "cloud".

## Modelli
Questa sezione elenca una serie di modelli, con i relativi scenari, usati per soddisfare le esigenze di clienti reali. Questi modelli rappresentano classi di problemi applicabili a un'ampia gamma di soluzioni messe a punto dai clienti grazie a Microsoft Azure. Da questi scenari basati su casi reali sono stati estrapolati molti dei problemi specifici dei domini per rendere i modelli di più facile comprensione per il lettore. È probabile che la maggior parte del codice di esempio appaia semplice o ovvio. Si è scelto di includerlo a scopo di completezza espositiva e non perché sia particolarmente complesso o ingegnoso.

I modelli presentati in questo documento non intendono essere completi o canonici: è possibile che alcuni sviluppatori siano in grado di risolvere lo stesso problema in modo diverso rispetto a quello presentato.

[Modello: Smart Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Modello: Reti distribuite e grafici](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Modello: Governance delle risorse](service-fabric-reliable-actors-pattern-resource-governance.md)

[Modello: Composizione dei servizi con stato](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Modello: Internet delle cose](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Modello: Calcolo distribuito](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alcuni anti-modelli](service-fabric-reliable-actors-anti-patterns.md)

### Breve cronologia del modello Actor
Sebbene l'[articolo](http://dl.acm.org/citation.cfm?id=1624804) redatto da Hewitt et al., da cui prende origine il modello Actor, sia stato pubblicato nel 1973, solo in anni più recenti si è iniziato a considerare il modello Actor come strumento per risolvere i problemi di simultaneità e complessità nei sistemi distribuiti. Il modello Actor supporta singoli oggetti con granularità fine (gli attori), isolati l'uno dall'altro e in grado di comunicare tramite l'interfaccia MPI (Message Passing Interface) asincrona, che consente comunicazioni dirette tra gli attori. Gli attori, inoltre, eseguono con una semantica a thread singolo. Combinato con l'incapsulamento dello stato dell'attore e l'isolamento dagli altri attori, questo aspetto semplifica la scrittura di sistemi altamente paralleli ed evita problemi di simultaneità nel codice dell'attore. Gli attori vengono creati dinamicamente nel pool delle risorse hardware disponibili.

[Erlang](http://www.erlang.org/) è l'implementazione più diffusa del modello Actor. La riscoperta da parte degli sviluppatori del modello Actor ha stimolato un rinnovato interesse in Erlang e incentivato la creazione di nuove soluzioni simili a Erlang: attori [Scala](http://www.scala-lang.org/), [Akka](http://akka.io), [Akka.net](http://getakka.net/) e [DCell](http://research.microsoft.com/pubs/75988/dcell.pdf).

## Breve panoramica di Azure Service Fabric
Azure Fabric Actors è un'implementazione del modello Actor che combina alcuni aspetti di Erlang con sistemi a oggetti distribuiti, aggiunge ad essi un livello di riferimento indiretto all'attore e li espone in un modello di programmazione integrato che sfrutta la piattaforma Azure Service Fabric.

I principali vantaggi offerti da Azure Fabric Actors sono: 1) **la produttività degli sviluppatori**, anche per i programmatori meno esperti e 2) **la scalabilità trasparente per impostazione predefinita**, senza speciali interventi da parte del programmatore. Azure Fabric Actors è una libreria .NET in esecuzione su Azure Fabric e su strumenti che semplificano notevolmente lo sviluppo di applicazioni distribuite complesse e rendono facilmente scalabile la progettazione delle applicazioni risultanti. Di seguito viene illustrato nel dettaglio ciascuno di questi vantaggi. Il modello di programmazione di Azure Fabric Actors contribuisce ad aumentare la produttività dei programmatori, esperti e non, fornendo le astrazioni chiave, le garanzie e i servizi di sistema seguenti.

* *Familiarità del paradigma di programmazione orientata agli oggetti*. Gli attori sono classi .NET che implementano interfacce di attore .NET dichiarate con proprietà e metodi asincroni. Gli attori appaiono quindi al programmatore come oggetti remoti con proprietà e metodi che possono essere richiamati direttamente. Questo consente ai programmatori di usare il paradigma di programmazione orientata agli oggetti, con cui hanno familiarità, trasformando in messaggi le chiamate ai metodi, indirizzandoli all'endpoint corretto, richiamando i metodi dell'attore di destinazione e gestendo errori e casi limite in modo completamente trasparente.

* *Esecuzione di attori a thread singolo.* Il runtime di Azure Fabric garantisce che un attore non esegua mai operazioni su più thread contemporaneamente. Questo vantaggio, combinato con l'isolamento degli attori, consente al programmatore di non dover mai affrontare problemi di simultaneità a livello di attore e, quindi, di non dover mai usare blocchi o altri meccanismi di sincronizzazione per controllare l'accesso ai dati condivisi. Questa funzione, inoltre, consente anche a programmatori non esperti di sviluppare applicazioni distribuite.

* *Attivazione trasparente.* Il runtime di Azure Fabric attiva un attore in base alle necessità, ovvero solo quando è presente un messaggio da elaborare. Questo consente di separare chiaramente il concetto di creazione logica di un attore, visibile e controllata dal codice dell'applicazione, dall'attivazione fisica dell'attore in memoria, trasparente per l'applicazione. Azure Fabric Actors è simile alla memoria virtuale in quanto stabilisce quando attivare o disattivare un attore; l'applicazione dispone inoltre di un accesso ininterrotto all'intero "spazio di memoria" degli attori creati logicamente, che si trovino o meno nella memoria fisica in un momento specifico. L'attivazione trasparente consente infine il bilanciamento adattivo e dinamico del carico tramite il posizionamento e la migrazione degli attori all'interno del pool di risorse hardware.

* *Trasparenza della posizione.* Un riferimento attore (oggetto proxy) che il programmatore usa per richiamare i metodi dell'attore o passare ad altri componenti solo l'identità logica dell'attore. La conversione dell'identità logica dell'attore in una posizione fisica e il corrispondente routing dei messaggi vengono eseguiti in modo trasparente dal runtime di Azure Fabric. Il codice dell'applicazione comunica con gli attori indipendentemente dalla loro posizione fisica, che può cambiare nel tempo a causa di errori o di interventi nella gestione delle risorse, o perché un attore risulta disattivato nel momento in cui viene chiamato.

* *Integrazione trasparente con archivio permanente.* Azure Fabric Actors consente il mapping dichiarativo dello stato in memoria degli attori all'archivio permanente. Sincronizza gli aggiornamenti, garantendo in modo trasparente che i chiamanti ricevano i risultati solo dopo il completo aggiornamento dello stato persistente.

* *Disponibilità elevata, supporto per il failover e gestione del ciclo di vita delle applicazioni.* Lo stato di Azure Fabric Actors è gestito dalla piattaforma e replicato in modo che possa essere ripristinato se, ad esempio, un nodo del cluster ha esito negativo. Azure Service Fabric gestisce inoltre il ciclo di vita delle applicazioni e consente di aggiornarle senza tempi di inattività. Il modello di programmazione di Azure Fabric Actors è pensato per consentire ai programmatori di arrivare a scalare correttamente la propria applicazione o il proprio servizio attraverso vari ordini di grandezza. Questo obiettivo può essere raggiunto solo incorporando procedure consigliate e modelli collaudati e conseguendo un'implementazione efficiente della funzionalità dei sistemi di livello inferiore. Questi sono alcuni dei fattori chiave che consentono la scalabilità e le prestazioni delle applicazioni Azure Fabric.

* *Partizionamento con granularità fine dello stato dell'applicazione implicito.* Usando gli attori come entità indirizzabili in modo diretto, i programmatori suddividono implicitamente lo stato complessivo delle proprie applicazioni. Anche se il modello di programmazione di Azure Fabric Actors non prescrive quanto grande deve essere un attore, nella maggior parte dei casi è consigliabile disporre di un numero relativamente grande di attori (milioni o persino di più), ciascuno costituendo un'entità fisica dell'applicazione, ad esempio un account utente, un ordine di acquisto e così via. La possibilità di indirizzare direttamente gli attori e di astrarne la posizione fisica attraverso il runtime consente ad Azure Fabric Actors di disporre di una flessibilità straordinaria nel bilanciamento del carico e nella capacità di gestire gli hot spot in modo trasparente e generico, senza alcun intervento da parte dello sviluppatore dell'applicazione.

* *Gestione adattiva delle risorse.* Poiché gli attori non formulano alcuna ipotesi sulla località degli attori con cui interagiscono e a causa della trasparenza del percorso, il runtime di Azure Fabric può gestire e modificare dinamicamente l'allocazione delle risorse hardware disponibili adottando decisioni specifiche sul posizionamento e la migrazione degli attori nel cluster di calcolo, in funzione dei modelli di caricamento e comunicazione e senza generare errori nelle richieste in ingresso. Creando più repliche di un determinato attore, il runtime può aumentare, se necessario, la velocità effettiva dell'attore, senza apportare modifiche al codice dell'applicazione.

* *Comunicazione multiplex.* In Azure Fabric gli attori presentano endpoint logici e i messaggi scambiati tra di essi vengono sottoposti a multiplex tramite un set fisso di connessioni fisiche "all-to-all" (socket TCP). In questo modo, il runtime di Azure Fabric Actors può ospitare un numero molto grande (milioni) di entità indirizzabili, senza alcun sovraccarico sul sistema operativo per ogni attore. L'attivazione/disattivazione di un attore, inoltre, non influisce sul costo di registrazione/annullamento della registrazione di un endpoint fisico, ad esempio una porta TCP o un URL HTTP.

* *Programmazione efficiente.* Il runtime di Azure Fabric pianifica l'esecuzione di un elevato numero di agenti a thread singolo all'interno di un pool di thread personalizzato, con un thread per ogni core di processore fisico. Con il codice dell'attore scritto in uno stile non di blocco basato sulla continuazione (un requisito del modello di programmazione Azure Fabric Actors), il codice dell'applicazione viene eseguito in una modalità multi-thread "cooperativa" ad alta efficienza, senza contese. Questa caratteristica consente al sistema di raggiungere un'elevata velocità effettiva e di essere eseguito con un impiego molto elevato della CPU (fino a oltre il 90%), che garantisce grande stabilità. Il fatto che un aumento del numero di attori nel sistema e nel carico non comporti thread aggiuntivi o altre primitive del sistema operativo favorisce la scalabilità dei singoli nodi e dell'intero sistema.

* *Modalità asincrona esplicita.* Il modello di programmazione di Azure Fabric Actors rende esplicita la natura asincrona di un'applicazione distribuita e guida i programmatori nella scrittura di codice asincrono non di blocco. Consente inoltre un alto grado di parallelismo distribuito e un'elevata velocità effettiva globale, senza l'uso esplicito del multi-threading.

<!---HONumber=August15_HO6-->