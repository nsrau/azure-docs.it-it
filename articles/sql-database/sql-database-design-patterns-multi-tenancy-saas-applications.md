<properties
   pageTitle="Modelli di progettazione per le applicazioni SaaS multi-tenant e il database SQL di Azure | Microsoft Azure"
   description="Questo articolo illustra i requisiti e i modelli comuni di architettura dei dati delle applicazioni di database multi-tenant in esecuzione in un ambiente cloud e i compromessi associati a tali modelli. Illustra anche l'utilità del database SQL di Azure, con i relativi pool di database elastici e strumenti elastici, per soddisfare questi requisiti senza dover scendere a compromessi."
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
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

# Modelli di progettazione per le applicazioni SaaS multi-tenant e il database SQL di Azure

Questo articolo fornisce informazioni sui requisiti e i modelli comuni di architettura dei dati delle applicazioni di database SaaS multi-tenant in esecuzione in un ambiente cloud. Illustra anche i fattori da considerare e i compromessi tra i diversi modelli di progettazione. I pool di database elastici e gli strumenti elastici nel database SQL di Azure consentono di soddisfare requisiti specifici senza compromettere altri obiettivi.

Gli sviluppatori effettuano talvolta scelte controproducenti nell'ottica dei propri interessi a lungo termine quando progettano modelli tenancy per i livelli di dati di applicazioni multi-tenant. Almeno nella fase iniziale la facilità di sviluppo e i costi ridotti del provider di servizi cloud potrebbero essere considerati fattori più importanti dell'isolamento dei tenant o della scalabilità di un'applicazione. Questa scelta può causare problemi a livello di soddisfazione dei clienti e richiedere costose correzioni di rotta successive.

Un'applicazione multi-tenant è un'applicazione ospitata in un ambiente cloud che fornisce lo stesso set di servizi a centinaia o migliaia di tenant che non condividono o non visualizzano i dati degli altri. Un esempio è un'applicazione SaaS che fornisce servizi ai tenant in un ambiente ospitato nel cloud.

## Applicazioni multi-tenant

Nelle applicazioni multi-tenant, i dati e il carico di lavoro possono essere facilmente partizionati. È possibile partizionare i dati e il carico di lavoro, ad esempio, in base ai limiti dei tenant, dato che la maggior parte delle richieste rientra proprio entro tali limiti. Questa proprietà intrinseca dei dati e del carico di lavoro predilige i modelli di applicazione illustrati in questo articolo.

Gli sviluppatori usano questo tipo di applicazione nell'intera gamma delle applicazioni basate su cloud, ad esempio:

- Applicazioni di database di partner di cui è in corso la transizione al cloud come applicazioni SaaS
- Applicazioni SaaS create per il cloud sin dall'inizio
- Applicazioni rivolte direttamente ai clienti
- Applicazioni aziendali per i dipendenti

Le applicazioni SaaS progettate per il cloud e quelle originate da applicazioni di database di partner in genere saranno applicazioni multi-tenant. Queste applicazioni SaaS distribuiscono ai tenant un'applicazione software specializzata come un servizio. I tenant hanno accesso al servizio dell'applicazione e hanno la proprietà completa dei dati associati, archiviati come parte dell'applicazione. Per poter sfruttare i vantaggi offerti da SaaS, i tenant devono tuttavia cedere un certo livello di controllo sui propri dati. Fanno affidamento sul provider di servizi SaaS per mantenere i propri dati sicuri e isolati dai dati degli altri tenant. Esempi di questo tipo di applicazione SaaS multi-tenant sono MYOB, SnelStart e Salesforce.com. Tutte queste applicazioni possono essere partizionate in base ai limiti dei tenant e supportano i modelli di progettazione delle applicazioni illustrati in questo articolo.

