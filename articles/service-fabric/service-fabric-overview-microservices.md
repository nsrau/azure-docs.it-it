---
title: Introduzione ai microservizi in Azure
description: Panoramica dell'importanza di compilare applicazioni cloud con un approccio basato su microservizi per lo sviluppo di applicazioni moderne e descrizione dell'uso di Azure Service Fabric come piattaforma per raggiungere questo obiettivo.
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: af18a6cb45808c0af5ec2782a3fd2100e3b7bf99
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750627"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Perché usare un approccio basato su microservizi per la creazione di applicazioni

Per gli sviluppatori di software, il factoring di un'applicazione in parti componenti non è una novità. Viene in genere usato un approccio a più livelli, con un archivio back-end, la logica di business di livello intermedio e un'interfaccia utente (UI) front-end. Ciò che *è* cambiato negli ultimi anni è che gli sviluppatori stanno creando applicazioni distribuite per il cloud.

Di seguito sono riportate alcune mutevoli esigenze aziendali:

* Un servizio compilato e gestito su larga scala per raggiungere i clienti in nuove aree geografiche.
* Distribuzione più rapida di funzionalità e funzionalità per rispondere alle richieste dei clienti in modo agile.
* Utilizzo delle risorse migliorato per ridurre i costi.

Queste esigenze aziendali incidono sul *modo* di compilare le applicazioni.

Per altre informazioni sull'approccio di Azure ai microservizi, vedere [microservizi: rivoluzione di un'applicazione basata sul cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservices-design-approach"></a>Approccio di progettazione monolitico rispetto ai microservizi

Le applicazioni si evolvono con il trascorrere del tempo. Le applicazioni che hanno maggior successo si evolvono diventando utili per le persone. Le applicazioni non riuscite non vengono sviluppate e vengono infine deprecate. Ecco la domanda: qual è la quantità di informazioni sui requisiti attuali e cosa saranno in futuro? Si immagini, ad esempio, di creare un'applicazione per la creazione di report per un reparto nell'azienda. Si è certi che l'applicazione venga applicata solo nell'ambito dell'azienda e che i report non verranno mantenuti a lungo. L'approccio sarà diverso da quello di, ad affermare, creare un servizio che fornisce contenuti video a decine di milioni di clienti.

In alcuni casi, è il fattore determinante come un modello di prova. Si sa che l'applicazione può essere riprogettata in un secondo momento. Si è verificato un po' di tempo per la progettazione di un elemento che non viene mai usato. D'altra parte, quando le aziende si basano sul cloud, l'aspettativa è la crescita e l'utilizzo. La crescita e la scalabilità sono imprevedibili. Si desidera eseguire rapidamente il prototipo, pur sapendo di essere in un percorso in grado di gestire un successo futuro. Questo è l'approccio di avvio snello: compilare, misurare, apprendere e ripetere.

Durante l'era del client/server, abbiamo avuto la tendenza a concentrarsi sulla creazione di applicazioni a più livelli usando tecnologie specifiche in ogni livello. Il termine applicazione *monolitica* è emerso per descrivere questi approcci. Le interfacce si trovavano di solito tra i diversi livelli e si usava una progettazione accoppiata più strettamente tra i componenti all'interno di ogni livello. Gli sviluppatori hanno progettato ed eseguito il factoring delle classi compilate in librerie e collegate insieme in pochi file eseguibili e dll.

Un approccio di progettazione monolitico presenta vantaggi. Le applicazioni monolitiche sono spesso più semplici da progettare e le chiamate tra i componenti sono più veloci, perché queste chiamate spesso superano la comunicazione interprocesso (IPC). Inoltre, tutti testano un singolo prodotto, che tende a essere un uso più efficiente delle risorse umane. Lo svantaggio è che esiste un accoppiamento stretto tra livelli a livelli e non è possibile ridimensionare i singoli componenti. Se è necessario eseguire correzioni o aggiornamenti, è necessario attendere che altri utenti completino i test. È più difficile essere agile.

