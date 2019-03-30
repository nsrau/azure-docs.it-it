---
title: Introduzione ai microservizi in Azure | Documentazione Microsoft
description: Panoramica dell'importanza di compilare applicazioni cloud con un approccio basato su microservizi per lo sviluppo di applicazioni moderne e descrizione dell'uso di Azure Service Fabric come piattaforma per raggiungere questo obiettivo.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: fae2be85-0ab4-4cd3-9d1f-e0d95fe1959b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/02/2017
ms.author: atsenthi
ms.openlocfilehash: 1e02e4fdf0dbe04d8756fc6355c6a9e414b27d2b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665597"
---
# <a name="why-a-microservices-approach-to-building-applications"></a>Perché usare un approccio ai microservizi per la compilazione di applicazioni

Per gli sviluppatori di software non c'è nulla di nuovo nel modo di considerare la fattorizzazione di un'applicazione nelle parti che la compongono. In genere viene adottato un approccio su più livelli con un archivio nel back-end, la logica di business al livello intermedio e l'interfaccia utente (UI) nel front-end. Ciò che *è* cambiato negli ultimi anni è il fatto che gli sviluppatori creano applicazioni distribuite per il cloud.

Ecco quali sono le mutevoli esigenze aziendali:

* Un servizio scalabile creato e usato per raggiungere i clienti in nuove aree geografiche.
* Recapito più rapido di funzionalità e caratteristiche per poter rispondere alle richieste dei clienti in modo agile.
* Utilizzo delle risorse migliorato per ridurre i costi.

Queste esigenze aziendali incidono sul *modo* di compilare le applicazioni.

Per altre informazioni sull'approccio di Azure ai microservizi, leggere [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservizi: una rivoluzione delle applicazioni basata sul cloud).

## <a name="monolithic-vs-microservice-design-approach"></a>Approccio alla progettazione monolitico o con microservizi

Le applicazioni si evolvono con il trascorrere del tempo. Le applicazioni che hanno maggior successo si evolvono diventando utili per le persone. Le applicazioni che invece non hanno successo non si evolvono e alla fine vengono deprecate. La domanda è: Quanto si conoscono i requisiti attuali e come si svilupperanno in futuro? Si supponga, ad esempio, di dover realizzare un'applicazione per la creazione di report per un reparto. Si ha la certezza che l'applicazione agisce esclusivamente all'interno dell'ambito aziendale e che i report hanno una durata breve. L'approccio scelto sarà diverso, ad esempio, rispetto alla creazione di un servizio per la distribuzione di contenuti video a decine di milioni di clienti.

In alcuni casi, il rilascio di un modello di verifica può essere il fattore determinante, sapendo che l'applicazione può essere riprogettata in un secondo momento. C'è poco senso nella sovraprogettazione di qualcosa che non verrà mai usato. È il solito compromesso della progettazione. D'altra parte, quando le aziende discutono della creazione per il cloud, l'aspettativa diventa crescita e utilizzo. Il problema è che la crescita e la scalabilità sono imprevedibili. Si vorrebbe poter creare prototipi velocemente, ma allo stesso tempo sapere di essere sulla strada giusta per gestire il successo futuro. Questo è l'approccio di avvio snello: compilare, misurare, apprendere e ripetere.

Nell'era del client/server si tendeva a concentrarsi sulla compilazione di applicazioni a più livelli, usando tecnologie specifiche in ogni livello. Per questi approcci è nato il termine applicazione *monolitica*. Le interfacce si trovavano di solito tra i diversi livelli e si usava una progettazione accoppiata più strettamente tra i componenti all'interno di ogni livello. Gli sviluppatori progettavano ed eseguivano il factoring delle classi compilate in librerie e collegate insieme in pochi file eseguibili e DLL. 

Questo approccio alla progettazione monolitica presenta dei vantaggi. Spesso è più semplice da progettare e prevede chiamate più rapide tra i componenti, perché queste chiamate spesso avvengono tramite comunicazione interprocesso (IPC). Inoltre, ognuno testa un singolo prodotto e questo approccio è tendenzialmente più efficiente a livello di risorse e persone. Lo svantaggio è che si ottiene un accoppiamento stretto fra i livelli e non è possibile ridimensionare i singoli componenti. Se occorre eseguire correzioni o aggiornamenti, si deve attendere che altri completino i test. Diventa quindi più difficile essere agili.

