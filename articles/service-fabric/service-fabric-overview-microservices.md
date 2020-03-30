---
title: Introduzione ai microservizi in AzureIntroduction to microservices on Azure
description: Panoramica dell'importanza di compilare applicazioni cloud con un approccio basato su microservizi per lo sviluppo di applicazioni moderne e descrizione dell'uso di Azure Service Fabric come piattaforma per raggiungere questo obiettivo.
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: af18a6cb45808c0af5ec2782a3fd2100e3b7bf99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750627"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Perché usare un approccio ai microservizi per la creazione di applicazioniWhy use a microservices approach to building applications

Per gli sviluppatori di software, il factoring di un'applicazione in parti componenti non è una novità. In genere, viene usato un approccio a più livelli, con un archivio back-end, una logica di business di livello intermedio e un'interfaccia utente front-end. Ciò che *è* cambiato negli ultimi anni è che gli sviluppatori stanno creando applicazioni distribuite per il cloud.

Ecco alcune mutevoli esigenze aziendali:

* Un servizio costruito e gestito su larga scala per raggiungere i clienti in nuove aree geografiche.
* Distribuzione più rapida di funzionalità e funzionalità per rispondere alle richieste dei clienti in modo agile.
* Utilizzo delle risorse migliorato per ridurre i costi.

Queste esigenze aziendali incidono sul *modo* di compilare le applicazioni.

Per altre informazioni sull'approccio di Azure ai microservizi, vedere [Microservizi: una rivoluzione delle applicazioni basata sul cloud.](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)

## <a name="monolithic-vs-microservices-design-approach"></a>Approccio di progettazione monolitico e microservizi

Le applicazioni si evolvono con il trascorrere del tempo. Le applicazioni che hanno maggior successo si evolvono diventando utili per le persone. Le applicazioni non riuscite non si evolvono e alla fine sono deprecate. Ecco la domanda: quanto sai sulle tue esigenze oggi e quali saranno in futuro? Si supponga, ad esempio, che si stia creando un'applicazione di reporting per un reparto della società. Si è certi che l'applicazione si applica solo all'interno dell'ambito della vostra azienda e che i rapporti non saranno conservati a lungo. Il tuo approccio sarà diverso da quello di, ad esempio, la creazione di un servizio che fornisce contenuti video a decine di milioni di clienti.

A volte, ottenere qualcosa fuori dalla porta come prova di concetto è il fattore guida. Si sa che l'applicazione può essere riprogettata in un secondo momento. Non ha senso ingegnerizzare qualcosa che non viene mai usato. D'altra parte, quando le aziende costruiscono per il cloud, l'aspettativa è la crescita e l'utilizzo. Crescita e scala sono imprevedibili. Vogliamo creare rapidamente un prototipo, pur sapendo che siamo su un percorso in grado di gestire il successo futuro. Questo è l'approccio di avvio snello: compilare, misurare, apprendere e ripetere.

Durante l'era client/server, tendevamo a concentrarci sulla creazione di applicazioni a più livelli utilizzando tecnologie specifiche in ogni livello. Il termine applicazione *monolitica* è emerso per descrivere questi approcci. Le interfacce si trovavano di solito tra i diversi livelli e si usava una progettazione accoppiata più strettamente tra i componenti all'interno di ogni livello. Gli sviluppatori hanno progettato e fattorizzato le classi che sono state compilate in librerie e collegate tra loro in alcuni file eseguibili e DLL.

Un approccio di progettazione monolitica offre vantaggi. Le applicazioni monolitiche sono spesso più semplici da progettare e le chiamate tra i componenti sono più veloci perché queste chiamate sono spesso su comunicazione interprocesso (IPC). Inoltre, ognuno testa un singolo prodotto, che tende ad essere un uso più efficiente delle risorse umane. Lo svantaggio è che c'è un accoppiamento stretto tra strati e non è possibile scalare i singoli componenti. Se hai bisogno di fare correzioni o aggiornamenti, devi aspettare che gli altri completino il loro test. È più difficile essere agili.