Le applicazioni che offrono un servizio diretto ai clienti o ai dipendenti di un'organizzazione, anche detti utenti, anziché tenant, sono un'altra categoria nella gamma delle applicazioni multi-tenant. I clienti sottoscrivono il servizio e non sono proprietari dei dati raccolti e archiviati dal provider di servizi. I provider di servizi hanno requisiti meno rigorosi per l'isolamento reciproco dei dati dei clienti, a parte le normative governative vincolanti in materia di privacy. Provider di contenuti multimediali come Netflix, Spotify e Xbox LIVE sono alcuni esempi di questo tipo di applicazione multi-tenant per i clienti. Altri esempi di applicazioni facilmente partizionabili sono le applicazioni per Internet per i clienti o le applicazioni Internet delle cose (IoT), in cui ogni cliente o dispositivo può essere usato come partizione ed è possibile tracciare i limiti delle partizioni tra i diversi utenti o dispositivi.

Non tutte le applicazioni tuttavia sono facilmente partizionabili in base a una singola proprietà, ad esempio il tenant, il cliente, l'utente o il dispositivo. Un esempio è un'applicazione ERP (Enterprise Resource Planning) complessa con prodotti, ordini e clienti. In genere ha uno schema complesso con migliaia di tabelle altamente interconnesse.

Non esiste una singola strategia di partizionamento applicabile a tutte le tabelle e valida per il carico di lavoro dell'applicazione. Questo articolo è incentrato sulle applicazioni multi-tenant con carichi di lavoro e dati facilmente partizionabili.

## Compromessi nella progettazione di applicazioni multi-tenant

Il modello di progettazione scelto dallo sviluppatore di applicazioni multi-tenant si basa in genere sui fattori seguenti:

-	**Isolamento del tenant**: lo sviluppatore deve impedire l'accesso non autorizzato di un tenant ai dati di altri tenant. Il requisito di isolamento si estende ad altre proprietà, come la protezione da altri tenant, la possibilità di ripristinare i dati di un tenant, l'implementazione di personalizzazioni specifiche dei tenant.
-	**Costo delle risorse cloud**: le applicazioni SaaS devono essere convenienti. Lo sviluppatore di applicazioni multi-tenant tende a ottimizzare la progettazione in base ai costi più bassi di uso delle risorse cloud, come quelli di archiviazione e calcolo.
-	**Facilità della metodologia DevOps**: lo sviluppatore di applicazioni multi-tenant deve incorporare la protezione con l'isolamento, gestire e monitorare l'integrità dell'applicazione e dello schema del database e risolvere i problemi dei tenant. La complessità di funzionamento e di sviluppo delle applicazioni si traduce direttamente in costi e sfide maggiori a livello di soddisfazione dei tenant.
-	**Scalabilità**: la possibilità di aggiungere in modo incrementale più tenant e la capacità necessaria per i tenant è fondamentale per un corretto funzionamento delle operazioni SaaS.

Ogni fattore implica compromessi. L'offerta del cloud a un costo inferiore potrebbe non offrire necessariamente l'esperienza di sviluppo più semplice. È importante che uno sviluppatore adotti decisioni informate su queste opzioni e i compromessi nella fase di progettazione delle applicazioni.

Un modello di sviluppo comune consiste nel riunire più tenant in uno o più database. I vantaggi di questo approccio sono il costo inferiore perché il numero di database è ridotto offrendo anche una semplicità relativa in termini di uso. Nel tempo tuttavia lo sviluppatore di applicazioni multi-tenant SaaS comprenderà che questa scelta presenta svantaggi sostanziali in termini dia scalabilità e isolamento dei tenant. Se l'isolamento dei tenant diventa importante, occorre proteggere meglio i dati del tenant nelle risorse di archiviazione condivise da accessi non autorizzati o dagli altri tenant. I costi di manutenzione dell'isolamento e l'impegno in fase di sviluppo potrebbero aumentare notevolmente. Analogamente, se è necessario aggiungere tenant, il ridimensionamento del livello di dati di un'applicazione in genere richiede competenze per ridistribuire i dati dei tenant tra i database in questo modello di sviluppo.

