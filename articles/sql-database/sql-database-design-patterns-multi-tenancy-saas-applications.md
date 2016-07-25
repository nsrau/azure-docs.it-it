<properties
   pageTitle="Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure | Microsoft Azure" 
   description="Questo articolo fornisce informazioni sui requisiti e sui modelli comuni di architettura dei dati delle applicazioni di database multi-tenant in esecuzione in un ambiente cloud, nonché sui compromessi associati a tali modelli. Illustra anche l'utilità del servizio database SQL di Azure, con i relativi pool di database elastici e strumenti elastici, nel rispondere a tali requisiti senza dover scendere a compromessi."
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-design"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure

Questo articolo fornisce informazioni sui requisiti e sui modelli comuni di architettura dei dati delle applicazioni di database SaaS multi-tenant in esecuzione in un ambiente cloud, nonché sui compromessi associati a tali modelli. Illustra anche l'utilità del servizio database SQL di Azure, con i relativi pool di database elastici e strumenti elastici, nel rispondere a tali requisiti senza dover scendere a compromessi.

## Introduzione

L'esperienza con i provider di applicazioni SaaS degli ultimi anni ha mostrato che, nella progettazione dei modelli di tenancy per i livelli dati delle applicazioni multi-tenant, gli sviluppatori fanno spesso scelte controproducenti nel lungo periodo. Nella fase iniziale, la facilità di sviluppo e il costo ridotto del provider di cloud vengono considerati più importanti dell'isolamento dei tenant o della scalabilità delle applicazioni. Questo conduce spesso a problemi di soddisfazione dei clienti e a costosi cambi di rotta successivi.

Nel contesto di questo articolo un'applicazione multi-tenant è un'applicazione ospitata in un ambiente cloud che fornisce lo stesso set di servizi a più tenant, centinaia o migliaia, che non condividono o non visualizzano i dati degli altri. Una categoria tipica è rappresentata da un'applicazione SaaS che fornisce servizi ai relativi tenant in un ambiente cloud ospitato.

## Applicazioni multi-tenant

Le applicazioni multi-tenant sono un esempio significativo di applicazione in cui è possibile partizionare facilmente i dati e i carichi di lavoro. Con le applicazioni multi-tenant, ad esempio, i dati e il carico di lavoro possono in genere essere partizionati in base ai limiti dei tenant, dato che la maggior parte delle richieste rientra proprio entro tali limiti. Questa proprietà intrinseca dei dati e del carico di lavoro predilige i modelli di applicazione illustrati nella parte restante di questo articolo.

Tali applicazioni coprono l'intera gamma delle applicazioni basate su cloud, ad esempio:

- Applicazioni di database ISV di cui è in corso la transizione nel cloud come applicazioni SaaS.
- Applicazioni SaaS compilate per il cloud da zero.
- Applicazioni rivolte al consumer diretto o all'utente finale.
- Applicazioni aziendali rivolte ai dipendenti.

Sia le applicazioni SaaS nate nel cloud che le applicazioni SaaS originate da applicazioni di database ISV danno luogo, in genere, ad applicazioni multi-tenant. Queste applicazioni SaaS distribuiscono ai tenant un'applicazione software specializzata come un servizio. I tenant hanno accesso al servizio dell'applicazione e hanno la proprietà completa dei dati associati, archiviati come parte dell'applicazione. Tuttavia, per poter sfruttare i vantaggi del modello SaaS, i relativi tenant devono cedere un certo livello di controllo sui dati, affidandone la sicurezza e l'isolamento dai dati di altri tenant al fornitore della soluzione SaaS. MyOB, SnelStart, Salesforce e così via ne sono esempi tipici. Tutte queste applicazioni consentono il partizionamento in base ai limiti dei tenant e supportano quindi i modelli di applicazione illustrati nelle sezioni successive di questo articolo.