I microservizi soddisfano questi svantaggi e si allineano più strettamente ai requisiti aziendali precedenti. Ma presentano anche vantaggi e passività. I vantaggi dei microservizi consistono nel fatto che ognuno incapsula solitamente funzionalità aziendali più semplici, che è possibile incrementare o ridurre, testare, distribuire e gestire in modo indipendente. Un vantaggio importante di un approccio basato su microservizi è che i team sono sempre più basati su scenari aziendali rispetto alla tecnologia. I team più piccoli sviluppano un microservizio sulla base di uno scenario del cliente e utilizzano le tecnologie che desiderano utilizzare.

In altre parole, l'organizzazione non deve necessariamente standardizzarsi sulla tecnologia per mantenere applicazioni di microservizi. I singoli team proprietari dei servizi possono procedere come meglio credono, a seconda dell'esperienza del team stesso o della soluzione più appropriata al problema da risolvere. In pratica, è preferibile un set di tecnologie consigliate, ad esempio un particolare archivio NoSQL o un Framework applicazione Web.

Lo svantaggio dei microservizi è la gestione di entità più separate e la gestione delle distribuzioni e del controllo delle versioni più complesse. Il traffico di rete tra i microservizi aumenta, così come le latenze di rete corrispondenti. Molti servizi granulari e granulari possono causare un incubo per le prestazioni. Senza strumenti che consentano di visualizzare le dipendenze, è difficile vedere l'intero sistema.

Gli standard consentono di usare l'approccio basato su microservizi specificando come comunicare e tollerare solo gli elementi necessari da un servizio, anziché contratti rigidi. È importante definire questi contratti all'inizio della progettazione perché i servizi vengono aggiornati in modo indipendente l'uno dall'altro. Un'altra descrizione coniata per la progettazione con approccio basato su microservizi è "SOA (Service Oriented Architecture) con granularità fine".

***Al suo più semplice, l'approccio di progettazione di microservizi riguarda una Federazione disaccoppiata di servizi, con modifiche indipendenti a ognuno e agli standard concordati per la comunicazione.***

Quando vengono generate più applicazioni cloud, le persone hanno scoperto che questa scomposizione dell'applicazione complessiva in servizi indipendenti e mirati agli scenari è un approccio migliore a lungo termine.

## <a name="comparison-between-application-development-approaches"></a>Confronto tra approcci allo sviluppo di applicazioni

![Sviluppo di applicazioni per la piattaforma Service Fabric][Image1]

1) Un'applicazione monolitica contiene funzionalità specifiche del dominio e in genere è divisa in livelli funzionali come Web, business e dati.

2) Per ridimensionare un'applicazione monolitica, è possibile clonarla in più server/macchine virtuali/contenitori.

3) Un'applicazione di microservizi separa le funzionalità in servizi più piccoli distinti.

4) La scalabilità orizzontale di questo approccio basato sui microservizi si ottiene con la distribuzione di ogni servizio in modo indipendente e la creazione di istanze di questi servizi in server/macchine virtuali/contenitori.

La progettazione con un approccio basato su microservizi non è appropriata per tutti i progetti, ma si allinea più strettamente agli obiettivi aziendali descritti in precedenza. Iniziare con un approccio monolitico potrebbe avere senso se si ha la possibilità di rielaborare il codice in un secondo momento in una progettazione di microservizi. In genere si inizia con un'applicazione monolitica e quindi la si suddivide lentamente in fasi, a partire dalle aree funzionali che devono essere maggiormente scalabili o agili.

Quando si usa un approccio basato su microservizi, si compone l'applicazione di molti servizi di piccole dimensioni. Questi servizi vengono eseguiti in contenitori distribuiti in un cluster di computer. I team più piccoli sviluppano un servizio incentrato su uno scenario e test, versione, distribuzione e scalabilità indipendenti di ogni servizio, in modo che l'intera applicazione possa evolvere.

## <a name="what-is-a-microservice"></a>Definizione di microservizio

Esistono diverse definizioni di microservizi. Tuttavia, la maggior parte di queste caratteristiche dei microservizi è ampiamente accettata:

