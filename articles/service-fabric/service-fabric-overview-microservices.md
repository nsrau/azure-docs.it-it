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
ms.date: 06/18/2019
ms.author: atsenthi
ms.openlocfilehash: 5bcb52165c7cae18b807eff03c80b51eae8e2717
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204798"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Perché usare un approccio ai microservizi per la creazione di applicazioni?

Per gli sviluppatori di software, eseguire il factoring un'applicazione nelle parti componenti, è nulla di nuovo. In genere, viene utilizzato un approccio a livelli, con un archivio nel back-end, la logica di business di livello intermedio e un'interfaccia utente front-end (UI). Che cosa *ha* cambiato negli ultimi anni è il fatto che gli sviluppatori creano applicazioni distribuite per il cloud.

Ecco alcuni mutevoli esigenze aziendali:

* Un servizio che ha creato e gestito su larga scala per raggiungere i clienti in nuove aree geografiche.
* Distribuzione più rapida di funzionalità e capacità di rispondere alle richieste dei clienti in modo agile.
* Utilizzo delle risorse migliorato per ridurre i costi.

Queste esigenze aziendali incidono sul *modo* di compilare le applicazioni.

Per altre informazioni sull'approccio Azure ai microservizi, vedere [Microservizi: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservizi: una rivoluzione delle applicazioni basata sul cloud).

## <a name="monolithic-vs-microservices-design-approach"></a>Monolitica e approccio alla progettazione di microservizi

Le applicazioni si evolvono con il trascorrere del tempo. Le applicazioni che hanno maggior successo si evolvono diventando utili per le persone. Le applicazioni non si evolvono e alla fine vengono deprecate. Ecco la domanda: qual è possibile sapere sui requisiti di oggi e che cosa saranno in futuro? Ad esempio supponga che si sta creando un'applicazione di reporting per un reparto della società. Si è certi dell'applicazione si applica solo all'interno dell'ambito dell'azienda e che i report non verrà mantenuti lungo. L'approccio sarà diversa da quella del metodo, ad esempio, la compilazione di un servizio che fornisce contenuti video a decine di milioni di clienti.

In alcuni casi, il rilascio come un modello di verifica è un fattore determinante. Si conosce che l'applicazione può essere riprogettata in un secondo momento. È poco senso nella sovraprogettazione di qualcosa che non viene mai utilizzata. D'altra parte, quando le aziende di compilazione per il cloud, l'aspettativa è crescita e utilizzo. Crescita e la scalabilità sono imprevedibili. Vogliamo creare prototipi rapidamente, anche sapendo che siamo in un percorso che può gestire successo futuro. Questo è l'approccio di avvio snello: compilare, misurare, apprendere e ripetere.

Durante l'epoca di client/server, si trovavano di concentrarsi sulla compilazione di applicazioni a più livelli con le tecnologie specifiche in ogni livello. Il termine *monolitica* dell'applicazione è apparsa per descrivere questi approcci. Le interfacce si trovavano di solito tra i diversi livelli e si usava una progettazione accoppiata più strettamente tra i componenti all'interno di ogni livello. Gli sviluppatori progettavano ed factoring delle classi compilate in librerie e collegate insieme in pochi file eseguibili e DLL.

Esistono vantaggi per un approccio di progettazione monolitica. Le applicazioni monolitiche sono spesso più semplice da progettare e chiamate tra i componenti sono più veloci perché queste chiamate spesso avvengono tramite comunicazione interprocesso (IPC). Inoltre, ognuno testa un unico prodotto, che tende a essere un utilizzo più efficiente delle risorse umane. Lo svantaggio è che è presente un accoppiamento stretto fra i livelli e non è possibile ridimensionare i singoli componenti. Se occorre eseguire correzioni o aggiornamenti, è necessario attendere che altri completino i test. È più difficile essere agili.

I Microservizi risolvono questi aspetti negativi e più allineati con i requisiti aziendali descritti sopra. Ma anche hanno sia vantaggi e punti deboli. I vantaggi dei microservizi consistono nel fatto che ognuno incapsula solitamente funzionalità aziendali più semplici, che è possibile incrementare o ridurre, testare, distribuire e gestire in modo indipendente. Un vantaggio importante dell'approccio basato su microservizi è che i team sono influenzati più dagli scenari aziendali che dalla tecnologia. Team più piccoli sviluppano un microservizio basato su uno scenario del cliente e usando le tecnologie che desiderano utilizzare.

