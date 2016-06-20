<properties
   pageTitle="Informazioni sui microservizi | Microsoft Azure"
   description="Panoramica dell'importanza di compilare applicazioni cloud con un approccio basato su microservizi per lo sviluppo di applicazioni moderne e descrizione dell'uso di Azure Service Fabric come piattaforma per raggiungere questo obiettivo."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="bscholl"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/24/2016"
   ms.author="mfussell"/>

# Perché usare un approccio ai microservizi per la compilazione di applicazioni
Per gli sviluppatori di software non c'è nulla di nuovo nel modo di considerare la fattorizzazione di un'applicazione nelle parti che la compongono. È il paradigma centrale della programmazione orientata a oggetti, delle astrazioni software e della componentizzazione. Attualmente questa fattorizzazione tende ad assumere la forma di classi e interfacce tra le librerie condivise e i livelli della tecnologia, in genere tramite un approccio a più livelli con un archivio back-end, una logica di business di livello intermedio e un'interfaccia utente front-end. Ciò che *è* cambiato negli ultimi anni è il fatto che gli sviluppatori creano applicazioni distribuite per il cloud in base alle esigenze aziendali.

Ecco quali sono le mutevoli esigenze aziendali:

- Necessità di compilare e gestire un servizio su vasta scala per poter entrare in contatto con un maggior numero di clienti, ad esempio in nuove aree geografiche o senza doverlo distribuire presso i clienti.
- Recapito più rapido di funzionalità e caratteristiche per poter rispondere alle richieste dei clienti in modo agile.
- Utilizzo delle risorse migliorato per ridurre i costi.

Queste esigenze aziendali incidono sul *modo* di compilare le applicazioni.

Per altre informazioni sull'approccio di Azure ai microservizi, leggere [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservizi: una rivoluzione delle applicazioni basata sul cloud).

## Approccio alla progettazione monolitico o con microservizi
Tutte le applicazioni si evolvono con il trascorrere del tempo. Le applicazioni che hanno maggior successo si evolvono diventando utili per le persone. Le applicazioni che invece non hanno successo non si evolvono e alla fine vengono deprecate. La domanda diventa quindi: quanto si sa attualmente dei propri requisiti e come si pensa che possano svilupparsi in futuro? Ad esempio, se si sta compilando un'applicazione per la creazione di report per un reparto e si è certi che rimarrà entro l'ambito dell'azienda e che i report avranno breve durata, la scelta dell'approccio sarà diversa rispetto alla compilazione di un servizio per il recapito di contenuto video a decine di milioni di clienti. A volte pubblicare qualcosa come modello di verifica è un fattore determinante, avendo la consapevolezza che l'applicazione potrà essere riprogettata in seguito. La sovraprogettazione di qualcosa che non si userà mai ha poco senso. È il solito compromesso della progettazione. D'altra parte, quando le aziende discutono della creazione per il cloud, l'aspettativa diventa crescita e utilizzo. Il problema è che la crescita e la scalabilità sono imprevedibili. Si vorrebbe poter creare prototipi velocemente, ma allo stesso tempo sapere di essere sulla strada giusta per gestire il successo futuro. Questo è l'approccio a un avvio snello: compilare, misurare, apprendere, reiterare.

Nell'era del client/server si tendeva a concentrarsi sulla compilazione di applicazioni a più livelli, usando tecnologie specifiche in ogni livello. Per questo tipo di applicazioni eta stato coniato il termine applicazione "monolitica". Le interfacce si trovavano tendenzialmente tra i diversi livelli e di solito tra i componenti all'interno di ogni livello si usava una progettazione più strettamente accoppiata. Gli sviluppatori progettavano e scomponevano in fattori, con classi compilate in librerie e collegate tra di esse in pochi file EXE e DLL. Questo approccio alla progettazione monolitica presenta dei vantaggi. Può essere più semplice progettare e avere chiamate più rapide tra i componenti, perché spesso avvengono tramite IPC. Inoltre, ognuno testa un singolo prodotto e questo approccio è tendenzialmente più efficiente a livello di risorse e persone. Gli aspetti negativi sono costituiti dal fatto che l'applicazione è strettamente accoppiata all'interno dei livelli e non è possibile ridimensionare i singoli componenti e se occorre eseguire correzioni o aggiornamenti, si deve attendere che altri completino i test. Diventa quindi più difficile essere agili.