* Incapsulano uno scenario aziendale o del cliente. Quale problema si sta risolvendo?
* Sono sviluppati da un piccolo team di progettazione.
* Scritto in qualsiasi linguaggio di programmazione, usando qualsiasi Framework.
* Sono costituiti da codice e, facoltativamente, dallo stato, entrambi con controllo delle versioni, distribuzione e ridimensionamento in modo indipendente.
* Interagiscono con altri microservizi tramite interfacce e protocolli ben definiti.
* Hanno nomi univoci (URL) usati per risolvere il percorso.
* Rimangono coerenti e disponibili in caso di errori.

Per sommare il risultato:

***Le applicazioni di microservizi sono costituite da piccoli servizi rivolti ai clienti, scalabili e sottoposti al controllo delle versioni indipendentemente, che comunicano tra di essi tramite protocolli standard e interfacce ben definite.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Scritto in qualsiasi linguaggio di programmazione, usando qualsiasi Framework

Gli sviluppatori desiderano scegliere una lingua o un Framework, a seconda delle competenze e delle esigenze del servizio che si sta creando. Per alcuni servizi, è possibile valorizzare i vantaggi a C++ livello di prestazioni indicati in precedenza. Per gli altri, la facilità di sviluppo gestito che si ottiene C# da o Java potrebbe essere più importante. In alcuni casi, potrebbe essere necessario utilizzare una specifica libreria partner, una tecnologia di archiviazione dati o un metodo per esporre il servizio ai client.

Dopo aver scelto una tecnologia, è necessario prendere in considerazione la gestione operativa o del ciclo di vita e il ridimensionamento del servizio.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Consentono di sottoporre al controllo delle versioni, distribuire e ridimensionare indipendentemente codice e stato

Indipendentemente dal modo in cui si scrivono i microservizi, il codice e, facoltativamente, lo stato, è necessario distribuire, aggiornare e ridimensionare in modo indipendente. Questo problema è difficile da risolvere perché si tratta di una scelta di tecnologie. Per la scalabilità, capire come partizionare (o partizionare) sia il codice che lo stato è complesso. Quando il codice e lo stato usano tecnologie diverse, attualmente comuni, gli script di distribuzione per il microservizio devono poterli ridimensionare entrambi. Questa separazione riguarda anche agilità e flessibilità, quindi è possibile aggiornare alcuni microservizi senza dover aggiornarli tutti contemporaneamente.

Torniamo al confronto degli approcci monolitici e dei microservizi per un attimo. In questo diagramma vengono illustrate le differenze negli approcci per l'archiviazione dello stato:

#### <a name="state-storage-for-the-two-approaches"></a>Archiviazione dello stato per i due approcci

![Archiviazione dello stato della piattaforma Service Fabric][Image2]

***L'approccio monolitico, a sinistra, ha un singolo database e livelli di tecnologie specifiche.***

***L'approccio basato su microservizi, a destra, ha un grafico di microservizi interconnessi in cui lo stato è in genere limitato al microservizio e vengono usate varie tecnologie.***

In un approccio monolitico, l'applicazione usa in genere un database singolo. Il vantaggio dell'utilizzo di un database è che si trova in un'unica posizione, semplificando la distribuzione. Ogni componente può avere una tabella singola per l'archiviazione del relativo stato. La parte più difficile riguarda la necessità che il team separi scrupolosamente lo stato. Inevitabilmente, qualcuno tenterà di aggiungere una colonna a una tabella Customer esistente, eseguire un join tra le tabelle e creare dipendenze a livello di archiviazione. In questo caso, non sarà possibile ridimensionare i singoli componenti.

Con l'approccio dei microservizi, ogni servizio gestisce e archivia il proprio stato. Ogni servizio è responsabile di ridimensionare sia il codice che lo stato insieme, in modo da soddisfare le richieste del servizio. Uno svantaggio è che quando è necessario creare visualizzazioni, o query, dei dati dell'applicazione, è necessario eseguire una query su più archivi Stati. Questo problema viene in genere risolto da un microservizio separato che compila una visualizzazione in una raccolta di microservizi. Se è necessario eseguire più query improvvise sui dati, è consigliabile scrivere i dati di ogni microservizio in un servizio di data warehousing per l'analisi offline.

