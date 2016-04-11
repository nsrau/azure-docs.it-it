<properties
   pageTitle="Linee guida di partizionamento di dati | Microsoft Azure"
   description="Indicazioni su come suddividere le partizioni per la gestione e l'accesso separati."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/26/2016"
   ms.author="masashin"/>

# Linee guida di partizionamento di dati

![](media/best-practices-data-partitioning/pnp-logo.png)

## Panoramica

In molte soluzioni su larga scala, i dati vengono suddivisi in partizioni separate per poter essere gestiti e per poter accedervi separatamente. La strategia di partizionamento deve essere scelta attentamente per ottimizzare le prestazioni riducendo al minimo gli effetti negativi. Il partizionamento può aiutare a migliorare la scalabilità, ridurre i conflitti e ottimizzare le prestazioni. Un altro vantaggio offerto dal partizionamento è costituito dalla possibilità di suddividere i dati in base al modello di utilizzo. È possibile ad esempio archiviare i dati meno recenti e usati raramente in un archivio dati più economico.

## Perché la partizione di dati?

La maggior parte dei servizi e delle applicazioni cloud archivia e recupera i dati come parte delle loro operazioni. La progettazione degli archivi dati utilizzati da un'applicazione può avere un impatto significativo sulle prestazioni, sulla velocità effettiva e sulla scalabilità di un sistema. Una tecnica comunemente applicata in sistemi di grandi dimensioni consiste nel suddividere i dati in partizioni separate.

> Il termine _partizionamento_ usato in questa guida si riferisce al processo di suddivisione fisica dei dati in archivi dati separati. Questo processo non è identico al partizionamento di una tabella in SQL Server, che è un concetto diverso.

Il partizionamento dei dati può offrire una serie di vantaggi. Ad esempio, può essere applicato al fine di:

- **Migliorare la scalabilità**. L'aumento delle dimensioni del sistema del singolo database potrebbe causare il raggiungimento di un limite hardware fisico. Se si suddividono i dati in più partizioni, ognuna delle quali ospitata in un server separato, sarà possibile aumentare le dimensioni del sistema quasi all'infinito.
- **Migliorare le prestazioni** Le operazioni di accesso ai dati in ogni partizione vengono eseguite su un volume di dati più piccolo. Se i dati sono partizionati in modo appropriato, il partizionamento può rendere il sistema più efficiente. Le operazioni che interessano più di una partizione possono essere eseguite in parallelo. Ogni partizione può essere situata nell'applicazione che la utilizza per ridurre al minimo la latenza di rete.
- **Migliorare la disponibilità**. La separazione dei dati tra più server consente di evitare singoli punti di errore. Se un server fallisce, o è sottoposto a manutenzione pianificata, solo i dati collocati in quella partizione non sono disponibili. Le operazioni su altre partizioni possono continuare. L'aumento del numero di partizioni riduce l'impatto relativo di un singolo errore del server, riducendo la percentuale di dati che non saranno disponibili. Eseguire la replica di ogni partizione permette di ridurre ulteriormente le probabilità di un singolo errore della partizione singola che interessi le operazioni. Sarà inoltre possibile separare i dati critici per i quali deve essere garantita una disponibilità continua ed elevata dai dati di valore inferiore con minori requisiti di disponibilità, ad esempio i file di log.
- **Migliorare la sicurezza**. A seconda della natura dei dati e della modalità di partizionamento, è possibile separare i dati sensibili e non sensibili in partizioni diverse e, pertanto, in diversi server o archivi dati. La sicurezza può essere ottimizzata in modo specifico per i dati sensibili.
- **Fornire flessibilità operativa**. Il partizionamento offre molte opportunità per operazioni di ottimizzazione, ottimizzazione dell'efficienza amministrativa e riduzione dei costi. È possibile ad esempio definire diverse strategie per la gestione, il monitoraggio, il backup e il ripristino e altre attività amministrative in base all'importanza dei dati in ogni partizione.
- **Combinare l'archivio dati al modello di utilizzo**. Il partizionamento consente a ogni partizione di essere distribuita in un diverso tipo di archivio dati, basato sul costo e le funzionalità incorporate offerte dall’archivio dati. Ad esempio, i dati binari di grandi dimensioni possono essere archiviati in un archivio di dati BLOB, mentre i dati più strutturati possono essere conservati in un database di documenti. Per altre informazioni, vedere [Creazione di una soluzione Polyglot] in Indicazioni su modelli e procedure e [Accesso ai dati per le soluzioni altamente scalabili mediante SQL, NoSQL e persistenza Polyglot] nel sito Web Microsoft.

Alcuni sistemi non implementano il partizionamento perché viene considerato un costo anziché un vantaggio. Motivi comuni per questa spiegazione sono:

- Molti sistemi di archiviazione di dati non supportano i join tra le partizioni e può essere difficile mantenere l'integrità referenziale in un sistema partizionato. Spesso è necessario implementare join e controlli di integrità nel codice dell'applicazione (nel livello di partizionamento), che può comportare I/O aggiuntive e la complessità dell'applicazione.
- La gestione delle partizioni non è sempre un compito facile. In un sistema in cui i dati sono volatili, potrebbe essere necessario ribilanciare periodicamente le partizioni per ridurre i conflitti e le aree sensibili.
- Alcuni strumenti comuni non funzionano ovviamente con dati partizionati.

## Progettazione di partizioni

I dati possono essere partizionati in modi diversi: orizzontalmente, verticalmente o dal punto di vista funzionale. La strategia scelta dipende dal motivo del partizionamento dei dati e dai requisiti delle applicazioni e dei servizi che utilizzeranno i dati.

> [AZURE.NOTE] Gli schemi di partizionamento descritti in questa guida sono spiegati in modo indipendente dalla tecnologia di archiviazione dati sottostante. Possono essere applicati a molti tipi di archivi dati, inclusi i database relazionali e NoSQL.

### Strategie di partizionamento

Le tre strategie più comuni per il partizionamento dei dati sono:

- **Il partizionamento orizzontale** (spesso chiamato _sharding_). In questa strategia ogni partizione è un archivio dati indipendente, ma tutte le partizioni hanno lo stesso schema. Ogni partizione è denominata _shard_ e contiene un sottoinsieme specifico dei dati, ad esempio tutti gli ordini per un set specifico di clienti in un'applicazione di e-commerce.
- **Il partizionamento verticale**. In questa strategia ogni partizione contiene un sottoinsieme dei campi per gli elementi nell'archivio dati. I campi sono suddivisi in base ai loro modello di utilizzo. I campi usati di frequente ad esempio possono essere collocati in una partizione verticale, mentre i campi usati raramente possono essere collocati in un'altra partizione.
- **Partizionamento funzionale**. In questa strategia i dati vengono aggregati in base alla loro modalità di utilizzo da parte di ogni contesto limitato nel sistema. Ad esempio, un sistema di e-commerce che implementa funzioni di business separate per la fatturazione e la gestione dell'inventario dei prodotti può archiviare i dati delle fatture in una partizione e i dati di inventario dei prodotti in un'altra.

È importante notare che le tre strategie descritte di seguito possono essere combinate. Non si escludono a vicenda ed è consigliabile prenderle tutte in considerazione durante la progettazione di uno schema di partizionamento. Ad esempio, si potrebbe dividere i dati in partizioni e quindi utilizzare il partizionamento verticale per suddividere ulteriormente i dati in ogni partizione. Analogamente, i dati in una partizione funzionale possono essere suddivisi in partizioni che possono anche essere partizionate verticalmente.

Tuttavia, i diversi requisiti di ogni strategia possono generare diversi problemi di conflitto. È necessario valutare e bilanciare tutti questi elementi quando si progetta uno schema di partizionamento che soddisfi gli obiettivi di prestazioni di elaborazione dei dati complessivi del sistema. Nelle sezioni seguenti queste strategie vengono illustrate più in dettaglio.

### Partizionamento orizzontale (sharding)

La figura 1 mostra una panoramica del partizionamento orizzontale o sharding. In questo esempio, i dati di inventario del prodotto sono divisi in partizioni in base alla chiave di prodotto. Ogni partizione contiene i dati per un intervallo contiguo di chiavi di partizione (A-G e H-Z), organizzati in ordine alfabetico.

![Partizionamento orizzontale (sharding) dei dati in base a una chiave di partizione](media/best-practices-data-partitioning/DataPartitioning01.png)

_Figura 1. Partizionamento orizzontale (sharding) dei dati in base a una chiave di partizione_

Il partizionamento orizzontale consente di distribuire il carico su più computer, riducendo i conflitti e migliorando le prestazioni. È possibile scalare orizzontalmente il sistema aggiungendo ulteriori partizioni che vengono eseguite su altri server.

Il fattore più importante quando si implementa questa strategia di partizionamento è la scelta della chiave di partizionamento orizzontale. Può essere difficile modificare la chiave quando il sistema è in esecuzione. La chiave deve garantire che i dati siano partizionati in modo che il carico di lavoro sia il più possibile uniforme tra le partizioni.

Si noti che le diverse partizioni non devono contenere volumi simili di dati. È invece importante bilanciare il numero di richieste. Alcune partizioni possono essere molto grandi, ma i singoli elementi possono essere oggetto di un numero ridotto di operazioni di accesso. Altre partizioni invece possono essere più piccole, ma è possibile che ogni elemento venga usato più frequentemente. È inoltre importante garantire che una singola partizione non superi i limiti di scalabilità (in termini di capacità e risorse di elaborazione) dell'archivio dati usato per ospitare la partizione.

Se si usa uno schema di partizionamento, evitare di creare aree sensibili (o partizioni critiche) che possono influire sulle prestazioni e sulla disponibilità. Ad esempio, usare un hash di un identificatore di cliente anziché la prima lettera del nome di un cliente impedirà la distribuzione sbilanciata che risulterebbe dall'uso delle lettere iniziali, comuni e meno comuni. Questa è una tecnica tipica che consente di distribuire i dati in modo più uniforme tra le partizioni.

Scegliere una chiave di partizionamento che riduca al minimo eventuali esigenze future di suddividere le partizioni di grandi dimensioni in parti più piccole, unire partizioni di piccole dimensioni in partizioni di dimensioni maggiori o modificare lo schema che descrive i dati archiviati in un set di partizioni. Queste operazioni possono richiedere molto tempo e potrebbe essere necessario disconnettere una o più partizioni.

Se viene eseguita la replica delle partizioni, potrebbe essere possibile mantenere alcune delle repliche online mentre altre vengono suddivise, unite o riconfigurate. È possibile tuttavia che il sistema limiti le operazioni che possono essere eseguite sui dati in tali partizioni durante la riconfigurazione. Ad esempio, i dati nelle repliche possono essere contrassegnati come di sola lettura per limitare l'ambito di eventuali incoerenze che possono verificarsi durante la ristrutturazione delle partizioni.

> Per altre informazioni e istruzioni sulla maggior parte di queste considerazioni e tecniche consigliate per la progettazione di archivi dati che implementano il partizionamento orizzontale, vedere [Modello di partizionamento orizzontale]

### Partizionamento verticale

L'utilizzo più comune per il partizionamento verticale è la riduzione dei costi di I/O e delle prestazioni associati con il recupero degli elementi utilizzati più frequentemente. La Figura 2 mostra un esempio di partizionamento verticale. Nell'esempio, le diverse proprietà di ogni elemento di dati vengono mantenute in partizioni diverse. Una partizione contiene i dati cui viene eseguito l'accesso più frequentemente, inclusi nome, descrizione e informazioni sul prezzo dei prodotti. Un'altra partizione contiene il volume a magazzino e la data dell'ultimo ordine.

![Partizionamento verticale dei dati in base al modello di utilizzo](media/best-practices-data-partitioning/DataPartitioning02.png)

_Figura 2. Partizionamento verticale dei dati in base al modello di utilizzo_