Le applicazioni che forniscono un servizio diretto ai consumer o ai dipendenti di un'organizzazione, anche detti utenti, anziché tenant, costituiscono un'altra categoria nella gamma delle applicazioni multi-tenant. I clienti sottoscrivono il servizio e non sono proprietari dei dati raccolti e archiviati dal provider di servizi. I provider di servizi hanno requisiti meno rigorosi per l'isolamento dei dati dei clienti, a parte le normative obbligatorie in materia di privacy. I provider di contenuti multimediali come Netflix, Spotify, Xbox Live e così via ne sono esempi tipici. Altri esempi di applicazioni facilmente partizionabili sono dati dalle applicazioni Internet rivolte ai clienti o dalle applicazioni IoT, in cui ogni cliente o dispositivo può essere usato come una partizione ed è possibile tracciare i limiti delle partizioni tra diversi utenti o dispositivi.

Tuttavia, non tutte le applicazioni sono facilmente partizionabili in base a una singola proprietà, ad esempio il tenant, il cliente, l'utente o il dispositivo. Si pensi, ad esempio, alle applicazioni ERP (Enterprise Resource Planning) complesse con prodotti, ordini e clienti. Queste hanno in genere uno schema complesso con migliaia di tabelle altamente interconnesse. Non c'è una singola strategia di partizionamento applicabile a tutte le tabelle e valida per l'intero carico di lavoro. Verranno quindi escluse in modo esplicito dalla trattazione di questo articolo.

Gli schemi progettuali multi-tenant illustrati nelle sezioni seguenti si applicano a tutti i modelli di applicazione con dati e carichi di lavoro facilmente partizionabili appena descritti. Per semplificare la presentazione, verranno semplicemente indicati come applicazioni SaaS multi-tenant.

## Compromessi nella progettazione di applicazioni multi-tenant

Gli sviluppatori che compilano applicazioni multi-tenant nel cloud devono tenere conto delle dimensioni generali seguenti:

-	***Isolamento del tenant***: gli sviluppatori devono prevenire l'accesso non autorizzato ai dati di altri tenant. Il requisito di isolamento si estende ad altre proprietà, come la protezione da altri tenant, la possibilità di ripristinare i dati di un determinato tenant, le personalizzazioni specifiche dei tenant e così via.
-	***Costo delle risorse cloud***: le applicazioni SaaS devono essere convenienti. Per questo motivo, nella progettazione delle applicazioni multi-tenant gli sviluppatori tendono a ottimizzare le applicazioni SaaS per costi inferiori di utilizzo delle risorse cloud, come calcolo, archiviazione e così via.
-	***Facilità della metodologia DevOps***: i provider di applicazioni multi-tenant devono garantirne l'isolamento, gestire le applicazioni e lo schema del database, monitorare l'integrità e risolvere i problemi dei tenant. La complessità di funzionamento e di sviluppo delle applicazioni si traduce direttamente in costi maggiori e minore soddisfazione dei tenant.
-	***Scalabilità***: l'aggiunta incrementale di altri tenant è fondamentale per la riuscita delle operazioni SaaS, così come l'aggiunta di capacità aggiuntiva per i singoli tenant che richiedono più risorse.

È necessario trovare dei compromessi tra queste dimensioni. L'offerta cloud al costo inferiore potrebbe non offrire necessariamente l'esperienza di sviluppo più semplice. Spesso gli sviluppatori non hanno a disposizione informazioni complete per le scelte relative a queste quattro dimensioni.

Per le applicazioni SaaS multi-tenant destinate ad altre aziende, l'isolamento dei tenant è spesso un requisito fondamentale. Gli sviluppatori si lasciano spesso tentare dagli apparenti vantaggi legati alla semplicità e ai costi, a scapito della scalabilità e dell'isolamento dei tenant. Questo compromesso può rivelarsi complesso e costoso, man mano che il servizio cresce e i requisiti di isolamento dei tenant diventano più importanti e vanno affrontati a livello di applicazione.

Per le applicazioni multi-tenant che forniscono un servizio consumer agli utenti finali, l'isolamento dei tenant potrebbe diventare meno prioritario rispetto all'ottimizzazione per il costo delle risorse cloud.