I microservizi affrontano questi svantaggi e sono più strettamente in linea con i requisiti aziendali precedenti. Ma hanno anche benefici e passività. I vantaggi dei microservizi consistono nel fatto che ognuno incapsula solitamente funzionalità aziendali più semplici, che è possibile incrementare o ridurre, testare, distribuire e gestire in modo indipendente. Uno dei vantaggi importanti di un approccio ai microservizi è che i team sono guidati più da scenari aziendali che dalla tecnologia. I team più piccoli sviluppano un microservizio basato su uno scenario del cliente e utilizzano le tecnologie che desiderano utilizzare.

In altre parole, l'organizzazione non deve necessariamente standardizzarsi sulla tecnologia per mantenere applicazioni di microservizi. I singoli team proprietari dei servizi possono procedere come meglio credono, a seconda dell'esperienza del team stesso o della soluzione più appropriata al problema da risolvere. In pratica, è preferibile un set di tecnologie consigliate, ad esempio un particolare archivio NoSQL o un framework di applicazioni Web.

Lo svantaggio dei microservizi è che è necessario gestire entità più separate e gestire distribuzioni e controllo delle versioni più complessi. Il traffico di rete tra i microservizi aumenta, così come le latenze di rete corrispondenti. Un sacco di chiacchierone, servizi granulari possono causare un incubo di prestazioni. Senza strumenti che ti aiutino a visualizzare queste dipendenze, è difficile vedere l'intero sistema.

Gli standard consentono di utilizzare l'approccio dei microservizi specificando come comunicare e tollerando solo le operazioni necessarie da un servizio, anziché contratti rigidi. È importante definire questi contratti in anticipo nella progettazione perché i servizi vengono aggiornati in modo indipendente l'uno dall'altro. Un'altra descrizione coniata per la progettazione con approccio basato su microservizi è "SOA (Service Oriented Architecture) con granularità fine".

***Nella sua forma più semplice, l'approccio di progettazione dei microservizi riguarda una federazione di servizi disaccoppiata, con modifiche indipendenti a ciascuno e standard concordati per la comunicazione.***

Man mano che vengono prodotte più applicazioni cloud, le persone hanno scoperto che questa scomposizione dell'applicazione complessiva in servizi indipendenti incentrati sugli scenari è un approccio migliore a lungo termine.

## <a name="comparison-between-application-development-approaches"></a>Confronto tra approcci allo sviluppo di applicazioni

![Sviluppo di applicazioni per la piattaforma Service Fabric][Image1]

1) Un'applicazione monolitica contiene funzionalità specifiche del dominio ed è in genere suddivisa in livelli funzionali come web, business e dati.

2) Per ridimensionare un'applicazione monolitica, clonarla in più server/macchine virtuali/contenitori.

3) Un'applicazione di microservizi separa le funzionalità in servizi più piccoli distinti.

4) La scalabilità orizzontale di questo approccio basato sui microservizi si ottiene con la distribuzione di ogni servizio in modo indipendente e la creazione di istanze di questi servizi in server/macchine virtuali/contenitori.

La progettazione con un approccio ai microservizi non è appropriata per tutti i progetti, ma si allinea più strettamente con gli obiettivi aziendali descritti in precedenza. Iniziare con un approccio monolitico potrebbe avere senso se sai che avrai la possibilità di rielaborare il codice in un secondo momento in una progettazione di microservizi. In genere si inizia con un'applicazione monolitica e quindi la si suddivide lentamente in fasi, a partire dalle aree funzionali che devono essere maggiormente scalabili o agili.

Quando si utilizza un approccio ai microservizi, si compone l'applicazione di molti servizi di piccole dimensioni. Questi servizi vengono eseguiti in contenitori distribuiti in un cluster di computer. I team più piccoli sviluppano un servizio incentrato su uno scenario e che consente di testare, verificare, distribuire e ridimensionare in modo indipendente ogni servizio in modo che l'intera applicazione possa evolversi.

## <a name="what-is-a-microservice"></a>Definizione di microservizio

Esistono diverse definizioni di microservizi. Ma la maggior parte di queste caratteristiche dei microservizi sono ampiamente accettate:

* Incapsulano uno scenario aziendale o del cliente. Che problema stai risolvendo?
* Sono sviluppati da un piccolo team di progettazione.
* Scritto in qualsiasi linguaggio di programmazione, utilizzando qualsiasi framework.
* Consiste di codice e, facoltativamente, di stato, entrambi con controllo delle versioni, distribuiti e ridimensionati in modo indipendente.
* Interagiscono con altri microservizi tramite interfacce e protocolli ben definiti.
* Dispone di nomi univoci (URL) utilizzati per risolverne la posizione.
* Rimangono coerenti e disponibili in caso di errori.

Per riassumere:

***Le applicazioni di microservizi sono costituite da piccoli servizi rivolti ai clienti, scalabili e sottoposti al controllo delle versioni indipendentemente, che comunicano tra di essi tramite protocolli standard e interfacce ben definite.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Scritto in qualsiasi linguaggio di programmazione, utilizzando qualsiasi linguaggio

Come sviluppatori, vogliamo essere liberi di scegliere un linguaggio o un framework, a seconda delle nostre competenze e delle esigenze del servizio che stiamo creando. Per alcuni servizi, è possibile valutare i vantaggi in termini di prestazioni di C, sopra qualsiasi altra cosa. Per gli altri, la facilità di sviluppo gestito che si ottiene da C , o Java potrebbe essere più importante. In alcuni casi, potrebbe essere necessario usare una libreria partner specifica, una tecnologia di archiviazione dei dati o un metodo per esporre il servizio ai client.

Dopo aver scelto una tecnologia, è necessario considerare la gestione operativa o del ciclo di vita e la scalabilità del servizio.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Consentono di sottoporre al controllo delle versioni, distribuire e ridimensionare indipendentemente codice e stato

Indipendentemente dalla modalità di scrittura dei microservizi, il codice e, facoltativamente, lo stato, devono distribuire, aggiornare e ridimensionare in modo indipendente. Questo problema è difficile da risolvere perché si tratta di vostra scelta di tecnologie. Per la scalabilità, comprendere come partizionare (o partizionare) sia il codice che lo stato è difficile. Quando il codice e lo stato usano tecnologie diverse, che è comune oggi, gli script di distribuzione per il microservizio devono essere in grado di scalarli entrambi. Questa separazione riguarda anche l'agilità e la flessibilità, quindi è possibile aggiornare alcuni dei microservizi senza doverli aggiornare tutti contemporaneamente.

Torniamo al nostro confronto degli approcci monolitici e microservizi per un momento. Questo diagramma mostra le differenze negli approcci all'archiviazione dello stato:This diagram shows the differences in the approaches to storing state:

#### <a name="state-storage-for-the-two-approaches"></a>Archiviazione dello stato per i due approcci

![Archiviazione dello stato della piattaforma Service Fabric][Image2]

***L'approccio monolitico, a sinistra, ha un unico database e livelli di tecnologie specifiche.***

***L'approccio dei microservizi, a destra, contiene un grafico dei microservizi interconnessi in cui lo stato è in genere limitato al microservizio e vengono utilizzate varie tecnologie.***

In un approccio monolitico, l'applicazione usa in genere un database singolo. Il vantaggio di utilizzare un database è che si trova in un'unica posizione, che lo rende facile da distribuire. Ogni componente può avere una tabella singola per l'archiviazione del relativo stato. La parte più difficile riguarda la necessità che il team separi scrupolosamente lo stato. Inevitabilmente, si sarà tentati di aggiungere una colonna a una tabella cliente esistente, eseguire un join tra le tabelle e creare dipendenze a livello di archiviazione. In questo caso, non sarà possibile ridimensionare i singoli componenti.

Con l'approccio dei microservizi, ogni servizio gestisce e archivia il proprio stato. Ogni servizio è responsabile di ridimensionare sia il codice che lo stato insieme, in modo da soddisfare le richieste del servizio. Uno svantaggio è che quando è necessario creare visualizzazioni, o query, dei dati dell'applicazione, è necessario eseguire query su più archivi di stato. Questo problema viene in genere risolto da un microservizio separato che crea una visualizzazione in una raccolta di microservizi. Se è necessario eseguire più query estemporanee sui dati, è consigliabile scrivere i dati di ogni microservizio in un servizio di data warehousing per l'analisi offline.