Nell'esempio, l'applicazione ricerca regolarmente il nome del prodotto, la descrizione e il prezzo quando visualizza i dettagli dei prodotti ai clienti. Poiché la data e il livello disponibile sono due elementi che vengono comunemente utilizzati insieme, quando il prodotto è stato ordinato dal produttore sono stati conservati in una partizione separata.

Questo schema di partizionamento offre il vantaggio che i dati relativamente lenti (nome prodotto, descrizione e prezzo) sono separati dai dati più dinamici (livello disponibile e ultima data ordinata). Per un'applicazione può risultare vantaggioso memorizzare nella cache i dati lenti usati di frequente.

Un altro scenario tipico per questa strategia di partizionamento è ottimizzare la sicurezza dei dati sensibili. È possibile ad esempio archiviare i numeri delle carte di credito e i corrispondenti numeri di verifica di sicurezza delle carte in partizioni separate.

Il partizionamento verticale può inoltre ridurre la quantità di accessi simultanei richiesta per i dati.

> Il partizionamento verticale opera a livello di entità all'interno di un archivio dati, normalizzando parzialmente un'entità per suddividerla da un elemento _di grandi dimensioni_ a una raccolta di elementi._ristretti_. È particolarmente adatto per gli archivi di dati orientati alla colonna, ad esempio HBase e Cassandra. Se è improbabile modificare i dati in una raccolta di colonne, è possibile utilizzare archivi di colonne in SQL Server.

### Partizionamento funzionale

Per i sistemi in cui è possibile identificare un contesto delimitato per ogni area di attività distinta o per ogni servizio nell'applicazione, il partizionamento funzionale fornisce una tecnica per migliorare le prestazioni di accesso ai dati e di isolamento. Un altro utilizzo comune del partizionamento funzionale è la separazione dei dati di sola scrittura dai dati di sola lettura usati per la creazione di report. La figura 3 mostra una panoramica del partizionamento funzionale in cui i dati di inventario sono separati dai dati del cliente.

![Partizionamento funzionale dei dati in base al contesto limitato o al sottodominio](media/best-practices-data-partitioning/DataPartitioning03.png)

_Figura 3. Partizionamento funzionale dei dati in base al contesto limitato o al sottodominio_

Questa strategia di partizionamento può contribuire a ridurre i conflitti di accesso ai dati in diverse parti del sistema.

## Progettazione di partizioni per la scalabilità

È importante considerare le dimensioni e il carico di lavoro di ogni partizione e bilanciarle in modo che i dati siano distribuiti per ottenere la massima scalabilità. È tuttavia necessario partizionare i dati in modo che non superino i limiti di ridimensionamento della singola partizione di un archivio.

Quando si progettano le partizioni per la scalabilità, attenersi alla seguente procedura:

1. Analizzare l'applicazione per comprendere i modelli di accesso ai dati, ad esempio la dimensione del set di risultati restituito da ogni query, la frequenza di accesso, la latenza intrinseca e i requisiti di elaborazione di calcolo sul lato server. In molti casi, è possibile che alcune entità principali richiederanno la maggior parte delle risorse di elaborazione.
2. Usare questa analisi per determinare gli obiettivi di scalabilità attuali e futuri, ad esempio la dimensione dei dati e il carico di lavoro. Distribuire i dati nelle partizioni in modo da soddisfare l'obiettivo di scalabilità. Nella strategia di partizionamento orizzontale è importante scegliere la chiave di partizionamento appropriata per assicurarsi che la distribuzione sia uniforme. Per altre informazioni, vedere il [Modello di partizionamento].
3. È necessario assicurarsi che le risorse disponibili in ogni partizione siano sufficienti per gestire i requisiti di scalabilità in termini di dimensioni dei dati e di velocità. Il nodo che ospita una partizione, ad esempio, potrebbe imporre un limite hardware sulla quantità di spazio di archiviazione, sulla potenza di elaborazione o sulla larghezza di banda di rete che fornisce. Se i requisiti di elaborazione e archiviazione dati rischiano di superare tali limiti potrebbe essere necessario perfezionare la strategia di partizionamento o dividere ulteriormente i dati. Ad esempio, un approccio di scalabilità potrebbe essere quello di separare i dati di registrazione dalle funzionalità principali dell'applicazione. A tale scopo si usano archivi dati separati per impedire che i requisiti totali di archiviazione superino il limite di scalabilità del nodo. Se il numero totale di archivi dati supera il limite del nodo, può essere necessario usare nodi di archiviazione separati.
4. Monitorare il sistema durante l'uso per verificare che i dati vengano distribuiti come previsto e che le partizioni siano in grado di gestire il carico imposto. È possibile che l'utilizzo non corrisponda all'utilizzo previsto dall'analisi. In tal caso, potrebbe essere necessario ribilanciare le partizioni. Se l'operazione non riesce, potrebbe essere necessario riprogettare alcune parti del sistema per ottenere il bilanciamento richiesto.

Si noti che alcuni ambienti cloud allocano risorse in termini di limiti di infrastruttura. Assicurarsi che tali limiti forniscano spazio sufficiente per una crescita anticipata del volume di dati, in termini di archiviazione dei dati, potenza di elaborazione e larghezza di banda.

Ad esempio, se si usa l'archiviazione tabelle di Azure, una partizione occupata potrebbe richiedere più risorse rispetto a quelli disponibili per una singola partizione per gestire le richieste. Esiste un limite al volume di richieste che possono essere gestite da una singola partizione in un determinato periodo di tempo. Per altre informazioni, vedere la pagina [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure] nel sito Web Microsoft.

 In questo caso, è possibile che la partizione debba essere ripartizionata in modo da ripartire il carico. Se la dimensione totale o la velocità effettiva delle tabelle supera la capacità di un account di archiviazione, può essere necessario creare ulteriori account di archiviazione e suddividere le tabelle tra questi. Se il numero di account di archiviazione supera il numero di account disponibili per una sottoscrizione, può essere necessario usare più sottoscrizioni.

## Progettazione di partizioni per le prestazioni delle query

Le prestazioni delle query possono spesso essere aumentate usando set di dati più piccoli ed eseguendo query parallele. Ogni partizione deve contenere una piccola parte dell'intero set di dati. La riduzione del volume può migliorare le prestazioni delle query. Tuttavia, il partizionamento non è un'alternativa per la progettazione e la configurazione di un database in modo appropriato. Ad esempio, assicurarsi di disporre degli indici necessari richiesti se si utilizza un database relazionale.

Quando si progettano le partizioni per le prestazioni delle query, attenersi alla seguente procedura:

1. Esaminare i requisiti dell'applicazione e delle prestazioni:
	- Usare i requisiti aziendali per determinare le query critiche che devono sempre essere eseguite rapidamente.
	- Monitoraggio del sistema per identificare qualsiasi query eseguita lentamente.
	- Stabilire quali le query vengono eseguite più frequentemente. Una singola istanza di ogni query potrebbe avere un costo minimo, ma il consumo cumulativo di risorse potrebbe essere significativo. Può essere vantaggioso separare i dati recuperati da queste query in una partizione distinta o anche in una cache.
2. Partizionare i dati che causano un rallentamento delle prestazioni:
	- Limitare le dimensioni di ogni partizione in modo che il tempo di risposta della query sia compreso nel target.
	- Progettare la chiave di partizionamento in modo che l'applicazione possa trovare facilmente la partizione, se si implementa il partizionamento orizzontale. In questo modo la query non deve analizzare ogni partizione.
	- Considerare la posizione di una partizione. Se possibile, provare a mantenere i dati nelle partizioni geograficamente vicine alle applicazioni e gli utenti che vi accedono.
3. Se un'entità dispone di requisiti relativi alle prestazioni di velocità effettiva e della query, utilizzare il partizionamento funzionale in base a tale entità. Se tale entità non soddisfa i requisiti, è necessario applicare anche il partizionamento orizzontale. Nella maggior parte dei casi è sufficiente una singola strategia di partizionamento, ma in alcuni casi è preferibile combinare entrambe le strategie.
4. È possibile usare query asincrone che vengono eseguite in parallelo tra le partizioni per migliorare le prestazioni.

## Progettazione di partizioni per la disponibilità

Il partizionamento dei dati può migliorare la disponibilità delle applicazioni garantendo che l'intero set di dati non costituisca un singolo punto di errore e che i singoli sottoinsiemi del set di dati possano essere gestiti in modo indipendente. Replicare le partizioni che contengono dati critici può inoltre migliorare la disponibilità.

Quando si progettano e implementano partizioni, considerare i seguenti fattori che influiscono sulla disponibilità:

- **Criticità dei dati nelle operazioni aziendali**. Alcuni dati potrebbero includere informazioni aziendali critiche, ad esempio dettagli di fatture o transazioni bancarie. Altri dati possono includere dati operativi meno critici, ad esempio i file di log, le tracce di prestazioni e così via. Dopo aver individuato tutti i tipi di dati, prendere in considerazione:
	- L'archiviazione dei dati critici in partizioni a disponibilità elevata con un piano di backup appropriato.
	- La definizione di gestione separata e meccanismi di monitoraggio o procedure per le diverse criticità di ogni set di dati. Il posizionamento di dati che hanno lo stesso livello di criticità nella stessa partizione in modo da poter eseguire il backup con una frequenza appropriata. Le partizioni contenenti dati per le transazioni bancarie, ad esempio, potrebbero richiedere backup più frequenti rispetto alle partizioni che contengono informazioni di registrazione o di traccia.
- **Gestione delle singole partizioni**. La progettazione di partizioni per supportare la manutenzione e la gestione indipendenti offre diversi vantaggi. Ad esempio:
	- Se una partizione fallisce, può essere recuperata in modo indipendente, senza interferire con le istanze delle applicazioni che accedono ai dati di altre partizioni.
	- Il partizionamento dei dati per aree geografiche può consentire attività di manutenzione pianificate che vengono eseguite in determinate fasce orarie per ogni posizione. Assicurarsi che le partizioni non siano troppo grandi per evitare che le operazioni di manutenzione pianificata non siano completate durante questo periodo.
- **Replica dei dati critici tra le partizioni**. Questa strategia può migliorare disponibilità e prestazioni, anche se può causare problemi di coerenza. La sincronizzazione delle modifiche apportate ai dati in una partizione in tutte le repliche richiede tempo. Durante questo periodo, le diverse partizioni conterranno valori di dati diversi.

## Informazioni sull'effetto del partizionamento sulla progettazione e sullo sviluppo

L'uso del partizionamento aggiunge complessità alla progettazione e allo sviluppo del sistema. Considerare il partizionamento come parte fondamentale della progettazione del sistema anche se inizialmente il sistema contiene una singola partizione. Se il partizionamento viene considerato in un secondo momento quando il sistema inizia ad accusare problemi di prestazioni e scalabilità, l'operazione risulterà più complessa dal momento che esiste già un sistema in tempo reale da mantenere.

Se si aggiorna il sistema per incorporare il partizionamento in questo ambiente, è necessario modificare la logica di accesso ai dati. Può inoltre essere necessario eseguire la migrazione di grandi quantità di dati esistenti per distribuirli nelle diverse partizioni, spesso mentre gli utenti si aspettano di poter continuare a usare il sistema.

In alcuni casi, il partizionamento non è considerato importante perché il set di dati iniziale è ridotto e può essere facilmente gestito da un singolo server. Ciò può verificarsi in un sistema che non prevede una scalabilità che supera le dimensioni iniziali, ma molti sistemi commerciali necessitano di espandersi se il numero di utenti aumenta. Questa espansione in genere è accompagnata da un aumento delle dimensioni del volume di dati.

È inoltre importante comprendere che il partizionamento non è sempre una funzione di archivi dati di grandi dimensioni. Ad esempio, un archivio dati di piccole dimensioni potrebbe essere utilizzato molto frequentemente da centinaia di client simultanei. Il partizionamento dei dati in questa situazione può aiutare a ridurre i conflitti e a migliorare la velocità effettiva.