I microservizi sono con versione. È possibile eseguire side-by-side diverse versioni di un microservizio. Una versione più recente di un microservizio potrebbe non riuscire durante un aggiornamento ed è necessario eseguirne il rollback a una versione precedente. Il controllo delle versioni è utile anche per i test A/B, in cui utenti diversi sperimentano versioni diverse del servizio. Ad esempio, è comune aggiornare un microservizio per un set di clienti specifico per testare nuove funzionalità prima di distribuirlo in modo più ampio.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interagiscono con altri microservizi tramite interfacce ben definite e protocolli.

Negli ultimi 10 anni sono state pubblicate informazioni complete che descrivono i modelli di comunicazione nelle architetture orientate ai servizi. Di solito la comunicazione tra servizi usa un approccio REST con i protocolli HTTP e TCP e XML o JSON come formato di serializzazione. Dal punto di vista dell'interfaccia, è consigliabile adottare un approccio di progettazione Web. Tuttavia, non è consigliabile evitare di usare protocolli binari o formati di dati personalizzati. È sufficiente tenere presente che le persone avranno un tempo più difficile con i microservizi se questi protocolli e formati non sono disponibili in modo aperto.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Hanno un nome (URL) univoco usato per risolvere il percorso

Il microservizio deve essere indirizzabile ovunque venga eseguito. Se si sta pensando ai computer che eseguono un determinato microservizio, è possibile che si stia verificando rapidamente un problema.

Così come il DNS risolve un URL particolare in un computer specifico, il microservizio deve avere un nome univoco per consentire l'individuazione della sua posizione attuale. I microservizi richiedono nomi indirizzabili indipendenti dall'infrastruttura in cui sono in esecuzione. Ciò implica che esiste un'interazione tra la modalità di distribuzione del servizio e il modo in cui viene individuata, perché è necessario un registro del servizio. Quando si verifica un errore in un computer, il servizio Registro di sistema deve indicare la posizione in cui è stato spostato il servizio.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Rimangono coerenti e disponibili in caso di errori.

Affrontare gli errori imprevisti è uno dei problemi più difficili da risolvere, specialmente in un sistema distribuito. Gran parte del codice che scriviamo come sviluppatori è per la gestione delle eccezioni. Durante i test, si dedica anche la maggior parte del tempo alla gestione delle eccezioni. Il processo è più necessario per la scrittura di codice per la gestione degli errori. Cosa accade quando il computer in cui è in esecuzione il microservizio non riesce? È necessario rilevare l'errore, che è un problema complesso. È anche necessario riavviare il microservizio.

Per la disponibilità, un microservizio deve essere resiliente agli errori e può essere riavviato in un altro computer. Oltre a questi requisiti di resilienza, i dati non devono essere persi e i dati devono rimanere coerenti.

La resilienza è difficile da raggiungere quando si verificano errori durante l'aggiornamento di un'applicazione. Il microservizio, che interagisce con il sistema di distribuzione, non deve essere ripristinato. Deve determinare se può continuare a passare alla versione più recente o eseguire il rollback a una versione precedente per mantenere uno stato coerente. È necessario prendere in considerazione alcune domande, ad esempio se sono disponibili computer sufficienti per proseguire e come ripristinare le versioni precedenti del microservizio. Per prendere queste decisioni, è necessario che il microservizio emetta informazioni sull'integrità.

### <a name="reports-health-and-diagnostics"></a>Segnalano integrità e diagnostica

Potrebbe sembrare ovvio e spesso trascurato, ma un microservizio deve segnalare l'integrità e la diagnostica. In caso contrario, è possibile approfondire l'integrità dal punto di vista delle operazioni. Correlare gli eventi di diagnostica in un set di servizi indipendenti e gestire le differenze di orario dei computer per comprendere l'ordine degli eventi è difficile. Nello stesso modo in cui si interagisce con un microservizio usando protocolli e formati dati concordati, è necessaria una standardizzazione della modalità di registrazione delle informazioni sull'integrità e degli eventi di diagnostica che, alla fine, si traduce in un archivio di eventi che possono essere visualizzati e su cui si possono eseguire query. Con un approccio basato su microservizi, i team diversi devono accettare un singolo formato di registrazione. È necessario un approccio coerente alla visualizzazione degli eventi di diagnostica nell'applicazione nel suo complesso.

