---
title: Informazioni sul servizio database SQL di Azure
description: 'Introduzione al database SQL: dettagli tecnici e funzionalità del sistema di gestione di database relazionali Microsoft (RDBMS) nel cloud.'
keywords: introduzione a sql,intro a sql,informazioni sul database sql
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/08/2019
ms.openlocfilehash: 0d50ddbbeeaed48c14d07c42588efcbb20bb7d79
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411165"
---
# <a name="what-is-the-azure-sql-database-service"></a>Informazioni sul servizio database SQL di Azure

Il database SQL di Azure è un motore di database PaaS (Platform as a Service, Piattaforma distribuita come servizio) completamente gestito che si occupa della maggior parte delle funzioni di gestione del database, ad esempio l'aggiornamento, l'applicazione di patch, il backup e il monitoraggio, senza alcun coinvolgimento dell'utente. Il database SQL di Azure è sempre in esecuzione nella versione stabile più recente del motore di database di SQL Server e del sistema operativo con patch con disponibilità del 99,99%. Le funzionalità PaaS integrate nel database SQL di Azure consentono di concentrarsi sulle attività di amministrazione e ottimizzazione del database specifiche del dominio che sono fondamentali per l'azienda.

Con il database SQL di Azure è possibile creare un livello di archiviazione dati a disponibilità elevata e ad alte prestazioni per le applicazioni e le soluzioni in Azure.With Azure SQL Database, you can create a highly available and high-performance data storage layer for the applications and solutions in Azure. Il database SQL può essere la scelta giusta per un'ampia gamma di applicazioni cloud moderne perché consente di elaborare sia dati relazionali che [strutture non relazionali,](sql-database-multi-model-features.md)ad esempio grafici, JSON, spaziali e XML.

Si basa sull'ultima versione stabile del modulo di [gestione di database Microsoft SQL Server.](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json) È possibile utilizzare funzionalità avanzate di elaborazione delle query, ad esempio [tecnologie in memoria ad alte prestazioni](sql-database-in-memory.md) ed elaborazione intelligente delle [query.](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json) Infatti, le funzionalità più recenti di SQL Server vengono rilasciate prima al database SQL e quindi a SQL Server stesso. Si ottengono le funzionalità più recenti di SQL Server senza sovraccarico per l'applicazione di patch o l'aggiornamento, testate in milioni di database. 

Il database SQL consente di definire e scalare facilmente le prestazioni all'interno di due diversi modelli di acquisto: un modello di [acquisto basato su vCore](sql-database-service-tiers-vcore.md) e un modello di [acquisto basato su DTU.](sql-database-service-tiers-dtu.md) Il database SQL è un servizio completamente gestito con disponibilità elevata incorporata, backup e altre operazioni di manutenzione comuni. Microsoft gestisce tutte le patch e l'aggiornamento del codice SQL e del sistema operativo. Non è necessario gestire l'infrastruttura sottostante.

> [!NOTE]
> Per i termini pertinenti e le relative definizioni, vedere il [glossario](sql-database-glossary-terms.md)dei termini del database SQL .

## <a name="deployment-models"></a>Modelli di distribuzione

Per la distribuzione di un database SQL di Azure sono disponibili le opzioni seguenti:

![Diagramma delle opzioni di distribuzione](./media/sql-database-technical-overview/deployment-options.png)