I microservizi risolvono questi aspetti negativi e si allineano più strettamente ai requisiti aziendali descritti sopra, ma anch'essi hanno lati positivi e negativi. I vantaggi dei microservizi sono da ricercare nel fatto che di solito ognuno incapsula funzionalità aziendali più semplici, che le relative risorse possono essere aumentate o ridotte e che possono essere testati, distribuiti e gestiti indipendentemente. Un importante vantaggio dell'approccio ai microservizi riguarda la tendenza dei team a essere influenzati più dagli scenari aziendali che dalla tecnologia, cosa che invece l'approccio a più livelli incoraggiava. In pratica, questo significa che team più piccoli sviluppano un microservizio sulla base di uno scenario del cliente, usando le tecnologie che preferiscono. In altre parole, l'organizzazione non deve necessariamente standardizzarsi sulla tecnologia per mantenere i componenti monolitici. Inoltre, i singoli team proprietari dei servizi possono procedere come meglio credono, a seconda dell'esperienza del team stesso o della soluzione più appropriata al problema che il servizio prova a risolvere. In pratica, è preferibile avere un set di tecnologie consigliate, ad esempio un particolare archivio NoSQL o un framework di applicazioni Web. Il lato negativo dei microservizi si evidenzia con la gestione di un maggior numero di entità distinte, la gestione di distribuzioni più complesse e il controllo delle versioni, oltre alla maggiore quantità di traffico tra i microservizi e le corrispondenti latenze di rete. La presenza di una quantità di servizi frammentati e molto granulari è una premessa allo svilupparsi di problemi di prestazioni. Senza strumenti che facilitino la visualizzazione di queste dipendenze è difficile avere una "visione" dell'intero sistema. In ultima analisi, sono gli standard che consentono il funzionamento dell'approccio ai microservizi, definendo la modalità di comunicazione e accettando solo ciò che è necessario ottenere da un servizio, invece di rigidi contratti. È importante definire questi aspetti fin dall'inizio della progettazione, perché i servizi verranno aggiornati indipendentemente gli uni dagli altri. Un'altra descrizione coniata per la progettazione di un approccio ai microservizi è "SOA con granularità fine".


***Al livello più semplice, l'approccio alla progettazione di microservizi è una federazione di servizi disaccoppiata, con modifiche singolarmente indipendenti e standard concordati per la comunicazione.***


Mentre vengono prodotte sempre più app per cloud, più persone scoprono che questa scomposizione dell'app complessiva in servizi indipendenti per scenari specifici è un approccio più a lungo termine migliore.
## Confronto tra approcci allo sviluppo di applicazioni

![Sviluppo di applicazioni per la piattaforma Service Fabric][Image1]

1. Un'app monolitica include funzionalità specifiche del dominio e normalmente è divisa per livelli di funzionalità, ad esempio Web, business e dati.

2. Per la scalabilità di un'app monolitica, occorre clonarla in più server/VM/contenitori.

3. Un'applicazione di microservizi separa le funzionalità in servizi più piccoli distinti.

4. La scalabilità orizzontale di questo approccio avviene con la distribuzione di ogni servizio indipendentemente e la creazione di istanze in server/VM/contenitori.


La progettazione di un approccio ai microservizi non è un toccasana per tutti i progetti, ma offre un maggiore allineamento con gli obiettivi di business descritti in precedenza. Iniziare con un approccio monolitico può anche essere accettabile se si sa che in seguito si potrà rielaborare il codice in una progettazione di microservizi, se necessario. Più comunemente, si inizia con un'app monolitica e quindi la si suddivide lentamente in fasi, a partire dalle aree funzionali che devono essere maggiormente scalabili o agili.