Spesso l'isolamento dei tenant è un requisito fondamentale nelle applicazioni SaaS multi-tenant offerte ad aziende e organizzazioni. Gli sviluppatori potrebbero essere tentati dagli apparenti vantaggi legati alla semplicità e ai costi, a scapito della scalabilità e dell'isolamento dei tenant. Questo compromesso può rivelarsi complesso e costoso, mano a mano che il servizio cresce e i requisiti di isolamento dei tenant diventano più importanti e vengono gestiti a livello di applicazione. Per le applicazioni multi-tenant che offrono un servizio consumer ai clienti, l'isolamento dei tenant potrebbe tuttavia diventare meno prioritario rispetto all'ottimizzazione per il costo delle risorse cloud.

## Modelli di dati multi-tenant

Le comuni procedure di progettazione per l'inserimento dei dati dei tenant seguono tre modelli distinti, illustrati nella figura 1.


  ![Modelli di dati di applicazioni multi-tenant](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png) Figura 1: Procedure di progettazione comuni per modelli di dati multi-tenant

-	**Database per ogni tenant**: ogni tenant dispone del proprio database. Tutti i dati specifici di un tenant vengono limitati a quel database e isolati dagli altri tenant e dai relativi dati.
-	**Database condiviso partizionato**: più tenant condividono uno di più database. A ogni database viene assegnato un set distinto di tenant con una strategia di partizionamento come il partizionamento hash, a intervallo oppure a elenco. Questa strategia di distribuzione dei dati è spesso detta partizionamento orizzontale.
-	**Database condiviso singolo**: un unico database, talvolta di grandi dimensioni, contiene i dati di tutti i tenant, identificati senza ambiguità tramite una colonna ID tenant.

> [AZURE.NOTE] Lo sviluppatore di applicazioni potrebbe scegliere di inserire tenant diversi in schemi di database diversi, in cui il nome dello schema viene usato per evitare l'ambiguità tra i diversi tenant. Questo approccio non è consigliato perché in genere richiede l'uso di istruzioni SQL dinamiche e non può fare un uso efficace della memorizzazione nella cache del piano. La parte restante di questo articolo sarà quindi incentrata sull'approccio basato sulla tabella condivisa in questa categoria dell'applicazione multi-tenant.

## Modelli di dati multi-tenant comuni

È importante valutare i diversi tipi di modelli di dati multi-tenant nei termini di compromessi di progettazione dell'applicazione già identificati. Questi fattori consentono di identificare i tre modelli di dati multi-tenant più comuni descritti in precedenza e il relativo uso del database, come illustrato nella figura 2.

-	**Isolamento**. livello di isolamento tra tenant come misura della quantità di isolamento dei tenant offerta da un modello di dati.
-	**Costo delle risorse cloud**: la quantità di condivisione delle risorse tra tenant può ottimizzare i costi delle risorse cloud. Una risorsa può essere definita come costo di calcolo e archiviazione.
-	**Costo della metodologia DevOps**: la facilità di sviluppo e distribuzione e la gestibilità delle applicazioni riducono il costo complessivo delle operazioni SaaS.

L'asse Y nella figura 2 indica il livello di isolamento dei tenant. L'asse X indica il livello di condivisione delle risorse. La freccia grigia in diagonale nella parte centrale indica la direzione dei costi della metodologia DevOps con tendenza all'aumento o alla diminuzione.

![Modelli comuni di progettazione per le applicazioni multi-tenant](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png) Figura 2: modelli di dati multi-tenant comuni

Il quadrante in basso a destra nella figura 2 mostra un modello di applicazione che usa un approccio con database singolo condiviso potenzialmente di grandi dimensioni insieme a una tabella condivisa o schema separato. Offre vantaggi in termini di condivisione delle risorse perché tutti i tenant usano le stesse risorse di database, come CPU, memoria e input/output, in un database singolo. L'isolamento dei tenant è tuttavia limitato. Potrebbe essere necessario eseguire passaggi aggiuntivi per proteggere i tenant tra loro al livello dell'applicazione. Può aumentare notevolmente il costo della metodologia DevOps nello sviluppo e nella gestione dell'applicazione. Un altro limite è la scalabilità dell'hardware usato per ospitare il database.