I microservizi risolvono questi aspetti negativi e sono più allineati ai requisiti aziendali descritti sopra, ma anch'essi hanno vantaggi e punti deboli. I vantaggi dei microservizi consistono nel fatto che ognuno incapsula solitamente funzionalità aziendali più semplici, che è possibile incrementare o ridurre, testare, distribuire e gestire in modo indipendente. Un importante vantaggio dell'approccio basato su microservizi è il fatto che i team sono influenzati più dagli scenari aziendali che dalla tecnologia, cosa che invece l'approccio a più livelli incoraggia. In pratica, team più piccoli sviluppano un microservizio sulla base di uno scenario del cliente, usando le tecnologie che preferiscono.

In altre parole, l'organizzazione non deve necessariamente standardizzarsi sulla tecnologia per mantenere applicazioni di microservizi. I singoli team proprietari dei servizi possono procedere come meglio credono, a seconda dell'esperienza del team stesso o della soluzione più appropriata al problema da risolvere. In pratica, è preferibile avere un set di tecnologie consigliate, ad esempio un particolare archivio NoSQL o un framework di applicazioni Web.

Lo svantaggio dei microservizi sta nel dover gestire un maggior numero di entità separate nonché distribuzioni e controllo di versione più complessi. Il traffico di rete tra i microservizi aumenta insieme alle latenze di rete corrispondenti. La presenza di una grande quantità di servizi frammentati e granulari non può che dare origine a problemi di prestazioni. Senza strumenti che facilitino la visualizzazione di queste dipendenze è difficile avere una "visione" dell'intero sistema.

Sono gli standard che consentono il funzionamento dell'approccio basato su microservizi, definendo la modalità di comunicazione e accettando solo ciò che è necessario ottenere da un servizio, invece di rigidi contratti. È importante definire questi aspetti fin dall'inizio della progettazione, perché i servizi vengono aggiornati in modo indipendente gli uni dagli altri. Un'altra descrizione coniata per la progettazione con approccio basato su microservizi è "SOA (Service Oriented Architecture) con granularità fine".

***Al livello più semplice, l'approccio di progettazione con microservizi è una federazione di servizi disaccoppiata, con modifiche indipendenti di ciascuno e standard concordati per la comunicazione.***

Man mano che vengono prodotte più app per cloud, si scopre che questa scomposizione dell'app complessiva in servizi indipendenti per scenari specifici è un approccio migliore a lungo termine.

## <a name="comparison-between-application-development-approaches"></a>Confronto tra approcci allo sviluppo di applicazioni

![Sviluppo di applicazioni per la piattaforma Service Fabric][Image1]

1) Un'app monolitica include funzionalità specifiche del dominio e normalmente è divisa per livelli di funzionalità, ad esempio Web, business e dati.

2) Per la scalabilità di un'app monolitica, occorre clonarla in più server/macchine virtuali/contenitori.

3) Un'applicazione di microservizi separa le funzionalità in servizi più piccoli distinti.

4) La scalabilità orizzontale di questo approccio basato sui microservizi si ottiene con la distribuzione di ogni servizio in modo indipendente e la creazione di istanze di questi servizi in server/macchine virtuali/contenitori.

La progettazione di un approccio ai microservizi non è un toccasana per tutti i progetti, ma offre un maggiore allineamento con gli obiettivi di business descritti in precedenza. Un approccio iniziale monolitico può essere accettabile se si sa che in caso di necessità sarà possibile rielaborare il codice in una struttura a microservizi. In genere si inizia con un'applicazione monolitica e quindi la si suddivide lentamente in fasi, a partire dalle aree funzionali che devono essere maggiormente scalabili o agili.

L'approccio basato su microservizi comporta la composizione di un'applicazione con molti servizi più piccoli. Questi servizi sono eseguiti in contenitori distribuiti in un cluster di computer. Team più piccoli sviluppano un servizio focalizzato su uno scenario ed eseguono test, controllo delle versioni, distribuzione e ridimensionamento di ogni servizio in modo indipendente, contribuendo all'evoluzione complessiva dell'applicazione.

## <a name="what-is-a-microservice"></a>Definizione di microservizio