Un modello di sviluppo comune consiste nel riunire più tenant in uno o più database. I vantaggi apparenti di questo approccio sono dati dal costo inferiore, perché si pagano pochi database, e dalla semplicità di operare sul minor numero possibile di database. Per le applicazioni SaaS multi-tenant, nel tempo la decisione presenta svantaggi sostanziali in termini di scalabilità e isolamento dei tenant. Se è necessario l'isolamento dei tenant, occorre proteggere meglio i dati dei tenant nelle risorse di archiviazione condivise da accessi non autorizzati o dagli altri tenant e questo può aumentare notevolmente le attività di sviluppo delle applicazioni e i costi di manutenzione dell'isolamento. Analogamente, quando si aggiungono nuovi tenant il ridimensionamento corretto del livello dati dell'applicazione richiede, in genere, competenze di sviluppo per la ridistribuzione dei dati dei tenant tra i database.

## Modelli di dati multi-tenant 

Le comuni procedure di progettazione per l'inserimento dei dati dei tenant seguono tre modelli distinti, illustrati di seguito.


  ![modelli di dati multi-tenant](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png) Figura 1: Procedure di progettazione comuni per modelli di dati multi-tenant
  
1.	***Database per tenant***: questo approccio inserisce ogni tenant nel relativo database. Tutti i dati specifici di un tenant vengono limitati a quel database e isolati dagli altri tenant e dai relativi dati.
2.	***Database condiviso partizionato***: questo approccio usa più database, con più tenant che condividono un database. A ogni database viene assegnato un set distinto di tenant con una strategia di partizionamento, come il partizionamento hash, a intervallo oppure a elenco. Questa strategia di distribuzione dei dati è spesso detta partizionamento orizzontale.
3.	***Database condiviso singolo***: questo approccio usa un unico database, talvolta di grandi dimensioni, contenente i dati di tutti i tenant identificati senza ambiguità tramite una colonna ID tenant.
  
> [AZURE.NOTE] In alcuni casi tenant diversi vengono anche inseriti in schemi di database differenti, in cui il nome dello schema viene usato per eliminare l'ambiguità tra i diversi tenant. Questo approccio non è consigliato, perché in genere richiede l'uso di SQL dinamico e non può fare un uso efficace della memorizzazione nella cache del piano. La parte restante di questo articolo sarà quindi incentrata sull'approccio tabella condivisa in questa categoria.
 
## Caratterizzazione dei modelli di dati multi-tenant più comuni

Quando si valuta l'uso di questi modelli di dati multi-tenant è importante inquadrarli in termini di compromessi nella progettazione delle applicazioni, come illustrato nella sezione precedente.

-	***Isolamento***: livello di isolamento tra tenant come misura della quantità di isolamento dei tenant offerta da un modello di dati.
-	***Costo delle risorse cloud***: livello di condivisione delle risorse tra i tenant per ottimizzare il costo delle risorse cloud. Una risorsa può essere definita come costo di calcolo e archiviazione.
-	***Costo della metodologia DevOps***: la facilità di sviluppo e distribuzione e la gestibilità delle applicazioni riducono il costo complessivo delle operazioni SaaS.

Queste dimensioni permettono di caratterizzare i modelli di dati multi-tenant descritti in precedenza e il relativo utilizzo di database con lo spazio diviso in quadranti illustrato nella figura 2. Il grado di isolamento dei tenant e il livello di condivisione delle risorse rappresentano gli assi X e Y dello spazio. La grande freccia diagonale nella parte centrale indica i costi della metodologia DevOps.

![modelli più diffusi](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png) Figura 2: Caratterizzazione dei modelli di dati multi-tenant più comuni

Il quadrante inferiore destro nella figura 2 illustra l'uso di un approccio con un database condiviso singolo potenzialmente di grandi dimensioni insieme a una tabella condivisa, o uno schema separato, come uno dei modelli di applicazione. Offre ottime prestazioni di condivisione delle risorse, dato che tutti i tenant usano le stesse risorse di database, come CPU, memoria e I/O, in un database singolo. Tuttavia, l'isolamento dei tenant è limitato. È necessario eseguire altre operazioni per proteggere i tenant gli uni dagli altri a livello di applicazione. Questo può far aumentare notevolmente i costi della metodologia DevOps nello sviluppo e nella gestione di applicazioni. Un altro limite è costituito dalla scalabilità dell'hardware usato per ospitare il database.