Il quadrante in basso a sinistra nella figura 2 illustra più tenant partizionati tra più database, in genere con unità di scala di hardware diverse. Ogni database ospita un sottoinsieme di tenant risolvendo il problema di scalabilità di altri modelli. Se è necessaria maggiore capacità per più tenant, è possibile inserire facilmente i tenant in nuovi database allocati in nuove unità di scala dell'hardware. Tuttavia, il livello di condivisione delle risorse è ridotto poiché solo i tenant inseriti nella stessa unità di scala condividono le risorse. Questo approccio non migliora di molto l'isolamento dei tenant perché molti tenant si trovano ancora senza una protezione automatica dalle azioni degli altri tenant. La complessità dell'applicazione rimane elevata.

Il quadrante superiore sinistro nella figura 2 illustra il terzo approccio, in cui i dati di ogni tenant vengono inseriti nel relativo database. Questo approccio offre adeguate proprietà di isolamento dei tenant ma riduce la condivisione delle risorse quando ogni database dispone di risorse dedicate. È l'approccio ideale quando tutti i tenant hanno carichi di lavoro prevedibili. Il provider non può tuttavia ottimizzare la condivisione delle risorse se i carichi di lavoro dei tenant diventano meno prevedibili, come spesso avviene nelle applicazioni SaaS. Il provider deve eseguire il provisioning eccessivo per soddisfare le esigenze o ridurre le risorse, in entrambi i casi con maggiori costi o minore soddisfazione dei tenant. Si rende necessario un maggiore livello di condivisione delle risorse tra i tenant per ridurre i costi della soluzione. L'aumento del numero di database aumenta anche i costi DevOps in termini di manutenzione e distribuzione dell'applicazione. Nonostante queste problematiche, questo metodo fornisce l'isolamento migliore e più semplice per i tenant.

Questi fattori influiscono anche sul modello di progettazione scelto dal cliente:

-	**Proprietà dei dati dei tenant**: in un'applicazione in cui i tenant mantengono la proprietà dei propri dati viene preferito il modello con un database singolo per ogni tenant.
-	**Scalabilità**. in un'applicazione destinata a centinaia di migliaia o milioni di tenant viene preferito un approccio di condivisione, come il partizionamento orizzontale del database. I requisiti di isolamento possono tuttavia comportare problemi.
-	**Modello aziendale e di valore**: se i ricavi per tenant di un'applicazione sono molto bassi, inferiori a un dollaro, i requisiti di isolamento diventano meno importanti e viene preferita la condivisione dei database. Se i ricavi per tenant sono maggiori, è più appropriato un modello di database per ogni tenant. Può essere utile a ridurre i costi di sviluppo.

Considerati i compromessi di progettazione illustrati nella figura 2, un modello multi-tenant ideale deve incorporare adeguate proprietà di isolamento dei tenant con condivisione ottimale delle risorse tra i tenant. Si tratta di un modello che rientra nella categoria descritta nel quadrante superiore destro della figura 2.

## Supporto multi-tenancy con il database SQL di Azure

Il database SQL di Azure supporta tutti i modelli di applicazione multi-tenant illustrati dalla figura 2. Con il pool di database elastici, ora supporta anche un nuovo modello di applicazione che combina i vantaggi in termini di condivisione delle risorse e isolamento dell'approccio con database per ogni tenant. Il modello è illustrato nel quadrante superiore destro della figura 3. Le funzionalità e gli strumenti di database elastici nel database SQL consentono di ridurre i costi per lo sviluppo e il funzionamento di un'applicazione che include molti database (area ombreggiata della figura 3). Questi strumenti consentono di creare e gestire applicazioni che usano uno dei modelli con più database.

