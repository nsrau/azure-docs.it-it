---
title: Gestione dopo la migrazione - Database SQL di Azure | Microsoft Docs
description: Informazioni su come gestire il database dopo la migrazione al database SQL di Azure.
services: sql-database
documentationcenter: 
author: joesackmsft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 10/14/2016
ms.author: Joe.Sack
ms.suite: SQL
ms.prod_service: sql-database
ms.component: management
ms.openlocfilehash: 95e364c289aac394e1b3824533288b45725022a8
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2017
---
# <a name="how-should-i-manage-my-azure-sql-database-after-migration"></a>Come gestire il database SQL di Azure dopo la migrazione?

*Domande frequenti sulla gestione degli investimenti nel database SQL di Azure*

In una situazione in cui i database di SQL Server siano stati spostati nel database SQL di Azure o verranno spostati a breve, qual è la fase successiva? Poiché il database SQL è una *piattaforma distribuita come servizio* (PaaS, Platform as a Service), Microsoft gestisce varie aree per conto dell'utente. In che modo questa condizione influisce sulle procedure aziendali in aree essenziali quali la sicurezza, la continuità aziendale, la manutenzione dei database, l'ottimizzazione delle prestazioni e il monitoraggio? 

Lo scopo di questo articolo è quello di organizzare sinteticamente le risorse e il materiale sussidiario necessari durante il passaggio alla gestione degli investimenti sul database SQL. Le aree principali di questo articolo riguardano continuità aziendale, sicurezza, manutenzione e monitoraggio dei database, prestazioni e spostamento dei dati. Verranno affrontate le aree principali diverse tra SQL Server e il database SQL e illustrate le procedure operative consigliate che consentono di massimizzare i benefici e ridurre al minimo i rischi. 

## <a name="manage-business-continuity-after-migration"></a>Gestire la continuità aziendale dopo la migrazione

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Come creare e gestire i backup nel database SQL? 
Il database SQL esegue automaticamente il backup dei database e consente di eseguire un ripristino in un punto qualsiasi nel tempo durante il periodo di memorizzazione. Il periodo di memorizzazione è 35 giorni per i database Standard e Premium e 7 giorni per i database di base. La funzionalità di conservazione a lungo termine consente di mantenere i file di backup per un periodo più lungo (fino a 10 anni) ed eseguire il ripristino da questi backup in qualsiasi punto. I backup dei database sono anche sottoposti a replica geografica per consentire il ripristino in qualsiasi area in caso di emergenza o catastrofe. Vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-a-regional-catastrophe"></a>Come garantire la continuità aziendale in caso di emergenza a livello di data center o di catastrofe? 

I backup dei database sono sottoposti a replica geografica per consentire il ripristino in qualsiasi area in caso di emergenza o catastrofe. Vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md). Il database SQL consente anche di gestire attivamente i database secondari con replica geografica in un'altra area. Configurandoli in un gruppo di failover automatico, i database effettuano automaticamente il failover al database secondario in uno scenario di emergenza. Se non è configurato un gruppo di failover automatico, l'applicazione deve monitorare attivamente la presenza di una situazione di emergenza e avviare un failover al database secondario. 
### <a name="sql-server-provided-me-readable-secondary-replicas-can-i-access-the-secondaries-on-sql-database"></a>SQL Server fornisce repliche secondarie leggibili: è possibile accedere ai database secondari nel database SQL? 