La sezione centrale illustra l'uso di più tenant partizionati tra più database diversi, in genere con unità di scala hardware diverse, in cui ogni database ospita un sottoinsieme di tenant. Questo risolve il problema di scalabilità visto nel modello precedente. Se è necessaria una maggiore capacità per più tenant, questi possono essere facilmente inseriti in nuovi database allocati in nuove unità di scala hardware. Tuttavia, il livello di condivisione delle risorse risulta ridotto, dato che solo i tenant inseriti nelle stesse unità di scala condividono le risorse. Si noti anche che questo approccio non migliora di molto l'isolamento dei tenant, perché molti si trovano ancora nelle stesse posizioni senza essere protetti automaticamente dalle azioni degli altri tenant. La complessità dell'applicazione rimane elevata. Questo approccio viene spesso definito "partizionamento orizzontale".

Il quadrante superiore sinistro nella figura 2 illustra il terzo approccio, in cui ogni tenant viene inserito nel relativo database. Questo approccio offre ottime proprietà di isolamento dei tenant ma riduce la condivisione delle risorse quando ogni database fornisce risorse dedicate. È l'approccio ideale se tutti i tenant hanno carichi di lavoro prevedibili. Tuttavia, se i carichi di lavoro dei tenant si fanno meno prevedibili, come spesso avviene per le applicazioni SaaS, non è possibile ottimizzare la condivisione delle risorse. Questo può portare il provider a effettuare un provisioning eccessivo, per soddisfare le richieste, oppure a ridurre le risorse, con conseguente aumento dei costi o minore soddisfazione dei tenant. Di conseguenza, si rende necessario un maggiore livello di condivisione delle risorse tra i tenant per ridurre i costi della soluzione. L'aumento del numero di database aumenta anche i costi DevOps in termini di manutenzione e distribuzione delle applicazioni. Queste problematiche verranno trattate nella sezione successiva, ma è importante ricordare che questo metodo fornisce il miglior livello di isolamento dei tenant rispetto alle altre.

La scelta dello schema progettuale da parte del cliente dipende anche da questi fattori aggiuntivi:

-	***Proprietà dei dati dei tenant***: se i tenant mantengono la proprietà dei dati, viene preferito il modello con un database singolo per tenant.
-	***Scalabilità***: se l'applicazione è destinata a centinaia di migliaia o milioni di tenant, viene preferito un approccio di condivisione del database, come il partizionamento orizzontale, ma i requisiti di isolamento possono comunque comportare dei problemi.
-	***Modello aziendale e di valore***: se i ricavi per tenant sono molto bassi, inferiori a un dollaro, i requisiti di isolamento diventano meno importanti e viene preferita la condivisione dei database. Se i ricavi sono maggiori, è possibile usare l'approccio con un database per ogni tenant e ridurre il costo delle operazioni di sviluppo dell'applicazione.

Dati i compromessi illustrati nella figura 2, un modello multi-tenant ideale dovrebbe incorporare ottime proprietà di isolamento dei tenant con una condivisione ottimale delle risorse tra i tenant. Vale a dire, un modello che rientra nel quadrante superiore destro.

## Supporto multi-tenancy con il database SQL di Azure

Il database SQL di Azure supporta tutti i modelli di applicazione multi-tenant illustrati dalla figura 2. Con il pool di database elastici, ora supporta anche un nuovo modello di applicazione che riunisce i vantaggi in termini di condivisione delle risorse e isolamento dell'approccio database per tenant. Il modello è illustrato in verde nel quadrante superiore destro della figura 3. Le funzionalità e gli strumenti di database elastici nel database SQL di Azure contribuiscono a ridurre il costo DevOps legato allo sviluppo e al funzionamento di un'applicazione con più database, indicati dall'area ombreggiata nella figura 3. Gli strumenti consentono di compilare e gestire le applicazioni adottando uno qualsiasi dei modelli a più database. Le sottosezioni seguenti illustrano le funzionalità del database SQL e ne descrivono i vantaggi per i modelli multi-tenant in modo più dettagliato.

