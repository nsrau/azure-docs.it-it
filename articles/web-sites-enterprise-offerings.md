<properties 
	pageTitle="Esecuzione della migrazione di siti Web IIS a Siti Web di Azure usando Migration Assistant" 
	description="Illustra come usare Siti Web di Azure per creare soluzioni di siti Web aziendali per l'organizzazione" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2014" 
	ms.author="cephalin"/>

# White paper sulle offerte di Siti Web di Azure per le aziende #

La necessità di ridurre i costi e fornire soluzioni IT più velocemente in un ambiente in rapida evoluzione crea nuove sfide per gli sviluppatori, i professionisti IT e i responsabili. Gli utenti richiedono sempre più applicazioni Web line-of-business che siano veloci, reattive e disponibili da qualsiasi dispositivo. Allo stesso tempo, le aziende stanno provando a trarre vantaggio dalla maggiore produttività ed efficienza che proviene dall'integrazione con servizi mobili e cloud: ciò potrebbe essere qualcosa di semplice, come l'accesso Single Sign-On tra dispositivi usando Active Directory, fino alla collaborazione in Office365 usando i dati estratti da un'applicazione line-of-business interna che a sua volta effettua il pull dei dati dall'applicazione aziendale di Salesforce. [Siti Web di Microsoft Azure](http://azure.microsoft.com/services/websites/) è un servizio cloud di livello aziendale per lo sviluppo, il test e l'esecuzione di applicazioni Web e mobili, API Web e siti Web generici. Può essere usato per eseguire siti Web aziendali, applicazioni commerciali e campagne di marketing digitale su una rete globale di data center ottimizzata per la scalabilità e disponibilità, oltre al supporto per l'integrazione continuata e moderne procedure DevOps. 

In questo white paper vengono evidenziate le funzionalità del servizio Siti Web di Microsoft Azure appositamente progettate per l'esecuzione di applicazioni Web line-of-business, inclusa la migrazione delle applicazioni Web esistenti e la distribuzione di nuove applicazioni Web ine-of-business sulla piattaforma. 

## Destinatari ##

Professionisti IT, architetti e responsabili che mirano a eseguire la migrazione al cloud i carichi di lavoro Web attualmente in esecuzione in locale. I carichi di lavoro Web possono comprendere applicazioni Web azienda-dipendente o azienda-partner.

## Introduzione ##

Siti Web di Microsoft Azure è una piattaforma ideale in cui ospitare servizi e applicazioni Web esterni e interni, poiché rappresenta una soluzione economica, altamente scalabile e gestita, consentendo di concentrarsi sull'offerta di valore di business agli utenti piuttosto che operare un significativo investimento in termini di tempo e denaro nella gestione e nel supporto di ambienti separati. Siti Web di Microsoft Azure è una piattaforma flessibile in cui distribuire applicazioni Web aziendali, che offre la possibilità di continuare a eseguire l'autenticazione in Active Directory in locale attraverso l'integrazione con Microsoft Azure Active Directory, il supporto di distribuzioni facili e veloci usando le procedure di integrazione e distribuzione continua interne, con una scalabilità automatica che le consente di crescere di pari passo con le esigenze aziendali. Tutto questo su una piattaforma gestita che consente di concentrarsi sull'applicazione e non sull'infrastruttura. 

## Definizione del problema ##

Il panorama IT è in rapida evoluzione a causa dell'abbandono dell'hosting su server tradizionali, con i loro elevati costi di capitale su lead time lunghi; il nuovo approccio prevede l'uso di servizi su richiesta scalabili automaticamente per gestire il carico. Ai reparti IT viene chiesto di ridurre i costi e il footprint delle spese relative all'infrastruttura e alla manutenzione, con l'obiettivo di ridurre le spese in conto capitale aumentando contemporaneamente la flessibilità. L'imminente termine del ciclo di vita delle piattaforme di infrastruttura più obsolete, ad esempio Windows Server 2003, costringe i reparti IT a rivedere la migrazione verso il cloud come un potenziale mezzo per evitare nuovi costi di capitale a lungo termine. In passato erano i CIO a prendere le decisioni di acquisto per altri reparti. Oggi, un numero crescente di CMO e di capi di altre business unit assumono un ruolo più attivo nella gestione della spesa del budget e nella determinazione del ritorno sull'investimento. Le aziende hanno più che mai bisogno che la forza lavoro sia mobile, richiedendo ai propri dipendenti di lavorare in remoto e dedicando maggior tempo ai clienti che vogliono accedere ai sistemi senza alcun inconveniente.

Le esigenze aziendali cambiano ogni mese, settimana e giorno: le aziende richiedono una scalabilità globale istantanea, con servizi aggiornati regolarmente, ricchi di nuove funzionalità, forniti da una terza parte o sviluppati internamente. Le aspettative degli utenti sono più elevate: molti di essi adoperano i servizi nella vita privata, ad esempio Office365, e si aspettano di accedere a servizi simili, cioè aggiornati e ricchi di funzionalità, anche nella vita professionale. Per far fronte a questa domanda, i reparti IT devono mirare ad aiutare le aziende a conseguire questo scopo attraverso la selezione e l'integrazione con servizi di terze parti, l'attenta selezione delle piattaforme che possono adattarsi alle esigenze aziendali, garantendo contemporaneamente l'affidabilità e un costo totale di proprietà ridotto.

I team di sviluppo mirano a offrire vantaggi aziendali immediati, fornendo nuove funzionalità con una certa frequenza, e sono alla ricerca di una piattaforma affidabile ed economica che si integri con gli strumenti e le procedure esistenti per lo sviluppo, il test e il rilascio. La collaborazione con i reparti IT consente di automatizzare la distribuzione, la gestione e gli avvisi, tutto ciò ponendosi come obiettivo tempi di inattività pari a zero.

<a href="highlevel" />
## Soluzione di alto livello ##

Si fa un uso sempre più frequente di piattaforme Web e framework per sviluppare, testare e ospitare applicazioni line-of-business.  Una tipica applicazione line-of-business potrebbe essere, ad esempio, un sistema interno per la registrazione delle note spese dei dipendenti, spesso costituito unicamente da un sito Web con un database di backup per archiviare i dati connessi all'applicazione.

Siti Web di Microsoft Azure è una buona soluzione per l'hosting di tali applicazioni e offre un'infrastruttura scalabile e affidabile che viene gestita e a cui vengono applicate patch con interventi manuali e tempo di inattività quasi pari a zero. La piattaforma Microsoft Azure offre numerose opzioni di archiviazione di dati per supportare le applicazioni Web ospitate in Siti Web di Microsoft Azure, a partire dal Database SQL di Microsoft Azure, un database relazionale distribuito come servizio gestito e scalabile, fino ai servizi popolari forniti dai nostri partner, come ClearDB MySQL Database e MongoDB.

Un approccio alternativo consiste nello sfruttare l'investimento operato in locale. Nello scenario di esempio, un sistema relativo alle spese dei dipendenti, può essere opportuno gestire l'archivio dati nell'ambito dell'infrastruttura interna. Lo scopo potrebbe essere l'integrazione con i sistemi interni (creazione di report, retribuzioni, fatturazione ecc.) oppure la soddisfazione di un requisito di governance IT.  Siti Web di Microsoft Azure fornisce due metodi per connettersi all'infrastruttura locale:

- [Connessioni ibride](http://aka.ms/hybridconnections): la funzionalità Connessioni ibride di Servizi BizTalk di Microsoft Azure consente a Siti Web di Azure di connettersi alle risorse locali in modo sicuro, ad esempio SQL Server, MySQL, API Web e servizi Web personalizzati.
- [Integrazione della rete virtuale](http://aka.ms/websitesvnet): l'integrazione della rete virtuale di Siti Web di Azure consente di connettere il proprio sito Web a una rete virtuale di Azure, che a sua volta può essere connessa all'infrastruttura in locale attraverso una connessione VPN da sito a sito.

Nel seguente diagramma è illustrato un esempio di soluzione di alto livello con opzioni di connettività per le risorse locali.

![](./media/web-sites-enterprise-offerings/on-premise-connectivity-solutions.png)

## Vantaggi aziendali ##

Siti Web di Microsoft Azure fornisce una serie di vantaggi aziendali che rendono la funzione più efficiente ed economica per soddisfare le esigenze aziendali. 

### Modello PaaS ###

Siti Web di Azure si basa su un modello di piattaforma distribuita come servizio che fornisce una serie di risparmi di costi ed efficienza.  Non è più necessario impiegare ore e ore nella gestione delle macchine virtuali o nell'applicazione di patch a sistemi operativi e framework. Siti Web di Azure è un ambiente con applicazione automatica delle patch, che consente di concentrarsi sulla gestione delle applicazioni Web e non delle macchine virtuali, lasciando al team la libertà di offrire valore di business aggiuntivo.

Il modello PaaS a sostegno di Siti Web di Microsoft Azure consente ai professionisti della metodologia DevOps di soddisfare i propri obiettivi. Per un'azienda ciò significa la gestione e l'integrazione completa attraverso l'intero ciclo di vita dell'applicazione, tra cui sviluppo, test, rilascio, monitoraggio, gestione e supporto. 

I team di sviluppo potranno configurare i flussi di lavoro di integrazione e distribuzione continue da Visual Studio Online, GitHub, TeamCity, Hudson o BitBucket, abilitando la compilazione, il test e la distribuzione automatizzati, consentendo cicli di rilascio di nuove versioni più veloci e la riduzione dell'attrito implicato dal rilascio nell'infrastruttura esistente. Siti Web di Azure supporta anche la creazione di più ambienti di test e di gestione temporanea per il flusso di lavoro del rilascio: non è più necessario riservare o allocare hardware a tale scopo, ma è possibile creare tutti gli ambienti necessari e definire la promozione personalizzata del flusso di lavoro di rilascio. Un'azienda potrebbe voler decidere di eseguire il rilascio in uno slot di test dal controllo del codice sorgente, eseguire una serie di test e, dopo il corretto completamento, alzare il livello a uno slot di gestione temporanea: Infine, potrebbe passare alla produzione senza tempi di inattività, con il vantaggio aggiunto che le applicazioni Web ospitate su Siti Web di Azure sono precaricate e attive, in modo da offrire la migliore esperienza possibile al cliente.

I team operativi potranno star sicuri di trovarsi nella migliore posizione possibile per rispondere a qualsiasi problema con tutte le applicazioni Web ospitate in Siti Web di Azure grazie alle funzionalità predefinite di monitoraggio e avvisi. Qualora i team operativi avessero già investito in soluzioni di analisi e monitoraggio come Application Insights per Microsoft Visual Studio, New Relic e AppDynamics, queste sono completamente supportate su Siti Web di Azure, permettendo di ottenere la continuità e un ambiente familiare dal quale monitorare le applicazioni Web.

Infine, Siti Web di Azure fornisce la funzionalità di backup automatico dei siti e dei database ospitati diretta a un contenitore di archiviazione BLOB di Azure, offrendo un metodo più semplice e molto conveniente con cui eseguire il ripristino di emergenza, riducendo la necessità di hardware e software locali complessi.

### Facilità di migrazione ###

La manutenzione e la rotazione dell'hardware rivestono un'importanza fondamentale per le aziende per velocizzare i cicli di rilascio di hardware e sistemi operativi. Se, ad esempio, si hanno a disposizione diversi server Windows Server 2003 R2, per cui il supporto sta per terminare nel 2015 e in cui sono ospitate ancora applicazioni Web di importanza critica per l'azienda, Siti Web di Azure è un ottimo candidato su cui ospitare tali applicazioni Web, consentendo di razionalizzare il patrimonio hardware aziendale. Siti Web di Azure consente di accedere a una gamma di specifiche hardware che sono gestite e mantenute come parte del servizio, eliminando la necessità di calcolare i costi di gestione e di sostituzione come parte del budget dell'infrastruttura.  La migrazione può essere semplice come un'operazione di copia e incolla dalla distribuzione esistente a Siti Web di Azure o più complessa come una migrazione, in cui il valore aggiunto è dato dall'uso di Migration Assistant di Siti Web Azure. Le applicazioni Web migrate sfruttano la gamma completa dei servizi di Azure, integrando servizi aggiuntivi per le applicazioni Web. Ad esempio, è possibile considerare l'aggiunta di Azure Active Directory per controllare l'accesso all'applicazione in base all'associazione degli utenti ai gruppi di sicurezza. Un altro esempio può essere l'aggiunta di servizi cache per migliorare le prestazioni e ridurre la latenza, fornendo complessivamente una migliore esperienza utente. 

### Hosting di classe Enterprise ###

Siti Web di Azure rappresenta una piattaforma stabile e affidabile che ha già dimostrato di poter gestire un'ampia gamma di esigenze aziendali, da piccoli carichi di lavoro di sviluppo e test interni a siti Web a traffico elevato ed elevata scalabilità. Usando Siti Web di Azure si adopera la stessa piattaforma di hosting di livello aziendale che Microsoft usa come società per carichi di lavoro Web ad alto valore. Siti Web di Azure, assieme a tutti i servizi presenti sulla piattaforma Azure, sono compilati in tutta sicurezza e conformità ai requisiti normativi, come le attestazioni ISO (ISO/IEC 27001:2005), attestazioni SOC1 e SOC2 SSAE 16/ISAE 3402, HIPAA BAA, PCI e Fedramp, vero e proprio fulcro di ogni elemento e funzionalità. Per altre informazioni, vedere [http://aka.ms/azurecompliance](http://aka.ms/azurecompliance). 

La piattaforma Microsoft Azure consente il controllo degli accessi in base al ruolo, per controllare le risorse a livello aziendale in Siti Web di Azure. Il controllo degli accessi in base al ruolo offre alle aziende il potere di implementare i propri criteri di gestione degli accessi nell'ambiente Azure, assegnando gli utenti a gruppi e quindi assegnando a loro volta le autorizzazioni necessarie a tali gruppi per accedere alla risorsa, ad esempio un sito Web di Azure. Per altre informazioni su RBAC in Azure, vedere [http://aka.ms/azurerbac](http://aka.ms/azurerbac). Grazie all'uso di Siti Web di Azure è possibile garantire che le applicazioni Web vengano distribuite in un ambiente sicuro e protetto e di avere il controllo totale nel territorio in cui vengono distribuite le risorse. 

Per di più, Siti Web di Azure è anche in grado di sfruttare completamente gli investimenti operati in locale, offrendo la possibilità di riconnettersi alle risorse interne, ad esempio il data warehouse o un ambiente SharePoint. Come descritto nella sezione [Soluzione di alto livello](#highlevel) è possibile usare le connessioni ibride e la connettività alla rete virtuale per stabilire connessioni a infrastrutture e servizi locali.

### Scalabilità globale ###

Siti Web di Azure è una piattaforma scalabile e globale, che consente alle applicazioni Web di crescere e adattarsi rapidamente alle esigenze delle aziende in espansione, con pianificazione e costi a lungo termine ridotti al minimo. Nei tipici scenari di infrastruttura locale, l'espansione e l'aumento della domanda a livello locale e geografico richiederebbe di dedicare più tempo alla gestione e alla pianificazione e un maggior dispendio per eseguire il provisioning e la gestione di un'infrastruttura aggiuntiva. Siti Web di Azure offre la possibilità di scalare le applicazioni Web di pari passo con gli alti e bassi dei propri requisiti. Ad esempio, usando l'applicazione per le spese come modello, per gran parte del mese gli utenti faranno un uso modico dell'applicazione, ma con l'avvicinarsi della scadenza mensile per l'invio del resoconto delle spese e l'aumento dell'uso dell'applicazione, Siti Web di Azure può eseguire il provisioning automatico di altre infrastrutture per l'applicazione. Quando poi l'uso si sarà nuovamente ridotto, potrà annullare la scalabilità in modo da tornare all'infrastruttura di base definita dall'utente.

Siti Web di Azure è disponibile a livello globale in 17 data center in tutto il mondo (e il loro numero è in continua crescita). Per un elenco più aggiornato di aree e località, vedere [http://aka.ms/azlocations](http://aka.ms/azlocations). Con Siti Web di Azure l'azienda può facilmente conseguire la scalabilità e la portata globale. Con l'ampliarsi dell'azienda in nuove aree, è possibile distribuire facilmente i dashboard dell'applicazione di registrazione in uso, ospitati su Siti Web di Azure, in ulteriori data center e destinarli agli utenti locali in maniera molto più rapida attraverso la combinazione di Siti Web di Azure e Gestione traffico di Azure, e tutto ciò con il valore aggiunto dell'infrastruttura scalabile a supporto, in grado di contrarsi ed espandersi con il mutare delle esigenze delle filiali regionali.
 
## Dettagli della soluzione ##

Di seguito verrà esaminato un esempio di uno scenario di migrazione di applicazioni, in cui sono illustrati i dettagli di come le funzionalità di Siti Web di Azure collaborano per fornire valore di business e la soluzione ideale.
 
In tutto l'esempio, l'applicazione line-of-business presa in considerazione è un'applicazione per la registrazione di note spese che consente ai dipendenti di inviare le note spese per il rimborso. L'applicazione è ospitata in un Windows Server 2003 R2 che esegue IIS6 e il database è un database di SQL Server 2005. Il motivo per cui è stato scelto un server obsoleto è la terminazione del servizio per Windows Server 2003 R2 e SQL Server 2005. Sono disponibili [strumenti](http://aka.ms/websitesmigration) e [informazioni aggiuntive](http://aka.ms/websitesmigrationresources) per eseguire la migrazione automatica dei carichi di lavoro in Azure. Tenendo presente tutto ciò, il modello usato in questo esempio si applica a un'ampia varietà di scenari di migrazione. 

### Migrazione di applicazioni esistenti ###

Il primo passo della soluzione generale per lo spostamento di un'applicazione line-of-business in Siti Web di azure consiste nell'identificare le risorse e l'architettura dell'applicazione esistente. L'esempio contenuto in questo articolo è un'applicazione Web ASP.NET ospitata in un singolo Server IIS con il database ospitato in un SQL Server separato, come illustrato nella seguente figura. I dipendenti accedono al sistema usando una combinazione di nome utente e password, immettono i dettagli delle spese e caricano nel database copie digitalizzate delle ricevute per ogni elemento di nota spese. 
 
![](./media/web-sites-enterprise-offerings/on-premise-app-example.png)

#### Elementi da considerare ###

Quando si esegue la migrazione dell'applicazione da un ambiente locale, è consigliabile tenere presenti alcune limitazioni di Siti Web di Azure. Di seguito sono descritti alcuni argomenti chiave da tenere presenti quando si esegue la migrazione di applicazioni Web a Siti Web di Azure ([http://aka.ms/websitesmigrationresources](http://aka.ms/websitesmigrationresources)):

-	Associazioni delle porte: Siti Web di Azure supporta solo la porta 80 per HTTP e la porta 443 per il traffico HTTPS. Se l'applicazione o il sito usa qualsiasi altra porta, una volta eseguita la migrazione dell'applicazione o del sito, verrà usata la porta 80 per HTTP e la porta 443 per il traffico HTTPS. Si tratta spesso di un problema innocuo in quanto è comune nelle distribuzioni locali fare uso di porte diverse per evitare l'uso di nomi di dominio, soprattutto in ambienti di sviluppo e test
-	Autenticazione: Siti Web di Azure supporta l'autenticazione anonima per impostazione predefinita e l'autenticazione basata su form identificata da un'applicazione. Siti Web di Azure può offrire l'autenticazione Windows quando l'applicazione o il sito viene integrato solo con Azure Active Directory e ADFS. Questa funzione viene descritta in dettaglio [qui](http://aka.ms/azurebizapp) 
-	Assembly basati su GAC: Siti Web di Azure non consente la distribuzione di assembly alla Global Assembly Cache (GAC), pertanto se l'applicazione o un sito migrato fa uso di questa funzionalità in locale, si consiglia di spostare gli assembly nella cartella bin dell'applicazione o del sito
-	Modalità di compatibilità IIS5: Siti Web di Azure non supporta la modalità di compatibilità IIS5 e di conseguenza ogni sito e tutte le applicazioni Web nel sito padre vengono eseguiti nello stesso processo di lavoro all'interno di un singolo pool di applicazioni
-	Uso di librerie COM: Siti Web di Azure non consente la registrazione di componenti COM sulla piattaforma. Pertanto, se il sito o l'applicazione fa uso di qualsiasi componente COM, questi dovranno essere riscritti nel codice gestito e distribuiti con il sito o l'applicazione
-	Filtri ISAPI: i filtri ISAPI possono essere supportati in Siti Web di Azure, ma dovranno essere distribuiti come parte del sito e registrati nel file web.config delle applicazioni Web o del sito. Per altre informazioni, vedere [http://aka.ms/azurewebsitesxdt](http://aka.ms/azurewebsitesxdt). 

Una volta considerati questi argomenti, l'applicazione Web dovrebbe essere pronta per il Cloud. Non bisogna preoccuparsi se alcuni argomenti non vengono seguiti alla lettera: lo strumento di migrazione consentirà di usare la procedura migliore per la migrazione. 

I passaggi successivi del processo di migrazione consistono nel creare un sito Web di Azure e un Database SQL di Azure. Esistono più dimensioni di istanze di Siti Web di Microsoft Azure con un numero variabile di core CPU e di quantità di RAM disponibili da selezionare in base alle proprie esigenze di applicazioni Web. Per altre informazioni e dettagli sui prezzi, vedere [http://aka.ms/azurewebsitesskus](http://aka.ms/azurewebsitesskus). Analogamente, il Database SQL di Microsoft Azure risolve tutte le esigenze di un'azienda con vari livelli di servizio e livelli di prestazioni per soddisfare i requisiti. Altre informazioni sono disponibili all'indirizzo [http://aka.ms/azuresqldbskus](http://aka.ms/azuresqldbskus). Una volta creato, il sito/l'applicazione viene caricato nel sito Web di Azure tramite FTP o WebDeploy e quindi trasferito al database.

In questa migrazione, la soluzione usa il Database SQL di Azure, che è non l'unico database supportato in Azure. Le aziende potranno usare anche MySQL, MongoDB, Azure DocumentDB e molte altre soluzioni acquistando componenti aggiuntivi in [Azure Store](http://aka.ms/azurestore). 

Durante la creazione di un Database SQL di Azure, è disponibile una serie di opzioni per importare un database esistente da un server locale: dalla generazione di uno script di un database esistente all'uso delle operazioni di [esportazione e importazione di applicazioni livello dati](http://aka.ms/dacpac). 

Il database dell'applicazione note spese è stato prodotto creando un nuovo Database SQL di Azure, connettendosi al database tramite SQL Server Management Studio e quindi eseguendo uno script per creare lo schema del database e popolarlo con i dati dal database locale.

Il passaggio finale in questa prima fase della migrazione richiede l'aggiornamento delle stringhe di connessione al database per l'applicazione. Ciò può essere ottenuto tramite il portale di gestione di Azure: ogni sito Web di Azure ha un proprio dashboard insieme a una schermata di configurazione che consente la modifica delle impostazioni specifiche dell'applicazione, tra cui le stringhe di connessione usate dall'applicazione per la connessione a qualsiasi database usato.

### Alternative all'uso del Database SQL di Azure ###

La piattaforma Azure offre diverse alternative all'uso di Database SQL di Azure come database primario delle applicazioni Web, e ciò per abilitare diversi carichi di lavoro, ad esempio l'uso di una soluzione NoSQL, oppure per abilitare la piattaforma in base alle esigenze aziendali relative ai dati; ad esempio, un'azienda potrebbe conservare dati che non devono essere archiviati fuori sede o in un ambiente cloud pubblico e pertanto potrebbe voler mantenere l'uso del proprio database locale.

#### Connettività alle risorse locali ###
Siti Web di Azure offre due metodi di connessione alle risorse locali, quali i database, che consentono il riutilizzo dell'esistente infrastruttura di valore elevato. I due metodi sono l'integrazione della rete virtuale di Siti Web di Azure e le connessioni ibride:

- L'integrazione della rete virtuale di Siti Web di Azure supporta l'integrazione tra Siti Web di Azure e una rete virtuale di Azure, consentendo l'accesso alle risorse in esecuzione nella rete virtuale, che se connessa alla rete locale con una connessione VPN da sito a sito consente la connettività diretta ai sistemi in locale.
- Le connessioni ibride sono una funzionalità di Servizi BizTalk di Azure che consente di connettersi facilmente a singole risorse locali, ad esempio SQL Server, MySQL, API Web HTTP e alla maggior parte dei servizi Web personalizzati.

#### Scalabilità e resilienza ###

Di pari passo con l'aumentare della forza lavoro di un'azienda tramite acquisizioni o la naturale crescita organica è necessario scalare le applicazioni Web per soddisfare queste nuove domande. Effettivamente oggi è comune assistere a una diffusione sempre maggiore di team con risorse condivise e dipendenti remoti, ad esempio nelle società con sedi negli Stati Uniti, in Europa e in Asia, con una forza vendita mobile in molti più territori. Siti Web di Azure ha la capacità di gestire le modifiche elastiche nella scala in modo facile e automatico.

Siti Web di Microsoft Azure consente di configurare la scalabilità automatica delle applicazioni Web tramite il portale di gestione, a seconda di due vettori: orari pianificati o utilizzo della CPU. La scalabilità automatica per i siti Web di Microsoft Azure rappresenta un metodo conveniente ed estremamente flessibile per offrire importanti modifiche d'uso per tutte le applicazioni aziendali, dalle applicazioni Web come il sistema Expense Reporting ai siti Web di marketing, caratterizzati da picchi di traffico per la breve durata di una promozione. Per altre informazioni e linee guida sulla scalabilità delle applicazioni Web con Siti Web di Microsoft Azure, vedere [Come scalare siti Web](http://azure.microsoft.com/documentation/articles/web-sites-scale/).

Oltre alla flessibilità di scalabilità di Siti Web di Microsoft Azure, l'intera piattaforma abilita la continuità aziendale e la resilienza attraverso la possibile distribuzione di applicazioni Web e le relative risorse tra più data center e aree geografiche.

## Riepilogo ##
Siti Web di Microsoft Azure offre una soluzione flessibile, conveniente e reattive alle esigenze dinamiche di un'azienda in un ambiente in rapida evoluzione. Le aziende usano Siti Web di Azure per aumentare la produttività e l'efficienza tramite una piattaforma gestita con moderne funzionalità DevOps e ridotta gestione pratica, fornendo allo stesso tempo capacità aziendali per quanto riguarda scalabilità, resilienza, sicurezza e integrazione con le risorse locali.

## Invito all'azione ##
Per altre informazioni sul servizio Siti Web di Microsoft Azure, visitare [http://aka.ms/enterprisewebsites](http://aka.ms/enterprisewebsites), dove vengono originate altre informazioni, e iscriversi per una versione di valutazione gratuita oggi stesso all'indirizzo [http://aka.ms/azuretrial](http://aka.ms/azuretrial) per valutare il servizio e scoprire i vantaggi che può offrire alla propria azienda.



<!--HONumber=42-->