Sì, la funzionalità "Replica geografica attiva" viene utilizzata per creare repliche secondarie leggibili. 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premise-to-sql-database"></a>In che modo il piano di ripristino di emergenza cambia in locale e nel database SQL? 
Le implementazioni di SQL Server richiedono la gestione attiva dei backup con funzioni come ad esempio Clustering di failover, Mirroring del database, Replica, Log shipping o semplici backup BACPAC. Tuttavia, nel database SQL, i backup sono completamente gestiti da Microsoft ed è possibile configurare piani di backup e ripristino di emergenza con pochi clic nel portale di Azure (o alcuni comandi in PowerShell). ‌
### <a name="in-the-event-of-disaster-how-do-i-recover-my-databases"></a>In caso di emergenza, come ripristinare i database? 
Il database SQL consente di ripristinare automaticamente i database in qualsiasi punto nel tempo negli ultimi 35 giorni. Questa opzione è utile in caso di perdita di dati o in una situazione di emergenza correlata a un'applicazione. 

In caso di catastrofe, se sono configurati i database secondari con replica geografica, è possibile eseguire il ripristino dai database di replica geografica secondaria in un'altra area. Per l'accesso in tempo reale alle applicazioni, è possibile eseguire manualmente il failover al database di replica geografica secondaria in un'altra area. In alternativa, se è configurato il gruppo di failover automatico, il failover al database di replica geografica secondaria avviene automaticamente in caso di emergenza. Se non è configurato un database secondario con replica geografica, è comunque possibile recuperare i database da file di backup replicati automaticamente (funzionalità incorporata, configurazione non necessaria), con tempi di recupero relativamente più lunghi (RTO di 12 ore) e fino a un'ora per la perdita di dati. 

### <a name="are-the-failovers-to-secondary-transparent-how-does-my-application-handle-database-failovers"></a>I failover ai database secondari sono trasparenti? In che modo un'applicazione gestisce i failover di database? 
Se sono configurati gruppi di failover automatico, il failover al database secondario è trasparente. In caso contrario, invece, l'applicazione deve incorporare la logica per monitorare la disponibilità del database primario e quindi eseguire manualmente il failover al database secondario. 
 
## <a name="manage-security-after-migration"></a>Gestire la sicurezza dopo la migrazione

### <a name="how-can-i-restrict-access-to-my-sql-database"></a>Come limitare l'accesso al database SQL? 
 
Esistono alcuni modi per bloccare l'accesso di connettività ai database SQL. 
1. Limitare il traffico su Internet: ExpressRoute offre fibra dedicata alla rete di Azure, in modo che i dati non vengano trasmessi tramite Internet. È anche possibile configurare la connettività tra più aree con ExpressRoute. I collegamenti seguenti descrivono ExpressRoute in modo più dettagliato: 
 - [Introduzione a ExpressRoute](../expressroute/expressroute-introduction.md)
 - [Prerequisiti](../expressroute/expressroute-prerequisites.md) 
 - [Flussi di lavoro](../expressroute/expressroute-workflows.md) 
 