In altre parole, l'organizzazione non deve necessariamente standardizzarsi sulla tecnologia per mantenere applicazioni di microservizi. I singoli team proprietari dei servizi possono procedere come meglio credono, a seconda dell'esperienza del team stesso o della soluzione più appropriata al problema da risolvere. In pratica, un set di tecnologie, quali un particolare archivio NoSQL consigliato o framework applicazione web, è preferibile.

Lo svantaggio dei microservizi è che è necessario gestire le entità più separate e gestire il controllo delle versioni e distribuzioni più complesse. Il traffico di rete tra i microservizi aumenta, così come le latenze di rete corrispondente. Un numero elevato di servizi frammentati e granulari può causare un incubo di prestazioni. Senza strumenti che consentono di visualizzare le dipendenze, è difficile visualizzare l'intero sistema.

Gli standard che consentono l'approccio ai microservizi funzionano tramite la specifica come comunicare e tollerare solo ciò che è necessario ottenere da un servizio, invece di rigidi contratti. È importante definire questi aspetti fin dall'inizio della progettazione perché aggiornare i servizi indipendenti tra loro. Un'altra descrizione coniata per la progettazione con approccio basato su microservizi è "SOA (Service Oriented Architecture) con granularità fine".

***Nella forma più semplice, l'approccio di progettazione di microservizi è su una federazione di servizi, con modifiche indipendenti di ciascuno e standard concordati per la comunicazione disaccoppiata.***

Mentre vengono prodotte altre applicazioni cloud, le persone hanno scoperto che questa scomposizione dell'applicazione complessiva in servizi indipendenti per scenari specifici è un approccio migliore a lungo termine.

## <a name="comparison-between-application-development-approaches"></a>Confronto tra approcci allo sviluppo di applicazioni

![Sviluppo di applicazioni per la piattaforma Service Fabric][Image1]

1) Un'applicazione monolitica include funzionalità specifiche del dominio e normalmente è divisa in livelli funzionali, ad esempio web, business e dati.

2) Ridimensionare un'applicazione monolitica clonarla in più server/macchine virtuali/contenitori.

3) Un'applicazione di microservizi separa le funzionalità in servizi più piccoli distinti.

4) La scalabilità orizzontale di questo approccio basato sui microservizi si ottiene con la distribuzione di ogni servizio in modo indipendente e la creazione di istanze di questi servizi in server/macchine virtuali/contenitori.

Progettazione con microservizi un approccio non è appropriata per tutti i progetti, ma allineano più da vicino gli obiettivi di business descritti in precedenza. Iniziare con un approccio monolitico può rivelarsi utile se si conosce che avrai la possibilità di rielaborare il codice in una progettazione di microservizi. In genere si inizia con un'applicazione monolitica e quindi la si suddivide lentamente in fasi, a partire dalle aree funzionali che devono essere maggiormente scalabili o agili.

Quando si usa un approccio ai microservizi, si compone un'applicazione con molti servizi più piccoli. Questi servizi vengono eseguiti in contenitori distribuiti in un cluster di computer. Team più piccoli sviluppano un servizio focalizzato su uno scenario e in modo indipendente il test, versione, distribuire e ridimensionare ogni servizio in modo puoi far evolvere l'intera applicazione.

## <a name="what-is-a-microservice"></a>Definizione di microservizio

Esistono diverse definizioni di microservizi. Ma la maggior parte di queste caratteristiche dei microservizi sono ampiamente accettata:

* Incapsulano uno scenario aziendale o del cliente. Quali sono risoluzione dei problemi?
* Sono sviluppati da un piccolo team di progettazione.
* Scritto in qualsiasi linguaggio di programmazione, usando qualsiasi framework.
* Costituiti da codice e, facoltativamente, stato, che sono in modo indipendente con controllo delle versioni, distribuiti e ridimensionati.
* Interagiscono con altri microservizi tramite interfacce e protocolli ben definiti.
* Hanno nomi univoci (URL) che consentono di risolvere la propria posizione.
* Rimangono coerenti e disponibili in caso di errori.