Quando si progetta un schema di partizionamento dei dati, tenere presente quanto riportato di seguito:

- **Dove possibile, mantenere i dati per le operazioni di database più comuni insieme in ogni partizione per ridurre al minimo le operazioni di accesso ai dati tra partizioni**. Le query tra partizioni possono richiedere più tempo rispetto alle query all'interno di una singola partizione, ma ottimizzare le partizioni per un set di query potrebbe avere effetti negativi su latri set di query. Quando non è possibile evitare l'esecuzione di query tra partizioni, ridurre il tempo necessario per l'esecuzione delle query eseguendo query parallele e aggregando i risultati all'interno dell'applicazione. Questo approccio potrebbe non essere possibile in alcuni casi, ad esempio quando è necessario ottenere da una query un risultato da usare nella query successiva.
- **Se le query usano dati di riferimento relativamente statici, ad esempio tabelle di codici postali o elenchi di prodotti, considerare la possibilità di eseguire la replica dei dati in tutte le partizioni per ridurre la richiesta di operazioni di ricerca separate in altre partizioni**. Questo approccio può inoltre ridurre la probabilità che i dati di riferimento diventino un set di dati "critici" soggetti a traffico elevato nell'intero sistema. Esiste tuttavia un costo aggiuntivo relativo alla sincronizzazione di tutte le modifiche che potrebbero verificarsi per i dati di riferimento.
- **Dove possibile, ridurre al minimo i requisiti per l'integrità referenziale tra le partizioni verticali e funzionali**. In questi schemi, l'applicazione stessa è responsabile della gestione dell'integrità referenziale tra le partizioni quando i dati vengono aggiornati e utilizzati. Le query che devono unire i dati tra più partizioni vengono eseguite più lentamente delle query che uniscono solo i dati all'interno della stessa partizione, poiché l'applicazione in genere richiede di eseguire query consecutive basate su una chiave e quindi su una chiave esterna. Si consiglia di replicare o de-normalizzare i dati rilevanti. Per ridurre al minimo il tempo di query in cui sono necessari i join tra partizioni, eseguire query parallele nelle partizioni e unire i dati all'interno dell'applicazione.
- **È necessario considerare l'effetto che lo schema di partizionamento potrebbe avere sulla coerenza dei dati tra partizioni.** Valutare se la coerenza assoluta è effettivamente un requisito. Al contrario, un approccio comune nel cloud consiste nell'implementare la coerenza finale. I dati in ogni partizione vengono aggiornati separatamente e la logica dell'applicazione garantisce che tutti gli aggiornamenti vengano completati correttamente. La logica gestisce inoltre le incoerenze che possono essere generate da query sui dati durante l'esecuzione di un'operazione coerente. Per altre informazioni sull'implementazione di coerenza finale, vedere le informazioni relative alla [coerenza dei dati].
- **È necessario considerare come le query individuano la partizione corretta**. Se una query deve analizzare tutte le partizioni per individuare i dati richiesti, ci sarà un impatto significativo sulle prestazioni, anche usando più query parallele. Le query usate con strategie di partizionamento verticale e funzionale possono naturalmente specificare le partizioni. Tuttavia, quando si usa il partizionamento orizzontale (sharding), l'individuazione di un elemento può essere difficile poiché ogni partizione ha lo stesso schema. Una tipica soluzione di partizionamento orizzontale consiste nel mantenere una mappa che può essere utilizzata per cercare il percorso della partizione per elementi specifici di dati. Questa mappa può essere implementata nella logica di partizionamento orizzontale dell'applicazione o gestita dall'archivio dati se supporta il partizionamento orizzontale trasparente.
- **Quando si usa una strategia di partizionamento orizzontale, prendere in considerazione la possibilità di ribilanciare periodicamente le partizioni**. Ciò consente di distribuire uniformemente i dati secondo le dimensioni e il carico di lavoro per ridurre al minimo le aree sensibili, ottimizzare le prestazioni delle query e aggirare le limitazioni di archiviazione fisiche. Tuttavia, si tratta di un'attività complessa che spesso richiede l'utilizzo di uno strumento personalizzato o di un processo.
- **La replica di ogni partizione offre ulteriore protezione dagli errori**. Se una singola replica ha esito negativo, le query possono essere indirizzate verso una copia di lavoro.
- **Se si raggiungono i limiti fisici di una strategia di partizionamento, potrebbe essere necessario estendere la scalabilità a un livello diverso**. Ad esempio, se il partizionamento è a livello di database, può essere necessario individuare o eseguire la replica delle partizioni in più database. Se il partizionamento è già a livello di database e i limiti fisici sono un problema, può essere necessario individuare o eseguire la replica delle partizioni in più account di hosting.
- **Evitare transazioni che accedono ai dati in più partizioni**. Alcuni archivi di dati implementano la coerenza transazionale e l'integrità per le operazioni che modificano i dati, ma solo quando i dati si trovano in una singola partizione. Se è necessario supporto transazionale tra più partizioni, probabilmente sarà necessario implementare questo come parte della logica dell'applicazione poiché la maggior parte dei sistemi di partizionamento non forniscono il supporto nativo.

Tutti gli archivi dati richiedono una gestione operativa e il monitoraggio dell'attività. Le attività variano dal caricamento dei dati, backup e ripristino dei dati, riorganizzazione dei dati e la garanzia che il sistema funziona in modo corretto ed efficiente.

Considerare i seguenti fattori che influiscono sulla gestione operativa:

- **Come implementare attività di gestione e operative appropriate quando i dati sono partizionati**. Queste attività possono includere il backup e il ripristino, l'archiviazione dei dati, il monitoraggio del sistema e altre attività amministrative. Mantenere la coerenza logica durante le operazioni di backup e ripristino, ad esempio, può essere una sfida.
- **Come caricare i dati in più partizioni e aggiungere nuovi dati provenienti da altre origini**. Alcuni strumenti e utilità potrebbero non supportare le operazioni di dati partizionati, quale il caricamento dei dati nella partizione corretta. Ciò significa che potrebbe essere necessario creare o ottenere nuovi strumenti e utilità.
- **Come archiviare ed eliminare i dati a intervalli regolari**. Per impedire una crescita eccessiva delle partizioni, è necessario archiviare ed eliminare i dati a intervalli regolari, ad esempio ogni mese. Può essere necessario trasformare i dati in base a uno schema di archiviazione differente.
- **Come individuare i problemi di integrità dei dati**. Considerare la possibilità di eseguire regolarmente un processo per individuare eventuali problemi di integrità dei dati, ad esempio dati in una partizione che fanno riferimento a informazioni mancanti in un'altra partizione. Il processo potrebbe o tentare di correggere automaticamente questi problemi o generare un avviso a un operatore per risolvere i problemi manualmente. Ad esempio, in un'applicazione di e-commerce, le informazioni sugli ordini possono essere conservate in una partizione, mentre le voci che costituiscono ogni ordine possono essere conservate in un'altra partizione. Il processo di inserimento di un ordine dovrà aggiungere i dati in entrambe le partizioni. Se questo processo fallisce, potrebbe essere archiviate voci per le quali non esiste alcun ordine corrispondente.

Le tecnologie di archiviazione di dati diversi in genere forniscono le proprie funzionalità per il supporto di partizionamento. Nelle sezioni seguenti sono descritte le opzioni che vengono implementate dagli archivi dati comunemente usati dalle applicazioni Azure. Le sezioni includono anche considerazioni sulla progettazione di applicazioni che possono usare al meglio queste funzionalità.

## Strategie di partizionamento per Database SQL Azure

Il Database di SQL Azure è un database relazionale as-a-service che viene eseguito nel cloud. È basato su Microsoft SQL Server. Un database relazionale divide le informazioni in tabelle e ogni tabella contiene informazioni sulle entità come una serie di righe. Ogni riga include colonne che contengono i dati per i singoli campi di un'entità. La pagina [Informazioni sul database SQL] del sito Web Microsoft contiene informazioni dettagliate sulla creazione e l'uso di database SQL.

## Partizionamento orizzontale con database elastico

Un singolo database SQL ha un limite per il volume di dati che può contenere. La velocità effettiva è vincolata da fattori di architettura e dal numero di connessioni simultanee supportate. La funzione Database elastico del database SQL supporta la scalabilità orizzontale per un database SQL. Usando Database elastico è possibile suddividere i dati in partizioni che vengono distribuite in più database SQL. È inoltre possibile aggiungere o rimuovere partizioni in base alla crescita o alla riduzione del volume di dati da gestire. Database elastico consente inoltre di ridurre i conflitti distribuendo il carico sui database.

> [AZURE.NOTE] Il database elastico è attualmente in anteprima a partire da dicembre 2015. La funzione sostituisce le federazioni di database SQL di Azure che verranno ritirate. È possibile eseguire la migrazione delle installazioni di federazioni di database SQL esistenti in Database elastico usando l'[Utilità di migrazione di federazioni]. In alternativa, è possibile implementare il proprio meccanismo di partizionamento orizzontale se lo scenario non si presta naturalmente alle funzionalità offerte da Database elastico.

Ogni partizione viene implementata come database SQL. Una partizione può contenere più di un set di dati, denominato _shardlet_. Ogni database include metadati che descrivono gli shardlet contenuti al suo interno. Un shardlet può essere un singolo elemento dati o può essere un gruppo di elementi che condividono la stessa chiave shardlet. Ad esempio, se si stanno partizionando dati in un'applicazione multi-tenant, la chiave shardlet potrebbe essere l'ID tenant e tutti i dati per un tenant specificato saranno conservati come parte dello shardlet stesso. I dati per altri tenant saranno conservati in shardlet diversi.

È compito del programmatore associare un set di dati a una chiave shardlet. Un database SQL separato gestisce il mapping globale delle partizioni e contiene un elenco dei database (partizioni) che includono l'intero sistema e le informazioni sugli shardlet di ogni database. Un'applicazione client che accede ai dati si connette prima al database di gestione del mapping globale per ottenere una copia del mapping delle partizioni in cui sono elencate le partizioni e gli shardlet e la memorizza nella cache locale.

Quindi, l'applicazione utilizza queste informazioni per indirizzare le richieste alla partizione appropriata. Questa funzionalità è nascosta dietro una serie di API contenute nella libreria client del database elastico del database SQL di Azure, disponibile come pacchetto NuGet. La pagina [Panoramica sulle funzionalità di database elastico] nel sito Web Microsoft offre un'introduzione più completa al database elastico.

> [AZURE.NOTE] È possibile replicare il database di gestione del mapping globale delle partizioni per ridurre la latenza e migliorare la disponibilità. Se si implementa il database usando uno dei piani tariffari Premium è possibile configurare la replica geografica attiva per copiare continuamente i dati in database situati in diverse aree geografiche. Creare una copia del database in ogni area geografica in cui si trovano gli utenti. Configurare quindi l'applicazione per connettersi a tale copia per ottenere la mappa delle partizioni.

> Un approccio alternativo consiste nell'usare la sincronizzazione dati SQL di Azure o una pipeline di Azure Data Factory per replicare il database di gestione del mapping delle partizioni nelle aree geografiche. Il modulo di replica viene eseguito periodicamente e risulta più appropriato se il mapping della partizione viene modificato raramente. Inoltre, il database di gestione del mapping delle partizioni non deve essere creato usando un piano tariffario Premium.

Il database elastico offre due schemi per la mappature di dati per gli shardlet e per archiviarli in partizioni:

- Un **mapping delle partizioni di tipo elenco** descrive l'associazione tra una singola chiave e uno shardlet. Ad esempio, in un sistema multi-tenant, i dati per ogni tenant possono essere associati a una chiave univoca e archiviati in un proprio shardlet. Per garantire la riservatezza e l'isolamento, ovvero per impedire a un tenant di esaurire le risorse di archiviazione dati disponibili per altri tenant, ogni shardlet può essere bloccato all'interno della propria partizione.

