---
title: Informazioni sul servizio database SQL di Azure
description: Introduzione a Database SQL, con dettagli tecnici e funzionalità relative al sistema di gestione di database relazionali (RDBMS) Microsoft nel cloud.
keywords: introduzione a sql,intro a sql,informazioni sul database sql
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: overview
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/21/2020
ms.openlocfilehash: bf7e99f6e6201afefd316deafe37e38088fb9fae
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187120"
---
# <a name="what-is-azure-sql-database"></a>Che cos'è il database SQL di Azure?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Il database SQL di Azure è un motore di database PaaS (piattaforma distribuita come servizio) completamente gestito che esegue la maggior parte delle funzioni di gestione dei database, ad esempio l'aggiornamento, l'applicazione di patch, i backup e il monitoraggio, senza intervento dell'utente. Il database SQL di Azure è sempre in esecuzione sull'ultima versione stabile del motore di database di SQL Server in un sistema operativo con patch, con il 99,99% di disponibilità. Le funzionalità PaaS integrate nel database SQL di Azure consentono di concentrarsi sulle attività di ottimizzazione e amministrazione del database specifiche del dominio e cruciali per l'azienda.

Con il database SQL di Azure è possibile creare un livello di archiviazione dei dati a disponibilità elevata e ad alte prestazioni per le applicazioni e le soluzioni in Azure. Database SQL può essere la scelta ideale per un'ampia varietà di applicazioni cloud moderne, perché consente di elaborare sia dati relazionali che [strutture non relazionali](../multi-model-features.md), ad esempio grafi, JSON, dati spaziali e XML.

Il database SQL di Azure è basato sull'ultima versione stabile del [motore di database di Microsoft SQL Server](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json). È possibile usare funzionalità avanzate di elaborazione delle query, ad esempio [tecnologie in memoria a elevate prestazioni](../in-memory-oltp-overview.md) ed [elaborazione intelligente di query](/sql/relational-databases/performance/intelligent-query-processing?toc=%2fazure%2fsql-database%2ftoc.json). Le funzionalità più recenti di SQL Server vengono infatti rilasciate prima per il database SQL e quindi per SQL Server. È possibile ottenere le funzionalità più recenti di SQL Server senza sovraccarico per l'applicazione di patch o l'aggiornamento, testate in milioni di database. 

Database SQL offre prestazioni scalabili e facilmente definibili all'interno di due diversi modelli di acquisto: un [modello di acquisto basato su vCore](service-tiers-vcore.md) e un [modello di acquisto basato su DTU](service-tiers-dtu.md). Il database SQL è un servizio completamente gestito che fornisce per impostazione predefinita disponibilità elevata, backup e altre operazioni di manutenzione comuni. Microsoft si occupa di tutta la distribuzione di patch e aggiornamenti del codice di SQL e del sistema operativo. Non è necessario gestire l'infrastruttura sottostante.

Se non si ha familiarità con il database SQL di Azure, vedere il video *Panoramica del database SQL di Azure* della [serie di video approfonditi su SQL di Azure](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Database-Overview-7-of-61/player]

## <a name="deployment-models"></a>Modelli di distribuzione

Per la distribuzione di un database sono disponibili le opzioni seguenti:

- Il [database singolo](single-database-overview.md) rappresenta un database completamente gestito e isolato. Usare questa opzione se è necessario avere una singola origine dati affidabile per applicazioni cloud e microservizi moderni. Un database singolo è simile a un [database indipendente](/sql/relational-databases/databases/contained-databases?toc=%2fazure%2fsql-database%2ftoc.json) nel [motore di database di SQL Server](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json).
- Il [pool elastico](elastic-pool-overview.md) è una raccolta di [database singoli](single-database-overview.md) con un set condiviso di risorse, ad esempio CPU o memoria. I database singoli possono essere spostati all'interno e all'esterno di un pool elastico.

> [!IMPORTANT]
> Per informazioni sulle differenze tra le funzionalità del database SQL e di SQL Server, oltre che tra le diverse opzioni del database SQL di Azure, vedere [Funzionalità del database SQL](features-comparison.md).