Per riassumere che:

***Le applicazioni di microservizi sono costituite da piccoli servizi rivolti ai clienti, scalabili e sottoposti al controllo delle versioni indipendentemente, che comunicano tra di essi tramite protocolli standard e interfacce ben definite.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Scritto in qualsiasi linguaggio di programmazione, usando qualsiasi framework

Gli sviluppatori, vogliamo essere liberi di scegliere un linguaggio o framework, a seconda delle competenze e le esigenze del servizio che stiamo creando. Per alcuni servizi, si potrebbe vantaggi di prestazioni di C++ di sopra di qualsiasi altro elemento. Per altri utenti, la facilità di sviluppo di applicazioni gestita che si ottiene da C# o Java potrebbe essere più importante. In alcuni casi, potrebbe essere necessario usare una raccolta di partner specifico, tecnologia di archiviazione dati o metodo per esporre il servizio ai client.

Dopo aver scelto una tecnologia, è necessario prendere in considerazione l'operativa o del ciclo di vita di gestione e ridimensionamento del servizio.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Consentono di sottoporre al controllo delle versioni, distribuire e ridimensionare indipendentemente codice e stato

Indipendentemente dal modo in cui si scrive i microservizi, il codice e, facoltativamente, lo stato, devono in modo indipendente distribuire, aggiornare e ridimensionare. Questo problema è difficile da risolvere poiché coinvolge la scelta delle tecnologie. Per ridimensionamento, sapere come partizionare (o condividere) sia il codice che lo stato è complesso. Quando il codice e lo stato usano tecnologie diverse, che non è comune attualmente, gli script di distribuzione per il microservizio devono essere in grado di scalare le app a entrambi. Questa separazione è anche sull'agilità e la flessibilità, pertanto è possibile aggiornare alcuni microservizi senza doverli aggiornare tutti gli elementi in una sola volta.

Torniamo al nostro confronto degli approcci monolitici e microservizi per qualche istante. Questo diagramma illustra le differenze negli approcci per l'archiviazione dello stato:

#### <a name="state-storage-for-the-two-approaches"></a>Archiviazione dello stato per i due approcci

![Archiviazione dello stato della piattaforma Service Fabric][Image2]

***L'approccio monolitico, a sinistra ha un database singolo e livelli di tecnologie specifiche.***

***L'approccio ai microservizi, a destra, dispone di un grafico dei microservizi interconnessi in cui lo stato è in genere ambito limitato al microservizio e vengono usate molteplici tecnologie.***

In un approccio monolitico, l'applicazione usa in genere un database singolo. Il vantaggio dell'uso di un database è che sia in un'unica posizione, che rende più semplice da distribuire. Ogni componente può avere una tabella singola per l'archiviazione del relativo stato. La parte più difficile riguarda la necessità che il team separi scrupolosamente lo stato. Inevitabilmente, un utente sarà tentato di aggiungere una colonna a una tabella di clienti esistente, creare un join tra tabelle e creare dipendenze a livello di archiviazione. In questo caso, non sarà possibile ridimensionare i singoli componenti.

Con l'approccio dei microservizi, ogni servizio gestisce e archivia il proprio stato. Ogni servizio è responsabile di ridimensionare sia il codice che lo stato insieme, in modo da soddisfare le richieste del servizio. Un lato negativo è che quando occorre creare viste o query dei dati dell'applicazione, è necessario eseguire una query in più archivi dello stato. Questo problema viene risolto in genere un microservizio separato che crea una visualizzazione in una raccolta di microservizi. Se è necessario eseguire più query specifiche sui dati, si dovrà considerare la scrittura dei dati di ogni microservizio per un servizio di data warehousing per analitica in linea.