Per riepilogare, l'approccio ai microservizi consiste nel comporre un'applicazione di molti servizi più piccoli eseguiti in contenitori distribuiti in un cluster di computer. Ogni servizio è sviluppato da un team più piccolo focalizzato su uno scenario e in cui ogni servizio viene testato, sottoposto al controllo delle versioni, distribuito e ridimensionato indipendentemente, per consentire l'evoluzione complessiva dell'applicazione.

## Definizione di microservizio

Esistono molte definizioni diverse dei microservizi e una ricerca in Internet restituisce molte risorse valide, che forniscono definizioni e punti di vista personalizzati. Molte delle caratteristiche dei microservizi seguenti sono tuttavia ampiamente concordate:

- Incapsulano uno scenario aziendale o del cliente. Qual è il problema da risolvere?
- Sono sviluppati da un piccolo team di progettazione.
- Possono essere scritti in qualsiasi linguaggio di programmazione e usare qualsiasi framework.
- Sono costituiti da codice e facoltativamente da uno stato, sottoposti al controllo delle versioni, distribuiti e ridimensionati in maniera indipendente.
- Interagiscono con altri microservizi tramite interfacce ben definite e protocolli.
- Hanno nomi univoci (URL) che possono essere usati per risolvere la propria posizione.
- Rimangono coerenti e disponibili in caso di errori.

Queste informazioni possono essere sintetizzate come indicato di seguito:

***Le applicazioni di microservizi sono costituite da piccoli servizi rivolti ai clienti, scalabili e sottoposti al controllo delle versioni indipendentemente, che comunicano tra di essi tramite protocolli standard e interfacce ben definite.***


Sono stati descritti i primi due punti nella sezione precedente, ora verranno estesi e chiariti gli altri.

### Possono essere scritti in qualsiasi linguaggio di programmazione e usare qualsiasi framework.
Gli sviluppatori dovrebbero essere liberi di scegliere il linguaggio o il framework che preferiscono, a seconda delle competenze o delle esigenze del servizio. In alcuni servizi si potrebbero valutare prima di tutto i vantaggi di C++ in termini di prestazioni, mentre in altri potrebbe essere più importante la facilità di sviluppo gestito in C# o Java. In alcuni casi potrebbe essere necessaria una libreria di terze parti, una tecnologia di archiviazione dati o mezzi per l'esposizione del servizio a client specifici.

Dopo aver scelto una tecnologia, occorre considerare la gestione operativa o del ciclo di vita e il ridimensionamento del servizio.

### Consentono di sottoporre al controllo delle versioni, distribuire e ridimensionare indipendentemente codice e stato  

In qualsiasi modo si decida di scrivere i propri microservizi, ognuno dovrà essere distribuito, aggiornato e ridimensionato indipendentemente per quanto riguarda il codice e, facoltativamente, per lo stato. Questo è in effetti uno dei problemi più impegnativi da risolvere, perché si riduce alla scelta delle tecnologie e, per il ridimensionamento, alla comprensione della modalità di partizionamento del codice e dello stato. Quando il codice e lo stato usano tecnologie distinte, che sembra essere la tendenza attuale, gli script di distribuzione del microservizio devono poter supportare il ridimensionamento di entrambi. Questo aspetto ha a che fare anche con l'agilità e la flessibilità con cui si possono aggiornare alcuni microservizi senza doverli aggiornare tutti contemporaneamente. Tornando per un momento all'approccio monolitico rispetto ai microservizi, il diagramma seguente mostra le differenze di approccio all'archiviazione dello stato.

#### Archiviazione dello stato tra stili dell'applicazione
![Archiviazione dello stato della piattaforma Service Fabric][Image2]