Database SQL offre prestazioni prevedibili con più tipi di risorse, livelli di servizio e dimensioni di calcolo. Assicura scalabilità dinamica senza tempi di inattività, ottimizzazione intelligente incorporata, scalabilità e disponibilità globali, oltre a opzioni avanzate di sicurezza. Queste funzionalità consentono di impegnarsi per accelerare lo sviluppo di app e il time-to-market, invece di occuparsi della gestione di macchine virtuali e infrastruttura. Il database SQL è attualmente attivo in 38 data center di tutto il mondo, quindi è possibile eseguire il proprio database in un data center nelle vicinanze.

## <a name="scalable-performance-and-pools"></a>Prestazioni e pool scalabili

È possibile definire la quantità di risorse assegnate. 
- Con i database singoli, ogni database è isolato dagli altri ed è portabile. Ognuno ha una propria quantità garantita di risorse di calcolo, memoria e archiviazione. La quantità di risorse assegnate è dedicata a tale database e non è condivisa con altri in Azure. È possibile [dimensionare dinamicamente le risorse di database singolo](single-database-scale.md) aumentandole e riducendole. L'opzione del database singolo prevede risorse di calcolo, memoria e archiviazione diverse per esigenze diverse. È ad esempio possibile ottenere da 1 a 80 vCore o da 32 GB a 4 TB. Il [livello di servizio Hyperscale](service-tier-hyperscale.md) per i database singoli consente di aumentare lo spazio fino a 100 TB, con funzionalità di backup e ripristino rapidi.
- Con i pool elastici è possibile assegnare le risorse condivise da tutti i database nel pool. È possibile creare un nuovo database o spostare i database singoli esistenti in un pool di risorse per massimizzare l'uso delle risorse e risparmiare sui costi. Questa opzione offre inoltre la possibilità di [dimensionare dinamicamente le risorse del pool elastico](elastic-pool-scale.md), aumentandole e riducendole.

Si può creare la prima app in un piccolo database singolo, a un costo ridotto al mese, nel livello di servizio per utilizzo generico. È quindi possibile cambiare in qualsiasi momento il livello di servizio, manualmente o a livello di codice, per passare al livello di servizio business critical e soddisfare le esigenze della soluzione. È possibile regolare le prestazioni senza tempi di inattività per l'app o per i clienti. La scalabilità dinamica consente al database di rispondere in modo trasparente ai requisiti delle risorse soggetti a rapidi cambiamenti. Si paga solo per le risorse necessarie, quando sono necessarie.