2. Selezionare le risorse che si connettono al database SQL: 

   Per impostazione predefinita, il database SQL è configurato per consentire tutti i servizi di Azure, ovvero tutte le VM in Azure possono tentare di connettersi al database.  È comunque necessario eseguire l'autenticazione di tutti gli account di accesso. Se non si desidera che il database sia accessibile a tutti gli indirizzi IP di Azure, è possibile disabilitare "Allow all Azure services" (Consenti tutti i servizi di Azure) e utilizzare gli [endpoint del servizio di rete virtuale](sql-database-vnet-service-endpoint-rule-overview.md) per limitare l'accesso in ingresso al database solo dalle risorse di Azure all'interno di una determinata subnet di rete virtuale di Azure. 

   ![Endpoint del servizio di rete virtuale](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

   Un'opzione alternativa consiste nell'effettuare il provisioning degli [indirizzi IP riservati](../virtual-network/virtual-networks-reserved-public-ip.md) per le VM e includere nell'elenco degli elementi consentiti gli indirizzi IP di queste VM nelle impostazioni firewall del server (vedere la schermata di esempio di seguito nel portale di Azure). L'assegnazione degli indirizzi IP riservati evita di dover aggiornare le regole firewall quando si modificano gli indirizzi IP. 

3. Evitare di esporre la porta 1433 all'esterno di Azure:

   Eseguire SSMS in Azure con [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps). Non è necessario aprire le connessioni in uscita alla porta 1433, l'indirizzo IP è statico e pertanto il database può essere aperto solo per RemoteApp, supporta l'autenticazione a più fattori ed è multiutente. 

### <a name="what-authentication-methods-are-offered-in-sql-database"></a>Quali metodi di autenticazione vengono offerti nel database SQL?

I principali metodi di autenticazione offerti nel database SQL e in SQL Data Warehouse sono l'autenticazione di Azure Active Directory e l'autenticazione di SQL. Azure Active Directory (AD) è un servizio di gestione centralizzata delle identità e degli accessi e SQL è solo uno dei numerosi servizi di Azure integrati in Azure AD. Il vantaggio di un servizio gestito centralizzato è che le credenziali dell'utente vengono condivise tra tutti i servizi di Azure usati per semplificare l'autenticazione. In questo modo, il database SQL e SQL Data Warehouse possono anche offrire l'autenticazione a più fattori e gli account utente guest all'interno di un dominio di Azure AD. 

Se Active Directory è già disponibile in locale, è possibile attuare la federazione della directory con Azure Active Directory per estendere la directory ad Azure. 


|||
|---|---|
| Se…|Database SQL di Azure/Azure SQL Data Warehouse|
| Si preferisce non usare Azure Active Directory (AD) in Azure|Usare l'[autenticazione in SQL](sql-database-security-overview.md)|
| È stato usato Active Directory in SQL Server in locale|[Attuare la federazione di Active Directory con Azure AD](../active-directory/connect/active-directory-aadconnect.md) e usare l'autenticazione di Azure AD. In questo caso è possibile usare Single Sign-On.|
| È necessario applicare l'autenticazione a più fattori (MFA)|Richiedere MFA come criterio mediante l'[accesso condizionale di Microsoft](sql-database-conditional-access.md) e usare l'[autenticazione universale di Azure AD con il supporto di MFA](sql-database-ssms-mfa-authentication.md).|
| Si dispone di account guest Microsoft (live.com, outlook.com) o di altri domini (gmail.com)|Usare l'[autenticazione universale di Azure AD](sql-database-ssms-mfa-authentication.md) nel database SQL o in Data Warehouse, che sfrutta la [collaborazione B2B di Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
| Si è connessi a Windows con le credenziali di Azure AD da un dominio federato|Usare l'[autenticazione integrata di Azure AD](sql-database-aad-authentication-configure.md).|
| Si è connessi a Windows con le credenziali di un dominio non federato con Azure|Usare l'[autenticazione della password di Azure AD](sql-database-aad-authentication-configure.md).|
| Si dispone di servizi di livello intermedio che devono connettersi al database SQL di Azure o a Data Warehouse|Usare l'[autenticazione del token di Azure AD](sql-database-aad-authentication-configure.md).
|||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-databases-from-the-application-side"></a>Come limitare l'accesso ai dati sensibili nei database dal lato applicazione? 

Per impedire a utenti non autorizzati di visualizzare dati sensibili, sono disponibili alcune opzioni nel database SQL: 

- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) è un tipo di crittografia lato client che crittografa le colonne sensibili nel database (in modo che siano in testo crittografato per gli amministratori di database e gli utenti non autorizzati). La chiave per Always Encrypted è archiviata sul lato client, pertanto solo i client autorizzati sono in grado di decrittografare le colonne sensibili. Always Encrypted supporta oggi i confronti di uguaglianza, pertanto gli amministratori di database possono continuare a eseguire query sulle colonne crittografate nell'ambito dei comandi SQL. Always Encrypted può essere usato con un'ampia gamma di opzioni di archiviazione di chiavi, ad esempio [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), l'archivio certificati di Windows e i moduli di protezione hardware locali.
- [Dynamic Data Masking](sql-database-dynamic-data-masking-get-started.md) è una funzionalità di mascheramento dei dati che limita l'esposizione a dati sensibili mascherandoli agli utenti sprovvisti di privilegi sul lato applicazione. Definire una regola di mascheramento che possa creare un criterio (ad esempio, per visualizzare solo le ultime 4 cifre di un documento d'identità nazionale e mascherare le altre cifre con X) e identificare gli utenti che possano essere esclusi dalla regola di mascheramento.
- La [sicurezza a livello di riga](/sql/relational-databases/security/row-level-security) consente di controllare l'accesso alle righe in una tabella di database in base all'utente che esegue la query (appartenenza a un gruppo o contesto di esecuzione). La limitazione dell'accesso viene eseguita a livello di database anziché a livello di applicazione, per semplificare la logica dell'app. 