![modello di database SQL](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png) Figura 3: Modelli di applicazione multi-tenant con database SQL di Azure

## Modello database per tenant con strumenti e pool elastici

Il database SQL di Azure offre pool di database elastici che migliorano il supporto dell'approccio database per tenant, unendo l'isolamento dei tenant alla condivisione delle risorse tra i database dei tenant. È progettato come soluzione di livello dati per consentire ai provider SaaS di compilare applicazioni multi-tenant. Se combinato con offerte di strumenti di database elastici, il multi-tenancy è integrato e l'attività di condivisione delle risorse tra i tenant viene spostata dall'applicazione al livello del servizio di database. Le complessità legate alla gestione e all'esecuzione di query su larga scala tra i database vengono semplificate con transazioni, query e processi elastici, e con la libreria client dei database elastici.

| Requisiti dell'applicazione | Funzionalità del database SQL |
| ------------------------ | ------------------------- |
| Isolamento dei tenant e condivisione delle risorse | [Pool di database elastici:](sql-database-elastic-pool.md) questa funzionalità consente di allocare un pool di risorse SQLDB e di condividerle in vari database. I singoli database possono prendere dal pool tutte le risorse necessarie per rispondere ai picchi di domanda di capacità dovuti a modifiche nei carichi di lavoro dei tenant. In base alle esigenze è possibile aumentare o ridurre le prestazioni dello stesso pool elastico. I pool elastici sono anche facili da gestire, monitorare e correggere, in caso di problemi, a livello di pool. |
| Facilità di DevOps tra i database | [Pool di database elastici:](sql-database-elastic-pool.md) come sopra.|
||[Query elastica:](sql-database-elastic-query-horizontal-partitioning.md) consente di eseguire query tra database a scopo di creazione di report o analisi tra tenant.|
||[Processi elastici:](sql-database-elastic-jobs-overview.md) consente di creare pacchetti e distribuire in modo affidabile le operazioni di manutenzione dei database o le modifiche allo schema di database a vari database.|
||[Transazioni elastiche:](sql-database-elastic-transactions-overview.md) consente di elaborare modifiche a più database in modo atomico e isolato. Questa funzionalità è necessaria quando le applicazioni richiedono garanzie di tipo "tutto o niente" su varie operazioni di database. |
||[Libreria client dei database elastici:](sql-database-elastic-database-client-library.md) consente di gestire le distribuzioni dei dati e il mapping dei tenant ai database. |
||||

## Modelli condivisi

Come descritto in precedenza, per la maggior parte dei provider SaaS l'approccio con un modello condiviso può rappresentare un problema in termini di isolamento dei tenant, ma anche di complessità nello sviluppo e nella manutenzione delle applicazioni. Tuttavia, per le applicazioni multi-tenant che forniscono un servizio direttamente ai consumer finali, i requisiti di isolamento dei tenant possono non essere prioritari quanto l'ottimizzazione dei costi. Potrebbe essere possibile riunire i tenant in uno o più database a densità molto elevata per ridurre i costi. I modelli di database condivisi che usano un database singolo o più database partizionati possono offrire una maggiore efficienza in termini di condivisione delle risorse e riduzione del costo complessivo. Il database SQL di Azure offre alcune funzionalità che consentono a tali clienti di compilare l'isolamento per la sicurezza e la gestione su larga scala nel livello dati.