Esistono diverse definizioni di microservizi. Molte delle caratteristiche dei microservizi seguenti sono tuttavia ampiamente concordate:

* Incapsulano uno scenario aziendale o del cliente. Qual è il problema da risolvere?
* Sono sviluppati da un piccolo team di progettazione.
* Sono scritti in qualsiasi linguaggio di programmazione e usano qualsiasi framework.
* Sono costituiti da codice e facoltativamente da uno stato, entrambi sottoposti al controllo delle versioni, distribuiti e ridimensionati in maniera indipendente.
* Interagiscono con altri microservizi tramite interfacce e protocolli ben definiti.
* Hanno nomi univoci (URL) usati per risolvere la propria posizione.
* Rimangono coerenti e disponibili in caso di errori.

In sintesi:

***Le applicazioni di microservizi sono costituite da piccoli servizi rivolti ai clienti, scalabili e sottoposti al controllo delle versioni indipendentemente, che comunicano tra di essi tramite protocolli standard e interfacce ben definite.***

### <a name="written-in-any-programming-language-and-use-any-framework"></a>Sono scritti in qualsiasi linguaggio di programmazione e usano qualsiasi framework

Gli sviluppatori vogliono essere liberi di scegliere il linguaggio o il framework che preferiscono, a seconda delle proprie competenze o delle esigenze del servizio. Per alcuni servizi i vantaggi di prestazioni di C++ potrebbero superare qualsiasi altro aspetto. In altri servizi la facilità dello sviluppo gestito in C# o Java potrebbe essere più importante. In alcuni casi potrebbe essere necessaria una libreria di terze parti, una tecnologia di archiviazione dati o mezzi per l'esposizione del servizio a client specifici.

Dopo aver scelto una tecnologia, occorre considerare la gestione operativa o del ciclo di vita e il ridimensionamento del servizio.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Consentono di sottoporre al controllo delle versioni, distribuire e ridimensionare indipendentemente codice e stato

In qualsiasi modo si decida di scrivere i propri microservizi, il codice e, facoltativamente, lo stato devono essere distribuiti, aggiornati e ridimensionati in modo indipendente. Questo è uno dei problemi più difficili da risolvere, poiché coinvolge la scelta delle tecnologie. Per il ridimensionamento, sapere come partizionare sia il codice che lo stato è complesso. Quando il codice e lo stato usano tecnologie distinte, una tendenza comune attualmente, gli script di distribuzione del microservizio devono poter ridimensionare entrambe. Questo aspetto ha a che fare anche con l'agilità e la flessibilità con cui si possono aggiornare alcuni microservizi senza doverli aggiornare tutti contemporaneamente.

Tornando per un momento all'approccio monolitico contrapposto ai microservizi, il diagramma seguente mostra le differenze di approccio all'archiviazione dello stato.

#### <a name="state-storage-between-application-styles"></a>Archiviazione dello stato tra stili dell'applicazione

![Archiviazione dello stato della piattaforma Service Fabric][Image2]

***A sinistra l'approccio monolitico con un database singolo e livelli di tecnologie specifiche.***

***A destra è illustrato l'approccio basato su microservizi, con un grafico dei microservizi interconnessi in cui lo stato presenta in genere un ambito limitato al microservizio e vengono usate molteplici tecnologie.***

In un approccio monolitico, l'applicazione usa in genere un database singolo. Il vantaggio è dato dal fatto che si tratta di una posizione singola e ciò facilita la distribuzione. Ogni componente può avere una tabella singola per l'archiviazione del relativo stato. La parte più difficile riguarda la necessità che il team separi scrupolosamente lo stato. È inevitabile la tentazione di aggiungere una nuova colonna a una tabella esistente del cliente, creare un join tra le tabelle e creare dipendenze a livello di archiviazione. In questo caso, non sarà possibile ridimensionare i singoli componenti.

Con l'approccio dei microservizi, ogni servizio gestisce e archivia il proprio stato. Ogni servizio è responsabile di ridimensionare sia il codice che lo stato insieme, in modo da soddisfare le richieste del servizio. Un aspetto negativo è il fatto che quando occorre creare viste o query dei dati dell'applicazione, le query vanno eseguite su archivi che si trovano in più stati. In genere, questo problema si risolve con un microservizio separato che crea una visualizzazione della raccolta di microservizi. Se è necessario eseguire più query specifiche sui dati, per ogni microservizio si dovrà considerare la scrittura dei dati in un servizio di data warehousing per le analisi offline.