***A sinistra è illustrato l'approccio monolitico con un database singolo e livelli di tecnologie specifiche.***

***A destra è illustrato l'approccio ai microservizi con un grafico dei microservizi interconnessi, dove lo stato è solitamente con ambito specifico del microservizio e vengono usate molteplici tecnologie.***

In un approccio monolitico è in genere presente un database singolo usato dall'applicazione. Il vantaggio è dato dal fatto che si tratta di una posizione singola e quindi facilita la distribuzione. Ogni componente può avere una tabella singola per l'archiviazione del relativo stato. La parte più difficile riguarda la necessità che il team separi scrupolosamente lo stato, perché si è inevitabilmente tentati di aggiungere semplicemente una nuova colonna a una tabella esistente del cliente, creare un join tra le tabelle e in genere creare dipendenze a livello di archiviazione. In questo caso, non sarà possibile ridimensionare i singoli componenti. Nell'approccio ai microservizi ogni servizio gestisce e archivia il proprio stato ed è quindi responsabile del ridimensionamento del codice e dello stato per soddisfare le esigenze del servizio. Il lato negativo si manifesta quando occorre creare visualizzazioni, o query, dei dati dell'applicazione, perché in questo caso si dovranno eseguire query su tutte le diverse istanze di archivio stati. In genere, questo problema si risolve con un microservizio separato che crea una visualizzazione della raccolta di microservizi. Se è necessario eseguire più query ad-hoc sui dati, per ogni microservizio si dovrà considerare la scrittura dei dati in un servizio di data warehousing per le analisi offline.


Il controllo delle versioni è specifico della versione del microservizio distribuita. È obbligatorio per consentire l'implementazione e l'esecuzione di diverse versioni affiancate. Il controllo delle versioni è rivolto agli scenari in cui l'aggiornamento a una versione più recente di un microservizio non riesce ed è necessario eseguire il rollback a una versione precedente. L'altro scenario per il controllo delle versioni riguarda l'esecuzione dei test di tipo A/B in cui utenti diversi provano versioni diverse del servizio. Ad esempio, è normale aggiornare un microservizio per consentire a un set specifico di clienti di testare nuove funzionalità prima di implementarlo più diffusamente. Dopo la gestione del ciclo di vita dei microservizi, si esaminerà la comunicazione tra di essi.


### Interagiscono con altri microservizi tramite interfacce ben definite e protocolli.

Su questo argomento non c'è molto da aggiungere, se non suggerire la lettura della vasta letteratura su Service Oriented Architecture (SOA) pubblicata negli ultimi 10 anni, che in buona parte è incentrata sui modelli di comunicazione. Attualmente, l'argomento si riduce in genere all'uso di un approccio REST con i protocolli HTTP e TCP e XML o JSON come formato di serializzazione. Da un punto di vista dell'interfaccia, si tratta di adottare l'approccio alla progettazione Web. Nulla vieta tuttavia di usare protocolli binari o formati dati personalizzati. Occorre però essere preparati al fatto che le persone avranno maggiori difficoltà a usare i microservizi se sono disponibili pubblicamente

### Hanno un nome (URL) univoco che può essere usato per risolvere il percorso.

Come è stato ribadito più volte, tenere presente che l'approccio ai microservizi è come il Web. Come il Web il microservizio deve essere indirizzabile ovunque sia in esecuzione. Se si inizia a pensare ai computer e in quale è in esecuzione un microservizio specifico, presto inizieranno le difficoltà. Così come il DNS risolve un URL particolare in un computer specifico, il microservizio deve avere un nome univoco per individuare dove sia attualmente in esecuzione. I microservizi devono avere nomi indirizzabili che li rendano indipendenti dall'infrastruttura in cui sono in esecuzione. Naturalmente, questo implica la presenza di un'interazione tra la modalità di distribuzione del servizio e il modo in cui viene individuato, perché deve essere disponibile un registro servizi. In modo analogo, deve esserci un'interazione tra il momento in cui si verifica un errore del computer e cosa accade al microservizio, in modo che dal servizio Registro applicazioni risulti dov'è attualmente in esecuzione. Questo introduce l'argomento seguente sulla resilienza e la coerenza.