| Requisiti dell'applicazione | Funzionalità del database SQL |
| ------------------------ | ------------------------- |
| Funzionalità di isolamento di sicurezza | [Sicurezza a livello di riga](https://msdn.microsoft.com/library/dn765131.aspx) |
|| [Schema del database](https://msdn.microsoft.com/library/dd207005.aspx) |
| Facilità di DevOps tra i database | [Query elastica](sql-database-elastic-query-horizontal-partitioning.md) |
|| [Processi elastici](sql-database-elastic-jobs-overview.md) |
|| [Transazioni elastiche](sql-database-elastic-transactions-overview.md) |
|| [Libreria client dei database elastici](sql-database-elastic-database-client-library.md) |
|| [Divisione e unione dei database elastici](sql-database-elastic-scale-overview-split-and-merge.md) |
||||

## Conclusioni

I requisiti di isolamento dei tenant sono molto importanti per la maggior parte delle applicazioni SaaS multi-tenant. Il modo migliore per garantire l'isolamento propende decisamente verso l'approccio "database per tenant". Gli altri due approcci comportano investimenti complessi a livello di applicazione, che richiedono sviluppatori competenti per garantire l'isolamento. Questo può far aumentare notevolmente i costi e i rischi. Se non si tiene conto dei requisiti di isolamento nelle prime fasi di sviluppo del servizio, l'adeguamento può risultare ancora più dispendioso nei primi due modelli. Gli svantaggi principali associati al modello "database per tenant" sono correlati all'aumento dei costi delle risorse cloud. Questo a causa del livello di condivisione ridotto e della gestione di un numero elevato di database. Gli sviluppatori di applicazioni SaaS incontrano spesso difficoltà a causa di questi compromessi.

Con la maggior parte dei provider di servizi di database cloud, questi compromessi possono rappresentare ostacoli importanti. Il servizio di database SQL di Azure, invece, elimina gli ostacoli grazie ai pool di database elastici e alle funzionalità di database elastico. Gli sviluppatori SaaS possono combinare le caratteristiche di isolamento del modello "database per tenant" e ottimizzare la condivisione delle risorse e la facilità di gestione di un numero elevato di database con i pool elastici e gli strumenti associati.

Per i provider di applicazioni multi-tenant che non hanno requisiti di isolamento dei tenant e possono inserirli in un database a densità molto elevata per ridurre i costi, i modelli di dati "condivisi" possono offrire una maggiore efficienza nella condivisione delle risorse e contribuire a ridurre il costo complessivo. Gli strumenti di database elastici del database SQL di Azure, le librerie di partizionamento orizzontale e le funzionalità di sicurezza consentono ai provider SaaS di compilare e gestire le applicazioni multi-tenant.

## Passaggi successivi

Per un'app di esempio che illustra la libreria client, vedere l'[introduzione agli strumenti di database elastici](sql-database-elastic-scale-get-started.md).

Per un'applicazione di esempio che offra una soluzione per uno scenario SaaS (Software-as-a-Solution) che sfrutta i pool elastici per fornire un back-end di database economico e scalabile per un'applicazione SaaS, vedere [Elastic Pool Custom Dashboard for Saas](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) (Dashboard personalizzato pool elastico per Saas).

Per convertire i database esistenti e usare gli strumenti, vedere [Eseguire la migrazione dei database esistenti per ottenere scalabilità orizzontale](sql-database-elastic-convert-to-use-elastic-tools.md).

Per creare un nuovo pool, vedere [Creare un nuovo pool di database elastici con il portale di Azure](sql-database-elastic-pool-create-portal.md).

Per monitorare e gestire un pool di database elastici, vedere [Monitorare e gestire un pool di database elastici con il portale di Azure](sql-database-elastic-pool-manage-portal.md).

## Risorse aggiuntive

- [Che cos'è un pool di database elastici di Azure?](sql-database-elastic-pool.md)
- [Aumento del numero di istanze con il database SQL di Azure](sql-database-elastic-scale-introduction.md)
- [Applicazioni multi-tenant con strumenti di database elastici e sicurezza a livello di riga](sql-database-elastic-tools-multi-tenant-row-level-security.md)
- [Authentication in multitenant apps, using Azure AD and OpenID Connect (Autenticazione in app multi-tenant con Azure AD e OpenID Connect)](../guidance/guidance-multitenant-identity-authenticate.md)
- [Informazioni sull'applicazione Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)
- [Avvio rapido: esplorare soluzioni del database SQL di Azure](sql-database-solution-quick-starts.md)

## Domande e richieste di funzionalità

Per domande è possibile visitare il [forum sul database SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) mentre è possibile inserire le richieste di nuove funzionalità nel [forum dei commenti e suggerimenti sul database SQL](https://feedback.azure.com/forums/217321-sql-database/).









	

<!---HONumber=AcomDC_0713_2016-->