![Uso di un mapping delle partizioni di tipo elenco per l'archiviazione dei dati dei tenant in partizioni separate](media/best-practices-data-partitioning/PointShardlet.png)

_Figura 4. Uso di un mapping delle partizioni di tipo elenco per l'archiviazione dei dati dei tenant in partizioni separate_

- Un **mapping delle partizioni di tipo intervallo** descrive l'associazione tra un set di valori di chiavi contigue e uno shardlet. Nell'esempio di multi-tenant descritto in precedenza, come alternativa all'implementazione di shardlet dedicati, è possibile raggruppare i dati per un set di tenant (ognuno con la propria chiave) all'interno di uno stesso shardlet. Questo schema è meno costoso del primo poiché i tenant condividono le risorse di archiviazione dati ma crea un rischio di riduzione della privacy e dell'isolamento dei dati.

![Uso di un mapping delle partizioni di tipo intervallo per l'archiviazione dei dati per un intervallo di tenant in una partizione](media/best-practices-data-partitioning/RangeShardlet.png)

_Figura 5. Uso di un mapping delle partizioni di tipo intervallo per l'archiviazione dei dati per un intervallo di tenant in una partizione_

Si noti che una singola partizione può contenere i dati per shardlet diversi. Ad esempio, è possibile usare gli shardlet dell'elenco per archiviare i dati per i diversi tenant non contigui nella stessa partizione. È inoltre possibile usare contemporaneamente shardlet di intervallo e di elenco nella stessa partizione, anche se verranno indirizzati tramite mapping diversi nel database di gestione del mapping globale delle partizioni. Il database di gestione del mapping globale delle partizioni può contenere più mapping delle partizioni. La figura 6 descrive questo approccio.

![Implementazione di più mapping delle partizioni](media/best-practices-data-partitioning/MultipleShardMaps.png)

_Figura 6. Implementazione di più mapping delle partizioni_

Lo schema di partizionamento implementato può avere un impatto significativo sulle prestazioni del sistema. Può influire inoltre sulla velocità con cui le partizioni devono essere aggiunte o rimosse o la velocità con cui i dati devono essere ripartizionati tra le partizioni. Quando si usa Database elastico per partizionare i dati, tenere presente quanto riportato di seguito:

- Raggruppare i dati usati insieme nella stessa partizione ed evitare operazioni di accesso ai dati contenuti in più partizioni. Tenere presente che con Database elastico una partizione è un database SQL indipendente e che il database SQL di Azure non supporta i join tra database che devono essere eseguiti sul lato client. È importante ricordare anche che nel database SQL di Azure i vincoli di integrità referenziale, i trigger e le stored procedure presenti in un database non possono fare riferimento a oggetti presenti in un altro database. Per questa ragione, non progettare un sistema con dipendenze tra le partizioni. Un database SQL può, tuttavia, contenere tabelle che contengono copie dei dati di riferimento usati di frequente da query e altre operazioni. Queste tabelle non devono necessariamente appartenere a uno shardlet specifico. La replica dei dati tra partizioni elimina la necessità di unire dati che si estendono in più database. In teoria, tali dati devono essere statici o lenti per ridurre al minimo lo sforzo di replica e ridurre le probabilità di diventare obsoleti.

	> [AZURE.NOTE] Anche se il database SQL non supporta i join tra database, l'API Database elastico consente di eseguire query in più partizioni. Queste query possono eseguire in modo trasparente l'iterazione attraverso i dati contenuti in tutti gli shardlet cui viene fatto riferimento in un mapping delle partizioni. L'API Database elastico suddivide le query tra partizioni in una serie di singole query, una per ogni database, e quindi unisce i risultati. Per altre informazioni, vedere la pagina [Esecuzione di query su più partizioni] nel sito Web Microsoft.

- I dati archiviati in shardlet che appartengono alla stessa mappa di partizione devono avere lo stesso schema. Ad esempio, non creare un elenco di mappe di partizionamento che puntano ad alcuni shardlet contenenti i dati del tenant e ad altri shardlet contenenti informazioni sul prodotto. Questa regola non viene applicata dal database elastico, ma la gestione dei dati e l'esecuzione di query diventa molto complessa se ogni shardlet ha uno schema diverso. Nell'esempio precedente, un'ottima soluzione consiste nel creare due mapping delle partizioni di tipo elenco: uno che fa riferimento a dati dei tenant e un altro che punta alle informazioni sul prodotto. Si tenga presente che i dati appartenenti a diversi shardlet possono essere archiviati nella stessa partizione.

	> [AZURE.NOTE] La funzionalità di query tra partizioni dell'API del database elastico dipende da ogni shardlet nella mappa di partizione che contiene lo stesso schema.

- Le operazioni transazionali sono supportate solo per i dati contenuti all'interno della stessa partizione e non in più partizioni. Le transazioni possono estendere gli shardlet in quanto parti della stessa partizione. Pertanto, la logica di business deve eseguire transazioni, archiviare i dati nella partizione stessa oppure implementare la coerenza finale. Per altre informazioni, vedere le informazioni sulla [coerenza dei dati].
- Posizionare le partizioni vicino agli utenti che accedono ai dati in tali partizioni, ovvero posizionarle in base all'area geografica. Questa strategia consente di ridurre la latenza.
- Evitare di utilizzare una combinazione di partizioni attive (hotspot) e partizioni relativamente inattive. Provare a distribuire uniformemente il carico tra le partizioni. Questo potrebbe richiedere l'hashing delle chiavi degli shardlet.
- In caso di individuazione geografica delle partizioni, assicurarsi che le chiavi con hashing eseguano il mapping di shardlet contenuti in partizioni archiviate vicino agli utenti che accedono a tali dati.
- Attualmente, solo un set limitato di dati SQL è supportato come chiavi shardlet; _int, bigint, varbinary,_ e _uniqueidentifier_. L'istruzione SQL _int_ e _bigint_corrisponde ai tipi di dati in c# _int_ e _long_, e hanno gli stessi intervalli. Il codice SQL _varbinary_ può essere gestito tramite un _Byte_ matrice in c# e il tipo SQL _uniqueidentier_ corrisponde alla classe _Guid_ in .NET Framework.

Come suggerisce il nome, il database elastico consente al sistema di aggiungere e rimuovere partizioni quando il volume dei dati cresce e si riduce. Le API nella libreria client di Database elastico del database SQL di Azure consentono a un'applicazione di creare ed eliminare le partizioni in modo dinamico e di aggiornare in modo trasparente la gestione del mapping delle partizioni. Tuttavia, la rimozione di una partizione è un'operazione distruttiva che richiede anche l'eliminazione di tutti i dati della partizione.

Se un'applicazione deve suddividere una partizione in due partizioni separate o combinare partizioni, Database elastico offre un servizio di suddivisione/unione separato. Questo servizio viene eseguito in un servizio ospitato nel cloud creato dallo sviluppatore ed esegue la migrazione dei dati in modo sicuro tra le partizioni. Per altre informazioni, vedere l'argomento [Scalabilità tramite lo strumento di suddivisione-unione del database elastico] del sito Web Microsoft.

## Strategie di partizionamento per l’archiviazione di Azure

L’archiviazione di Azure fornisce tre astrazioni per la gestione dei dati:

- L'archiviazione tabelle che implementa l'archiviazione di struttura scalabile. Una tabella contiene una raccolta di entità, ognuna delle quali può contenere un set di proprietà e valori.
- L'archiviazione BLOB che offre l'archiviazione di file e oggetti di grandi dimensioni.
- Le code di archiviazione che supportano la messaggistica asincrona affidabile tra applicazioni.

L'archiviazione tabelle e l'archiviazione BLOB sono essenzialmente archivi chiave-valore ottimizzati per contenere rispettivamente dati strutturati e non strutturati. Le code di archiviazione offrono un meccanismo per la creazione di applicazioni scalabili, a regime di controllo libero. L'archiviazione tabelle, l'archiviazione BLOB e le code di archiviazione vengono create all'interno del contesto di un account di archiviazione di Azure. Gli account di archiviazione supportano tre forme di ridondanza:

- L'**archiviazione con ridondanza locale** che mantiene tre copie dei dati all'interno di un singolo data center. Questa forma di ridondanza protegge dagli errori hardware ma non da una situazione di emergenza che interessa l'intero centro dati.
- L'**archiviazione con ridondanza della zona** che mantiene tre copie dei dati distribuiti tra più data center diversi nella stessa area o in due aree geograficamente vicine. Questa forma di ridondanza permette di proteggersi da emergenze che si verificano all'interno di un singolo centro dati, ma non offrono protezione da disconnessioni di rete su larga scala che interessano un'intera area. Si noti che l'archiviazione con ridondanza della zona attualmente è disponibile solo per i BLOB in blocchi.
- L'**archiviazione con ridondanza geografica** che mantiene sei copie dei dati: tre copie in un'unica area (l'area locale) e le altre tre copie in un'area remota. Questa forma di ridondanza fornisce il massimo livello di protezione dalle emergenze.

Microsoft ha pubblicato gli obiettivi di scalabilità per Archiviazione di Azure. Per altre informazioni, vedere la pagina [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure] nel sito Web Microsoft. Attualmente, la capacità dell'account di archiviazione totale non può superare i 500 TB. Sono incluse le dimensioni dei dati contenuti in archiviazione tabelle e archiviazione BLOB, nonché i messaggi in sospeso mantenuti nella coda di archiviazione.

La velocità massima della richiesta, presupponendo un'entità, un BLOB o messaggio di 1 KB, è di 20 KB al secondo. Se è probabile che il sistema superi questi limiti, è consigliabile partizionare il carico tra più account di archiviazione. Ogni sottoscrizione di Azure consente di creare fino a 100 account di archiviazione. Si noti tuttavia che questi limiti possono cambiare nel tempo.

## Partizionamento di archiviazione tabelle di Azure

L'archiviazione tabelle di Azure è un archivio chiave-valore progettato in base al partizionamento. Tutte le entità vengono archiviate in una partizione e le partizioni vengono gestite internamente dall'archiviazione tabelle di Azure. Ogni entità archiviata in una tabella deve fornire una chiave in due parti che comprende:

- **La chiave di partizione**. Si tratta di un valore di stringa che determina la partizione in cui l'archiviazione tabelle di Azure inserisce l'entità. Tutte le entità con la stessa chiave di partizione verranno archiviate nella stessa partizione.
- **La chiave di riga**. Si tratta di un altro valore di stringa che identifica l'entità all'interno della partizione. Tutte le entità all'interno di una partizione vengono ordinate in base al livello lessicale, in ordine crescente, per chiave. La combinazione chiave di partizione/chiave di riga deve essere univoca per ogni entità e non può superare 1 KB di lunghezza.

Il resto dei dati di un'entità è costituito da campi definiti dall'applicazione. Non vengono applicati schemi particolari e ogni riga può contenere un diverso set di campi definiti dall'applicazione. L'unica limitazione è che la dimensione massima di un'entità, incluse le chiavi di riga e di partizione, è attualmente di 1 MB. La dimensione massima di una tabella è di 200 TB, ma questi valori potrebbero cambiare in futuro. Per informazioni aggiornate su questi limiti, vedere la pagina [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure] nel sito Web Microsoft.

Se si sta tentando di archiviare entità che superano questa capacità, è possibile suddividerle in più tabelle. Usare il partizionamento verticale per suddividere i campi in gruppi ai quali con maggior probabilità viene eseguito l'accesso contemporaneamente.

La figura 7 mostra la struttura logica di un esempio di account di archiviazione (dati di Contoso) di un'applicazione di e-commerce fittizia. Gli account di archiviazione contengono tre tabelle: Informazioni sul cliente, Informazioni sul prodotto e Informazioni relative all'ordine. Ogni tabella include più partizioni.

Nella tabella Informazioni sul cliente, i dati vengono partizionati in base alle città in cui si trova il cliente e la chiave di riga contiene l'ID cliente. Nella tabella Informazioni sul prodotto, i prodotti vengono partizionati per categoria e la chiave di riga contiene il numero del prodotto. Nella tabella Informazioni relative all'ordine, gli ordini vengono partizionati in base alla data in cui vengono inseriti e la chiave di riga specifica l'ora di ricezione dell'ordine. Si noti che tutti i dati sono ordinati per chiave di riga in ogni partizione.

![Tabelle e partizioni in un esempio di account di archiviazione](media/best-practices-data-partitioning/TableStorage.png)

_Figura 7. Tabelle e partizioni in un esempio di account di archiviazione_

> [AZURE.NOTE] Archiviazione tabelle di Azure aggiunge inoltre un campo timestamp per ogni entità. Il campo timestamp viene gestito dall'archiviazione tabelle e viene aggiornato ogni volta che l'entità viene modificata e scritta su una partizione. Il servizio di archiviazione tabelle usa questo campo per implementare la concorrenza ottimistica. Ogni volta che un'applicazione scrive un'entità nell'archiviazione tabelle, il servizio di archiviazione tabelle confronta il valore del timestamp dell'entità da scrivere con il valore contenuto nell'archiviazione tabelle. Se i valori sono diversi, significa che un'altra applicazione deve aver modificato l'entità dall'ultimo recupero e l'operazione di scrittura ha esito negativo. Non modificare questo campo nel proprio codice e non specificare un valore per questo campo quando si crea una nuova entità.

Archiviazione tabelle di Azure utilizza la chiave di partizione per determinare come archiviare i dati. Se un'entità viene aggiunta a una tabella con una chiave di partizione precedentemente non usata, l'archiviazione tabelle di Azure crea una nuova partizione per questa entità. Tutte le altre entità con la stessa chiave di partizione verranno archiviate nella stessa partizione.

Questo meccanismo implementa in modo efficace una strategia di scalabilità automatica. Ogni partizione viene archiviata in un singolo server in un data center di Azure in modo che le query che recuperano dati da una singola partizione vengano eseguite rapidamente È tuttavia possibile distribuire diverse partizioni in più server. Inoltre, un singolo server può ospitare più partizioni, se queste partizioni hanno dimensioni limitate.

Quando si progettano le entità per l'archiviazione tabelle di Azure, tenere presente quanto riportato di seguito:

- La selezione dei valori della chiave di partizione e della chiave e di riga deve essere guidata dal modo in cui si accede ai dati. Scegliere una combinazione di chiave di partizionei/chiave di riga che supporti la maggior parte delle query. Le query più efficienti recuperano i dati specificando la chiave di partizione e la chiave di riga. Le query che specificano una chiave di partizione e un intervallo di chiavi di riga possono essere eseguite analizzando una singola partizione. L'operazione risulta relativamente veloce perché i dati seguono l'ordine delle chiavi di riga. Se le query non specificano la partizione da analizzare, è possibile che la chiave di partizione richieda all'archiviazione tabelle di Azure l'analisi di tutte le partizioni di dati.

	> [AZURE.TIP] Se un'entità dispone di una chiave naturale, è consigliabile utilizzarla come chiave di partizione e specificare una stringa vuota come chiave di riga. Se un'entità dispone di una chiave composta che comprende due proprietà, selezionare la proprietà che cambia più lentamente come chiave di partizione e l'altra proprietà come chiave di riga. Se un'entità dispone di più di due proprietà chiave, utilizzare una concatenazione delle proprietà per fornire le chiavi di partizione e di riga.

- Se si eseguono regolarmente query che ricercano i dati usando campi diversi dalle chiavi di partizione e di riga, si consiglia di implementare l'[ITP (Index Table Pattern)].
- Se le chiavi di partizione vengono generate usando una sequenza monotona di aumento o diminuzione (ad esempio "0001", "0002", "0003" e così via) e ogni partizione contiene solo una quantità limitata di dati, l'archiviazione tabelle di Azure può raggruppare fisicamente queste partizioni nello stesso server. Questo meccanismo presuppone che l'applicazione più probabilmente eseguirà query su un intervallo contiguo di partizioni (query di intervallo) ed è ottimizzato per questo caso. Questo approccio può tuttavia causare aree sensibili focalizzate su un singolo server poiché tutti gli inserimenti di nuove entità saranno probabilmente concentrati su una delle due estremità degli intervalli contigui. Permette inoltre di ridurre la scalabilità. Per distribuire il carico in modo più uniforme tra i server, prendere in considerazione la chiave di partizione per rendere più casuale la sequenza.
- Archiviazione tabelle di Azure supporta le operazioni transazionali per le entità che appartengono alla stessa partizione. Ciò significa che un'applicazione può eseguire più operazioni di inserimento, aggiornamento, eliminazione, sostituzione o unione come unità atomica, a condizione che la transazione non includa più di 100 entità e che il payload della richiesta non superi i 4 MB. Le operazioni che si estendono su più partizioni non sono transazionali e potrebbe essere necessario implementare la coerenza finale come descritto nella sezione relativa alla [coerenza dei dati]. Per altre informazioni sull'archiviazione tabelle e le transazioni, visitare la pagina [Esecuzione di transazioni dei gruppi di entità] nel sito Web Microsoft.
- Prestare attenzione alla granularità della chiave di partizione per le ragioni seguenti:
	- Se si usa la stessa chiave di partizione per ogni entità, il servizio di archiviazione tabelle crea una singola partizione di grandi dimensioni che viene mantenuta in un unico server. Questo impedisce di scalabilità orizzontale e concentra il carico su un singolo server. Di conseguenza, questo approccio è adatto solo per sistemi che gestiscono un numero ridotto di entità. Tuttavia, questo approccio garantisce che tutte le entità possano partecipare alle transazioni del gruppo di entità.
	- Se si usa una chiave di partizione univoca per ogni entità, il servizio di archiviazione tabelle crea una partizione separata per ogni entità causando la presenza di un numero elevato di partizioni piccole, a seconda delle dimensioni delle entità. Questo approccio è più scalabile rispetto all'uso di una singola chiave di partizione, ma le transazioni dei gruppi di entità non saranno possibili. Inoltre, le query che recuperano più di un'entità potrebbero implicare la lettura da più di un server. Tuttavia, se l'applicazione esegue query di intervallo, l'utilizzo di una sequenza monotona per generare le chiavi di partizione può ottimizzare le query.
	- La condivisione della chiave di partizione in un sottoinsieme di entità consente di raggruppare le entità correlate nella stessa partizione. È possibile eseguire le operazioni che coinvolgono le entità correlate usando transazioni dei gruppi di entità, mentre le query che recuperano un set di entità correlate possono essere soddisfatte tramite l'accesso a un singolo server.

Per altre informazioni sul partizionamento dei dati nell'archiviazione tabelle di Azure, vedere l'articolo [Guida alla progettazione della tabella di archiviazione di Azure] nel sito Web Microsoft.

## Partizionamento di archiviazione blob di Azure

L'archiviazione BLOB di Azure consente di contenere oggetti binari di grandi dimensioni, attualmente fino a 200 GB per i BLOB in blocchi o 1 TB per i BLOB di pagine. Per informazioni aggiornate, vedere la pagina [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure] nel sito Web Microsoft. Utilizzo dei BLOB in blocchi in scenari come i flussi dove è necessario caricare o scaricare rapidamente grandi volumi di dati. Utilizzo dei BLOB di pagine per le applicazioni che richiedono accesso casuale anziché seriale a parti dei dati.

Ogni blob (blocco o pagina) viene conservato in un contenitore in un account di archiviazione Azure. È possibile usare i contenitori per raggruppare BLOB correlati con gli stessi requisiti di sicurezza, sebbene questo raggruppamento sia logico anziché fisico. All'interno di un contenitore ogni BLOB ha un nome univoco.

Archiviazione BLOB viene partizionata automaticamente in base al nome del blob. Ogni BLOB viene mantenuto nella propria partizione. I BLOB nello stesso contenitore non condividono una partizione. Questa architettura consente all'archiviazione BLOB di Azure di bilanciare il carico tra server in modo trasparente poiché BLOB diversi nello stesso contenitore possono essere distribuiti in server diversi.

Le operazioni di scrittura di un singolo blocco (blob in blocchi) o una pagina (blob di pagine) sono atomiche, ma le operazioni che interessano blocchi, pagine o BLOB non lo sono. Se è necessario garantire la coerenza durante l'esecuzione di operazioni di scrittura in blocchi, pagine e BLOB, sarà necessario estrarre un blocco di scrittura usando un lease del BLOB.

L'archiviazione BLOB di Azure supporta velocità di trasferimento fino a 60 MB al secondo o 500 richieste al secondo per ogni BLOB. Se si prevede di superare tali limiti e i dati BLOB sono relativamente statici, provare a replicare BLOB usando la Rete di distribuzione dei contenuti di Azure. Per altre informazioni, vedere la pagina [Uso della rete CDN per Azure] nel sito Web Microsoft. Per altre indicazioni e considerazioni, vedere [Uso della rete CDN per Azure].

## Partizionamento di code di archiviazione di Azure

Le code di archiviazione di Azure consentono di implementare la messaggistica asincrona tra processi. Un account di archiviazione di Azure può contenere qualsiasi numero di code e ogni coda può contenere qualsiasi numero di messaggi. L'unica limitazione è lo spazio disponibile nell'account di archiviazione. La dimensione massima di un singolo messaggio è di 64 KB. Se sono necessari messaggi di dimensioni superiori, utilizzare Code del bus di servizio di Azure.

Ogni coda di archiviazione ha un nome univoco all'interno dell'account di archiviazione in cui è contenuta. Le code di partizione di Azure sono basate sul nome. Tutti i messaggi per la stessa coda vengono archiviati nella stessa partizione, controllata da un singolo server. Code diverse possono essere gestite da server differenti per bilanciare il carico. L'allocazione di code da server è trasparente alle applicazioni e agli utenti.

 In un'applicazione di grandi dimensioni, non usare la stessa coda di archiviazione per tutte le istanze dell'applicazione, poiché con questo approccio il server che ospita la coda potrebbe diventare un'area sensibile. È consigliabile usare code diverse per le diverse aree funzionali dell'applicazione. Le code di archiviazione di Azure non supportano le transazioni, quindi l’indirizzamento dei messaggi a code diverse dovrebbe avere un impatto minimo sulla coerenza della messaggistica.

Una coda di archiviazione di Azure è in grado di gestire fino a 2000 messaggi al secondo. Se è necessario elaborare i messaggi a una velocità più elevata, è consigliabile creare più code. In un'applicazione globale, ad esempio, è consigliabile creare code di archiviazione separate in account di archiviazione separati per gestire le istanze dell'applicazione in esecuzione in ogni area.

## Strategie di partizionamento per Bus di servizio di Azure

Il bus di servizio di Azure usa un broker messaggi per gestire i messaggi inviati a una coda del bus di servizio o a un argomento. Per impostazione predefinita, tutti i messaggi inviati a una coda o a un argomento vengono gestiti dallo stesso processo di broker messaggi. Questa architettura può inserire un limite alla velocità effettiva complessiva della coda di messaggi. Tuttavia, è anche possibile partizionare una coda o argomento quando viene creato. A tale scopo, impostare la proprietà _EnablePartitioning_ della descrizione della coda o dell'argomento su _true_.

Una coda o un argomento partizionato viene diviso in più frammenti, ognuno dei quali è supportato da un archivio messaggi e da un broker messaggi separato. Il Bus di servizio si assume la responsabilità per la creazione e la gestione di questi frammenti. Quando un'applicazione invia un messaggio a una coda o argomento partizionati, il Bus di servizio assegna il messaggio a un frammento per quella coda o quell’argomento. Quando un'applicazione riceve un messaggio da una coda o da una sottoscrizione, il Bus di servizio controlla ogni frammento per il successivo messaggio disponibile e quindi lo passa all'applicazione per l'elaborazione.

Questa struttura consente di distribuire il carico tra broker messaggi e archivi messaggi aumentando la scalabilità e migliorando la disponibilità. Se il broker messaggi o l'archivio messaggi di un frammento è temporaneamente non disponibile, il bus di servizio può recuperare i messaggi da uno dei frammenti disponibili rimanenti.

Il Bus di servizio assegna un messaggio a un frammento nel modo seguente:

- Se il messaggio appartiene a una sessione, tutti i messaggi con lo stesso valore per la proprietà \_SessionId \_ vengono inviati allo stesso frammento.
- Se il messaggio non appartiene a una sessione, ma il mittente ha specificato un valore per la proprietà _PartitionKey_, tutti i messaggi con lo stesso valore _PartitionKey_ vengono inviati allo stesso frammento.

	> [AZURE.NOTE] Se entrambe le proprietà _SessionId_ e _PartitionKey_ sono specificate, è necessario che siano impostate sullo stesso valore altrimenti il messaggio verrà rifiutato.
- Se le proprietà _SessionId_ e _PartitionKey_ per un messaggio non sono specificate, ma è abilitato il rilevamento dei duplicati, verrà utilizzata la proprietà _MessageId_. Tutti i messaggi con lo stesso _MessageId_ verranno indirizzati allo stesso frammento.
- Se i messaggi non includono una proprietà _SessionId, PartitionKey_ o _MessageId_, il bus di servizio assegna i messaggi a frammenti in modo sequenziale. Se un frammento non è disponibile, il Bus di servizio passerà al successivo. In questo modo, un errore temporaneo nell'infrastruttura di messaggistica non determina l'esito negativo dell'operazione di invio del messaggio.

Quando si decide se e come partizionare una coda o un argomento dei messaggi del bus di servizio, tenere presente quanto riportato di seguito:

- Gli argomenti e le code del Bus di servizio vengono creati nell'ambito di uno spazio dei nomi del Bus di servizio. Il bus di servizio attualmente consente fino a 100 code o argomenti partizionati per spazio dei nomi.
- Ogni spazio dei nomi del bus di servizio impone delle quote per le risorse disponibili, ad esempio il numero di sottoscrizioni per argomento, il numero di trasmissioni e ricezioni simultanee di richieste al secondo e il numero massimo di connessioni simultanee che possono essere stabilite. Queste quote sono documentate nel sito Web Microsoft alla pagina [Quote del bus di servizio]. Se si prevede di superare questi valori, è consigliabile creare ulteriori spazi dei nomi con le proprie code e argomenti e distribuire il lavoro tra questi spazi dei nomi. Ad esempio, in un'applicazione globale, è consigliabile creare spazi dei nomi separati in ogni area e configurare le istanze dell'applicazione per utilizzare le code e argomenti dello spazio dei nomi più vicino.
- I messaggi inviati come parte di una transazione devono specificare una chiave di partizione. La chiave può essere costituita da una proprietà _SessionId, PartitionKey_ o _MessageId_. Tutti i messaggi che vengono inviati come parte della stessa transazione devono specificare la stessa chiave di partizione perché essi devono essere gestiti dallo stesso processo del gestore dei messaggi. È possibile inviare messaggi a diverse code o argomenti all'interno della stessa transazione.
- Code e argomenti partizionati non possono essere configurati per essere eliminati automaticamente quando diventano inattivi.
- Se si creano soluzioni multipiattaforma o ibride, non è attualmente possibile usare code e argomenti partizionati con il protocollo AMQP (Advanced Message Queuing Protocol).

## Strategie di partizionamento per Azure DocumentDB

Azure DocumentDB è un database NoSQL che può archiviare documenti. Un documento in DocumentDB è una rappresentazione serializzata JSON di un oggetto o di un altro dato. Nessuno schema fisso viene applicato ad eccezione del fatto che ogni documento deve contenere un ID univoco.

I documenti sono organizzati in raccolte. Una raccolta consente di raggruppare i documenti correlati. Ad esempio, in un sistema che gestisce post di blog, è possibile archiviare il contenuto di ogni post di blog come documento in una raccolta. È anche possibile creare raccolte per ogni tipo di argomento. In alternativa, in un'applicazione multi-tenant, ad esempio un sistema che consente ad autori diversi di controllare e gestire i propri post di blog, è possibile partizionare i blog per autore e creare una raccolta separata per ogni autore. Lo spazio di archiviazione allocato alle raccolte è flessibile e può essere ridotto o incrementato in base alle esigenze.

Le raccolte di documenti offrono un meccanismo naturale per partizionare i dati all'interno di un unico database. Internamente, un database di DocumentDB può estendersi su più server e potrebbe tentare di distribuire il carico distribuendo le raccolte tra i server. Il modo più semplice per implementare il partizionamento orizzontale consiste nel creare una raccolta per ogni partizione.

> [AZURE.NOTE] Ogni DocumentDB ha un _livello di prestazioni_ che determina la quantità di risorse ottenute. A ogni livello di prestazioni è associato un limite di velocità dell'_unità richiesta_. Il limite di velocità dell'unità richiesta specifica il volume di risorse riservato e disponibile a uso esclusivo della raccolta. Il costo di una raccolta dipende dal livello di prestazioni selezionato per la raccolta. Più elevato è il livello di prestazioni e il limite di velocità dell'unità richiesta, maggiore sarà il costo. Il livello di prestazioni di una raccolta può essere regolato usando il portale di Azure. Per ulteriori informazioni, vedere la pagina [Livelli di prestazioni in DocumentDB] sul sito Web Microsoft.

Tutti i database vengono creati nel contesto di un account di DocumentDB. Un singolo account DocumentDB può contenere più database e specifica in quale area vengono creati i database. Ogni account DocumentDB impone inoltre il proprio controllo di accesso. È possibile utilizzare account DocumentDB per individuare partizioni a livello geografico (raccolte all'interno del database) più prossime agli utenti che vi devono accedere e imporre restrizioni in modo che solo tali utenti possano connettersi a esse.

Ogni account DocumentDB ha una quota che limita il numero di database e raccolte che può contenere e la quantità di spazio di archiviazione dei documenti disponibile. Questi limiti sono soggetti a modifiche, descritte alla pagina [DocumentDB limiti e quote] sul sito Web Microsoft. In teoria, se si implementa un sistema in cui tutte le partizioni appartengono allo stesso database, è possibile che venga raggiunto il limite di capacità di archiviazione dell'account.

In questo caso, potrebbe essere necessario creare altri account e database di DocumentDB e distribuire le partizioni nei database. Tuttavia, anche se è poco probabile che si raggiunga la capacità di archiviazione di un database, è consigliabile usare più database. Di conseguenza, poiché ogni database ha un proprio set di utenti e autorizzazioni, è possibile usare questo meccanismo per isolare l'accesso alle raccolte in base al database.

La figura 8 mostra la struttura di alto livello dell'architettura di DocumentDB.

![Struttura di DocumentDB](media/best-practices-data-partitioning/DocumentDBStructure.png)

_Figura 8. Struttura di DocumentDB_

L'applicazione client indirizza le richieste alla partizione appropriata, in genere mediante l'implementazione del proprio meccanismo di mapping basato su alcuni attributi dei dati che definiscono la chiave di partizione. La figura 9 mostra due database di DocumentDB, ognuno dei quali contiene due raccolte che svolgono la funzione di partizione. I dati vengono partizionati in base all'ID tenant e includono i dati di un tenant specifico. I database vengono creati in account DocumentDB separati. Questi account si trovano nella stessa area dei tenant dei quali contengono i dati. La logica di routing nell'applicazione client utilizza l'ID tenant come chiave di partizione.

![Implementazione del partizionamento orizzontale usando Azure DocumentDB](media/best-practices-data-partitioning/DocumentDBPartitions.png)

_Figura 9. Implementazione del partizionamento orizzontale usando Azure DocumentDB_

Quando si decide come partizionare i dati con DocumentDB, tenere presente quanto riportato di seguito:

- **Le risorse disponibili per un database di DocumentDB sono soggette alle limitazioni di quota dell'account DocumentDB**. Ogni database può contenere un numero di raccolte (anche in questo caso, esiste un limite) e ogni raccolta è associata a un livello di prestazioni che regola il limite di velocità RU (velocità effettiva riservata) per la raccolta. Per altre informazioni, visitare la pagina [DocumentDB limiti e quote] nel sito Web Microsoft.
- **Ogni documento deve avere un attributo da usare per identificare in modo univoco tale documento all'interno della raccolta in cui è contenuto**. Questo attributo è diverso dalla chiave di partizione che definisce la raccolta contenente il documento. Una raccolta può contenere un numero elevato di documenti. In teoria, l'unico limite è la lunghezza massima dell'ID di documento. L'ID di documento può contenere fino a 255 caratteri.
- **Tutte le operazioni eseguite su un documento vengono eseguite nel contesto di una transazione. Le transazioni in DocumentDB sono limitate alla raccolta in cui è contenuto il documento.** Se un'operazione ha esito negativo, viene eseguito il rollback del lavoro che è stato eseguito. Quando viene eseguita un'operazione su un documento, tutte le modifiche apportate sono soggette all'isolamento a livello di snapshot. Questo meccanismo garantisce ad esempio che nel caso in cui una richiesta di creazione di un nuovo documento ha esito negativo, un altro utente che contemporaneamente esegue una query nel database non visualizzi un documento parziale che verrà in seguito rimosso.
- **Le query DocumentDB sono anche limitate al livello di raccolta**. Una singola query può recuperare dati solo da una raccolta. Se è necessario recuperare dati da più raccolte, è necessario eseguire query in ogni raccolta singolarmente e incorporare i risultati nel codice dell'applicazione.
- **DocumentDB supporta elementi programmabili che possono essere archiviati in una raccolta insieme ai documenti**. Questi includono stored procedure, funzioni definite dall'utente e trigger scritti in JavaScript. Questi elementi possono accedere a qualsiasi documento all'interno della stessa raccolta. Inoltre, questi elementi vengono eseguiti nell'ambito della transazione di ambiente (nel caso di un trigger che viene generato in seguito a una creazione, eliminazione o sostituzione eseguita su un documento) o avviando una nuova transazione (nel caso di una stored procedure eseguita in seguito a una richiesta client esplicita). Se il codice in un elemento programmabile genera un'eccezione, viene eseguito il rollback della transazione. È possibile utilizzare stored procedure e trigger per mantenere l'integrità e la coerenza tra i documenti, ma tutti questi documenti devono far parte della stessa raccolta.
- **È improbabile che le raccolte che si prevede di mantenere nei database di un account DocumentDB superino i limiti di velocità effettiva definiti dai livelli di prestazioni delle raccolte**. Questi limiti sono descritti nella pagina [Informazioni sulla capacità e sull'archiviazione di documenti in DocumentDB] nel sito Web Microsoft. Se si prevede di raggiungere questi limiti, considerare la suddivisione di raccolte tra database in account DocumentDB diversi per ridurre il carico per ogni raccolta.

## Strategie di partizionamento per Ricerca di Azure

La possibilità di ricercare dati è spesso il metodo principale di navigazione ed esplorazione offerto da molte applicazioni Web. Consente agli utenti di trovare le risorse rapidamente, ad esempio i prodotti in un'applicazione di e-commerce, in base a combinazioni di criteri di ricerca. Il servizio di ricerca di Azure offre funzionalità di ricerca full-text nel contenuto web e include funzionalità quali il suggerimento automatico di query basate su quasi corrispondenze ed esplorazione in base a facet. Una descrizione completa di queste funzionalità è disponibile nella pagina [Che cos'è la Ricerca di Azure?] nel sito Web Microsoft.

Ricerca di Azure memorizza il contenuto disponibile per la ricerca come documenti JSON all'interno di un database . Definire gli indici che specificano i campi disponibili per la ricerca nei documenti e inserire tali definizioni anche in Ricerca di Azure. Quando un utente invia una richiesta di ricerca, Ricerca di Azure usa gli indici appropriati per trovare gli elementi corrispondenti.

Per ridurre i conflitti, lo spazio di archiviazione usato da Ricerca di Azure può essere diviso in 1, 2, 3, 4, 6 o 12 partizioni e ogni partizione può essere replicata fino a 6 volte. Il prodotto del numero di partizioni moltiplicato per il numero di repliche è denominato _unità di ricerca_. Una singola istanza di Ricerca di Azure può contenere un massimo di 36 unità di ricerca. Un database con 12 partizioni supporta un massimo di 3 repliche.

Ogni SU allocata al servizio viene fatturata. Man mano che il volume dei contenuti disponibili per la ricerca o la frequenza delle richieste di ricerca aumenta, è possibile aggiungere unità di ricerca a un'istanza esistente di Ricerca di Azure per gestire il carico aggiuntivo. Ricerca di Azure distribuisce i documenti in modo uniforme in tutte le partizioni. Attualmente non sono supportate strategie di partizionamento manuale.

Ogni partizione può contenere un massimo di 15 milioni di documenti o può occupare 300 GB di spazio di archiviazione, in base al valore minore dei due. È possibile creare fino a 50 indici. Le prestazioni del servizio variano a seconda della complessità dei documenti, degli indici disponibili e degli effetti della latenza di rete. In media, una singola replica (1 unità di ricerca) deve essere in grado di gestire 15 query al secondo (QPS), anche se è consigliabile eseguire il benchmark con i propri dati per ottenere una misura della velocità effettiva più precisa. Per altre informazioni, vedere la pagina [Limiti dei servizi in Ricerca di Azure] nel sito Web Microsoft.

> [AZURE.NOTE] È possibile archiviare un set limitato di tipi di dati nei documenti disponibili per la ricerca, inclusi stringhe, valori booleani, dati numerici, dati di tipo datetime e alcuni dati geografici. Per altre informazioni, vedere la pagina [Tipi di dati supportati (Ricerca di Azure)] nel sito Web Microsoft.

Il controllo sul partizionamento dei dati da parte di Ricerca di Azure per ogni istanza del servizio è limitato. In un ambiente globale, tuttavia, è possibile migliorare le prestazioni e ridurre la latenza e i conflitti con un ulteriore partizionamento del servizio applicando una delle seguenti strategie:

- Creare un'istanza di Ricerca di Azure in ogni area geografica e assicurarsi che le applicazioni client vengano indirizzate verso l'istanza più vicina disponibile. Questa strategia richiede che tutti gli aggiornamenti al contenuto di ricerca siano replicati tempestivamente in tutte le istanze del servizio.

- Creare due livelli di Ricerca di Azure:
    - Un servizio locale in ogni area contenente i dati a cui gli utenti dell'area accedono più di frequente. Gli utenti possono indirizzare le richieste al servizio locale per ottenere risultati rapidi ma limitati.
    - Un servizio globale che comprende tutti i dati. Gli utenti possono indirizzare le richieste al servizio globale per ottenere risultati più lenti ma completi.

Questo approccio è più adatto quando esiste una variazione regionale significativa nei dati da ricercare.

## Strategie di partizionamento per Cache Redis di Azure

Cache Redis di Azure offre un servizio di memorizzazione nella cache condivisa nel cloud basato sull'archivio dati chiave-valore di Redis. Come suggerisce il nome, Cache Redis di Azure è una soluzione di memorizzazione nella cache. Usare questa soluzione solo per contenere dati temporanei e non come archivio dati permanente. Le applicazioni che usano Cache Redis di Azure dovrebbero continuare a funzionare anche se la cache non è disponibile. Cache Redis di Azure supporta la replica primaria o secondaria per garantire una disponibilità elevata, ma attualmente limita la dimensione massima della cache a 53 GB. Se è necessario più spazio, si devono creare cache aggiuntive. Per altre informazioni, visitare la pagina [Cache Redis di Azure] nel sito Web Microsoft.

Il partizionamento di un archivio dati Redis prevede la suddivisione dei dati tra istanze del servizio Redis. Ogni istanza rappresenta una singola partizione. Cache Redis di Azure consente di astrarre i servizi di Redis dietro un’interfaccia e non li espone direttamente. Il modo più semplice per implementare il partizionamento consiste nel creare più cache Redis di Azure e distribuire i dati nelle cache.

È possibile associare ogni elemento di dati a un identificatore (chiave di partizione) che specifica la cache in cui è memorizzato l'elemento. La logica dell'applicazione client potrà usare l'identificatore per indirizzare le richieste alla partizione appropriata. Questo schema è molto semplice. Tuttavia, se lo schema di partizionamento viene modificato, ad esempio se vengono create altre cache Redis di Azure, è possibile sia necessario riconfigurare le applicazioni client.

Redis nativo (non Cache Redis di Azure) supporta il partizionamento sul lato server basato sul clustering di Redis. Con questo approccio, è possibile dividere i dati in modo uniforme nei server usando un meccanismo hash. Ogni server Redis archivia i metadati che descrivono l’intervallo delle chiavi di hash che la partizione contiene e contiene inoltre informazioni su quali chiavi di hash si trovano nelle partizioni su altri server.

Le applicazioni client si limitano a inviare le richieste a un server Redis partecipante, probabilmente il più vicino. Il server Redis esamina la richiesta del client. Se la richiesta può essere risolta in locale, il server eseguirà l'operazione richiesta. In caso contrario, la richiesta verrà inoltrata al server appropriato.

Questo modello viene implementato dal clustering Redis e viene descritto più dettagliatamente alla pagina [Esercitazione del cluster Redis] sul sito Web Redis. Il clustering di Redis è trasparente per le applicazioni client. È possibile aggiungere altri server Redis al cluster e ripartizionare nuovamente i dati senza dover riconfigurare i client.

> [AZURE.IMPORTANT] La Cache Redis di Azure non supporta attualmente il clustering. Se si desidera implementare questo approccio con Azure, è necessario implementare i propri server Redis installando Redis in un set di macchine virtuali di Azure e configurandole manualmente. La pagina relativa all'[esecuzione di Redis in una macchina virtuale CentOS Linux in Azure] nel sito Web Microsoft descrive un esempio di creazione e configurazione di un nodo Redis in esecuzione come macchina virtuale di Azure.

La pagina relativa al [partizionamento e alla suddivisione dei dati in più istanze di Redis] nel sito Web Redis fornisce ulteriori informazioni sull'implementazione del partizionamento con Redis. Il resto di questa sezione presuppone l'implementazione di partizionamento sul lato client o assistito dal proxy.

Quando si decide come partizionare i dati con Cache Redis di Azure, tenere presente quanto riportato di seguito:

- Poiché Cache Redis di Azure non è progettata per svolgere la funzione di archivio dati permanente, indipendentemente dallo schema di partizionamento implementato, è necessario che il codice dell'applicazione sia in grado di recuperare i dati da una posizione diversa dalla cache.
- È consigliabile mantenere nella stessa partizione i dati cui viene di frequente eseguito l'accesso contemporaneamente. Redis è un archivio chiave-valore avanzato che offre diversi meccanismi altamente ottimizzati per la definizione della struttura dei dati. I meccanismi possono essere i seguenti:
    - Stringhe semplici (dati binari di una lunghezza massima di 512 MB)
    - Tipi di aggregazione, ad esempio elenchi, che possono svolgere la funzione di code e stack
    - Set ordinati e non ordinati
    - Hash che consentono di raggruppare i campi correlati, ad esempio gli elementi che rappresentano i campi in un oggetto

- I tipi di aggregazione consentono di associare numerosi valori correlati alla stessa chiave. Una chiave Redis identifica un elenco, un set o un hash anziché gli elementi di dati in esso contenuti. Questi tipi sono tutti disponibili con Cache Redis di Azure e sono descritti nella pagina relativa ai [tipi di dati] nel sito Web Redis. Ad esempio, in una parte di un sistema di e-commerce che tiene traccia degli ordini effettuati dai clienti, i dettagli di ogni cliente possono essere archiviati in un hash Redis associato a una chiave costituita dall'ID cliente. Ogni hash può contenere un insieme di ID degli ordini del cliente. Un set di Redis separato può contenere gli ordini, anch'essi strutturati come hash e associati a una chiave costituita dall'ID ordine. Nella figura 10 viene illustrata questa struttura. Si noti che Redis non implementa alcuna forma di integrità referenziale, pertanto è responsabilità dello sviluppatore mantenere le relazioni tra clienti e ordini.

![Struttura consigliata nell'archivio Redis per la registrazione degli ordini dei clienti e dei relativi dettagli](media/best-practices-data-partitioning/RedisCustomersandOrders.png)

_Figura 10. Struttura consigliata nell'archivio Redis per la registrazione degli ordini dei clienti e dei relativi dettagli_

> [AZURE.NOTE] In Redis, tutte le chiavi sono valori di dati binari, come le stringhe Redis, e possono contenere fino a 512 MB di dati. In teoria, una chiave può contenere qualsiasi informazione. Tuttavia, è consigliabile adottare una convenzione di denominazione coerente per le chiavi che descriva il tipo di dati e identifichi l'entità senza essere eccessivamente lunga. Un approccio comune consiste nell'usare chiavi nel formato "tipo\_entità:ID". Ad esempio, è possibile usare "cliente:99" per indicare la chiave per il cliente con ID 99.

- È possibile implementare il partizionamento verticale archiviando le informazioni correlate in aggregazioni diverse nello stesso database. Ad esempio, in un'applicazione di e-commerce è possibile archiviare le informazioni sui prodotti cui viene eseguito l'accesso di frequente in un hash di Redis e le informazioni dettagliate usate meno frequentemente in un altro hash. Entrambi gli hash possono usare lo stesso ID prodotto come parte della chiave. Ad esempio, è possibile usare "prodotto: _nn_" dove _nn_ è l'ID prodotto per le informazioni sul prodotto e "dettagli\_prodotto: _nn_" per i dati dettagliati. Questa strategia può contribuire a ridurre il volume dei dati recuperati dalla maggior parte delle query.
- Sebbene sia possibile ripartizionare un archivio dati Redis, tenere presente che si tratta di un'attività lunga e complessa. Il clustering Redis può ripartizionare i dati automaticamente, ma questa funzionalità non è disponibile con Cache Redis di Azure. Di conseguenza, quando si progetta lo schema di partizionamento, cercare di lasciare spazio libero sufficiente in ogni partizione per consentire la crescita dei dati prevista nel tempo. Tuttavia, tenere presente che Cache Redis di Azure consente di memorizzare dati temporaneamente e che i dati contenuti nella cache possono avere una durata limitata, specificata come valore time-to-live (TTL). Per i dati relativamente volatili, il TTL può essere più breve, ma per i dati statici il TTL può essere molto più lungo. Evitare di archiviare grandi quantità di dati di lunga durata nella cache, se il volume dei dati riempe la cache. È possibile specificare un criterio di rimozione che spinge la Cache Redis di Azure a rimuovere i dati se lo spazio è prezioso.

	> [AZURE.NOTE] Cache Redis di Azure consente di specificare le dimensioni massime della cache (da 250 MB a 53 GB) selezionando la fascia di prezzo appropriato. Tuttavia, dopo aver creato una cache Redis di Azure, non è possibile aumentare o diminuire le dimensioni della cache.

- Poiché i batch e le transazioni di Redis non possono estendersi su più connessioni, è consigliabile mantenere tutti i dati interessati da un batch o da una transazione nello stesso database (partizione).

	> [AZURE.NOTE] Una sequenza di operazioni in una transazione di Redis non è necessariamente atomica. I comandi che costituiscono una transazione vengono verificati e messi in coda prima dell'esecuzione. Se si verifica un errore durante questa fase, l'intera coda viene eliminata. Tuttavia, dopo che la transazione è stata inviata correttamente, i comandi in coda vengono eseguiti in sequenza. Se un comando non viene eseguito, viene interrotta soltanto l'esecuzione di quel comando. Tutti i comandi precedenti e successivi nella coda vengono eseguiti. Per altre informazioni, visitare la pagina relativa alle [transazioni] nel sito Web Redis.

- Redis supporta un numero limitato di operazioni atomiche. Le uniche operazioni di questo tipo che supportano più valori e chiavi sono le operazioni MGET e MSET. Le operazioni MGET restituiscono una raccolta di valori per un elenco di chiavi specificato, mentre le operazioni MSET archiviano una raccolta di valori per un elenco di chiavi specificato. Se è necessario usare queste operazioni, le coppie chiave-valore cui fanno riferimento i comandi MSET e MGET devono essere archiviate nello stesso database.

## Ribilanciamento delle partizioni

Man mano che il sistema cresce e se ne conoscono i modelli di utilizzo, può essere necessario modificare lo schema di partizionamento. È possibile ad esempio che singole partizioni inizino ad attrarre un volume di traffico eccessivo e si trasformino in aree sensibili che causano un numero elevato di conflitti. È anche possibile che il volume dei dati di alcune partizioni sia stato sottostimato, causando un avvicinamento ai limiti di capacità di archiviazione di queste partizioni. Indipendentemente dall'origine, talvolta è necessario ribilanciare le partizioni per ripartire il carico in modo più uniforme.

In alcuni casi, i sistemi di archiviazione di dati che non espongono pubblicamente il modo in cui i dati sono allocati nei server possono ribilanciare automaticamente le partizioni entro i limiti delle risorse disponibili. In altri casi, il ribilanciamento è un'attività amministrativa costituita da due fasi:

1. L'individuazione della nuova strategia di partizionamento da verificare:
    - Quali partizioni potrebbe essere necessario suddividere o eventualmente combinare.
    - Come allocare i dati in queste nuove partizioni progettando nuove chiavi di partizione.
2. La migrazione dei dati interessati dallo schema di partizionamento precedente al nuovo set di partizioni.

> [AZURE.NOTE] Il mapping delle raccolte di DocumentDB sui server è trasparente, ma è ancora possibile che vengano raggiunti i limiti di capacità di archiviazione e di velocità effettiva di un account DocumentDB. Se ciò si verifica, potrebbe essere necessario riprogettare lo schema di partizionamento e migrare i dati.

A seconda della tecnologia di archiviazione dati e della progettazione del sistema di archiviazione dati, può essere possibile eseguire la migrazione dei dati tra partizioni mentre sono in uso (migrazione online). Se non è possibile, potrebbe essere necessario rendere temporaneamente non disponibili le partizioni interessate mentre i dati vengono spostati (migrazione offline).

## Migrazione offline

La migrazione offline è probabilmente l'approccio più semplice in quanto riduce le probabilità che si verifichino conflitti. Non apportare modifiche ai dati mentre vengono spostati e ristrutturati.

Concettualmente, questo processo comprende i passaggi seguenti:

1. Contrassegnare la partizione come offline.
2. Dividere/unire e spostare i dati nelle nuove partizioni.
3. Verificare i dati.
4. Portare online le nuove partizioni.
5. Rimuovere la partizione precedente.

Per mantenere una disponibilità, è possibile contrassegnare la partizione originale come di sola lettura nel passaggio 1 anziché renderla non disponibile. Questo consentirebbe alle applicazioni di leggere i dati mentre vengono spostati ma non di modificarli.

## Migrazione online

La migrazione online è più complessa da eseguire ma meno problematica per gli utenti poiché i dati rimangono disponibili durante l'intera procedura. Il processo è simile a quella utilizzato dalla migrazione offline, ad eccezione del fatto che la partizione originale non è contrassegnata come non in linea (passaggio 1). A seconda della granularità del processo di migrazione, ad esempio se viene eseguita per ogni singolo elemento o per ogni singola partizione, il codice di accesso ai dati nelle applicazioni client potrebbe dover gestire la lettura e scrittura dei dati contenuti in due posizioni, ovvero la partizione originale e la nuova partizione.

Per un esempio di soluzione che supporta la migrazione online, vedere l'articolo [Scalabilità tramite lo strumento di suddivisione-unione del database elastico] nel sito Web Microsoft.

## Modelli correlati e informazioni aggiuntive

Quando si esaminano le strategie per l'implementazione della coerenza dei dati, anche i modelli seguenti possono essere rilevanti per il proprio scenario:

- La pagina relativa alla [coerenza dei dati] nel sito Web Microsoft descrive le strategie per la gestione della coerenza in un ambiente distribuito, ad esempio il cloud.
- La pagina [Linee guida di partizionamento di dati] nel sito Web Microsoft offre una panoramica generale sulla progettazione delle partizioni per soddisfare diversi criteri in una soluzione distribuita.
- Il [modello di partizionamento orizzontale] descritto nel sito Web Microsoft riepiloga alcune strategie comuni per il partizionamento orizzontale dei dati.
- L'[ITP (Index Table Pattern)] descritto nel sito Web Microsoft illustra come creare indici secondari nei dati. Questo approccio consente a un'applicazione di recuperare rapidamente i dati utilizzando le query che non fanno riferimento alla chiave primaria di una raccolta.
- Il [modello di vista materializzata] descritto nel sito Web Microsoft illustra come generare viste prepopolate che riepilogano i dati per favorire operazioni di query rapide. Questo approccio può essere utile in un archivio dati partizionati se le partizioni che contengono dati riepilogati vengono distribuite tra più siti.
- L'articolo [Uso della rete CDN per Azure] nel sito Web Microsoft offre ulteriori istruzioni per la configurazione e l'uso della Rete di distribuzione dei contenuti di Azure.

## Altre informazioni

- La pagina [Informazioni sul database SQL] nel sito Web Microsoft contiene informazioni dettagliate sulla creazione e l'uso di database SQL.
- La pagina [Panoramica sulle funzionalità di database elastico] nel sito Web Microsoft offre un'introduzione completa al database elastico.
- La pagina [Scalabilità tramite lo strumento di suddivisione-unione del database elastico] nel sito Web Microsoft contiene informazioni sull'uso del servizio di suddivisione-unione per gestire le partizioni del database elastico.
- La pagina [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](https://msdn.microsoft.com/library/azure/dn249410.aspx) nel sito Web Microsoft descrive gli attuali limiti di ridimensionamento e velocità effettiva dell'archiviazione di Azure.
- La pagina [Esecuzione di transazioni di gruppi di entità] nel sito Web Microsoft offre informazioni dettagliate sull'implementazione di operazioni transazionali su entità archiviate nell'archiviazione tabelle di Azure.
- L'articolo [Guida alla progettazione della tabella di archiviazione di Azure] nel sito Web Microsoft include informazioni dettagliate sul partizionamento dei dati nell'archiviazione tabelle di Azure.
- La pagina [Uso della rete CDN per Azure] nel sito Web Microsoft descrive come replicare i dati contenuti nell'archiviazione BLOB di Azure usando la Rete di distribuzione dei contenuti di Azure.
- La pagina [Gestione della capacità di DocumentDB ] nel sito Web Microsoft contiene informazioni su come DocumentDB Azure alloca le risorse ai database.
- La pagina [Che cos'è la Ricerca di Azure?] nel sito Web Microsoft offre una descrizione completa delle funzionalità disponibili in Ricerca di Azure.
- La pagina [Limiti dei servizi in Ricerca di Azure] nel sito Web Microsoft include informazioni sulla capacità di ogni istanza di Ricerca di Azure.
- La pagina [Tipi di dati supportati (Ricerca di Azure)] nel sito Web Microsoft riepiloga i tipi di dati che è possibile usare nei documenti e indici in cui è possibile eseguire ricerche.
- La pagina [Cache Redis di Azure] nel sito Web Microsoft offre un'introduzione a Cache Redis di Azure.
- La pagina relativa al [partizionamento e alla suddivisione dei dati in più istanze di Redis] nel sito Web Redis include informazioni sull'implementazione del partizionamento con Redis.
- La pagina relativa all'[esecuzione di Redis in una macchina virtuale CentOS Linux in Azure] nel sito Web Microsoft descrive un esempio di creazione e configurazione di un nodo Redis in esecuzione come macchina virtuale di Azure.
- La pagina relativa ai [tipi di dati] nel sito Web Redis descrive i tipi di dati che sono disponibili con Redis e Cache Redis di Azure.

[Cache Redis di Azure]: http://azure.microsoft.com/services/cache/
[Obiettivi di scalabilità e prestazioni per Archiviazione di Azure]: storage/storage-scalability-targets.md
[Guida alla progettazione della tabella di archiviazione di Azure]: storage/storage-table-design-guide.md
[Creazione di una soluzione Polyglot]: https://msdn.microsoft.com/library/dn313279.aspx
[Accesso ai dati per le soluzioni altamente scalabili mediante SQL, NoSQL e persistenza Polyglot]: https://msdn.microsoft.com/library/dn271399.aspx
[coerenza dei dati]: http://aka.ms/Data-Consistency-Primer
[Linee guida di partizionamento di dati]: https://msdn.microsoft.com/library/dn589795.aspx
[tipi di dati]: http://redis.io/topics/data-types
[DocumentDB limiti e quote]: documentdb/documentdb-limits.md
[Panoramica sulle funzionalità di database elastico]: sql-database/sql-database-elastic-scale-introduction.md
[Utilità di migrazione di federazioni]: https://code.msdn.microsoft.com/vstudio/Federations-Migration-ce61e9c1
[ITP (Index Table Pattern)]: http://aka.ms/Index-Table-Pattern
[Gestione della capacità di DocumentDB ]: documentdb/documentdb-manage.md
[Informazioni sulla capacità e sull'archiviazione di documenti in DocumentDB]: documentdb/documentdb-manage.md
[modello di vista materializzata]: http://aka.ms/Materialized-View-Pattern
[Esecuzione di query su più partizioni]: sql-database/sql-database-elastic-scale-multishard-querying.md
[partizionamento e alla suddivisione dei dati in più istanze di Redis]: http://redis.io/topics/partitioning
[Livelli di prestazioni in DocumentDB]: documentdb/documentdb-performance-levels.md
[Esecuzione di transazioni dei gruppi di entità]: https://msdn.microsoft.com/library/azure/dd894038.aspx
[Esecuzione di transazioni di gruppi di entità]: https://msdn.microsoft.com/library/azure/dd894038.aspx
[Esercitazione del cluster Redis]: http://redis.io/topics/cluster-tutorial
[esecuzione di Redis in una macchina virtuale CentOS Linux in Azure]: http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx
[Scalabilità tramite lo strumento di suddivisione-unione del database elastico]: sql-database/sql-database-elastic-scale-overview-split-and-merge.md
[Uso della rete CDN per Azure]: cdn/cdn-how-to-use-cdn.md
[Quote del bus di servizio]: service-bus/service-bus-quotas.md
[Limiti dei servizi in Ricerca di Azure]: search/search-limits-quotas-capacity.md
[Modello di partizionamento]: http://aka.ms/Sharding-Pattern
[Modello di partizionamento orizzontale]: http://aka.ms/Sharding-Pattern
[Tipi di dati supportati (Ricerca di Azure)]: https://msdn.microsoft.com/library/azure/dn798938.aspx
[transazioni]: http://redis.io/topics/transactions
[Che cos'è la Ricerca di Azure?]: search/search-what-is-azure-search.md
[Informazioni sul database SQL]: sql-database/sql-database-technical-overview.md

<!---HONumber=AcomDC_0330_2016-->