I microservizi sono sottoposti a controllo delle versioni. È possibile che versioni diverse di un microservizio vengano eseguite affiancate. Una versione più recente di un microservizio potrebbe non riuscire durante un aggiornamento e deve essere eseguito il rollback a una versione precedente. Il controllo delle versioni è utile anche per i test A/B, in cui utenti diversi sperimentano versioni diverse del servizio. Ad esempio, è comune aggiornare un microservizio per un set specifico di clienti per testare nuove funzionalità prima di implementarlo in modo più ampio.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interagiscono con altri microservizi tramite interfacce ben definite e protocolli.

Negli ultimi 10 anni sono state pubblicate informazioni approfondite che descrivono i modelli di comunicazione nelle architetture orientate ai servizi. Di solito la comunicazione tra servizi usa un approccio REST con i protocolli HTTP e TCP e XML o JSON come formato di serializzazione. Dal punto di vista dell'interfaccia, si tratta di adottare un approccio di web design. Ma nulla dovrebbe impedirti di utilizzare protocolli binari o i tuoi formati di dati. Basta essere consapevoli del fatto che le persone avranno un tempo più difficile utilizzare i microservizi se questi protocolli e formati non sono apertamente disponibili.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Hanno un nome (URL) univoco usato per risolvere il percorso

Il microservizio deve essere indirizzabile ovunque venga eseguito. Se stai pensando alle macchine e a quale è in esecuzione un particolare microservizio, le cose possono andare male rapidamente.

Così come il DNS risolve un URL particolare in un computer specifico, il microservizio deve avere un nome univoco per consentire l'individuazione della sua posizione attuale. I microservizi necessitano di nomi indirizzabili indipendenti dall'infrastruttura in cui sono in esecuzione. Ciò implica che vi sia un'interazione tra il modo in cui il servizio viene distribuito e il modo in cui viene individuato perché è necessario un registro del servizio. Quando si verifica un errore in un computer, il servizio Registro di sistema deve indicare dove è stato spostato il servizio.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Rimangono coerenti e disponibili in caso di errori.

Affrontare gli errori imprevisti è uno dei problemi più difficili da risolvere, specialmente in un sistema distribuito. Gran parte del codice che scriviamo come sviluppatori è per la gestione delle eccezioni. Durante i test, dedichiamo anche la maggior parte del tempo alla gestione delle eccezioni. Il processo è più complesso rispetto alla scrittura di codice per gestire gli errori. Cosa succede quando si verifica un errore nel computer in cui è in esecuzione il microservizio? È necessario rilevare il guasto, che è un problema difficile da solo. Ma è anche necessario riavviare il microservizio.

Per garantire la disponibilità, un microservizio deve essere resiliente agli errori e in grado di riavviarlo in un altro computer. Oltre a questi requisiti di resilienza, i dati non devono andare persi e i dati devono rimanere coerenti.

La resilienza è difficile da raggiungere quando si verificano errori durante l'aggiornamento di un'applicazione. Il microservizio, che interagisce con il sistema di distribuzione, non deve essere ripristinato. Deve determinare se è possibile continuare a passare alla versione più recente o ripristinare una versione precedente per mantenere uno stato coerente. È necessario considerare alcune domande, ad esempio se sono disponibili abbastanza macchine per continuare a procedere e come ripristinare le versioni precedenti del microservizio. Per prendere queste decisioni, è necessario il microservizio per generare informazioni sull'integrità.

### <a name="reports-health-and-diagnostics"></a>Segnalano integrità e diagnostica

Potrebbe sembrare ovvio, ed è spesso trascurato, ma un microservizio deve segnalare l'integrità e la diagnostica. In caso contrario, si dispone di poche informazioni sulla sua salute dal punto di vista delle operazioni. Correlare gli eventi di diagnostica in un set di servizi indipendenti e gestire le differenze di orario dei computer per comprendere l'ordine degli eventi è difficile. Nello stesso modo in cui si interagisce con un microservizio usando protocolli e formati dati concordati, è necessaria una standardizzazione della modalità di registrazione delle informazioni sull'integrità e degli eventi di diagnostica che, alla fine, si traduce in un archivio di eventi che possono essere visualizzati e su cui si possono eseguire query. Con un approccio ai microservizi, diversi team devono concordare un singolo formato di registrazione. È necessario un approccio coerente alla visualizzazione degli eventi di diagnostica nell'applicazione nel suo complesso.