![Modelli nel database SQL di Azure](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png) Figura 3: Modelli di applicazione multi-tenant nel database SQL di Azure

## Modello database per tenant con strumenti e pool elastici

I pool di database elastici nel database SQL combinano l'isolamento dei tenant alla condivisione delle risorse tra i database dei tenant migliorando il supporto dell'approccio con database per ogni tenant. Il database SQL è progettato come soluzione di livello dati per consentire ai provider SaaS di compilare applicazioni multi-tenant. In combinazione con le offerte di strumenti di database elastici, il multi-tenancy è integrato e l'attività di condivisione delle risorse tra i tenant viene spostata dall'applicazione al livello del servizio di database. Le complessità legate alla gestione e all'esecuzione di query su larga scala tra i database vengono semplificate con transazioni, query e processi elastici, e con la libreria client dei database elastici.

| Requisiti dell'applicazione | Funzionalità del database SQL |
| ------------------------ | ------------------------- |
| Isolamento dei tenant e condivisione delle risorse | [Pool di database elastici:](sql-database-elastic-pool.md): un pool di risorse del database SQLD viene allocato e condiviso in vari database. I singoli database possono prendere dal pool tutte le risorse necessarie per rispondere ai picchi di domanda di capacità dovuti a modifiche nei carichi di lavoro dei tenant. In base alle esigenze è possibile aumentare o ridurre le prestazioni dello stesso pool elastico. I pool elastici sono anche facili da gestire, monitorare e correggere, in caso di problemi, a livello di pool. |
| Facilità di DevOps tra i database | [Pool di database elastici:](sql-database-elastic-pool.md) come sopra.|
||[Query elastica:](sql-database-elastic-query-horizontal-partitioning.md) la query viene eseguita tra database a scopo di creazione di report o l'analisi tra tenant.|
||[Processi elastici:](sql-database-elastic-jobs-overview.md) vengono creati pacchetti e distribuzioni affidabili delle operazioni di manutenzione dei database o delle modifiche allo schema di database a vari database.|
||[Transazioni elastiche:](sql-database-elastic-transactions-overview.md) le modifiche a più database vengono elaborate in modo atomico e isolato. Questa funzionalità è necessaria quando le applicazioni richiedono garanzie di tipo "tutto o niente" su varie operazioni di database. |
||[Libreria client dei database elastici](sql-database-elastic-database-client-library.md): le distribuzioni dei dati vengono gestite e i tenant vengono mappati ai database. |

## Modelli condivisi

Come descritto in precedenza, per la maggior parte dei provider SaaS l'approccio con un modello condiviso può rappresentare un problema in termini di isolamento dei tenant, ma anche di complessità nello sviluppo e nella manutenzione delle applicazioni. Tuttavia, per le applicazioni multi-tenant che forniscono un servizio direttamente ai consumer, i requisiti di isolamento dei tenant possono non essere prioritari quanto la riduzione al minimo dei costi. Potrebbe essere possibile riunire i tenant in uno o più database a densità molto elevata per ridurre i costi. I modelli di database condivisi che usano un database singolo o più database partizionati possono offrire una maggiore efficienza in termini di condivisione delle risorse e riduzione del costo complessivo. Il database SQL di Azure offre alcune funzionalità che consentono a tali clienti di compilare l'isolamento per una maggiore sicurezza e la gestione su larga scala nel livello dati.