- [Un singolo database](sql-database-single-database.md) rappresenta un database completamente gestito e isolato. È possibile usare questa opzione se si dispone di applicazioni cloud moderne e microservizi che richiedono una singola origine dati affidabile. Un singolo database è simile a un [database indipendente](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) in Motore di database di Microsoft [SQL ServerDatabase Engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
- [L'istanza gestita](sql-database-managed-instance.md) è un'istanza completamente gestita del Motore di database di [Microsoft SQL ServerDatabase Engine.](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json) Contiene un set di database che possono essere utilizzati insieme. Usare questa opzione per eseguire facilmente la migrazione dei database di SQL Server locali nel cloud di Azure e per le applicazioni che devono usare le funzionalità di database offerte da Motore di database di SQL ServerSQL Server Database Engine.Use this option for easy migration of on-premises SQL Server databases to the Azure cloud, and for applications that need to use the database features that SQL Server Database Engine provides.
- [Il pool elastico](sql-database-elastic-pool.md) è una raccolta di [singoli database](sql-database-single-database.md) con un set condiviso di risorse, ad esempio CPU o memoria. I database singoli possono essere spostati all'interno e all'esterno di un pool elastico.

> [!IMPORTANT]
> Per comprendere le differenze di funzionalità tra Il database SQL e SQL Server, nonché le differenze tra le diverse opzioni di distribuzione del database SQL di Azure, vedere [Funzionalità del database SQL](sql-database-features.md).

Il database SQL offre prestazioni prevedibili con più tipi di risorse, livelli di servizio e dimensioni di calcolo. Fornisce scalabilità dinamica senza tempi di inattività, ottimizzazione intelligente integrata, scalabilità e disponibilità globali e opzioni di sicurezza avanzate. Queste funzionalità consentono di concentrarsi sullo sviluppo rapido delle app e sull'accelerazione del time-to-market, anziché sulla gestione di macchine virtuali e infrastruttura. Il servizio database SQL si trova attualmente in 38 data center in tutto il mondo, pertanto è possibile eseguire il database in un data center nelle vicinanze.

## <a name="scalable-performance-and-pools"></a>Prestazioni e pool scalabili

È possibile definire la quantità di risorse assegnate. 
- Con singoli database, ogni database è isolato dagli altri ed è portabile. Ognuna dispone di una propria quantità garantita di risorse di elaborazione, memoria e archiviazione. The amount of the resources assigned to the database is dedicated to that database, and isn't shared with other databases in Azure. È possibile [scalare](sql-database-single-database-scale.md) dinamicamente singole risorse di database verso l'alto e verso il basso. L'opzione di database singolo fornisce risorse di calcolo, memoria e archiviazione diverse per esigenze diverse. Ad esempio, è possibile ottenere da 1 a 80 vCore o da 32 GB a 4 TB. Il livello di [servizio di hyperscale](sql-database-service-tier-hyperscale.md) per singolo database consente di scalare fino a 100 TB, con funzionalità di backup e ripristino veloci.
- Con i pool elastici è possibile assegnare risorse condivise da tutti i database del pool. È possibile creare un nuovo database o spostare i singoli database esistenti in un pool di risorse per ottimizzare l'utilizzo delle risorse e risparmiare denaro. Questa opzione consente inoltre di scalare dinamicamente le risorse del [pool elastico](sql-database-elastic-pool-scale.md) su e giù.
- Con le istanze gestite, ogni istanza è isolata dalle altre con risorse garantite. All'interno di un'istanza gestita, i database dell'istanza condividono un set di risorse. È possibile scalare dinamicamente le [risorse dell'istanza gestita](sql-database-managed-instance-resource-limits.md) verso l'alto e verso il basso.

Puoi creare la tua prima app in un database singolo di piccole dimensioni a un costo minimo al mese nel livello di servizio generico. È quindi possibile modificare il livello di servizio manualmente o a livello di codice in qualsiasi momento al livello di servizio business-critical, per soddisfare le esigenze della soluzione. È possibile regolare le prestazioni senza tempi di inattività per l'app o per i clienti. La scalabilità dinamica consente al database di rispondere in modo trasparente ai requisiti delle risorse soggetti a rapidi cambiamenti. Paghi solo per le risorse di cui hai bisogno quando ne hai bisogno.

*La scalabilità dinamica* è diversa dalla *scalabilità automatica.* Con la scalabilità automatica, un servizio viene ridimensionato automaticamente in base a vari criteri, mentre la scalabilità dinamica consente di gestire manualmente il ridimensionamento senza tempi di inattività. L'opzione di database singolo supporta la scalabilità dinamica manuale, ma non la scalabilità automatica. Per un'esperienza più automatica, valutare la possibilità di usare i pool elastici, che consentono ai database di condividere le risorse in un pool in base alle esigenze dei singoli database. Un'altra opzione consiste nell'utilizzare script che consentono di automatizzare la scalabilità per un singolo database. Per un esempio, vedere [Usare PowerShell per monitorare e ridimensionare un database singolo SQL](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Modelli di acquisto

Il database SQL offre i seguenti modelli di acquisto:
- Il modello di [acquisto basato su vCore](sql-database-service-tiers-vcore.md) consente di scegliere il numero di vCore, la quantità di memoria e la quantità e la velocità di archiviazione. Il modello di acquisto basato su vCore offre inoltre la possibilità di usare [Vantaggio Azure Hybrid per SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) per un risparmio in termini di costi. Per altre informazioni sul vantaggio Azure Hybrid, vedere la sezione "Domande frequenti" più avanti in questo articolo.
- Il modello di [acquisto basato su DTU](sql-database-service-tiers-dtu.md) offre una combinazione di risorse di elaborazione, memoria e I/O in tre livelli di servizio, per supportare carichi di lavoro di database da leggeri a carichi di lavoro di database pesanti. Le dimensioni di calcolo di ogni livello forniscono una diversa combinazione di queste risorse, a cui è possibile aggiungere altre risorse di archiviazione.
- Il [modello senza server](sql-database-serverless.md) scala automaticamente il calcolo in base alla domanda del carico di lavoro e le fatture per la quantità di calcolo utilizzata al secondo. Il livello di calcolo senza server sospende automaticamente i database durante i periodi di inattività quando viene fatturata solo l'archiviazione e riprende automaticamente i database al reso in corso.

### <a name="service-tiers"></a>Livelli di servizio

Il database SQL di Azure offre tre livelli di servizio progettati per diversi tipi di applicazioni:Azure SQL Database offers three service tiers that are designed for different types of applications:
- [Scopo generale/livello](sql-database-service-tier-general-purpose.md) di servizio Standard progettato per carichi di lavoro comuni. Offre opzioni di elaborazione e archiviazione bilanciate orientate al budget.
- Livello di servizio [Business Critical/Premium](sql-database-service-tier-business-critical.md) progettato per applicazioni OLTP con un tasso di transazioni elevato e un I/O con latenza più bassa. Offre la massima resilienza agli errori utilizzando diverse repliche isolate.
- Livello di servizio [Hyperscale](sql-database-service-tier-hyperscale.md) progettato per database OLTP di grandi dimensioni e la possibilità di scalare automaticamente l'archiviazione e la scalabilità elaborazione in modo fluido.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>I pool elastici ottimizzano l'utilizzo delle risorse

Per molte aziende e applicazioni, la possibilità di creare singoli database e aumentare o ridurre le prestazioni all'occorrenza è sufficiente, specialmente se i modelli d'utilizzo sono relativamente prevedibili. I modelli di utilizzo imprevedibili possono rendere difficile la gestione dei costi e del modello di business. [Le piscine elastiche](sql-database-elastic-pool.md) sono progettate per risolvere questo problema. Allocare le risorse delle prestazioni a un pool anziché a un singolo database. Si paga per le risorse di prestazioni collettive del pool anziché per le prestazioni del singolo database.

   ![Grafico che mostra le piscine elastiche nelle edizioni basic, standard e premium](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Con i pool elastici, non è necessario concentrarsi sulla composizione delle prestazioni del database in alto e in calo in quanto la domanda di risorse varia. I database in pool utilizzano le risorse relative alle prestazioni del pool elastico in base alla necessità. I database in pool utilizzano ma non superano i limiti del pool, pertanto il costo rimane prevedibile anche se l'utilizzo dei singoli database non lo fa.

È possibile [aggiungere e rimuovere database nel pool,](sql-database-elastic-pool-manage-portal.md)ridimensionando l'app da una manciata di database a migliaia, il tutto entro un budget controllato. È inoltre possibile controllare le risorse minime e massime disponibili per i database nel pool, per assicurarsi che nessun database del pool utilizzi tutte le risorse del pool e che ogni database in pool disponga di una quantità minima garantita di risorse. Per altre informazioni sui modelli di progettazione per le applicazioni SaaS (Software as a Service) che usano pool elastici, vedere [Progettare modelli per applicazioni SaaS multi-tenant con database SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Gli script possono essere utili per il monitoraggio e il ridimensionamento dei pool elastici. Per un esempio, vedere [Usare PowerShell per monitorare e ridimensionare un pool elastico SQL nel database SQL](scripts/sql-database-monitor-and-scale-pool-powershell.md)di Azure.For an example, see Use PowerShell to monitor and scale a SQL elastic pool in Azure SQL Database.

> [!IMPORTANT]
> Un'istanza gestita non supporta i pool elastici. Un'istanza gestita è invece una raccolta di database dell'istanza che condividono le risorse dell'istanza gestita.

### <a name="blend-single-databases-with-pooled-databases"></a>Unire database singoli e database in pool

È possibile combinare singoli database con pool elastici e modificare i livelli di servizio di singoli database e pool elastici per adattarsi alla situazione. È anche possibile combinare altri servizi di Azure con il database SQL per soddisfare le esigenze specifiche di progettazione dell'app, aumentare i costi e l'efficienza delle risorse e sbloccare nuove opportunità di business.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Funzionalità complete di monitoraggio e avviso

Il database SQL di Azure offre funzionalità avanzate di monitoraggio e risoluzione dei problemi che consentono di ottenere informazioni più approfondite sulle caratteristiche del carico di lavoro. Queste funzionalità e strumenti includono:
 - The built-in monitoring capabilities provided by the latest version of SQL Server Database Engine. Consentono di trovare informazioni dettagliate sulle prestazioni in tempo reale. 
 - Funzionalità di monitoraggio PaaS fornite da Azure che consentono di monitorare e risolvere i problemi di un numero elevato di istanze di database.

[Query Store](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store), una funzionalità di monitoraggio di SQL Server incorporata, registra le prestazioni delle query in tempo reale e consente di identificare i potenziali problemi di prestazioni e i principali consumer di risorse. L'ottimizzazione automatica e i consigli forniscono consigli sulle query con le prestazioni regredite e gli indici mancanti o duplicati. L'ottimizzazione automatica nel database SQL consente di applicare manualmente gli script in grado di risolvere i problemi o di consentire al database SQL di applicare la correzione. Il database SQL può inoltre testare e verificare che la correzione offra qualche vantaggio e mantenga o interviscerà la modifica a seconda del risultato. Oltre all'archivio query e alle funzionalità di ottimizzazione automatica, è possibile usare [dMV](sql-database-monitoring-with-dmvs.md) standard e XEvent per monitorare le prestazioni del carico di lavoro.

Azure offre strumenti di monitoraggio e [avviso](sql-database-insights-alerts-portal.md) [delle prestazioni incorporati,](sql-database-performance-guidance.md) combinati con le classificazioni delle prestazioni, che consentono di monitorare lo stato di migliaia di database. Utilizzando questi strumenti, è possibile valutare rapidamente l'impatto della scalabilità verticale o verso il basso, in base alle esigenze di prestazioni correnti o previste. Database SQL può anche [generare log di metrica e diagnostica](sql-database-metrics-diag-logging.md) per facilitare il monitoraggio. È possibile configurare il database SQL per archiviare l'utilizzo delle risorse, ruoli di lavoro, sessioni e connettività in una delle risorse di Azure seguenti:

- **Archiviazione di Azure**: per l'archiviazione di enormi quantità di dati di telemetria a un costo conveniente.
- Hub eventi di **Azure:** per l'integrazione della telemetria del database SQL con la soluzione di monitoraggio personalizzata o le pipeline a caldo.
- Log di **Monitoraggio di Azure:** per una soluzione di monitoraggio incorporata con funzionalità di report, avvisi e attenuazione.

![Diagramma dell'architettura di monitoraggio di Azure](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Funzionalità per la disponibilità

Il database SQL di Azure consente all'azienda di continuare a operare durante le interruzioni. In un ambiente SQL Server tradizionale, in genere sono disponibili almeno due computer in locale. Queste macchine dispongono di copie esatte, gestite in modo sincrono, dei dati da proteggere da un guasto di un singolo computer o componente. Questo ambiente offre un'elevata disponibilità, ma non protegge da un disastro naturale che distrugge il data center.

Il ripristino di emergenza presuppone che un evento catastrofico sia sufficientemente localizzato geograficamente da avere un altro computer o un altro set di computer con una copia dei dati lontano. In SQL ServerSQL Server è possibile usare Gruppi di disponibilità AlwaysOnAlways On Availability Groups in esecuzione in modalità asincrona per ottenere questa funzionalità. Le persone spesso non vogliono attendere che la replica venga eseguita così lontano prima di eseguire il commit di una transazione, quindi c'è la possibilità di perdita di dati quando si esegue failover non pianificato.

I database nei livelli di servizio premium e business critical fanno già qualcosa di [simile](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) alla sincronizzazione di un gruppo di disponibilità. I database nei livelli di servizio inferiori forniscono ridondanza tramite l'archiviazione utilizzando un [meccanismo diverso ma equivalente.](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) La logica integrata consente di proteggersi da un singolo errore del computer. La funzionalità di replica geografica attiva offre la possibilità di proteggersi da un'emergenza in cui viene distrutta un'intera area.

Le zone di disponibilità di Azure tentano di proteggersi dall'interruzione di un singolo data center all'interno di una singola area. Ti aiuta a proteggerti dalla perdita di energia o di rete per un edificio. Nel database SQL, inserire le diverse repliche in zone di disponibilità diverse (edifici diversi, in modo efficace).

Infatti, il contratto di servizio [(SLA)](https://azure.microsoft.com/support/legal/sla/) di Azure, alimentato da una rete globale di data center gestiti da Microsoft, consente di mantenere l'app in esecuzione 24 ore su 24, 7 giorni su 7. La piattaforma Azure gestisce completamente ogni database e garantisce nessuna perdita di dati e un'alta percentuale di disponibilità dei dati. Azure gestisce automaticamente l'applicazione di patch, i backup, la replica, il rilevamento degli errori, i possibili errori di hardware, software o rete sottostanti, la distribuzione di correzioni di bug, i failover, gli aggiornamenti del database e altre attività di manutenzione. La disponibilità standard viene ottenuta separando i livelli di calcolo e archiviazione. Premium availability is achieved by integrating compute and storage on a single node for performance, and then implementing technology similar to Always On Availability Groups. Per una descrizione completa delle funzionalità a disponibilità elevata del database SQL di Azure, vedere la [disponibilità del database SQL](sql-database-high-availability.md). 

Inoltre, il database SQL offre funzionalità di [continuità aziendale e scalabilità globale](sql-database-business-continuity.md) incorporate. incluse le seguenti:

- [Backup automatici](sql-database-automated-backups.md):

  Il database SQL esegue automaticamente backup completi, differenziali e del log delle transazioni dei database SQL per consentire il ripristino in qualsiasi momento. Per i singoli database e i database in pool, è possibile configurare il database SQL per archiviare i backup completi del database in Archiviazione di Azure per la conservazione dei backup a lungo termine. Per le istanza gestite, è anche possibile eseguire backup solo di copia per la conservazione a lungo termine.

- [Ripristino temporizzato](sql-database-recovery-using-backups.md):

  Tutte le opzioni di distribuzione del database SQL supportano il ripristino in qualsiasi momento all'interno del periodo di conservazione del backup automatico per qualsiasi database SQL.
- [Replica geografica attiva](sql-database-active-geo-replication.md):

  Le opzioni relative a database singolo e a database in pool consentono di configurare fino a quattro database secondari leggibili nello stesso data center di Azure o distribuiti a livello globale. Ad esempio, se si dispone di un'applicazione SaaS con un database di catalogo con un volume elevato di transazioni di sola lettura simultanee, utilizzare la replica geografica attiva per abilitare la scalabilità di lettura globale. In questo modo vengono rimossi i colli di bottiglia nel database primario dovuti ai carichi di lavoro di lettura. Per le istanze gestite, usare gruppi di failover automatico.
- [Gruppi di failover automatico](sql-database-auto-failover-group.md):

  Tutte le opzioni di distribuzione del database SQL consentono di usare i gruppi di failover per abilitare la disponibilità elevata e il bilanciamento del carico su scala globale. Sono inclusi la replica geografica trasparente e il failover di set di database, pool elastici e istanze gestite di grandi dimensioni. I gruppi di failover consentono la creazione di applicazioni SaaS distribuite a livello globale, con un sovraccarico amministrativo minimo. In questo modo tutte le complesse orchestrazione di monitoraggio, routing e failover al database SQL.
- [Database con ridondanza di zona](sql-database-high-availability.md):

  Il database SQL consente di effettuare il provisioning di database o pool elastici Premium o business critical in più zone di disponibilità. Poiché questi database e pool elastici dispongono di più repliche ridondanti per la disponibilità elevata, l'inserimento di tali repliche in più zone di disponibilità offre una maggiore resilienza. Ciò include la possibilità di eseguire automaticamente il ripristino dagli errori di scalabilità del data center, senza perdita di dati.

## <a name="built-in-intelligence"></a>Intelligenza incorporata

Con il database SQL, è possibile ottenere funzionalità incorporate che consentono di ridurre drasticamente i costi di esecuzione e gestione dei database e che consentono di ottimizzare le prestazioni e la sicurezza dell'applicazione. Eseguendo milioni di carichi di lavoro dei clienti 24 ore su 24, SQL Database raccoglie ed elabora un'enorme quantità di dati di telemetria, rispettando al contempo pienamente la privacy dei clienti. Vari algoritmi valutano continuamente i dati di telemetria in modo che il servizio possa apprendere e adattarsi all'applicazione.

### <a name="automatic-performance-monitoring-and-tuning"></a>Monitoraggio e ottimizzazione automatici delle prestazioni

Il database SQL offre informazioni dettagliate per le query che richiedono monitoraggio. Il database SQL apprende i modelli di database e consente di adattare lo schema del database al carico di lavoro. Il database SQL offre [raccomandazioni per ottimizzare le prestazioni](sql-database-advisor.md), nelle quali è possibile verificare le azioni di ottimizzazione e applicarle.

Tuttavia, il monitoraggio costante di un database è un'attività difficile e noiosa, soprattutto quando si tratta di molti database. [Intelligent Insights](sql-database-intelligent-insights.md) esegue questo processo automaticamente monitorando automaticamente le prestazioni del database SQL su larga scala. Informa l'utente dei problemi di riduzione delle prestazioni, identifica la causa principale di ogni problema e fornisce consigli per il miglioramento delle prestazioni quando possibile.

La gestione di un numero enorme di database potrebbe essere impossibile da eseguire in modo efficiente anche con tutti gli strumenti e i report disponibili forniti dal database SQL e da Azure.Managing a huge number of databases might be impossible to do efficiently even with all available tools and reports that SQL Database and Azure provide. Anziché monitorare e ottimizzare manualmente il database, è possibile delegare alcune delle azioni di monitoraggio e ottimizzazione al database SQL utilizzando [l'ottimizzazione automatica.](sql-database-automatic-tuning.md) Il database SQL applica automaticamente raccomandazioni, test e verifica ognuna delle azioni di ottimizzazione per garantire che le prestazioni continuino a migliorare. In questo modo, il database SQL si adatta automaticamente al carico di lavoro in modo controllato e sicuro. L'ottimizzazione automatica significa che le prestazioni del database vengono attentamente monitorate e confrontate prima e dopo ogni azione di ottimizzazione. Se le prestazioni non migliorano, l'azione di ottimizzazione viene annullata.

Molti dei nostri partner che eseguono [app multi-tenant SaaS](sql-database-design-patterns-multi-tenancy-saas-applications.md) in cima al database SQL si affidano all'ottimizzazione automatica delle prestazioni per assicurarsi che le applicazioni abbiano sempre prestazioni stabili e prevedibili. Questa funzionalità consente loro di ridurre notevolmente il rischio di problemi di prestazioni durante la notte. Inoltre, poiché parte della base di clienti utilizza anche SQL Server, utilizzano gli stessi consigli di indicizzazione forniti dal database SQL per aiutare i clienti di SQL Server.

In [Database SQL](sql-database-automatic-tuning.md)sono disponibili due aspetti di ottimizzazione automatica:

- **Gestione automatica degli indici**: consente di identificare gli indici da aggiungere al database e quelli che è consigliabile rimuovere.
- **Correzione automatica del piano**: Identifica i piani problematici e risolve i problemi di prestazioni del piano SQL.

### <a name="adaptive-query-processing"></a>Elaborazione di query adattive

È possibile utilizzare [l'elaborazione adattiva](/sql/relational-databases/performance/intelligent-query-processing)delle query , inclusa l'esecuzione con interfoliazione per funzioni con valori di tabella con più istruzioni, feedback sulle concessioni di memoria in modalità batch e join adattivi in modalità batch. Ognuna di queste funzionalità di elaborazione delle query adattiva applica tecniche di "apprendimento e adattamento" simili, aiutando a risolvere ulteriormente i problemi di prestazioni correlati a problemi di ottimizzazione delle query storicamente intrattabili.

## <a name="advanced-security-and-compliance"></a>Sicurezza e conformità avanzate

Il database SQL offre una gamma di funzionalità di [sicurezza e conformità incorporate](sql-database-security-overview.md) che consentono all'applicazione di soddisfare vari requisiti di sicurezza e conformità.

> [!IMPORTANT]
> Microsoft ha certificato il database SQL di Azure (tutte le opzioni di distribuzione) in base a una serie di standard di conformità. Per altre informazioni, vedere il [Centro protezione di Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), in cui è possibile trovare l'elenco più aggiornato delle certificazioni di conformità del database SQL.

### <a name="advance-threat-protection"></a>Protezione avanzata dalle minacce

Sicurezza dei dati avanzata è un pacchetto unificato che include le funzionalità di sicurezza avanzate SQL. Include funzionalità per l'individuazione e la classificazione di dati sensibili, la gestione delle vulnerabilità del database e il rilevamento di attività anomale che potrebbero indicare una minaccia per il database. Consente di abilitare e gestire queste funzionalità da un'unica posizione.

- [Individuazione e classificazione dei dati](sql-database-data-discovery-and-classification.md):

  Questa funzionalità offre funzionalità integrate nel database SQL di Azure per l'individuazione, la classificazione, l'etichettatura e la protezione dei dati sensibili nei database. Fornisce visibilità sullo stato di classificazione del database e tiene traccia dell'accesso ai dati sensibili all'interno del database e oltre i suoi confini.
- [Valutazione della vulnerabilità](sql-vulnerability-assessment.md):

  Questo servizio consente di individuare, monitorare e risolvere potenziali vulnerabilità del database. Consente di visualizzare lo stato di sicurezza e prevede passaggi utili per risolvere i problemi di sicurezza e migliorare la protezione del database.
- [Rilevamento delle minacce](sql-database-threat-detection.md):

  Questa funzionalità rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare il database. Monitora in modo continuo il database in caso di attività sospette e fornisce avvisi di sicurezza immediati su potenziali vulnerabilità, attacchi SQL injection e in caso di modelli di accesso ai database anomali. Gli avvisi di rilevamento delle minacce forniscono dettagli sull'attività sospetta e consigliano azioni su come analizzare e mitigare la minaccia.

### <a name="auditing-for-compliance-and-security"></a>Controllo per conformità e sicurezza

Il servizio di [controllo](sql-database-auditing.md) tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo nell'account di archiviazione di Azure. Il controllo consente di agevolare la conformità alle normative, comprendere l'attività del database e ottenere informazioni dettagliate su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza.

### <a name="data-encryption"></a>Crittografia dei dati

Il database SQL consente di proteggere i dati fornendo la crittografia. Per i dati in movimento, viene utilizzata la protezione del livello di [trasporto.](https://support.microsoft.com/kb/3135244) Per i dati inattivi, utilizza la [crittografia dei dati trasparente.](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) Per i dati in uso, utilizza [sempre criptato](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrazione in Azure Active Directory e autenticazione a più fattori

Il database SQL consente di gestire a livello centralizzato le identità degli utenti di database e altri servizi Microsoft grazie all'[integrazione in Azure Active Directory](sql-database-aad-authentication.md). Questa funzionalità semplifica la gestione delle autorizzazioni e ottimizza la sicurezza. Azure Active Directory supporta [l'autenticazione](sql-database-ssms-mfa-authentication.md) a più fattori per aumentare la sicurezza dei dati e delle applicazioni, supportando al contempo un singolo processo di accesso.

## <a name="easy-to-use-tools"></a>Strumenti facili da usare

Il database SQL consente di creare e gestire le applicazioni in modo più facile e produttivo. Con il database SQL è possibile concentrarsi sull'attività principale, ovvero creare app straordinarie. È possibile gestire e sviluppare nel database SQL utilizzando gli strumenti e le competenze già disponibili.

- [Portale di Azure:](https://portal.azure.com/)

  Un'applicazione basata sul Web per la gestione di tutti i servizi di Azure.A web-based application for managing all Azure services.
- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms):

  Un'applicazione client scaricabile gratuita per la gestione di qualsiasi infrastruttura SQL, da SQL Server al database SQL.
- [SQL Server Data Tools in Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt):

  Un'applicazione client scaricabile gratuita per lo sviluppo di database relazionali di SQL Server, database SQL, pacchetti di Integration ServicesIntegration Services , modelli di dati di Analysis Services e report di Reporting ServicesReporting Services .
- [Codice](https://code.visualstudio.com/docs)di Visual Studio :

  Un editor di codice open source gratuito, scaricabile, per Windows, macOS e Linux. Supporta le estensioni, tra cui [l'estensione mssql per l'esecuzione](https://aka.ms/mssql-marketplace) di query su Microsoft SQL Server, il database SQL di Azure e Azure SQL Data Warehouse.

Sql Database supporta la creazione di applicazioni con Python, Java, Node.js, PHP, Ruby e .NET su macOS, Linux e Windows. Il database SQL supporta le stesse [librerie di connessione](sql-database-libraries.md) di SQL Server.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>Domande frequenti sul database SQL

### <a name="what-is-the-current-version-of-sql-database"></a>Qual è la versione corrente del database SQL?

La versione corrente del database SQL è V12. La versione V11 è stata ritirata.

### <a name="can-i-control-when-patching-downtime-occurs"></a>È possibile controllare quando si verifica il tempo di inattività delle patch?

No. L'impatto dell'applicazione di patch non è in genere rilevante se si [impiega la logica di ripetizione dei tentativi](sql-database-develop-overview.md#resiliency) nell'app. Per altre informazioni, vedere Pianificazione degli eventi di manutenzione di Azure nel database SQL di Azure.For more information, see [Planning for Azure maintenance events in Azure SQL Database.](sql-database-planned-maintenance.md)

### <a name="azure-hybrid-benefit-questions"></a>Domande relative a Vantaggio Azure Hybrid

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Il Vantaggio Azure Hybrid per SQL Server include diritti di doppio uso della licenza?

Si può disporre dei diritti di doppio uso della licenza per 180 giorni, per garantire che le migrazioni vengano eseguite senza problemi. Dopo tale periodo di 180 giorni, è possibile usare solo la licenza di SQL Server nel cloud nel database SQL. Non si dispone più di diritti di doppio utilizzo in locale e nel cloud.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Qual è la differenza tra il Vantaggio Azure Hybrid per SQL Server e la mobilità delle licenze?

Con Software Assurance offriamo vantaggi per la mobilità delle licenze ai clienti di SQL Server. Ciò consente di riassegnazione delle licenze ai server condivisi di un partner. È possibile utilizzare questo vantaggio in Azure IaaS e AWS EC2.

Il Vantaggio Azure Hybrid per SQL Server differisce dalla mobilità delle licenze per due aspetti principali:

- Offre vantaggi economici per lo spostamento di carichi di lavoro altamente virtualizzati in Azure. I clienti di SQL Server Enterprise Edition possono ottenere quattro core in Azure nello SKU General Purpose per ogni core di cui sono proprietari in locale per applicazioni altamente virtualizzate. La mobilità delle licenze non offre particolari vantaggi in termini di costi per lo spostamento di carichi di lavoro virtualizzati nel cloud.
- Fornisce una destinazione PaaS in Azure (istanza gestita dal database SQL) altamente compatibile con SQL Server locale.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Quali sono i diritti specifici inclusi nel Vantaggio Azure Hybrid per SQL Server?

I clienti del database SQL dispongono dei diritti seguenti associati al vantaggio Azure Hybrid per SQL Server:

|Impronta della licenza|Che cosa consente di ottenere il vantaggio Azure Hybrid per SQL Server?|
|---|---|
|Clienti di SQL Server Enterprise Edition con Software Assurance|<li>Può pagare la tariffa di base su uno Scopo Generale o SKU Business Critical</li><br><li>1 core in locale = 4 core nello SKU del livello Utilizzo generico</li><br><li>1 core in locale = 1 core nello SKU del livello Business Critical</li>|
|Clienti di SQL Server Standard Edition con Software Assurance|<li>Può pagare la tariffa di base solo su SKU generico</li><br><li>1 core in locale = 1 core nello SKU del livello Utilizzo generico</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Comunicare con il team di progettazione di SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Porre domande sull'amministrazione del database.
- [Overflow dello stack](https://stackoverflow.com/questions/tagged/sql-server): Porre domande di sviluppo.
- [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Porre domande tecniche.
- [Feedback](https://aka.ms/sqlfeedback): Segnala bug e richiedi funzionalità.
- [Reddit](https://www.reddit.com/r/SQLServer/): Discutere di SQL Server.

## <a name="next-steps"></a>Passaggi successivi

- Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/sql-database/) per i confronti dei costi e i calcolatori relativi a singoli database e pool elastici.
- Per iniziare, vedi queste guide introduttive:

  - [Creare un database SQL nel portale di Azure](sql-database-single-database-get-started.md)  
  - [Creare un database SQL con l'interfaccia della riga di comando di Azure](sql-database-get-started-cli.md)
  - [Creare un database SQL usando PowerShell](sql-database-get-started-powershell.md)

- Per un set di esempi dell'interfaccia della riga di comando di Azure e di PowerShell, vedere:
  - [Esempi dell'interfaccia della riga di comando di Azure per database SQL](sql-database-cli-samples.md)
  - [Esempi di Azure PowerShell per database SQL](sql-database-powershell-samples.md)

- Per informazioni sulle nuove funzionalità così come sono state annunciate, vedere [Guida di orientamento di Azure per](https://azure.microsoft.com/roadmap/?category=databases)il database SQL.
- Vedere il [blog sul database SQL](https://azure.microsoft.com/blog/topics/database)di Azure , in cui i membri del team del prodotto SQL Server sql su News e funzionalità del database SQL.