L'integrità è diversa dalla diagnostica. Per integrità si intende la segnalazione dello stato corrente da parte del microservizio per consentire l'esecuzione di azioni appropriate. Un esempio efficace riguarda l'interazione con i meccanismi di aggiornamento e distribuzione per assicurare la disponibilità. Anche se un servizio potrebbe essere attualmente non integro a causa di un arresto anomalo del processo o il riavvio del computer, il servizio potrebbe essere ancora operativo. L'ultima cosa di cui hai bisogno è peggiorare la situazione avviando un aggiornamento. L'approccio migliore consiste nell'esaminare prima o concedere il tempo necessario per il ripristino del microservizio. Gli eventi di integrità di un microservizio consentono di prendere decisioni informate e favoriscono in effetti la creazione di servizi con funzionalità di riparazione automatica.

## <a name="guidance-for-designing-microservices-on-azure"></a>Linee guida per la progettazione di microservizi in AzureGuidance for designing microservices on Azure

Visitare il Centro architettura di Azure per istruzioni sulla [progettazione e la creazione di microservizi in Azure.](https://docs.microsoft.com/azure/architecture/microservices/)

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric come piattaforma di microservizi

Azure Service Fabric è emerso quando Microsoft è passata dalla distribuzione di prodotti "in box", che in genere erano monolitici, alla distribuzione di servizi. L'esperienza di creazione e gestione di servizi di grandi dimensioni, come Il database SQL di Azure e Azure Cosmos DB, ha modellato Service Fabric.The experience of building and operating large services, like Azure SQL Database and Azure Cosmos DB, shaped Service Fabric. La piattaforma si è evoluta nel tempo con l'adozione di altri servizi. Service Fabric doveva poter essere eseguito non solo in Azure, ma anche autonomamente nelle distribuzioni di Windows Server.

***L'obiettivo di Service Fabric è quello di risolvere i problemi difficili della creazione e dell'esecuzione di un servizio e di utilizzare le risorse dell'infrastruttura in modo efficiente, in modo che i team possano risolvere i problemi aziendali utilizzando un approccio ai microservizi.***

Service Fabric consente di creare applicazioni che usano un approccio basato sui microservizi offrendo:

* Piattaforma con servizi di sistema per distribuire, aggiornare e trovare servizi, rilevare e riavviare i servizi con problemi, inoltrare messaggi, gestire lo stato e monitorare l'integrità.
* Possibilità di distribuire applicazioni in esecuzione in contenitori o come processi. Service Fabric è una struttura per la gestione di contenitori e processi.
* API di programmazione produttive che consentono di creare applicazioni come microservizi: [ASP.NET Core, Reliable Actors e Reliable Services](service-fabric-choose-framework.md). Ad esempio, è possibile ottenere informazioni su integrità e diagnostica o sfruttare la disponibilità elevata predefinita.

***Service Fabric è indipendente da come si crea il servizio ed è possibile usare qualsiasi tecnologia. Ma fornisce API di programmazione incorporate che semplificano la creazione di microservizi.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrazione di applicazioni esistenti a Service Fabric

Service Fabric consente di riutilizzare il codice esistente e modernizzarlo con nuovi microservizi. Ci sono cinque fasi per la modernizzazione dell'applicazione, e si può iniziare e fermarsi in qualsiasi fase. Le fasi sono:

1) Iniziare con un'applicazione monolitica tradizionale.  
2) Migrare. Usare contenitori o file eseguibili guest per ospitare codice esistente in Service Fabric.Use containers or guest executables to host existing code in Service Fabric.  
3) Modernizzare. Aggiungere nuovi microservizi insieme al codice containerizzato esistente.  
4) Innovare. Suddividere l'applicazione monolitica in microservizi in base alle esigenze.  
5) Trasformare le applicazioni in microservizi. Trasforma le applicazioni monolitiche esistenti o crea nuove applicazioni greenfield.

![Migrazione ai microservizi][Image3]

Ricorda, puoi *iniziare e fermarti in una di queste fasi.* Non è necessario passare alla fase successiva. 

Diamo un'occhiata agli esempi per ognuna di queste fasi.

**Migrare**  
Per due motivi, molte aziende stanno eseguendo la migrazione di applicazioni monolitiche esistenti in contenitori:For two reasons, many companies are migrating existing monolithic applications into containers:

* Riduzione dei costi, dovuta al consolidamento e alla rimozione di hardware esistente o a causa di applicazioni in esecuzione a densità più elevata.
* Un contratto di distribuzione coerente tra sviluppo e operazioni.

La riduzione dei costi è semplice. In Microsoft, molte applicazioni esistenti vengono containerizzate, portando a milioni di dollari di risparmi. Una distribuzione coerente è più difficile da valutare, ma altrettanto importante. Ciò significa che gli sviluppatori possono scegliere le tecnologie più adatte, ma le operazioni accetteranno un solo metodo per la distribuzione e la gestione delle applicazioni. Riduce le operazioni di dover affrontare la complessità del supporto di tecnologie diverse senza costringere gli sviluppatori a sceglierne solo alcuni. Essenzialmente, ogni applicazione viene inserita in immagini di distribuzione autonome.

Per molte organizzazioni il processo termina qui. Hanno già i vantaggi dei contenitori e Service Fabric offre l'esperienza di gestione completa, tra cui distribuzione, aggiornamenti, controllo delle versioni, rollback e monitoraggio dell'integrità.

**Modernizzare**  
La modernizzazione è l'aggiunta di nuovi servizi insieme al codice containerizzato esistente. Se si intende scrivere nuovo codice, è consigliabile eseguire piccoli passaggi lungo il percorso dei microservizi. Ciò potrebbe significare l'aggiunta di un nuovo endpoint dell'API REST o di una nuova logica di business. In questo modo, si inizia il processo di creazione di nuovi microservizi e si pratica lo sviluppo e la distribuzione di essi.

**Innovare**  
Un approccio basato su microservizi è in grado di gestire eventuali cambiamenti nelle esigenze aziendali. In questa fase, è necessario decidere se iniziare a dividere l'applicazione monolitica in servizi o innovare. Un esempio classico è quando un database che si sta utilizzando come coda del flusso di lavoro diventa un collo di bottiglia di elaborazione. Se il numero di richieste del flusso di lavoro aumenta è necessario suddividere in scala il carico di lavoro. Prendere quella particolare parte dell'applicazione che non è scalabilità, o che deve essere aggiornato più frequentemente, e dividerlo come un microservizio e innovare.

**Trasformare le applicazioni in microservizi**  
In questa fase, l'applicazione è completamente composta (o suddivisa in) microservizi. Per raggiungere questo punto, hai fatto il percorso dei microservizi. Puoi iniziare da qui, ma per farlo senza una piattaforma di microservizi che ti aiuti richiede un investimento significativo.

### <a name="are-microservices-right-for-my-application"></a>I microservizi sono appropriati per l'applicazione in uso?

È possibile. In Microsoft, man mano che sempre più team iniziavano a creare per il cloud per motivi aziendali, molti di loro si sono resi conto dei vantaggi di un approccio simile a un microservizio. Bing, ad esempio, utilizza microservizi da anni. Per altri team, l'approccio basato su microservizi era una novità. I team riscontravano problemi difficili da risolvere ed estranei alle loro aree di competenza principali. Questo è il motivo per cui Service Fabric ha guadagnato trazione come tecnologia per i servizi di costruzione.

L'obiettivo di Service Fabric è ridurre la complessità della creazione di applicazioni di microservizio in modo da non dover passare attraverso altrettante riprogettazioni costose. Iniziare con piccole soluzioni, ridimensionarle secondo le esigenze, deprecare servizi, aggiungerne di nuovi ed evolversi secondo le esigenze di utilizzo del cliente. È anche evidente che devono essere ancora risolti molti altri problemi per rendere i microservizi più accessibili per la maggior parte degli sviluppatori. I contenitori e il modello di programmazione attore sono esempi di piccoli passi in questa direzione. Siamo certi che emergeranno altre innovazioni per semplificare un approccio ai microservizi.

## <a name="next-steps"></a>Passaggi successivi

* [Microservizi: una rivoluzione delle applicazioni basata sul cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Azure Architecture Center: Building microservices on Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* [Procedure consigliate per applicazioni e cluster di Azure Service Fabric](service-fabric-best-practices-overview.md)
* [Panoramica della terminologia di Service Fabric](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