I Microservizi sono con controllo delle versioni. È possibile che versioni diverse di un microservizio per l'esecuzione side-by-side. Una versione più recente di un microservizio potrebbe non riuscire durante un aggiornamento e necessario eseguire il rollback a una versione precedente. È anche utile per il controllo delle versioni / testing B, in cui utenti diversi provano versioni diverse del servizio. Ad esempio, è normale aggiornare un microservizio per un set specifico di clienti di testare nuove funzionalità prima di implementarlo più ampiamente.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interagiscono con altri microservizi tramite interfacce ben definite e protocolli.

Negli ultimi 10 anni, sono state pubblicate numerose informazioni che descrivono i modelli di comunicazione in architetture orientate ai servizi. Di solito la comunicazione tra servizi usa un approccio REST con i protocolli HTTP e TCP e XML o JSON come formato di serializzazione. Dal punto di vista dell'interfaccia, ciò che è un approccio di progettazione web. Ma non dovrebbe impedisce di usare protocolli binari o formati dati personalizzati. Tenere semplicemente presente che le persone avranno maggiori difficoltà a usare i microservizi se questi protocolli e formati non sono disponibili pubblicamente.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Hanno un nome (URL) univoco usato per risolvere il percorso

Il microservizio deve essere indirizzabile ovunque sia in esecuzione. Se si sta valutando le macchine e quale è in esecuzione un determinato microservizio, le cose non va.

Così come il DNS risolve un URL particolare in un computer specifico, il microservizio deve avere un nome univoco per consentire l'individuazione della sua posizione attuale. I Microservizi devono avere nomi indirizzabili che non dipendono dell'infrastruttura in che vengono eseguite. Ciò implica che non esiste un'interazione tra la modalità di distribuzione del servizio e modo in cui viene individuato, perché deve essere un registro del servizio. Quando un computer non riesce, il servizio Registro di sistema deve indicare dove il servizio è stato spostato in.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Rimangono coerenti e disponibili in caso di errori.

Affrontare gli errori imprevisti è uno dei problemi più difficili da risolvere, specialmente in un sistema distribuito. Gran parte del codice scritto dagli sviluppatori sia per la gestione delle eccezioni. Durante il test, è inoltre dedicare più tempo sulla gestione delle eccezioni. Il processo è più complesso rispetto alla scrittura di codice per gestire gli errori. Cosa accade quando si verifica un errore di computer in cui viene eseguito il microservizio? È necessario rilevare l'errore, che è un problema di per sé difficile. Ma è anche necessario riavviare il microservizio.

Per la disponibilità, un microservizio deve essere resiliente agli errori e in grado di riavviarsi in un altro computer. Oltre a questi requisiti di resilienza, dati non devono essere persi e devono rimanere coerente dei dati.

La resilienza è difficile da raggiungere quando si verificano errori durante l'aggiornamento di un'applicazione. Il microservizio, che interagisce con il sistema di distribuzione, non deve essere ripristinato. È necessario determinare se è possibile continuare a passare alla versione più recente oppure eseguire il rollback a una versione precedente per mantenere uno stato coerente. È necessario prendere in considerazione alcune domande, ad esempio se numero sufficiente di computer sono disponibili per continuare e su come ripristinare le versioni precedenti del microservizio. Per prendere queste decisioni, è necessario il microservizio deve generare informazioni di integrità.

### <a name="reports-health-and-diagnostics"></a>Segnalano integrità e diagnostica

Potrebbe sembrare ovvio e spesso trascurato, ma un microservizio deve segnalare propria integrità e diagnostica. In caso contrario, è necessario solo poche indicazioni sul relativo integrità dal punto di vista operativo. Correlare gli eventi di diagnostica in un set di servizi indipendenti e gestire le differenze di orario dei computer per comprendere l'ordine degli eventi è difficile. Nello stesso modo in cui si interagisce con un microservizio usando protocolli e formati dati concordati, è necessaria una standardizzazione della modalità di registrazione delle informazioni sull'integrità e degli eventi di diagnostica che, alla fine, si traduce in un archivio di eventi che possono essere visualizzati e su cui si possono eseguire query. Con un approccio ai microservizi, è necessario che diversi team concordino su un singolo formato di registrazione. È necessario un approccio coerente alla visualizzazione degli eventi di diagnostica nell'applicazione nel suo complesso.