I microservizi sono elementi con versioni ed è possibile che più versioni di un microservizio possano essere eseguite side-by-side. Una versione più recente di un microservizio può non riuscire durante l'aggiornamento, in questo caso è necessario eseguire il rollback a una versione precedente. Il controllo delle versioni è utile anche per l'esecuzione dei test di tipo A/B in cui utenti diversi provano versioni diverse del servizio. Ad esempio, è normale aggiornare un microservizio per consentire a un set specifico di clienti di testare nuove funzionalità prima di implementarlo più diffusamente.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interagiscono con altri microservizi tramite interfacce ben definite e protocolli.

Esiste molta documentazione sull'architettura orientata ai servizi pubblicata negli ultimi 10 anni che descrive i modelli di comunicazione. Di solito, la comunicazione tra servizi usa un approccio REST con i protocolli HTTP e TCP e XML o JSON come formato di serializzazione. Da un punto di vista dell'interfaccia, si tratta di adottare l'approccio alla progettazione Web. Nulla vieta tuttavia di usare protocolli binari o formati di dati personalizzati. Tenere presente che le persone avranno maggiori difficoltà a usare i microservizi se questi protocolli e formati non sono disponibili pubblicamente.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Hanno un nome (URL) univoco usato per risolvere il percorso

Il microservizio deve essere indirizzabile ovunque sia in esecuzione. Se si inizia a chiedersi quale computer esegue un determinato microservizio, presto inizieranno le difficoltà. 

Così come il DNS risolve un URL particolare in un computer specifico, il microservizio deve avere un nome univoco per consentire l'individuazione della sua posizione attuale. I microservizi devono avere nomi indirizzabili che siano indipendenti dall'infrastruttura in cui sono in esecuzione. Questo implica un'interazione tra la modalità di distribuzione e la modalità di individuazione del servizio, perché deve essere disponibile un servizio del servizio. In caso di errore in un computer, il registro del servizio deve indicare dove è stato spostato il servizio.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Rimangono coerenti e disponibili in caso di errori.

Affrontare gli errori imprevisti è uno dei problemi più difficili da risolvere, specialmente in un sistema distribuito. La maggior parte del codice scritto dagli sviluppatori riguarda la gestione delle eccezioni ed è anche l'ambito in cui si impiega più tempo per i test. Il problema è più complesso della scrittura di codice per gestire gli errori. Cosa accade infatti in caso di errore del computer in cui è in esecuzione il microservizio? Non è solo necessario rilevare l'errore, un problema di per sé difficile, ma anche riavviare il microservizio.

Per motivi di disponibilità, un microservizio deve essere resiliente in caso di errori e deve potersi riavviare in un altro computer. Oltre al fatto che il codice in esecuzione deve essere resiliente, non dovrebbe esserci perdita di dati e, questi ultimi, devono mantenere coerenza.

La resilienza è difficile da raggiungere quando si verificano errori durante l'aggiornamento di un'applicazione. Il microservizio, che interagisce con il sistema di distribuzione, non deve essere ripristinato. Deve decidere se può continuare e passare alla versione più recente oppure eseguire il rollback a una versione precedente, per mantenere uno stato coerente. Occorre considerare alcune domande, ad esempio se sono disponibili computer sufficienti per continuare e come recuperare le versioni precedenti del microservizio. Per poter prendere queste decisioni, il microservizio deve emettere informazioni sull'integrità.

### <a name="reports-health-and-diagnostics"></a>Segnalano integrità e diagnostica

Anche se è un concetto apparentemente ovvio e spesso trascurato, un microservizio deve segnalare il proprio stato di integrità e fornire dati di diagnostica. In caso contrario, saranno disponibili pochi dettagli relativi all'integrità da un punto di vista operativo. Correlare gli eventi di diagnostica in un set di servizi indipendenti e gestire le differenze di orario dei computer per comprendere l'ordine degli eventi è difficile. Nello stesso modo in cui si interagisce con un microservizio usando protocolli e formati dati concordati, è necessaria una standardizzazione della modalità di registrazione delle informazioni sull'integrità e degli eventi di diagnostica che, alla fine, si traduce in un archivio di eventi che possono essere visualizzati e su cui si possono eseguire query. In un approccio basato su microservizi, è fondamentale che i diversi team siano concordi sull'uso di un unico formato di registrazione. È necessario un approccio coerente alla visualizzazione degli eventi di diagnostica nell'applicazione nel suo complesso.