L'integrità è diversa dalla diagnostica. Per integrità si intende la segnalazione dello stato corrente da parte del microservizio per consentire l'esecuzione di azioni appropriate. Un esempio efficace riguarda l'interazione con i meccanismi di aggiornamento e distribuzione per assicurare la disponibilità. Sebbene un servizio potrebbe essere attualmente non integro a causa di un arresto anomalo del processo o del riavvio del computer, il servizio potrebbe essere ancora operativo. L'ultima operazione necessaria consiste nel peggiorare la situazione avviando un aggiornamento. L'approccio migliore consiste nell'esaminare prima o attendere il tempo necessario per il ripristino del microservizio. Gli eventi di integrità di un microservizio consentono di prendere decisioni informate e favoriscono in effetti la creazione di servizi con funzionalità di riparazione automatica.

## <a name="guidance-for-designing-microservices-on-azure"></a>Linee guida per la progettazione di microservizi in Azure

Visitare il centro architetture di Azure per indicazioni sulla [progettazione e la creazione di microservizi in Azure](https://docs.microsoft.com/azure/architecture/microservices/).

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric come piattaforma di microservizi

Azure Service Fabric emerso in seguito alla transizione da Microsoft alla distribuzione di prodotti confezionati, che in genere erano monolitici per la distribuzione di servizi. L'esperienza di creazione e gestione di servizi di grandi dimensioni, come il database SQL di Azure e Azure Cosmos DB, con forma Service Fabric. La piattaforma si è evoluta nel tempo con l'adozione di altri servizi. Service Fabric doveva poter essere eseguito non solo in Azure, ma anche autonomamente nelle distribuzioni di Windows Server.

***L'obiettivo di Service Fabric consiste nel risolvere i problemi complessi della creazione e dell'esecuzione di un servizio e l'uso efficiente delle risorse dell'infrastruttura, in modo che i team possano risolvere i problemi aziendali usando un approccio basato su microservizi.***

Service Fabric consente di creare applicazioni che usano un approccio basato sui microservizi offrendo:

* Piattaforma con servizi di sistema per distribuire, aggiornare e trovare servizi, rilevare e riavviare i servizi con problemi, inoltrare messaggi, gestire lo stato e monitorare l'integrità.
* Possibilità di distribuire applicazioni in esecuzione in contenitori o come processi. Service Fabric è una struttura per la gestione di contenitori e processi.
* API di programmazione produttiva per semplificare la creazione di applicazioni come microservizi: [ASP.NET Core, Reliable Actors e reliable Services](service-fabric-choose-framework.md). Ad esempio, è possibile ottenere informazioni su integrità e diagnostica o sfruttare la disponibilità elevata predefinita.

***Service Fabric è indipendente dalla modalità di compilazione del servizio ed è possibile utilizzare qualsiasi tecnologia. Ma fornisce API di programmazione predefinite che semplificano la creazione di microservizi.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrazione di applicazioni esistenti a Service Fabric

Service Fabric consente di riutilizzare il codice esistente e di modernizzarlo con nuovi microservizi. Ci sono cinque fasi per la modernizzazione delle applicazioni ed è possibile avviare e arrestare in qualsiasi fase. Le fasi sono:

1) Iniziare con un'applicazione monolitica tradizionale.  
2) Eseguire la migrazione. Usare i contenitori o gli eseguibili Guest per ospitare il codice esistente in Service Fabric.  
3) Modernizzare. Aggiungere nuovi microservizi insieme al codice in contenitori esistente.  
4) Innova. Suddividere l'applicazione monolitica in microservizi in base alle esigenze.  
5) Trasforma le applicazioni in microservizi. Trasforma le applicazioni monolitiche esistenti o crea nuove applicazioni Greenfield.

![Migrazione ai microservizi][Image3]

Tenere presente che è possibile *avviare e arrestare in una qualsiasi di queste fasi*. Non è necessario passare alla fase successiva. 

Esaminiamo gli esempi per ognuna di queste fasi.

**Migrazione**  
Per due motivi, molte aziende stanno migrando le applicazioni monolitiche esistenti in contenitori:

* Riduzione dei costi, a causa del consolidamento e della rimozione dell'hardware esistente o a causa di applicazioni in esecuzione a densità più elevata.
* Un contratto di distribuzione coerente tra lo sviluppo e le operazioni.

Le riduzioni dei costi sono semplici. In Microsoft, molte applicazioni esistenti vengono incluse in contenitori, con un conseguente risparmio di milioni di dollari. Una distribuzione coerente è più difficile da valutare, ma ugualmente importante. Questo significa che gli sviluppatori possono scegliere le tecnologie adatte, ma le operazioni accetteranno solo un unico metodo per la distribuzione e la gestione delle applicazioni. Consente di evitare che le operazioni debbano occuparsi della complessità del supporto di tecnologie diverse senza forzare gli sviluppatori a scegliere solo determinate. In pratica, ogni applicazione è inclusa in contenitori in immagini di distribuzione autonomi.

Per molte organizzazioni il processo termina qui. Hanno già i vantaggi dei contenitori e Service Fabric offre un'esperienza di gestione completa, tra cui distribuzione, aggiornamenti, controllo delle versioni, rollback e monitoraggio dell'integrità.

**Modernizzare**  
La modernizzazione è l'aggiunta di nuovi servizi insieme al codice esistente in contenitori. Se si intende scrivere nuovo codice, è preferibile eseguire piccoli passaggi per il percorso dei microservizi. Questo potrebbe significare l'aggiunta di un nuovo endpoint API REST o di una nuova logica di business. In questo modo, si inizia il processo di creazione di nuovi microservizi e si pratica lo sviluppo e la distribuzione.

**Innovazione**  
Un approccio basato su microservizi è in grado di gestire eventuali cambiamenti nelle esigenze aziendali. A questo punto, è necessario decidere se iniziare a suddividere l'applicazione monolitica in servizi o innovare. Un esempio classico è quando un database usato come coda del flusso di lavoro diventa un collo di bottiglia di elaborazione. Se il numero di richieste del flusso di lavoro aumenta è necessario suddividere in scala il carico di lavoro. È possibile eseguire questa particolare parte dell'applicazione che non viene ridimensionata o che deve essere aggiornata con maggiore frequenza e suddividerla come microservizio e innovare.

**Trasformare le applicazioni in microservizi**  
In questa fase, l'applicazione è completamente composta da microservizi (o suddivisi in). Per raggiungere questo punto, è stato creato il percorso dei microservizi. È possibile iniziare da qui, ma a tale scopo senza una piattaforma di microservizi per aiutare a richiedere un investimento significativo.

### <a name="are-microservices-right-for-my-application"></a>I microservizi sono appropriati per l'applicazione in uso?

Forse. In Microsoft, poiché più team hanno iniziato a sviluppare per il cloud per motivi aziendali, molti di essi hanno realizzato i vantaggi derivanti dall'adozione di un approccio simile a un microservizio. Bing, ad esempio, USA i microservizi per anni. Per altri team, l'approccio basato su microservizi era una novità. I team riscontravano problemi difficili da risolvere ed estranei alle loro aree di competenza principali. Questo è il motivo per cui Service Fabric acquisito la trazione come tecnologia per la creazione di servizi.

L'obiettivo di Service Fabric consiste nel ridurre le complessità della creazione di applicazioni di microservizi, in modo da non dover superare il numero di riprogettazioni costose. Iniziare con piccole soluzioni, ridimensionarle secondo le esigenze, deprecare servizi, aggiungerne di nuovi ed evolversi secondo le esigenze di utilizzo del cliente. È anche evidente che devono essere ancora risolti molti altri problemi per rendere i microservizi più accessibili per la maggior parte degli sviluppatori. I contenitori e il modello di programmazione Actor sono esempi di piccoli passaggi in questa direzione. Per semplificare l'approccio ai microservizi, siamo sicuri che emergono più innovazioni.

## <a name="next-steps"></a>Passaggi successivi

* [Microservizi: una rivoluzione delle applicazioni basata sul cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Centro architetture di Azure: creazione di microservizi in Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* [Procedure consigliate per l'applicazione e il cluster di Azure Service Fabric](service-fabric-best-practices-overview.md)
* [Panoramica della terminologia di Service Fabric](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