| Requisiti dell'applicazione | Funzionalità del database SQL |
| ------------------------ | ------------------------- |
| Funzionalità di isolamento di sicurezza | [Sicurezza a livello di riga](https://msdn.microsoft.com/library/dn765131.aspx) |
|| [Schema del database](https://msdn.microsoft.com/library/dd207005.aspx) |
| Facilità di DevOps tra i database | [Query elastica](sql-database-elastic-query-horizontal-partitioning.md) |
|| [Processi elastici](sql-database-elastic-jobs-overview.md) |
|| [Transazioni elastiche](sql-database-elastic-transactions-overview.md) |
|| [Libreria client dei database elastici](sql-database-elastic-database-client-library.md) |
|| [Divisione e unione dei database elastici](sql-database-elastic-scale-overview-split-and-merge.md) |

## Riepilogo

I requisiti di isolamento dei tenant sono molto importanti per la maggior parte delle applicazioni SaaS multi-tenant. Il modo migliore per garantire l'isolamento propende decisamente verso l'approccio con database per ogni tenant. Gli altri due approcci comportano investimenti complessi a livello di applicazione, che richiedono sviluppatori competenti per garantire l'isolamento. Questo può far aumentare notevolmente i costi e i rischi. Se non si tiene conto dei requisiti di isolamento nelle prime fasi di sviluppo del servizio, l'adeguamento può risultare ancora più dispendioso nei primi due modelli. Gli svantaggi principali associati al modello con database per ogni tenant sono correlati all'aumento dei costi delle risorse cloud. Questo a causa del livello di condivisione ridotto e della gestione di un numero elevato di database. Gli sviluppatori di applicazioni SaaS incontrano spesso difficoltà a causa di questi compromessi.

Sebbene con la maggior parte dei provider di servizi di database cloud, questi compromessi possono rappresentare ostacoli importanti, il servizio di database SQL di Azure, elimina gli ostacoli grazie alle funzionalità di database elastico e dei pool di database elastici. Gli sviluppatori SaaS possono combinare le caratteristiche di isolamento del modello con database per ogni tenant e ottimizzare i miglioramenti apportati a livello di gestione e condivisione delle risorse di un numero elevato di database con i pool elastici e gli strumenti associati.

I provider di applicazioni multi-tenant che non hanno requisiti di isolamento dei tenant e che possono inserirli in un database a densità molto elevata per ridurre i costi, possono usare i modelli di dati "condivisi" per offrire maggiore efficienza nella condivisione delle risorse e ridurre il costo complessivo. Gli strumenti di database elastici del database SQL di Azure, le librerie di partizionamento orizzontale e le funzionalità di sicurezza consentono ai provider SaaS di compilare e gestire le applicazioni multi-tenant.

## Passaggi successivi

Per un'app di esempio che illustra la libreria client, vedere [Iniziare a utilizzare gli strumenti di database elastici](sql-database-elastic-scale-get-started.md).

Creare un [dashboard personalizzato del pool elastico per Saas](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) con un'applicazione di esempio che usa i pool elastici per una soluzione di database scalabile e conveniente.

Usare gli strumenti del database SQL di Azure per [eseguire la migrazione dei database esistenti per la scalabilità orizzontale](sql-database-elastic-convert-to-use-elastic-tools.md).

Visualizzare l'esercitazione su come [creare un pool elastico](sql-database-elastic-pool-create-portal.md).

Informazioni su come [monitorare e gestire un pool di database elastici](sql-database-elastic-pool-manage-portal.md).

## Risorse aggiuntive

- [Che cos'è un pool di database elastici di Azure?](sql-database-elastic-pool.md)
- [Aumento del numero di istanze con il database SQL di Azure](sql-database-elastic-scale-introduction.md)
- [Applicazioni multi-tenant con strumenti di database elastici e sicurezza a livello di riga](sql-database-elastic-tools-multi-tenant-row-level-security.md)
- [Authentication in multitenant apps, using Azure AD and OpenID Connect (Autenticazione in app multi-tenant con Azure AD e OpenID Connect)](../guidance/guidance-multitenant-identity-authenticate.md)
- [Informazioni sull'applicazione Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)
- [Avvio rapido: esplorare soluzioni del database SQL di Azure](sql-database-solution-quick-starts.md)

## Domande e richieste di funzionalità

Per domande, è disponibile il [forum sul database SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). Aggiungere una richiesta di funzionalità nel [forum dei commenti e suggerimenti sul database SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!---HONumber=AcomDC_0810_2016-->