L'integrità è diversa dalla diagnostica. Per integrità si intende la segnalazione dello stato corrente da parte del microservizio per consentire l'esecuzione di azioni appropriate. Un esempio efficace riguarda l'interazione con i meccanismi di aggiornamento e distribuzione per assicurare la disponibilità. Anche se un servizio può non essere attualmente integro a causa dell'arresto anomalo di un processo o del riavvio di un computer, potrebbe comunque essere ancora operativo. L'ultima cosa da fare è peggiorare la situazione eseguendo un aggiornamento. L'approccio migliore è procedere prima di tutto a un'indagine o attendere il ripristino del microservizio. Gli eventi di integrità di un microservizio consentono di prendere decisioni informate e favoriscono in effetti la creazione di servizi con funzionalità di riparazione automatica.

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric come piattaforma di microservizi

Azure Service Fabric è nato in seguito alla transizione operata da Microsoft dalla fornitura di prodotti preconfezionati, generalmente di tipo monolitico, alla fornitura di servizi. Service Fabric è stato plasmato dall'esperienza acquisita con la creazione e la gestione di servizi di grandi dimensioni, come database SQL di Azure e Azure Cosmos DB. La piattaforma si è evoluta nel tempo con l'adozione di un numero di servizi sempre maggiore. Service Fabric doveva poter essere eseguito non solo in Azure, ma anche autonomamente nelle distribuzioni di Windows Server.

***Lo scopo di Service Fabric consiste nel risolvere i difficili problemi di compilazione ed esecuzione di un servizio e di utilizzo efficiente delle risorse dell'infrastruttura, per consentire ai team di risolvere i problemi aziendali tramite un approccio basato su microservizi.***

Service Fabric consente di creare applicazioni che usano un approccio basato sui microservizi offrendo:

* Piattaforma con servizi di sistema per distribuire, aggiornare e trovare servizi, rilevare e riavviare i servizi con problemi, inoltrare messaggi, gestire lo stato e monitorare l'integrità.
* Possibilità di distribuire applicazioni eseguite in contenitori o sotto forma di processi. Service Fabric è una struttura per la gestione di contenitori e processi.
* API di programmazione produttiva che consentono di compilare applicazioni come microservizi: [ASP.NET Core, Reliable Actors e Reliable Services](service-fabric-choose-framework.md). Ad esempio, è possibile ottenere informazioni su integrità e diagnostica o sfruttare la disponibilità elevata predefinita.

***Service Fabric è indipendente dalla modalità di compilazione del servizio, quindi è possibile usare qualsiasi tecnologia. Tuttavia offre API di programmazione predefinite che facilitano la compilazione dei microservizi.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrazione di applicazioni esistenti a Service Fabric

Service Fabric consente il riuso del codice esistente, che può essere aggiornabile con nuovi microservizi. La modernizzazione delle applicazioni prevede cinque fasi ed è possibile iniziare e terminare con una qualsiasi di queste. Si tratta di:

1) Iniziare con un'applicazione monolitica tradizionale.  
2) Trasferimento in modalità lift-and-shift: usare contenitori o file eseguibili guest per ospitare in Service Fabric il codice esistente.  
3) Modernizzazione: nuovi microservizi aggiunti al codice esistente in contenitori.  
4) Innovazione: suddivisione della struttura monolitica in microservizi esclusivamente in base alle esigenze.  
5) Trasformazione in microservizi: conversione di applicazioni monolitiche esistenti o creazione di nuove applicazioni prive di vincoli.

![Migrazione ai microservizi][Image3]

È importante sottolineare di nuovo che è possibile **eseguire l'avvio e l'interruzione in una qualsiasi di queste fasi**. Non è obbligatorio passare alla fase successiva. Di seguito sono elencati esempi per ogni fase.

**Trasferimento in modalità lift-and-shift**  
Un numero crescente di aziende esegue la conversione delle applicazioni monolitiche esistenti in strutture a contenitori per due motivi:

- Riduzione dei costi derivante dal consolidamento e dalla rimozione dell'hardware esistente o dall'esecuzione di applicazioni a maggior densità.
- Contratti di distribuzione uniforme tra i settori sviluppo e operazioni.

Le riduzioni dei costi sono evidenti. Ad esempio in Microsoft è in corso la conversione di un numero elevato di applicazioni esistenti in contenitori, con risparmi sui costi di milioni di dollari. L'impatto della distribuzione uniforme è più difficile da valutare, ma ugualmente importante. Nel settore IT gli sviluppatori sono in grado di scegliere la tecnologia che preferiscono, mentre il settore operazioni può implementare un unico approccio alla distribuzione e gestione delle applicazioni. In tal modo il settore operazioni non deve far fronte alla complessità di molte tecnologie diverse, mentre gli sviluppatori non sono costretti a scegliere tra una gamma limitata di soluzioni. In termini semplici, ogni applicazione viene inserita in un'immagine di distribuzione autonoma.

Per molte organizzazioni il processo termina qui. I vantaggi dei contenitori sono già presenti e Service Fabric offre un'esperienza di gestione completa con distribuzione, aggiornamenti, controllo delle versioni, ripristino dello stato precedente, monitoraggio dell'integrità e così via.

**Modernizzazione**  
È l'aggiunta di nuovi servizi al codice esistente nei contenitori. Se si prevede di scrivere nuovo codice, è consigliabile orientarsi gradualmente verso i microservizi. Questo approccio può corrispondere all'aggiunta di un nuovo endpoint API REST o di nuova logica di business. In questo modo è possibile iniziare a creare nuovi microservizi e acquisire pratica con lo sviluppo e la distribuzione.

**Innovazione**  
Un approccio basato su microservizi è in grado di gestire eventuali cambiamenti nelle esigenze aziendali. In questa fase, è necessario determinare se frazionare l'app monolitica in servizi o adottare un approccio innovativo. Ad esempio, è possibile che un database usato come coda del flusso di lavoro diventi un collo di bottiglia a livello di elaborazione. Se il numero di richieste del flusso di lavoro aumenta è necessario suddividere in scala il carico di lavoro. Nel caso di questo componente dell'applicazione che non supporta la ripartizione in scala o che necessita di aggiornamenti più frequenti, adottare un approccio innovativo e ripartire il carico tra più microservizi.

**Trasformazione in microservizi**  
In questa fase, l'applicazione è totalmente composta da (o scomposta in) microservizi. Per raggiungere questo punto è stata portata a termine l'adozione dei microservizi. È possibile iniziare da questa fase, ma una scelta di questo tipo senza il supporto di una piattaforma per microservizi rappresenta un investimento significativo. 

### <a name="are-microservices-right-for-my-application"></a>I microservizi sono appropriati per l'applicazione in uso?

È possibile. Si è potuto osservare che molti dei sempre più numerosi team Microsoft incaricati di compilare prodotti per il cloud per motivi aziendali hanno sfruttato i vantaggi offerti da un approccio orientato ai microservizi. Bing, ad esempio, sviluppa usando microservizi da anni. Per altri team, l'approccio basato su microservizi era una novità. I team riscontravano problemi difficili da risolvere ed estranei alle loro aree di competenza principali. Ecco perché Service Fabric si è affermato come tecnologia preferita per la compilazione di servizi.

L'obiettivo di Service Fabric è ridurre le complessità della compilazione di applicazioni di microservizi, in modo da non dover affrontare molte attività di riprogettazione costose. Iniziare con piccole soluzioni, ridimensionarle secondo le esigenze, deprecare servizi, aggiungerne di nuovi ed evolversi secondo le esigenze di utilizzo del cliente. È anche evidente che devono essere ancora risolti molti altri problemi per rendere i microservizi più accessibili per la maggior parte degli sviluppatori. I contenitori e il modello di programmazione basato su attori sono esempi di piccoli passi in questa direzione e sicuramente emergeranno molte innovazioni per rendere tutto questo più facile.
 
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica della terminologia di Service Fabric](service-fabric-technical-overview.md)
* [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservizi: una rivoluzione delle applicazioni con tecnologia cloud)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