La *scalabilità dinamica* è diversa dalla *scalabilità automatica*. Con la scalabilità automatica, un servizio viene ridimensionato automaticamente in base a vari criteri, mentre la scalabilità dinamica consente di gestire manualmente il ridimensionamento senza tempi di inattività. L'opzione del database singolo supporta la scalabilità dinamica manuale, ma non la scalabilità automatica. Per un'esperienza più automatica, valutare la possibilità di usare i pool elastici, che consentono ai database di condividere le risorse in un pool in base alle esigenze dei singoli database. Un'altra opzione consiste nell'uso di script che consentono di automatizzare la scalabilità per un database singolo. Per un esempio, vedere [Usare PowerShell per monitorare e ridimensionare un database singolo SQL](scripts/monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Modelli di acquisto

Database SQL offre i due modelli di acquisto seguenti:
- Il [modello di acquisto basato su vCore](service-tiers-vcore.md) consente di scegliere il numero di vCore, la quantità di memoria e la quantità e la velocità della risorsa di archiviazione. Il modello di acquisto basato su vCore offre inoltre la possibilità di usare [Vantaggio Azure Hybrid per SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) per un risparmio in termini di costi. Per altre informazioni su Vantaggio Azure Hybrid, vedere la sezione di domande frequenti più avanti in questo articolo.
- Il [modello di acquisto basato su DTU](service-tiers-dtu.md) offre una combinazione di risorse di calcolo, memoria e I/O in tre livelli di servizio per supportare carichi di lavoro di database da leggeri a pesanti. Le dimensioni di calcolo di ogni livello forniscono una diversa combinazione di queste risorse, a cui è possibile aggiungere altre risorse di archiviazione.
- Il [modello serverless](serverless-tier-overview.md) ridimensiona automaticamente le risorse di calcolo in base alle esigenze dei carichi di lavoro, addebitando la quantità di risorse di calcolo usate al secondo. Il livello di calcolo serverless inoltre sospende automaticamente i database durante i periodi di inattività, in cui viene addebitata solo l'archiviazione, e li riprende automaticamente quando necessario.

### <a name="service-tiers"></a>Livelli di servizio

Database SQL di Azure offre tre livelli di servizio progettati per tipi di applicazioni differenti:
- [Livello di servizio per utilizzo generico/Standard](service-tier-general-purpose.md) progettato per i carichi di lavoro comuni. Offre opzioni di calcolo e archiviazione bilanciate e orientate al budget.
- [Livello di servizio business critical/Premium](service-tier-business-critical.md) progettato per le applicazioni OLTP con frequenza elevata delle transazioni e latenza minima di I/O. Offre la massima resilienza agli errori tramite diverse repliche isolate.
- [Livello di servizio Hyperscale](service-tier-hyperscale.md) progettato per database OLTP di dimensioni molto grandi, con scalabilità automatica delle risorse di archiviazione e scalabilità fluida delle risorse di calcolo.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>I pool elastici ottimizzano l'utilizzo delle risorse

Per molte aziende e applicazioni, la possibilità di creare singoli database e aumentare o ridurre le prestazioni all'occorrenza è sufficiente, specialmente se i modelli d'utilizzo sono relativamente prevedibili. Nel caso di modelli di utilizzo imprevedibili, può risultare difficile gestire i costi e il modello aziendale. I [pool elastici](elastic-pool-overview.md) sono stati progettati per risolvere questo problema. Le risorse a elevate prestazioni vengono allocate a un pool invece che a un database singolo. Si paga per le risorse collettive del pool invece che per le prestazioni dei database singoli.

   ![Immagine che mostra i pool elastici nelle edizioni Basic, Standard e Premium](./media/sql-database-paas-overview/sqldb_elastic_pools.png)

Con i pool elastici non è necessario occuparsi di aumentare e ridurre le prestazioni dei database in base alla fluttuazione della domanda di risorse. I database in pool utilizzano le risorse relative alle prestazioni del pool elastico in base alla necessità. I database utilizzano le risorse del pool ma non superano i limiti impostati, per cui i costi rimangono prevedibili, indipendentemente dall'utilizzo dei singoli database.

È possibile [aggiungere e rimuovere i database al pool](elastic-pool-overview.md), ridimensionando l'app da un numero limitato di database a diverse migliaia, il tutto entro un budget controllabile. È anche possibile controllare le risorse minime e massime disponibili per i database del pool, per assicurarsi che nessuno usi tutte le risorse del pool e che a ognuno venga garantita una quantità minima di risorse. Per altre informazioni sui modelli di progettazione per applicazioni SaaS (Software as a Service) con pool elastici, vedere [Modelli di progettazione per applicazioni SaaS multi-tenant con Database SQL di Azure](saas-tenancy-app-design-patterns.md).

Gli script possono essere utili per il monitoraggio e il ridimensionamento dei pool elastici. Per un esempio, vedere [Usare PowerShell per il monitoraggio e il ridimensionamento di un pool elastico nel database SQL di Azure](scripts/monitor-and-scale-pool-powershell.md).


### <a name="blend-single-databases-with-pooled-databases"></a>Unire database singoli e database in pool

È possibile combinare database singoli con pool elastici e cambiare i relativi livelli di servizio in base alla specifica situazione. È anche possibile combinare e integrare altri servizi di Azure con Database SQL per soddisfare specifiche esigenze di progettazione di app, promuovere l'efficienza in termini di costi e di risorse, nonché sfruttare nuove opportunità di business.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Funzionalità complete di monitoraggio e avviso

Database SQL di Azure offre funzionalità avanzate di monitoraggio e risoluzione dei problemi, che consentono di ottenere informazioni dettagliate sulle caratteristiche del carico di lavoro. Le funzionalità e gli strumenti in questione sono:
 - Funzionalità predefinite di monitoraggio fornite dalla versione più recente del motore di database di SQL Server. Consentono di trovare informazioni dettagliate sulle prestazioni in tempo reale. 
 - Funzionalità di monitoraggio di soluzioni PaaS fornite da Azure, che consentono di monitorare e risolvere i problemi di un numero elevato di istanze di database.

[Query Store](/sql/relational-databases/performance/best-practice-with-the-query-store), una funzionalità di monitoraggio predefinita di SQL Server, che registra le prestazioni delle query in tempo reale e consente di identificare i potenziali problemi di prestazioni e i principali consumer di risorse. L'ottimizzazione automatica e le raccomandazioni forniscono consigli riguardo alle query con prestazioni ridotte e agli indici mancanti o duplicati. Con l'ottimizzazione automatica disponibile in Database SQL, è possibile applicare manualmente gli script che possono risolvere i problemi o consentire a Database SQL di applicare la correzione automaticamente. Database SQL può anche testare e verificare che la correzione offra un vantaggio e quindi mantenere o annullare la modifica a seconda del risultato. Oltre a Query Store e alle funzionalità di ottimizzazione automatica, è possibile usare [DMV e XEvent](monitoring-with-dmvs.md) standard per monitorare le prestazioni del carico di lavoro.

Azure include strumenti predefiniti di [monitoraggio delle prestazioni](performance-guidance.md) e [invio di avvisi](alerts-insights-configure-portal.md), oltre a valutazioni delle prestazioni, che consentono di monitorare lo stato di migliaia di database. Con questi strumenti è possibile valutare rapidamente l'impatto dell'aumento o della riduzione delle risorse in base ai requisiti di prestazioni correnti o previsti. Inoltre, Database SQL può [generare log di metriche e di risorse](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) per semplificare il monitoraggio. È possibile configurare il database SQL per archiviare l'utilizzo delle risorse, ruoli di lavoro, sessioni e connettività in una delle risorse di Azure seguenti:

- **Archiviazione di Azure**: per l'archiviazione di enormi quantità di dati di telemetria a un costo conveniente.
- **Hub eventi di Azure**: per l'integrazione dei dati di telemetria di Database SQL con soluzioni di monitoraggio personalizzate o pipeline attive.
- **Log di Monitoraggio di Azure**: per usare una soluzione di monitoraggio predefinita con funzionalità di report, avvisi e mitigazione.

![Diagramma dell'architettura di monitoraggio di Azure](./media/sql-database-paas-overview/architecture.png)

## <a name="availability-capabilities"></a>Funzionalità per la disponibilità

Database SQL di Azure assicura la continuità operativa delle organizzazioni durante le interruzioni del servizio. In un tradizionale ambiente di SQL Server sono in genere configurati almeno due computer in locale. Questi computer hanno copie dei dati esatte, mantenute in modo sincrono, per la protezione dagli errori di un singolo computer o componente. Questo ambiente fornisce disponibilità elevata, ma non protegge dalla distruzione del data center causata da catastrofi naturali.

Il ripristino di emergenza presuppone che un evento catastrofico sia geograficamente localizzato al punto tale che sarà comunque disponibile in un'altra località distante un altro computer o un set di computer con una copia dei dati. Per ottenere questa funzionalità in SQL Server, è possibile usare Gruppi di disponibilità AlwaysOn in esecuzione in modalità asincrona. Spesso non si vuole attendere che la replica venga eseguita a tale distanza prima di eseguire il commit di una transazione, quindi è possibile che si verifichi una perdita di dati quando si eseguono failover non pianificati.

I database nei livelli di servizio Premium e business critical già [prevedono funzionalità simili](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) per la sincronizzazione di un gruppo di disponibilità. I database nei livelli di servizio più bassi forniscono la ridondanza tramite archiviazione usando un [meccanismo diverso ma equivalente](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability). La logica predefinita offre protezione dagli errori di un singolo computer. La funzionalità di replica geografica attiva offre protezione da situazioni di emergenza in cui viene distrutta un'intera area.

Le zone di disponibilità di Azure hanno lo scopo di offrire protezione da interruzioni del servizio, ad esempio di corrente o di rete, in un singolo edificio di data center e in una singola area. In Database SQL si inseriscono repliche diverse in zone di disponibilità diverse (in realtà proprio in edifici diversi).

Infatti, il [contratto di servizio](https://azure.microsoft.com/support/legal/sla/) di Azure, sulla base di una rete globale di data center gestiti da Microsoft, prevede l'operatività delle app 24 ore su 24, 7 giorni su 7. La piattaforma Azure gestisce completamente ogni database e garantisce l'assenza di perdita di dati e una percentuale elevata di disponibilità dei dati. Azure gestisce automaticamente l'applicazione di patch, i backup, la replica, il rilevamento degli errori, i possibili errori di hardware, software o rete sottostanti, la distribuzione di correzioni di bug, i failover, gli aggiornamenti del database e altre attività di manutenzione. La disponibilità standard viene ottenuta separando i livelli di calcolo e archiviazione. La disponibilità Premium si ottiene integrando le risorse di calcolo e di archiviazione in un singolo nodo per le prestazioni e quindi implementando una tecnologia simile a Gruppi di disponibilità AlwaysOn. Per una descrizione completa delle funzionalità a disponibilità elevata del database SQL di Azure, vedere la [disponibilità del database SQL](high-availability-sla.md). 

Database SQL offre anche funzionalità predefinite di [continuità aziendale e scalabilità globale](business-continuity-high-availability-disaster-recover-hadr-overview.md). Tra queste sono incluse:

- [Backup automatici](automated-backups-overview.md):

  Il database SQL esegue automaticamente backup completi, differenziali e dei log delle transazioni dei database per consentirne il ripristino temporizzato. Per i database singoli e quelli in pool, è possibile configurare Database SQL per l'archiviazione di backup completi in Archiviazione di Azure per la conservazione a lungo termine. Per le istanza gestite, è anche possibile eseguire backup solo di copia per la conservazione a lungo termine.

- [Ripristini temporizzati](recovery-using-backups.md):

  Tutte le opzioni di distribuzione del database SQL supportano il ripristino di uno stato riferito a qualsiasi momento entro il periodo di conservazione automatico del backup per qualsiasi database.
- [Replica geografica attiva](active-geo-replication-overview.md):

  Le opzioni di database singolo e in pool consentono di configurare fino a quattro database secondari leggibili nello stesso data center o nei data center di Azure distribuiti a livello globale. Nel caso di un'applicazione SaaS che usa un database catalogo con volumi elevati di transazioni di sola lettura concorrenti, ad esempio, è possibile usare la replica geografica attiva per supportare la scalabilità in lettura globale e rimuovere i colli di bottiglia nel database primario causati dai carichi di lavoro di lettura. Per le istanze gestite, usare gruppi di failover automatico.
- [Gruppi di failover automatico](auto-failover-group-overview.md):

  Tutte le opzioni di distribuzione di Database SQL consentono di usare gruppi di failover per la disponibilità elevata e il bilanciamento del carico su scala globale. Le funzionalità includono la replica geografica trasparente e il failover di grandi set di database, pool elastici e istanze gestite. I gruppi di failover consentono la creazione di applicazioni SaaS distribuite a livello globale, con un sovraccarico amministrativo minimo. In questo modo tutta la complessa orchestrazione di monitoraggio routing e failover ricade su Database SQL.
- [Database con ridondanza della zona](high-availability-sla.md):

  Il database SQL consente di effettuare il provisioning di database o pool elastici Premium o business critical in più zone di disponibilità. Poiché questi database e pool elastici hanno più repliche ridondanti per la disponibilità elevata, l'inserimento di tali repliche in più zone di disponibilità assicura una maggiore resilienza, oltre alla possibilità di eseguire il ripristino automatico dagli errori di scalabilità del data center, senza perdita di dati.

## <a name="built-in-intelligence"></a>Intelligenza incorporata

Con Database SQL si ottengono funzionalità di intelligenza incorporata che consentono di ridurre drasticamente i costi di esecuzione e gestione dei database, oltre a ottimizzare sia le prestazioni che la sicurezza delle applicazioni. Eseguendo milioni di carichi di lavoro dei clienti 24 ore su 24, Database SQL raccoglie ed elabora una quantità enorme di dati di telemetria, nel pieno rispetto della privacy dei clienti. Vari algoritmi valutano continuamente i dati di telemetria, per eseguire il training del servizio e adattarlo all'applicazione.

### <a name="automatic-performance-monitoring-and-tuning"></a>Monitoraggio e ottimizzazione automatici delle prestazioni

Il database SQL offre informazioni dettagliate per le query che richiedono monitoraggio. Database SQL apprende dai modelli di database e consente di adattare lo schema dei database ai carichi di lavoro. Il database SQL offre [raccomandazioni per ottimizzare le prestazioni](database-advisor-implement-performance-recommendations.md), nelle quali è possibile verificare le azioni di ottimizzazione e applicarle.

Il monitoraggio costante dei database è tuttavia un'attività complessa e tediosa, in particolare quando sono coinvolti molti database. [Intelligent Insights](intelligent-insights-overview.md) esegue questo processo controllando automaticamente le prestazioni di Database SQL su larga scala, quindi segnala i problemi di riduzione del livello delle prestazioni, identifica la causa radice di ogni problema e fornisce raccomandazioni per il miglioramento, laddove possibile.

La gestione efficiente di un numero enorme di database può risultare impossibile, anche con tutti gli strumenti e i report resi disponibili da Database SQL e da Azure. Invece di monitorare e ottimizzare il database manualmente, è consigliabile delegare alcune di queste attività a Database SQL con l'[ottimizzazione automatica](automatic-tuning-overview.md). Database SQL applica automaticamente le raccomandazioni, esegue i test e verifica ogni azione di ottimizzazione per assicurare il continuo miglioramento delle prestazioni. In questo modo, Database SQL si adatta automaticamente ai carichi di lavoro in modo controllato e sicuro. Ottimizzazione automatica significa che le prestazioni del database vengono attentamente monitorate e confrontate prima e dopo ogni azione. Se le prestazioni non migliorano, l'azione di ottimizzazione viene annullata.

Molti partner Microsoft che eseguono [app multi-tenant SaaS](saas-tenancy-app-design-patterns.md) su Database SQL si affidano all'ottimizzazione automatica delle prestazioni per assicurarsi che le loro applicazioni offrano sempre prestazioni stabili e prevedibili. Questa funzionalità consente loro di ridurre notevolmente il rischio di problemi di prestazioni durante la notte. Inoltre, dato che anche parte della loro base clienti usa SQL Server, questi partner usano le stesse raccomandazioni di indicizzazione proposte da Database SQL ai clienti di SQL Server.

In [Database SQL sono disponibili](automatic-tuning-overview.md) due aspetti di ottimizzazione automatica:

- **Gestione automatica degli indici**: consente di identificare gli indici da aggiungere al database e quelli che è consigliabile rimuovere.
- **Correzione automatica dei piani**: identifica i piani problematici e corregge i problemi di prestazioni dei piani di SQL.

### <a name="adaptive-query-processing"></a>Elaborazione di query adattive

È possibile usare l'[elaborazione adattiva delle query](/sql/relational-databases/performance/intelligent-query-processing), che include l'esecuzione interleaved per le funzioni con valori di tabella con più istruzioni, il feedback delle concessioni di memoria in modalità batch e i join adattivi in modalità batch. Ognuna di queste funzionalità di elaborazione adattiva delle query applica tecniche di "apprendimento e adattamento" simili e consente di agevolare ulteriormente la risoluzione dei problemi di prestazioni correlati all'ottimizzazione di query generalmente difficili da gestire.

## <a name="advanced-security-and-compliance"></a>Sicurezza e conformità avanzate

Il database SQL offre un'ampia gamma di [funzionalità predefinite per sicurezza e conformità](../../active-directory/identity-protection/concept-identity-protection-security-overview.md) utili per fare in modo che le applicazioni possano soddisfare svariati requisiti di sicurezza e conformità.

> [!IMPORTANT]
> Microsoft ha certificato Database SQL di Azure (tutte le opzioni di distribuzione) rispetto a diversi standard di conformità. Per altre informazioni, visitare il [Centro protezione di Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), dove è possibile trovare l'elenco più aggiornato di certificazioni di conformità del database SQL.

### <a name="advance-threat-protection"></a>Advanced Threat Protection

Azure Defender per SQL è un pacchetto unificato che include le funzionalità di sicurezza avanzate SQL. Include funzionalità per la gestione delle vulnerabilità dei database e per il rilevamento di attività anomale che potrebbero indicare una minaccia per il database. Consente di abilitare e gestire queste funzionalità da un'unica posizione.

- [Valutazione delle vulnerabilità](sql-vulnerability-assessment.md):

  Questo servizio consente di individuare, monitorare e risolvere potenziali vulnerabilità del database. Consente di visualizzare lo stato di sicurezza e prevede passaggi utili per risolvere i problemi di sicurezza e migliorare la protezione del database.
- [Rilevamento delle minacce](threat-detection-configure.md):

  Questa funzionalità rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento del database. Monitora in modo continuo il database in caso di attività sospette e fornisce avvisi di sicurezza immediati su potenziali vulnerabilità, attacchi SQL injection e in caso di criteri anomali di accesso ai database. Gli avvisi di Threat Protection includono dettagli sulle attività sospette e consigliano azioni per l'analisi e la mitigazione della minaccia.

### <a name="auditing-for-compliance-and-security"></a>Controllo per conformità e sicurezza

Il servizio di [controllo](../../azure-sql/database/auditing-overview.md) tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo nell'account di archiviazione di Azure. Il controllo consente di agevolare la conformità alle normative, comprendere l'attività del database e ottenere informazioni dettagliate su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza.

### <a name="data-encryption"></a>Crittografia dei dati

Il database SQL consente di proteggere i dati fornendo la crittografia. Per i dati in movimento, usa [Transport Layer Security](https://support.microsoft.com/kb/3135244). Per i dati inattivi, usa [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Per i dati in uso, usa [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="data-discovery-and-classification"></a>Individuazione e classificazione dei dati

[Individuazione dati e classificazione](data-discovery-and-classification-overview.md) offre funzionalità integrate nel database SQL di Azure per l'individuazione, la classificazione, l'etichettatura e la protezione dei dati sensibili presenti nei database. Assicura visibilità nello stato di classificazione del database e tiene traccia dell'accesso a dati sensibili sia all'interno che all'esterno del database.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrazione in Azure Active Directory e autenticazione a più fattori

Il database SQL consente di gestire a livello centralizzato le identità degli utenti di database e altri servizi Microsoft grazie all'[integrazione in Azure Active Directory](authentication-aad-overview.md). Questa funzionalità semplifica la gestione delle autorizzazioni e ottimizza la sicurezza. Azure Active Directory supporta la funzionalità [Multi-Factor Authentication](authentication-mfa-ssms-overview.md) per aumentare la sicurezza di dati e applicazioni, supportando al tempo stesso un processo Single Sign-On.

## <a name="easy-to-use-tools"></a>Strumenti facili da usare

Il database SQL consente di creare e gestire le applicazioni in modo più facile e produttivo. Con il database SQL è possibile concentrarsi sull'attività principale, ovvero creare app straordinarie. Per la gestione e lo sviluppo in Database SQL è possibile usare strumenti e competenze già disponibili.

|Strumento|Descrizione|
|:---|:---|
|[Il portale di Azure](https://portal.azure.com/)|un'applicazione Web per la gestione di tutti i servizi di Azure.|
|[Azure Data Studio](/sql/azure-data-studio/)|Strumento per database multipiattaforma eseguito in Windows, macOS e Linux.|
|[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)|un'applicazione client gratuita e scaricabile per la gestione di qualsiasi infrastruttura SQL, da SQL Server a Database SQL.|
|[SQL Server Data Tools in Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt)|Applicazione client gratuita e scaricabile per lo sviluppo di database relazionali di SQL Server, database nel database SQL di Azure, pacchetti di Integration Services, modelli di dati di Analysis Services e report di Reporting Services.|
|[Visual Studio Code](https://code.visualstudio.com/docs)|un editor di codice open source, gratuito e scaricabile per Windows, macOS e Linux. Supporta estensioni, tra cui l'[estensione mssql](https://aka.ms/mssql-marketplace) per l'esecuzione di query in Microsoft SQL Server, database SQL di Azure e Azure Synapse Analytics (in precedenza SQL Data Warehouse).|

Database SQL supporta lo sviluppo di applicazioni con Python, Java, Node.js, PHP, Ruby e .NET in macOS, Linux e Windows. Il database SQL supporta le stesse [librerie di connessione](connect-query-content-reference-guide.md#libraries) di SQL Server.

[!INCLUDE [sql-database-create-manage-portal](../includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>Domande frequenti su Database SQL

### <a name="can-i-control-when-patching-downtime-occurs"></a>È possibile controllare quando si verificano i tempi di inattività dovuti all'applicazione di patch?

No. L'impatto dell'applicazione di patch non è in genere rilevante se si [impiega la logica di ripetizione dei tentativi](develop-overview.md#resiliency) nell'app. Per altre informazioni, vedere [Pianificazione di eventi di manutenzione di Azure in Database SQL di Azure](planned-maintenance.md).



## <a name="engage-with-the-sql-server-engineering-team"></a>Comunicare con il team di progettazione di SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): per domande relative all'amministrazione dei database.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): per domande relative allo sviluppo.
- [Pagina di domande e risposte Microsoft](/answers/topics/azure-synapse-analytics.html): per domande di carattere tecnico.
- [Commenti e suggerimenti](https://aka.ms/sqlfeedback): per segnalare bug e richiedere funzionalità.
- [Reddit](https://www.reddit.com/r/SQLServer/): per comunicazioni su SQL Server.

## <a name="next-steps"></a>Passaggi successivi

- Per un confronto e un calcolo dei costi riguardanti database singoli e pool elastici, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/sql-database/).
- Per iniziare, vedere questi argomenti di avvio rapido:

  - [Creare un database nel portale di Azure](single-database-create-quickstart.md)  
  - [Creare un database con l'interfaccia della riga di comando di Azure](az-cli-script-samples-content-guide.md)
  - [Creare un database usando PowerShell](powershell-script-content-guide.md)

- Per un set di esempi dell'interfaccia della riga di comando di Azure e di PowerShell, vedere:
  - [Esempi dell'interfaccia della riga di comando di Azure per database SQL](az-cli-script-samples-content-guide.md)
  - [Esempi di Azure PowerShell per database SQL](powershell-script-content-guide.md)

- Per informazioni sulle nuove funzionalità annunciate, vedere [Roadmap di Azure per Database SQL](https://azure.microsoft.com/roadmap/?category=databases).
- Visitare il [blog di Database SQL di Azure](https://azure.microsoft.com/blog/topics/database), in cui i membri del team di prodotto SQL Server annunciano novità e funzionalità di Database SQL.