L'integrità è diversa dalla diagnostica. Per integrità si intende la segnalazione dello stato corrente da parte del microservizio per consentire l'esecuzione di azioni appropriate. Un esempio efficace riguarda l'interazione con i meccanismi di aggiornamento e distribuzione per assicurare la disponibilità. Anche se un servizio potrebbe non essere attualmente integro a causa di un arresto anomalo del processo o riavvio di un computer, il servizio potrebbe comunque essere operativo. L'ultima operazione che è necessario è rendere peggio situazione avviando un aggiornamento. L'approccio migliore consiste nell'analizzare innanzitutto o attendere il recupero del microservizio. Gli eventi di integrità di un microservizio consentono di prendere decisioni informate e favoriscono in effetti la creazione di servizi con funzionalità di riparazione automatica.

## <a name="guidance-for-designing-microservices-on-azure"></a>Materiale sussidiario per la progettazione di microservizi in Azure 
Visita il centro architetture Azure per indicazioni su [la progettazione e creazione di microservizi in Azure](https://docs.microsoft.com/azure/architecture/microservices/).

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric come piattaforma di microservizi

Azure Service Fabric è emersa quando Microsoft eseguita la transizione dalla fornitura boxed prodotti preconfezionati, generalmente di tipo monolitico, alla fornitura dei servizi. L'esperienza di creazione e la gestione di servizi di grandi dimensioni, ad esempio Database SQL di Azure e Azure Cosmos DB, la forma di Service Fabric. La piattaforma si evolve nel tempo adottato altri servizi. Service Fabric doveva poter essere eseguito non solo in Azure, ma anche autonomamente nelle distribuzioni di Windows Server.

***L'obiettivo di Service Fabric è risolvere i complicati problemi di compilazione ed esecuzione di un servizio e usare risorse di infrastruttura in modo efficiente, in modo che i team possano risolvere problemi aziendali usando un approccio ai microservizi.***

Service Fabric consente di creare applicazioni che usano un approccio basato sui microservizi offrendo:

* Piattaforma con servizi di sistema per distribuire, aggiornare e trovare servizi, rilevare e riavviare i servizi con problemi, inoltrare messaggi, gestire lo stato e monitorare l'integrità.
* La possibilità di distribuire applicazioni eseguite in contenitori che come processi. Service Fabric è una struttura per la gestione di contenitori e processi.
* API di programmazione produttive che consentono di compilare applicazioni come microservizi: [ASP.NET Core, Reliable Actors e Reliable Services](service-fabric-choose-framework.md). Ad esempio, è possibile ottenere informazioni su integrità e diagnostica o sfruttare la disponibilità elevata predefinita.

***Service Fabric è agnostico sul modo in cui si compila il servizio è possibile usare qualsiasi tecnologia. Ma fornisce API di programmazione predefinite che rendono più semplice creare microservizi.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrazione di applicazioni esistenti a Service Fabric

Service Fabric consente di riutilizzare il codice esistente e modernizzare lo con nuovi microservizi. Ci sono cinque fasi la modernizzazione delle applicazioni ed è possibile avviare e arrestare in qualsiasi fase. Le fasi sono:

1) Iniziare con un'applicazione monolitica tradizionale.  
2) Eseguire la migrazione. Usare contenitori o eseguibili guest per ospitare il codice esistente in Service Fabric.  
3) Modernizza la. Aggiungere nuovi microservizi al codice esistente in contenitori.  
4) Innova. Suddividere l'applicazione monolitica in microservizi in base alle necessità.  
5) Trasformare le applicazioni in microservizi. Trasformare le applicazioni monolitiche esistenti o creare nuove applicazioni greenfield.

![Migrazione ai microservizi][Image3]

Tenere presente che è possibile *iniziare e terminare con una qualsiasi di queste fasi*. Non è necessario per lo stato di avanzamento alla fase successiva. 

Verranno ora esaminati alcuni esempi per ognuna di queste fasi.

**Migrazione**  
Per due motivi, molte aziende esegue la migrazione di applicazioni monolitiche esistenti in contenitori:

* Riduzione dei costi, a causa di consolidamento e la rimozione dell'hardware esistente o a causa dell'esecuzione di applicazioni a maggior densità.
* Un contratto di distribuzione coerente tra sviluppo e operazioni.

Le riduzioni dei costi sono semplici. Presso Microsoft, molte applicazioni esistenti sono in contenitori, portano a milioni di dollari in risparmi. Distribuzione uniforme è più difficile da valutare, ma ugualmente importante. Significa che gli sviluppatori possono scegliere le tecnologie adatte a loro, ma le operazioni verranno accettate solo un singolo metodo per distribuire e gestire le applicazioni. Riduce le operazioni di dover affrontare la complessità delle diverse tecnologie di supporto senza imporre agli sviluppatori di scegliere solo determinati programmi. Essenzialmente, ogni applicazione viene inserita nelle immagini di distribuzione autonoma.

Per molte organizzazioni il processo termina qui. Hanno già i vantaggi dei contenitori e Service Fabric offre un'esperienza completa di gestione, tra cui distribuzione, gli aggiornamenti, controllo delle versioni, rollback e monitoraggio dell'integrità.

**Modernize**  
Modernizzazione è l'aggiunta di nuovi servizi al codice esistente nei contenitori. Se si intende scrivere nuovo codice, è consigliabile orientarsi gradualmente verso i microservizi. Ciò potrebbe significare l'aggiunta di un nuovo endpoint API REST o una nuova logica di business. In questo modo, si avvia il processo di creazione di nuovi microservizi e le pratiche per lo sviluppo e la loro distribuzione.

**Innovazione**  
Un approccio basato su microservizi è in grado di gestire eventuali cambiamenti nelle esigenze aziendali. In questa fase, è necessario decidere se iniziare la suddivisione dell'applicazione monolitica in servizi o l'innovazione. Ecco un esempio classico è un database in uso come una coda del flusso di lavoro diventi un collo di bottiglia di elaborazione. Se il numero di richieste del flusso di lavoro aumenta è necessario suddividere in scala il carico di lavoro. Richiedere questo particolare aspetto dell'applicazione che non è in corso o che deve essere aggiornata più spesso e dividerlo out come un microservizio e innovazione.

**Trasformare le applicazioni in microservizi**  
In questa fase, l'applicazione sia completamente composto da (o suddivise in) microservizi. Per raggiungere questo punto, sono state apportate l'adozione dei microservizi. È possibile iniziare da qui, ma questa operazione senza un microservizi piattaforma che consentono di richiede un investimento significativo.

### <a name="are-microservices-right-for-my-application"></a>I microservizi sono appropriati per l'applicazione in uso?

È possibile. Presso Microsoft, come altri team ha iniziato a creare applicazioni per il cloud per motivi commerciali, molti dei quali realizzati i vantaggi dell'utilizzo di un approccio orientato ai microservizi. Bing, ad esempio, ha usato i microservizi da anni. Per altri team, l'approccio basato su microservizi era una novità. I team riscontravano problemi difficili da risolvere ed estranei alle loro aree di competenza principali. Ecco perché si è affermato come tecnologia per la creazione di servizi di Service Fabric.

L'obiettivo di Service Fabric consiste nel ridurre la complessità della creazione di applicazioni di microservizi in modo da non dover passare attraverso tante riprogettazioni costose. Iniziare con piccole soluzioni, ridimensionarle secondo le esigenze, deprecare servizi, aggiungerne di nuovi ed evolversi secondo le esigenze di utilizzo del cliente. È anche evidente che devono essere ancora risolti molti altri problemi per rendere i microservizi più accessibili per la maggior parte degli sviluppatori. I contenitori e il modello di programmazione degli attori sono esempi di piccoli passi in quella direzione. Siamo sicuri che emergeranno molte innovazioni per rendere più facile un approccio ai microservizi.


## <a name="next-steps"></a>Passaggi successivi

* [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservizi: una rivoluzione delle applicazioni con tecnologia cloud)
* [Centro architetture Azure: Creazione di microservizi in Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* [Applicazione di Service Fabric di Azure e i cluster di procedure consigliate](service-fabric-best-practices-overview.md)
* [Panoramica della terminologia di Service Fabric](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