### Rimangono coerenti e disponibili in caso di errori.

Affrontare gli errori imprevisti è uno dei problemi più difficili da risolvere, specialmente in un sistema distribuito. La maggior parte del codice scritto dagli sviluppatori riguarda la gestione delle eccezioni ed è anche l'ambito in cui si impiega più tempo per i test. Il problema è tuttavia più complesso della scrittura di codice per gestire gli errori. Cosa accade infatti in caso di errore del computer in cui è in esecuzione il microservizio? Non è solo necessario rilevare l'errore del microservizio, un problema di per sé difficile, ma serve anche una soluzione per riavviarlo. Un microservizio deve essere resiliente in caso di errori e avere la capacità di riavvio, spesso in un altro computer per motivi di disponibilità. Anche in questo caso, tutto si riduce allo stato salvato per conto del microservizio, ovvero da dove può recuperarlo e se può riavviarsi correttamente. In altre parole, è necessario che sia resiliente a livello di calcolo, ovvero il processo viene riavviato, oltre che a livello di stato o dati, cioè non si sono verificate perdite di dati e questi rimangono coerenti.

I problemi di resilienza si complicano in altri scenari, ad esempio quando gli errori si verificano durante l'aggiornamento di un'applicazione. Il microservizio, che interagisce con il sistema di distribuzione, non solo deve essere ripristinato. Deve anche decidere se può continuare e passare alla versione più recente oppure eseguire il rollback a una versione precedente per mantenere uno stato coerente. Occorre considerare alcune domande, ad esempio se sono disponibili computer sufficienti per continuare e come recuperare le versioni precedenti del microservizio. Per poter prendere queste decisioni, il microservizio deve quindi emettere informazioni sull'integrità.

### Segnalano integrità e diagnostica

Anche se è un concetto apparentemente ovvio e spesso trascurato, è essenziale che il microservizio segnali il proprio stato di integrità e la diagnostica. In caso contrario, saranno disponibili pochi dettagli da un punto di vista operativo. La sfida consiste nel vedere la correlazione degli eventi di diagnostica in un set di servizi indipendenti, dove ognuno esegue la registrazione indipendentemente e gestisce le differenze di orario dei computer per comprendere l'ordine degli eventi. Nello stesso modo in cui si interagisce con un microservizio usando protocolli e formati dati concordati, emerge un'esigenza di standardizzazione della modalità di registrazione delle informazioni sull'integrità e degli eventi di diagnostica che, alla fine, si traduce in un archivio di eventi che possono essere visualizzati e su cui si possono eseguire query. In un approccio ai microservizi è fondamentale che i diversi team concordino l'uso di un singolo formato di registrazione, essendo necessario un approccio coerente per la visualizzazione degli eventi di diagnostica nell'applicazione complessiva.

L'integrità è diversa dalla diagnostica. Per integrità si intende la segnalazione dello stato corrente da parte del microservizio per consentire l'esecuzione di azioni. Le più ovvie riguardano l'interazione con i meccanismi di aggiornamento e distribuzione per assicurare la disponibilità. Ad esempio, un servizio potrebbe non essere attualmente integro a causa dell'arresto anomalo di un processo o del riavvio di un computer che però è ancora operativo. L'ultima cosa da fare è peggiorare la situazione eseguendo un aggiornamento. L'approccio migliore è procedere prima di tutto a un'indagine o attendere il ripristino del microservizio. Gli eventi di integrità di un microservizio consentono quindi allo sviluppatore di prendere decisioni basate su informazioni aggiornate e favorire in effetti la creazione di servizi con funzionalità di riparazione automatica.

## Service Fabric come piattaforma di microservizi