### <a name="what-encryption-options-do-i-have-in-sql-database-and-what-actors-does-the-encryption-protect-from"></a>Quali opzioni di crittografia sono disponibili nel database SQL e da quali attori protegge la crittografia?
Nel database SQL sono disponibili tre tecnologie di crittografia principali: 
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) (menzionata nella domanda precedente): crittografa le colonne riservate nella tabella end-to-end, dai client non autorizzati al disco fisico. Gli amministratori dei server e dei dati non possono visualizzare i dati sensibili, poiché le chiavi di crittografia vengono archiviate sul client. 
- [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) (TDE): crittografia dei dati inattivi a livello di database che protegge i file di dati, i file di log e i backup associati dal furto di supporti fisici. La tecnologia TDE è abilitata per impostazione predefinita per tutti i nuovi database creati.
 
  Il diagramma seguente mostra una panoramica delle tecnologie di crittografia disponibili.

   ![Panoramica della crittografia](./media/sql-database-manage-after-migration/overview-encryption.png)

### <a name="how-should-i-manage-encryption-keys-in-the-cloud"></a>Come gestire le chiavi di crittografia nel cloud? 
Sono disponibili opzioni di gestione delle chiavi per Always Encrypted (crittografia lato client) e Transparent Data Encryption (crittografia dei dati inattivi). Si consiglia di alternare periodicamente le chiavi di crittografia a una frequenza in linea con le normative interne e i requisiti di conformità.

- **Always Encrypted**: in Always Encrypted è presente una [gerarchia a due chiavi](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted). Una colonna di dati sensibili viene crittografata con una chiave di crittografia di colonna AES 256, che a sua volta viene crittografata da una chiave master di colonna. I driver del client forniti per Always Encrypted non presentano limitazioni di lunghezza per le chiavi master di colonna.

  Il valore crittografato della chiave di crittografia di colonna viene archiviato nel database e quello della chiave master di colonna in un archivio di chiavi attendibile, ad esempio l'archivio certificati di Windows, Azure Key Vault e i moduli di protezione hardware locali. 
  
  È possibile alternare [sia la chiave di crittografia di colonna sia la chiave master di colonna](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell). Per la rotazione delle chiavi di crittografia di colonna il tempo può essere un fattore importante, in base alle dimensioni delle tabelle contenenti le colonne crittografate. Pianificare quindi le rotazioni delle chiavi di crittografia di colonna con molta attenzione. La rotazione delle chiavi master di colonna, d'altra parte, non interferisce con le prestazioni del database e può essere eseguita con ruoli separati.

  Il diagramma seguente illustra le opzioni di archivio per le chiavi master di colonna in Always Encrypted 

   ![Provider di archivi per chiavi master di colonna Always Encrypted](./media/sql-database-manage-after-migration/always-encrypted.png)

- **Transparent Data Encryption (TDE)**: in TDE è presente una gerarchia a due chiavi. I dati in ogni database utente vengono crittografati con una chiave di crittografia simmetrica AES 256 univoca per il database, che a sua volta viene crittografata da una chiave master asimmetrica RSA 2048 univoca per il server. 

  Per impostazione predefinita, la chiave master per Transparent Data Encryption viene gestita dal servizio di database SQL per praticità. Se l'organizzazione intende gestire la chiave master, è disponibile un'opzione per utilizzare [Azure Key Vault](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql) come archivio delle chiavi. 

  Con Azure Key Vault l'organizzazione assume il controllo sul provisioning delle chiavi, sulla rotazione e sulle autorizzazioni. [La rotazione o il cambio del tipo di una chiave master TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) è un processo rapido, poiché ripete solo la crittografia della chiave di crittografia del database. 

  Per le organizzazioni con distinzione dei ruoli tra sicurezza e gestione dei dati, un amministratore della sicurezza può effettuare il provisioning del materiale per la chiave master di TDE in Azure Key Vault e fornire un identificatore di chiave di Azure Key Vault all'amministratore del database da utilizzare per la crittografia dei dati inattivi in un server. 

## <a name="monitoring-and-compliance-after-migration"></a>Monitoraggio e conformità dopo la migrazione