Azure Service Fabric è nata dalla transizione operata da Microsoft dalla fornitura di prodotti preconfezionati, generalmente di tipo monolitico, alla fornitura di servizi. Service Fabric si basa soprattutto sull'esperienza acquisita con la creazione e il funzionamento di servizi di grandi dimensioni, come i database SQL di Azure, DocumentDB e altri servizi di Azure di base. Sono state affrontate in modo completo le esigenze aziendali in termini di ridimensionamento, agilità e team indipendenti, lasciando che la piattaforma si evolvesse nel tempo grazie all'adozione da parte di un numero sempre maggiore di servizi. Era importante che Service Fabric venisse eseguita ovunque, non solo in Azure, ma anche autonomamente nelle distribuzioni di Windows Server.

***Lo scopo di Service Fabric consiste nel risolvere i difficili problemi di compilazione ed esecuzione di un servizio, quali errori, aggiornamenti, utilizzo efficiente delle risorse dell'infrastruttura, per consentire ai team di risolvere i problemi aziendali tramite un approccio ai microservizi.***

Service Fabric offre due vaste aree per facilitare la compilazione di applicazioni con un approccio ai microservizi:

- Una piattaforma costituita da un set di servizi di sistema che si occupano di distribuzioni, aggiornamenti, rilevamento e riavvio di servizi in cui si sono verificati errori, individuazione della posizione in cui i servizi sono attualmente in esecuzione, gestione dello stato, monitoraggio dello stato e così via. Questi servizi di sistema abilitano in effetti molte delle caratteristiche dei microservizi descritte in precedenza.

-  API di programmazione, o framework, che facilitano la compilazione di applicazioni come microservizi. Le API di programmazione fornite sono dette [Reliable Actors e Reliable Services](service-fabric-choose-framework.md). Naturalmente è possibile usare il codice preferito per compilare un microservizio. L'uso di queste API non solo rende il processo più semplice, ma poiché si integrano con la piattaforma a un livello più profondo, forniscono, ad esempio, informazioni su integrità e diagnostica o consentono di sfruttare la disponibilità elevata predefinita.

***Service Fabric è indipendente dalla modalità di compilazione del servizio, quindi è possibile usare qualsiasi tecnologia. Tuttavia offre API di programmazione predefinite che facilitano la compilazione dei microservizi***

### I microservizi sono appropriati per l'applicazione in uso?

È possibile. Si è potuto osservare che molti dei sempre più numerosi team Microsoft incaricati di compilare prodotti per il cloud per motivi aziendali, hanno sfruttato i vantaggi offerti da un approccio orientato ai microservizi. Bing, ad esempio, applica da anni questo approccio nella ricerca. Per altri team si è trattato di un'assoluta novità. Si sono trovati ad affrontare molti problemi difficili che dovevano essere risolti, cosa che non rientrava nelle loro competenze di base. Ecco perché Service Fabric si è affermato come tecnologia preferita per la compilazione di servizi.

L'obiettivo di Service Fabric è ridurre le complessità della compilazione di applicazioni con un approccio ai microservizi, evitando di dover affrontare molte attività di riprogettazione costose. Iniziare con piccole soluzioni, ridimensionarle secondo le esigenze, deprecare servizi, aggiungerne di nuovi ed evolversi secondo le esigenze di utilizzo del cliente, ecco l'approccio. Si è consapevoli del fatto che, in realtà, molti altri problemi devono ancora essere risolti per rendere i microservizi più accessibili per la maggior parte degli sviluppatori. I contenitori e il modello di programmazione basato su attori sono esempi di piccoli passi in questa direzione e sicuramente emergeranno molte innovazioni per rendere tutto questo più facile.
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

* Per altre informazioni:
	* [Panoramica di Service Fabric](service-fabric-overview.md)
    * [Microservizi: una rivoluzione delle applicazioni basata sul cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png

<!---HONumber=AcomDC_0608_2016-->