### <a name="how-do-i-monitor-database-activities-in-sql-database"></a>Come monitorare le attività di database nel database SQL?
Alcune funzionalità di monitoraggio incorporate nel database SQL consentono di tenere traccia della sicurezza e di altri eventi nel database:
- Il [servizio di controllo SQL](sql-database-auditing.md) consente di raccogliere i log di controllo degli eventi di database nell'account di archiviazione di Azure.
- Il [rilevamento delle minacce SQL](sql-database-threat-detection.md) consente di rilevare attività sospette che indicano un possibile tentativo dannoso di accesso, violazione o exploit dei dati nel database. Il rilevamento delle minacce del database SQL esegue vari set di algoritmi che rilevano potenziali vulnerabilità e attacchi SQL injection, nonché modelli anomali di accesso al database (ad esempio, accesso da una posizione insolita o da un'entità di sicurezza sconosciuta). I responsabili della sicurezza o altri amministratori designati ricevono una notifica e-mail se viene rilevata una minaccia nel database. Ogni notifica contiene dettagli sull'attività sospetta e consigli su come eseguire altre indagini e mitigare la minaccia. 
- La [valutazione della vulnerabilità SQL](sql-vulnerability-assessment.md) è un servizio di analisi e reporting di database che consente di monitorare lo stato di sicurezza dei database su larga scala e identificare i rischi di sicurezza e la deviazione da una baseline della sicurezza definita dall'utente. Al termine di ogni analisi vengono forniti un elenco personalizzato di passaggi eseguibili e gli script di correzione, nonché un report di valutazione che può essere usato per consentire di rispettare la conformità. 
- L'[applicazione di sincronizzazione della sicurezza SQL-OMS](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) usa le API pubbliche di Operations Management Suite (OMS) per eseguire il push dei log di controllo SQL in OMS per l'analisi dei log e la possibilità di definire avvisi di rilevamento personalizzati, tra cui: 
 - Riquadro e dashboard di controllo del database SQL che forniscono report chiari e coerenti delle attività del database. 
 - SQL Log Analytics per analizzare l'attività del database ed esaminare discrepanze e anomalie che potrebbero indicare violazioni sospette alla sicurezza.
 - Regole specifiche di avvisi avanzati sugli eventi osservati che attivano avvisi e-mail, webhook e runbook di Automazione di Azure (ad esempio in caso di modifiche di password, eventi dopo l'orario di lavoro o specifici comandi SQL).
- Il [Centro sicurezza di Azure](../security-center/security-center-intro.md) offre la gestione centralizzata della sicurezza per carichi di lavoro in esecuzione in Azure, in locale e in altri cloud. È possibile visualizzare se in tutte le risorse è configurata la protezione essenziale del database SQL, ad esempio controllo e Transparent Data Encryption, e creare criteri in base a requisiti personalizzati. 

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Il database SQL è conforme ai requisiti normativi e in che modo questo facilita la conformità dell'organizzazione? 
Il database SQL di Azure è rispetta una serie di conformità normative. Per visualizzare il set più recente di conformità soddisfatte, visitare il [Centro protezione di Microsoft](https://www.microsoft.com/trustcenter/compliance/complianceofferings) ed eseguire il drill-down nelle conformità importanti per l'organizzazione per capire se il database SQL di Azure sia incluso nei servizi conformi ad Azure. È importante notare che, sebbene il database SQL di Azure possa essere certificato come servizio conforme, agevola la conformità del servizio dell'organizzazione, ma non la garantisce automaticamente. 

## <a name="database-maintenance-and-monitoring-after-migration"></a>Manutenzione e monitoraggio del database dopo la migrazione

### <a name="how-do-i-monitor-growth-in-data-size-and-resource-utilization"></a>Come monitorare la crescita di dimensione dei dati e uso delle risorse?

- È possibile visualizzare metriche di monitoraggio sulle dimensioni del database e sull'uso delle risorse nel grafico di monitoraggio del portale di Azure. 

  ![Grafico di monitoraggio](./media/sql-database-manage-after-migration/monitoring-chart.png)

- Per ottenere informazioni approfondite ed eseguire il drill-down nei dettagli delle query, è possibile usare "Informazioni dettagliate prestazioni query" disponibile nel portale di Azure. "Query Store" deve essere attivo nel database.

  ![Informazioni dettagliate prestazioni query](./media/sql-database-manage-after-migration/query-performance-insight.png)

- In alternativa, è possibile visualizzare le metriche anche con le viste a gestione dinamica (DMV), usando [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database). 

### <a name="how-often-do-i-need-to-run-consistency-checks-like-dbcccheckdb"></a>Con quale frequenza è necessario eseguire verifiche di coerenza come DBCC_CHECKDB?
DBCC_CHECKDB verifica l'integrità logica e fisica di tutti gli oggetti nel database. Non è più necessario eseguire questi controlli, poiché questi vengono gestiti da Microsoft in Azure. Per altre informazioni, vedere [Data Integrity in Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) (Integrità dei dati nel database SQL di Azure)

## <a name="monitor-performance-and-resource-utilization-after-migration"></a>Monitorare le prestazioni e l'uso delle risorse dopo la migrazione

### <a name="how-do-i-monitor-performance-and-resource-utilization-in-azure-sql-database"></a>Come monitorare le prestazioni e l'uso delle risorse nel database SQL di Azure?
È possibile monitorare le prestazioni e l'uso delle risorse nel database SQL di Azure con i metodi seguenti:

- **Portale di Azure**: il portale di Azure mostra l'uso di un database singolo selezionandolo e facendo clic sul grafico nel riquadro Panoramica. È possibile modificare il grafico per visualizzare varie metriche, tra cui percentuale di CPU, percentuale di DTU, percentuale I/O dei dati, percentuale di sessioni e percentuale delle dimensioni del database. 
  ![uso delle risorse](./media/sql-database-manage-after-migration/resource-utilization.png)

  Da questo grafico è anche possibile configurare avvisi per risorsa. Questi avvisi consentono di rispondere alle condizioni delle risorse con un messaggio e-mail, scrivere in un endpoint HTTPS/HTTP o eseguire un'azione. Per istruzioni dettagliate, vedere [Monitoraggio delle prestazioni del database nel database SQL di Azure](sql-database-single-database-monitor.md).

- **Viste**: è possibile eseguire una query della vista a gestione dinamica [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) per restituire la cronologia delle statistiche sull'uso delle risorse dall'ultima ora e la vista del catalogo di sistema [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) per restituire la cronologia degli ultimi 14 giorni. 

- **Informazioni dettagliate prestazioni query**: [Informazioni dettagliate prestazioni query](sql-database-query-performance.md) consente di visualizzare una cronologia delle query principali a livello di uso delle risorse e le query con esecuzione prolungata per un database specifico. Questa funzionalità richiede che [Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) sia abilitato e attivo per il database.

- **Analisi SQL di Azure (anteprima) in Log Analytics**: [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md) consente di raccogliere e visualizzare le metriche di prestazioni chiave SQL di Azure, supportando fino a 150.000 database SQL di Azure e 5.000 pool elastici di SQL per area di lavoro. È possibile usare il servizio per monitorare e ricevere notifiche. È possibile monitorare le metriche del database SQL di Azure e dei pool elastici in più sottoscrizioni e pool elastici di Azure e usarle per identificare problemi a ogni livello dello stack di applicazioni. 

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>Come verificare di usare il livello di servizio e il livello di prestazioni appropriati?
Monitorare le viste a gestione dinamica [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) per individuare il consumo di memoria, CPU e I/O. È anche possibile usare [Informazioni dettagliate prestazioni query](sql-database-query-performance.md) del database SQL per consultare il consumo delle risorse. In caso di esecuzione costante a un'elevata percentuale di risorse disponibili, è opportuno provare a passare a un livello di prestazioni superiore all'interno del livello di servizio esistente oppure passare a un livello di servizio superiore. Viceversa, se si usa spesso una percentuale ridotta di risorse disponibili, è possibile provare a passare a un livello di prestazioni o di servizio inferiore.

### <a name="i-am-seeing-performance-issues-how-does-my-azure-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Si riscontrano problemi di prestazioni. In che modo la metodologia di risoluzione dei problemi del database SQL di Azure differisce da SQL Server?
Molti aspetti della metodologia di risoluzione dei problemi delle prestazioni restano invariati nel database SQL di Azure, ma sussistono alcune differenze. Se ad esempio si riscontra una riduzione generale delle prestazioni, monitorare le viste a gestione dinamica [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) per individuare il consumo di memoria, CPU e I/O. Potrebbe essere necessario modificare il livello di prestazioni e/o del servizio in base alle esigenze di carico di lavoro.
Per un set completo di indicazioni per la risoluzione dei problemi di prestazioni, vedere [Ottimizzazione delle prestazioni del database SQL di Azure](sql-database-performance-guidance.md). 

### <a name="do-i-need-to-maintain-indexes-and-statistics"></a>È necessario gestire indici e statistiche?
Il database SQL di Azure non gestisce automaticamente gli indici e le statistiche nell'ambito del servizio. L'utente è responsabile della pianificazione della manutenzione di indici e statistiche. L'articolo seguente sui metodi di Automazione di Azure illustra in dettaglio varie opzioni per pianificare i processi di manutenzione a fronte del database SQL di Azure.

## <a name="data-movement-after-migration"></a>Spostamento dei dati dopo la migrazione

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-azure-sql-database"></a>Come esportare e importare i dati come file BACPAC dal database SQL di Azure? 

- **Esportazione**: è possibile esportare il database SQL di Azure come file BACPAC dal portale di Azure.

  ![Esportazione come file BACPAC](./media/sql-database-export/database-export.png)

- **Importazione** è possibile importare un file BACPAC in un database usando il portale di Azure.

  ![Importazione di un file BACPAC](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-azure-sql-database-sql-server-2016--2012"></a>Come sincronizzare dati tra il database SQL di Azure e SQL Server 2016/2012?
La funzionalità di [sincronizzazione dati](sql-database-sync-data.md) consente di sincronizzare i dati in modo bidirezionale tra più database SQL Server locali e il database SQL di Azure. Tuttavia, poiché questa azione è basata su trigger, la coerenza finale è garantita (senza perdita di dati), ma non la coerenza delle transazioni. 

## <a name="next-steps"></a>Passaggi successivi
Informazioni sul [database SQL di Azure](sql-database-technical-overview.md).
