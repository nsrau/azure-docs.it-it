---
title: Gestione dopo la migrazione - Database SQL di Azure | Microsoft Docs
description: Informazioni su come gestire il database dopo la migrazione al database SQL di Azure.
services: sql-database
author: joesackmsft
manager: craigg
ms.service: sql-database
ms.custom: migrate
ms.topic: article
ms.date: 03/16/2018
ms.author: josack
ms.suite: sql
ms.prod_service: sql-database
ms.component: migration
ms.openlocfilehash: 4e50a1be3437ab1b027c1ca0f160402239e13e92
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="new-dba-in-the-cloud--managing-your-database-in-azure-sql-database"></a>Nuovo DBA nel cloud - Gestione del database nel database SQL di Azure

Il passaggio da un ambiente tradizionale, gestito e controllato in autonomia, a un ambiente PaaS può risultare inizialmente piuttosto complesso. Gli sviluppatori di app o i DBA hanno bisogno di conoscere le funzionalità principali della piattaforma che contribuiscono a mantenere l'applicazione sempre disponibile, efficiente e flessibile. Questo articolo mira esattamente a questo. Nell'articolo vengono organizzate in modo sintetico le risorse e vengono proposte alcune indicazioni su come usare al meglio le principali funzionalità del database SQL per gestire e mantenere efficiente l'applicazione, nonché ottenere risultati ottimali nel cloud. Destinatari tipici di questo articolo:
- Utenti che valutano la migrazione delle applicazioni al database SQL di Azure - Modernizzazione delle applicazioni.
- Utenti in fase di migrazione delle applicazioni - Scenari di migrazione in corso.
- Utenti che hanno recentemente completato la migrazione al database SQL di Azure - Nuovo DBA nel cloud.

In questo articolo vengono descritte alcune caratteristiche principali del database SQL di Azure come piattaforma immediatamente fruibile. Eccole: 
- Continuità aziendale e ripristino di emergenza (BCDR)
- Sicurezza e conformità
- Monitoraggio e manutenzione intelligenti del database
- Spostamento dati


## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Continuità aziendale e ripristino di emergenza (BCDR)
Le funzionalità della continuità aziendale e del ripristino di emergenza consentono di proseguire normalmente l'attività aziendale in caso di emergenza. Questa situazione di emergenza potrebbe essere un evento a livello di database (ad esempio, un utente che elimina erroneamente una tabella fondamentale) o un evento a livello di data center (calamità regionale, ad esempio uno tsunami). 

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Come creare e gestire i backup nel database SQL?
Non vengono creati backup nel database SQL di Azure, semplicemente perché non servono. Il database SQL esegue automaticamente il backup dei database, pertanto non è più necessario preoccuparsi di pianificare, eseguire e gestire i backup. La piattaforma esegue un backup completo ogni settimana, backup differenziali a intervalli di poche ore e un backup del log ogni 5 minuti per garantire un ripristino di emergenza efficiente e una perdita di dati minima. Il primo backup completo viene eseguito non appena si crea un database. Questi backup sono disponibili per un determinato periodo, chiamato "periodo di conservazione", e varia a seconda del livello di prestazioni scelto.  Il database SQL offre la possibilità di eseguire un ripristino in qualsiasi punto nel tempo all'interno di questo periodo di conservazione con il [recupero temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore).

|Livello di prestazioni|Periodo di conservazione in giorni|
|---|:---:|
|Basic|7|
|Standard|35|
|Premium|35|
|||

La funzionalità di [conservazione a lungo termine](sql-database-long-term-retention.md) consente anche di mantenere i file di backup per un periodo più lungo, nello specifico fino a 10 anni, ed eseguire il ripristino da questi backup in qualsiasi punto nel periodo. I backup dei database vengono quindi mantenuti in una risorsa di archiviazione con replica geografica per garantire resilienza da calamità regionali. È anche possibile ripristinare questi backup in un'area di Azure in qualsiasi punto nel tempo entro il periodo di conservazione. Vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Come garantire la continuità aziendale in caso di emergenza a livello di data center o di catastrofe?
I backup dei database sono archiviati in un sottosistema di archiviazione con replica geografica per garantire che, in caso di emergenza, sia possibile ripristinare il backup in un'altra area di Azure. Questo processo è noto come ripristino geografico. L'obiettivo del punto di ripristino (RPO) è in genere < 1 ora e il tempo di recupero stimato varia da pochi minuti ad alcune ore.

Per i database cruciali, il database SQL di Azure offre la replica geografica attiva. In sostanza, crea una copia secondaria con replica geografica del database originale in un'altra area. Se ad esempio il database è inizialmente ospitato nell'area di Azure degli Stati Uniti occidentali ed è necessaria la resilienza in caso di situazioni di emergenza, si creerà una replica geografica attiva del database negli Stati Uniti occidentali, ad esempio negli Stati Uniti orientali. Quando si verifica una calamità negli Stati Uniti occidentali, è possibile eseguire il failover nell'area degli Stati Uniti orientali. La configurazione in un gruppo di failover automatico è addirittura migliore, poiché in questo modo si garantisce il failover automatico del database nel database secondario negli Stati Uniti orientali in caso di emergenza. L'obiettivo del punto di ripristino (RPO) è < 5 secondi e il tempo di recupero stimato è < 30 secondi.

Se non è configurato un gruppo di failover automatico, l'applicazione deve monitorare attivamente la presenza di una situazione di emergenza e avviare un failover al database secondario. È possibile creare fino a 4 repliche geografiche attive di questo tipo in aree di Azure diverse. E non è finita qui. È anche possibile accedere in sola lettura a queste repliche geografiche attive secondarie. Questa situazione risulta molto utile per ridurre la latenza per uno scenario di applicazione con distribuzione geografica. 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>In che modo il piano di ripristino di emergenza passa da locale nel database SQL?
In breve, la configurazione locale di SQL Server prevede la gestione attiva della disponibilità usando funzionalità come il clustering di failover, il mirroring del database, la replica di tipo transazionale, il log shipping e così via, nonché la manutenzione e la gestione dei backup per garantire la continuità aziendale. Con il database SQL, la gestione avviene in automatico tramite la piattaforma, consentendo di concentrarsi sullo sviluppo e sull'ottimizzazione dell'applicazione di database, senza preoccuparsi più di tanto della gestione delle emergenze. È possibile configurare piani di backup e ripristino di emergenza con pochi clic nel portale di Azure (o con alcuni comandi nelle API PowerShell). 

Per altre informazioni sul ripristino di emergenza, vedere: [Azure SQL Db Disaster Recovery 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/) (Ripristino di emergenza 101 del database SQL di Azure)

## <a name="security-and-compliance"></a>Sicurezza e conformità
Il database SQL tiene in gran conto sicurezza e privacy. La sicurezza nel database SQL è disponibile a livello di database e di piattaforma e risulta più comprensibile se suddivisa in vari livelli. A ogni livello è possibile controllare e garantire la sicurezza ottimale dell'applicazione. I livelli sono:
- Identità e autenticazione ([autenticazione di Windows/SQL e autenticazione di Azure Active Directory [AAD]](sql-database-control-access.md)).
- Attività di monitoraggio ([controllo](sql-database-auditing.md) e [rilevamento delle minacce](sql-database-threat-detection.md)).
- Protezione dei dati effettivi ([Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) e [Always Encrypted [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)). 
- Controllo dell'accesso ai dati sensibili e con privilegi ([sicurezza a livello di riga](/sql/relational-databases/security/row-level-security) e [Dynamic Data Masking](/sql/relational-databases/security/dynamic-data-masking)).

Il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) offre la gestione centralizzata della sicurezza per carichi di lavoro in esecuzione in Azure, in locale e in altri cloud. È possibile visualizzare se in tutte le risorse è configurata la protezione essenziale del database SQL, ad esempio [controllo](sql-database-auditing.md) e [Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), e creare criteri in base a requisiti personalizzati.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Quali metodi di autenticazione degli utenti vengono offerti nel database SQL?
Nel database SQL sono disponibili [due metodi di autenticazione](sql-database-control-access.md#authentication): 
- [Autenticazione di Azure Active Directory](sql-database-aad-authentication.md)
- Autenticazione SQL. 

L'autenticazione tradizionale di Windows non è supportata. Azure Active Directory (AD) è un servizio di gestione centralizzato delle identità e degli accessi. In questo modo è possibile garantire l'accesso Single Sign-On (SSO) a tutto il personale dell'organizzazione. Ciò significa che le credenziali vengono condivise tra tutti i servizi di Azure per semplificare l'autenticazione. AAD supporta l'[autenticazione a più fattori (MFA, Multi Factor Authentication)](sql-database-ssms-mfa-authentication.md) e, con [un paio di clic](../active-directory/connect/active-directory-aadconnect-get-started-express.md), AAD può essere integrato in Windows Server Active Directory. L'autenticazione SQL Server funziona esattamente come in passato. Fornendo un nome utente/password è possibile autenticare gli utenti in qualsiasi database in un determinato server logico. In questo modo, il database SQL e SQL Data Warehouse possono anche offrire l'autenticazione a più fattori e gli account utente guest all'interno di un dominio di Azure AD. Se Active Directory è già disponibile in locale, è possibile attuare la federazione della directory con Azure Active Directory per estendere la directory ad Azure.

|**Se…**|**Database SQL/SQL Data Warehouse**|
|---|---|
|Si preferisce non usare Azure Active Directory (AD) in Azure|Usare l'[autenticazione in SQL](sql-database-security-overview.md)|
|È stato usato Active Directory in SQL Server in locale|[Attuare la federazione di Active Directory con Azure AD](../active-directory/connect/active-directory-aadconnect.md) e usare l'autenticazione di Azure AD. In questo caso è possibile usare Single Sign-On.|
|È necessario applicare l'autenticazione a più fattori (MFA)|Richiedere MFA come criterio mediante l'[accesso condizionale di Microsoft](sql-database-conditional-access.md) e usare l'[autenticazione universale di Azure AD con il supporto di MFA](sql-database-ssms-mfa-authentication.md).|
|Si dispone di account guest Microsoft (live.com, outlook.com) o di altri domini (gmail.com)|Usare l'[autenticazione universale di Azure AD](sql-database-ssms-mfa-authentication.md) nel database SQL o in Data Warehouse, che sfrutta la [collaborazione B2B di Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
|Si è connessi a Windows con le credenziali di Azure AD da un dominio federato|Usare l'[autenticazione integrata di Azure AD](sql-database-aad-authentication-configure.md).|
|Si è connessi a Windows con le credenziali di un dominio non federato con Azure|Usare l'[autenticazione integrata di Azure AD](sql-database-aad-authentication-configure.md).|
|Si dispone di servizi di livello intermedio che devono connettersi al database SQL o a SQL Data Warehouse|Usare l'[autenticazione integrata di Azure AD](sql-database-aad-authentication-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Come limitare o controllare l'accesso di connettività al database?
Esistono diverse tecniche a disposizione che è possibile usare per conseguire un'organizzazione di connettività ottimale per l'applicazione. 
- Regole del firewall
- Endpoint del servizio di rete virtuale
- IP riservati

#### <a name="firewall"></a>Firewall
Un firewall impedisce l'accesso al server da un'entità esterna, consentendo solo a entità specifiche l'accesso al server logico. Per impostazione predefinita, vengono rifiutate tutte le connessioni e i database all'interno del server logico, ad eccezione delle connessioni in entrata da altri servizi di Azure. Con una regola firewall è possibile aprire l'accesso al server solo alle entità (ad esempio, un computer per sviluppatori) approvate, consentendo all'indirizzo IP del computer di attraversare il firewall. Consente anche di specificare un intervallo di indirizzi IP a cui si intende consentire l'accesso al server logico. È ad esempio possibile aggiungere in una sola volta gli indirizzi IP del computer per sviluppatori specificando un intervallo nella pagina Impostazioni del firewall. 

È possibile creare regole firewall a livello di server o database. Le regole firewall a livello di server possono essere create dal portale o con SSMS. Per altre informazioni su come impostare una regola firewall a livello di server e database, vedere: [Create firewall rules in SQL Database](sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal) (Creare regole firewall nel database SQL).

#### <a name="service-endpoints"></a>Endpoint di servizio
Per impostazione predefinita, il database SQL è configurato per consentire tutti i servizi di Azure, ovvero qualsiasi VM in Azure può tentare di connettersi al database. Questi tentativi devono comunque essere autenticati. Se tuttavia non si intende rendere il database accessibile a eventuali indirizzi IP di Azure, è possibile disabilitare l'opzione che consente tutti i servizi di Azure. È anche possibile configurare gli [endpoint di servizio delle reti virtuali](sql-database-vnet-service-endpoint-rule-overview.md).

Gli endpoint di servizio consentono di esporre le risorse cruciali di Azure solo alla rete privata virtuale in Azure. In questo modo si elimina essenzialmente l'accesso pubblico alle risorse. Il traffico tra la rete virtuale e Azure rimane nella rete di backbone di Azure. Senza gli endpoint di servizio si ottiene il routing dei pacchetti con tunneling forzato. La rete virtuale forza il traffico Internet nell'organizzazione e il traffico del servizio Azure in modo che segua lo stesso route. Con gli endpoint di servizio è possibile ottimizzare questo processo, perché i pacchetti seguono il flusso direttamente dalla rete virtuale al servizio nella rete di backbone di Azure.

![Endpoint del servizio di rete virtuale](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

#### <a name="reserved-ips"></a>IP riservati
Un'altra opzione consiste nell'effettuare il provisioning degli [indirizzi IP riservati](../virtual-network/virtual-networks-reserved-public-ip.md) per le VM e includere nell'elenco degli elementi consentiti gli indirizzi IP di queste VM nelle impostazioni firewall del server. L'assegnazione degli indirizzi IP riservati evita di dover aggiornare le regole firewall quando si modificano gli indirizzi IP.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Con quale porta è possibile connettersi al database SQL?

La porta 1433. Il database SQL comunica attraverso questa porta. Per connettersi da una rete aziendale, è necessario aggiungere una regola in uscita nelle impostazioni del firewall dell'organizzazione. Come indicazione generale, evitare di esporre la porta 1433 all'esterno dei limiti di Azure. È possibile eseguire SSMS in Azure con [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps). Non è necessario aprire le connessioni in uscita alla porta 1433, l'indirizzo IP è statico e pertanto il database può essere aperto solo per RemoteApp e supporta l'autenticazione a più fattori.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Come è possibile monitorare e regolare l'attività nel server e nel database SQL?
#### <a name="sql-database-auditing"></a>Controllo del database SQL
Con il database SQL è possibile attivare il controllo per rilevare gli eventi di database. Il servizio di [controllo del database SQL](sql-database-auditing.md) registra gli eventi che si verificano nel database e li registra in un file di log di controllo nell'account di Archiviazione di Azure dell'utente. Il controllo è particolarmente utile se si intende ottenere informazioni su potenziali violazioni in termini di sicurezza e criteri, mantenere la conformità ai requisiti normativi e così via. Consente di definire e configurare determinate categorie di eventi che si ritiene necessitino di controllo e, sulla base di ciò, è possibile ottenere report preconfigurati e un dashboard per avere una panoramica degli eventi che si verificano nel database. È possibile applicare questi criteri di controllo a livello di database o server. Per una guida su come attivare il controllo per il server/database, vedere: [Abilitare il controllo del database SQL](sql-database-security-tutorial.md#enable-sql-database-auditing-if-necessary).

#### <a name="threat-detection"></a>Rilevamento delle minacce
Con il [rilevamento delle minacce](sql-database-threat-detection.md) è possibile intervenire in modo molto semplice sulle violazioni in termini di sicurezza o criteri individuate con il controllo. Non è necessario essere esperti di sicurezza per risolvere potenziali minacce o violazioni nel sistema. Il rilevamento delle minacce include anche alcune funzionalità incorporate come il rilevamento di attacchi SQL injection. Un attacco SQL injection è un tentativo di modificare o compromettere i dati e un modo molto comune per attaccare in genere un'applicazione di database. Il rilevamento delle minacce del database SQL esegue vari set di algoritmi che rilevano potenziali vulnerabilità e attacchi SQL injection, nonché modelli anomali di accesso al database (ad esempio, accesso da una posizione insolita o da un'entità di sicurezza sconosciuta). I responsabili della sicurezza o altri amministratori designati ricevono una notifica e-mail se viene rilevata una minaccia nel database. Ogni notifica contiene dettagli sull'attività sospetta e consigli su come eseguire altre indagini e mitigare la minaccia. Per scoprire come attivare il rilevamento delle minacce, vedere [Abilitare il rilevamento delle minacce per il database SQL](sql-database-security-tutorial.md#enable-sql-database-threat-detection). 
### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Come proteggere i dati in generale nel database SQL?
La crittografia offre un meccanismo sicuro per proteggere i dati riservati da intrusioni. I dati crittografati non sono di alcun uso per gli intrusi senza una chiave di decrittografia. In questo modo viene aggiunto un livello di protezione aggiuntivo ai livelli di sicurezza esistenti integrati nel database SQL. Due sono gli aspetti alla base della protezione dei dati nel database SQL: 
- I dati inattivi nei file di log e di dati 
- I dati in elaborazione. 

Nel database SQL, per impostazione predefinita, i dati inattivi nei file di log e di dati nel sottosistema di archiviazione sono sempre interamente crittografati con [Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Anche i backup sono crittografati. Con TDE non sono necessarie modifiche sul lato applicazione che accede a questi dati. Crittografia e decrittografia si verificano in modo trasparente (da qui il nome). Per proteggere i dati sensibili in elaborazione e inattivi, il database SQL include una funzionalità denominata [Always Encrypted (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted è un tipo di crittografia lato client che crittografa le colonne sensibili nel database (in modo che siano in testo crittografato per gli amministratori di database e gli utenti non autorizzati). Per iniziare, il server riceve i dati crittografati. Anche la chiave per Always Encrypted è archiviata sul lato client, pertanto solo i client autorizzati possono decrittografare le colonne sensibili. Gli amministratori dei server e dei dati non possono visualizzare i dati sensibili, poiché le chiavi di crittografia vengono archiviate sul client. Always Encrypted crittografa le colonne sensibili nella tabella end-to-end, dai client non autorizzati al disco fisico. Always Encrypted supporta oggi i confronti delle uguaglianze, pertanto i DBA possono continuare a eseguire query sulle colonne crittografate nell'ambito dei comandi SQL. Always Encrypted può essere usato con un'ampia gamma di opzioni di archiviazione di chiavi, ad esempio [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), l'archivio certificati di Windows e i moduli di protezione hardware locali.

|**Caratteristiche**|**Always Encrypted**|**Transparent Data Encryption**|
|---|---|---|
|**Intervallo di crittografia**|End-to-end|Dati inattivi|
|**Il server di database può accedere ai dati sensibili**|No |Sì, poiché la crittografia è per i dati inattivi|
|**Operazioni T-SQL consentite**|Confronto delle uguaglianze|L'intera superficie di attacco T-SQL è disponibile|
|**Modifiche all'app richieste per usare la funzionalità**|Minime|Estremamente minime|
|**Granularità di crittografia**|A livello di colonna|A livello di database|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Come limitare l'accesso ai dati sensibili nel database?
Ogni applicazione include quantità specifiche di dati sensibili nel database che non devono essere visibili a chiunque. Alcuni membri del personale all'interno dell'organizzazione devono essere in grado di visualizzare questi dati, a differenza di altri. Un esempio è rappresentato dai salari dei dipendenti. Un manager deve poter accedere alle informazioni salariali dei suoi riporti diretti, mentre i singoli membri del team non devono avere accesso alle informazioni salariali dei loro pari. Un altro scenario è rappresentato da sviluppatori di dati che potrebbero interagire con dati sensibili durante le fasi di sviluppo o test, ad esempio i codici fiscali dei clienti. Anche queste informazioni non devono essere esposte agli sviluppatori. In questi casi, i dati sensibili devono essere mascherati o addirittura non esposti. Per impedire a utenti non autorizzati di visualizzare dati sensibili, il database SQL offre due approcci di questo tipo:

[Dynamic Data Masking](sql-database-dynamic-data-masking-get-started.md) è una funzionalità di mascheramento dei dati che limita l'esposizione a dati sensibili mascherandoli agli utenti sprovvisti di privilegi sul lato applicazione. Definire una regola di mascheramento che possa creare criteri (ad esempio, per visualizzare solo le ultime quattro cifre di un codice fiscale: XXX-XX-0000 e mascherare le altre cifre con X) e identificare gli utenti che devono essere esclusi dalla regola di mascheramento. Il mascheramento avviene in tempo reale e sono disponibili varie funzioni di mascheramento per categorie di dati diverse. Dynamic Data Masking consente di rilevare automaticamente i dati sensibili nel database e applicarvi il mascheramento.

La [sicurezza a livello di riga](/sql/relational-databases/security/row-level-security) consente di controllare l'accesso a livello di riga. In altre parole, alcune righe in una tabella di database in base all'utente che esegue la query (appartenenza a un gruppo o contesto di esecuzione) sono nascoste. La limitazione dell'accesso viene eseguita a livello di database anziché a livello di applicazione, per semplificare la logica dell'app. Per iniziare, creare un predicato di filtro per escludere le righe che non devono essere esposte e i criteri di sicurezza che definiscono gli utenti con accesso a queste righe. Per concludere, l'utente finale esegue la query e, a seconda dei privilegi dell'utente, visualizza le righe con restrizioni o non ne visualizza alcuna.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Come gestire le chiavi di crittografia nel cloud?

Sono disponibili opzioni di gestione delle chiavi per Always Encrypted (crittografia lato client) e Transparent Data Encryption (crittografia dei dati inattivi). È consigliabile ruotare periodicamente le chiavi di crittografia. La frequenza di rotazione deve essere allineata alle normative interne e ai requisiti di conformità dell'organizzazione.

**Transparent Data Encryption (TDE)**: in TDE è presente una gerarchia a due chiavi. I dati in ogni database utente vengono crittografati con una chiave di crittografia simmetrica AES 256 univoca per il database, che a sua volta viene crittografata da una chiave master asimmetrica RSA 2048 univoca per il server. La chiave master può essere gestita:
- Automaticamente dalla piattaforma - Database SQL.
- Usando [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) come archivio delle chiavi.

Per impostazione predefinita, la chiave master per Transparent Data Encryption viene gestita dal servizio di database SQL per praticità. Se l'organizzazione intende gestire la chiave master, è disponibile un'opzione per usare Azure Key Vault (sql-database-always-encrypted-azure-key-vault.md) come archivio delle chiavi. Con Azure Key Vault l'organizzazione assume il controllo sul provisioning delle chiavi, sulla rotazione e sulle autorizzazioni. [La rotazione o il cambio del tipo di una chiave master TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) è un processo rapido, poiché ripete solo la crittografia della chiave di crittografia del database. Per le organizzazioni con distinzione dei ruoli tra sicurezza e gestione dei dati, un amministratore della sicurezza può effettuare il provisioning del materiale per la chiave master di TDE in Azure Key Vault e fornire un identificatore di chiave di Azure Key Vault all'amministratore del database da usare per la crittografia dei dati inattivi in un server. Key Vault è progettato in modo che Microsoft non possa vedere o estrarre le chiavi di crittografia. Si consegue anche una gestione centralizzata delle chiavi per l'organizzazione. 

**Always Encrypted**: in Always Encrypted è presente anche una [gerarchia a due chiavi](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted). Una colonna di dati sensibili viene crittografata con una chiave di crittografia di colonna AES 256, che a sua volta viene crittografata da una chiave master di colonna. I driver del client forniti per Always Encrypted non presentano limitazioni di lunghezza per le chiavi master di colonna. Il valore crittografato della chiave di crittografia di colonna viene archiviato nel database e quello della chiave master di colonna in un archivio di chiavi attendibile, ad esempio l'archivio certificati di Windows, Azure Key Vault e i moduli di protezione hardware locali. 
- È possibile alternare [sia la chiave di crittografia di colonna sia la chiave master di colonna](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell). 
- La rotazione delle chiavi di crittografia di colonna è un'operazione relativa alla dimensione dei dati e il tempo può essere un fattore importante, in base alle dimensioni delle tabelle contenenti le colonne crittografate. È dunque prudente pianificare di conseguenza la rotazione delle chiavi di crittografia di colonna. 
- La rotazione delle chiavi master di colonna, invece, non interferisce con le prestazioni del database e può essere eseguita con ruoli separati.
Il diagramma seguente illustra le opzioni di archivio per le chiavi master di colonna in Always Encrypted

![Provider di archivi per chiavi master di colonna Always Encrypted](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Come è possibile ottimizzare e proteggere il traffico tra l'organizzazione e il database SQL?
Il traffico di rete tra l'organizzazione e il database SQL viene in genere instradato sulla rete pubblica. Se tuttavia si sceglie di ottimizzare questo percorso per renderlo più sicuro, è possibile valutare ExpressRoute. ExpressRoute consente di estendere la rete aziendale alla piattaforma Azure tramite una connessione privata. In questo modo si evita l'Internet pubblico. Si conseguono anche livelli superiori di sicurezza, affidabilità e ottimizzazione del routing, che si traducono in minori latenze di rete e velocità più elevata rispetto a quanto si verifica nell'Internet pubblico. Se si intende trasferire un blocco significativo di dati tra l'organizzazione e Azure, l'uso di ExpressRoute può risultare vantaggioso in termini economici. Sono disponibili tre modelli di connettività diversi per la connessione dall'organizzazione ad Azure: 
- [Condivisione percorso per Cloud Exchange](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Any-to-any](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Point-to-Point](../expressroute/expressroute-connectivity-models.md#Ethernet)

ExpressRoute consente anche di raddoppiare il limite di larghezza di banda acquistato senza alcun costo aggiuntivo. È anche possibile configurare la connettività tra più aree con ExpressRoute. Per visualizzare un elenco di provider di connettività ExpressRoute, vedere: [Partner e località di peering per ExpressRoute](../expressroute/expressroute-locations.md). Gli articoli seguenti descrivono ExpressRoute in modo più dettagliato:
- [Introduzione a ExpressRoute](../expressroute/expressroute-introduction.md)
- [Prerequisiti](../expressroute/expressroute-prerequisites.md)
- [Flussi di lavoro](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Il database SQL è conforme ai requisiti normativi e in che modo questo facilita la conformità dell'organizzazione?
Il database SQL rispetta una serie di conformità normative. Per visualizzare il set più recente di conformità soddisfatte, visitare il [Centro protezione di Microsoft](https://www.microsoft.com/trustcenter/compliance/complianceofferings) ed eseguire il drill-down nelle conformità importanti per l'organizzazione per capire se il database SQL sia incluso nei servizi conformi ad Azure. È importante notare che, sebbene il database SQL possa essere certificato come servizio conforme, agevola la conformità del servizio dell'organizzazione, ma non la garantisce automaticamente.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Monitoraggio e manutenzione intelligenti del database dopo la migrazione

Dopo aver eseguito la migrazione del database al database SQL, procedere al monitoraggio del database (ad esempio, verificare l'uso delle risorse o i controlli DBCC) ed eseguire una manutenzione regolare (ad esempio, ricreare o riorganizzare gli indici, le statistiche e così via). Fortunatamente, il database SQL è intelligente nel senso che usa le tendenze cronologiche e le metriche e le statistiche registrate per contribuire in modo proattivo al monitoraggio e alla gestione del database, in modo che l'applicazione venga sempre eseguita in modo ottimale. In alcuni casi, il database SQL di Azure può eseguire automaticamente le attività di manutenzione a seconda delle impostazioni di configurazione. Sono tre gli aspetti alla base del monitoraggio nel database SQL:
- Monitoraggio e ottimizzazione delle prestazioni.
- Ottimizzazione della sicurezza.
- Ottimizzazione dei costi.

**Monitoraggio e ottimizzazione delle prestazioni**: con Informazioni dettagliate prestazioni query è possibile ottenere raccomandazioni personalizzate per il carico di lavoro del database in modo che le applicazioni vengano sempre eseguite a un livello ottimale. È anche possibile configurarlo in modo che queste raccomandazioni vengano applicate automaticamente, evitando di dover eseguire manualmente le attività di manutenzione. Con Index Advisor è possibile implementare automaticamente raccomandazioni relative agli indici in base al carico di lavoro. Questo processo è definito "ottimizzazione automatica". Le raccomandazioni si evolvono di pari passo con il carico di lavoro dell'applicazione per fornire quelle più pertinenti. È anche possibile esaminare manualmente queste raccomandazioni e applicarle a propria discrezione.  

**Ottimizzazione della sicurezza**: il database SQL offre raccomandazioni di sicurezza utilizzabili per consentire di proteggere i dati e il rilevamento delle minacce per identificare ed esaminare attività sospette del database che possono rappresentare una potenziale minaccia. La [valutazione della vulnerabilità SQL](sql-vulnerability-assessment.md) è un servizio di analisi e reporting di database che consente di monitorare lo stato di sicurezza dei database su larga scala e identificare i rischi di sicurezza e la deviazione da una baseline della sicurezza definita dall'utente. Al termine di ogni analisi vengono forniti un elenco personalizzato di passaggi eseguibili e gli script di correzione, nonché un report di valutazione che può essere usato per consentire di rispettare la conformità.

Con il Centro sicurezza di Azure è possibile identificare le raccomandazioni di sicurezza a livello generale e applicarle con un solo clic. 

**Ottimizzazione dei costi**: la piattaforma SQL di Azure analizza la cronologia di utilizzo tra i database in un server per valutare e consigliare opzioni di ottimizzazione dei costi. Questa analisi richiede in genere un paio di settimane per analizzare e formulare raccomandazioni pratiche. Il pool elastico è un'opzione di questo tipo. La raccomandazione viene visualizzata nel portale sotto forma di banner:

![raccomandazioni per i pool elastici](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png) 

È possibile visualizzare questa analisi anche nella sezione "Advisor":

![raccomandazioni per i pool elastici-advisor](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Come monitorare le prestazioni e l'uso delle risorse nel database SQL di Azure?

Nel database SQL è possibile sfruttare le informazioni intelligenti della piattaforma per monitorare le prestazioni e conseguentemente eseguire l'ottimizzazione. È possibile monitorare le prestazioni e l'uso delle risorse nel database SQL con i metodi seguenti:
- **Portale di Azure**: il portale di Azure mostra l'uso di un database singolo selezionandolo e facendo clic sul grafico nel riquadro Panoramica. È possibile modificare il grafico per visualizzare varie metriche, tra cui percentuale di CPU, percentuale di DTU, percentuale I/O dei dati, percentuale di sessioni e percentuale delle dimensioni del database.

   ![Grafico di monitoraggio](./media/sql-database-manage-after-migration/monitoring-chart.png)

   ![Grafico di monitoraggio 2](./media/sql-database-manage-after-migration/chart.png)

Da questo grafico è anche possibile configurare avvisi per risorsa. Questi avvisi consentono di rispondere alle condizioni delle risorse con un messaggio e-mail, scrivere in un endpoint HTTPS/HTTP o eseguire un'azione. Per istruzioni dettagliate, vedere [Monitoraggio delle prestazioni del database nel database SQL di Azure](sql-database-single-database-monitor.md).

- **DMV**: è possibile eseguire una query della vista a gestione dinamica [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) per restituire la cronologia delle statistiche sull'uso delle risorse dall'ultima ora e la vista del catalogo di sistema [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) per restituire la cronologia degli ultimi 14 giorni.
- **Informazioni dettagliate prestazioni query**: [Informazioni dettagliate prestazioni query](sql-database-query-performance.md) consente di visualizzare una cronologia delle query principali a livello di uso delle risorse e le query con esecuzione prolungata per un database specifico. È possibile identificare rapidamente le query principali in base all'uso delle risorse, alla durata e alla frequenza di esecuzione. È possibile tenere traccia delle query e rilevare la regressione. Questa funzionalità richiede che [Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) sia abilitato e attivo per il database.

   ![Informazioni dettagliate prestazioni query](./media/sql-database-manage-after-migration/query-performance-insight.png)

- **Analisi SQL di Azure (anteprima) in Log Analytics**: [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md) consente di raccogliere e visualizzare le metriche di prestazioni chiave SQL di Azure, supportando fino a 150.000 database SQL e 5.000 pool elastici di SQL per area di lavoro. È possibile usare il servizio per monitorare e ricevere notifiche. È possibile monitorare le metriche del database SQL e dei pool elastici in più sottoscrizioni e pool elastici di Azure e usarle per identificare problemi a ogni livello dello stack di applicazioni.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Sto riscontrando problemi di prestazioni: in che modo la metodologia di risoluzione dei problemi del database SQL differisce da SQL Server?
Una parte significativa delle tecniche di risoluzione dei problemi che verrebbero usate per diagnosticare problemi di query e prestazioni del database rimane invariata. Dopo tutto, lo stesso motore di SQL Server alimenta il cloud. Tuttavia, la piattaforma, il database SQL di Azure, include "intelligence" integrata. Consente di risolvere e diagnosticare i problemi di prestazioni anche più facilmente. Può inoltre eseguire alcune di queste azioni correttive per conto dell'utente e, in alcuni casi, correggerli automaticamente in modo proattivo. 

L'approccio alla risoluzione dei problemi delle prestazioni può offrire vantaggi significativi grazie all'uso di funzionalità intelligenti, ad esempio [Informazioni dettagliate prestazioni query](sql-database-query-performance.md) e [Advisor per database](sql-database-advisor.md) insieme, pertanto la differenza di metodologia differisce a tal proposito: non è più necessario eseguire l'attività manuale di recupero dei dettagli essenziali che consentirebbero di risolvere il problema in questione. La piattaforma esegue il lavoro per conto dell'utente. Un esempio a riguardo è QPI, che consente di eseguire il drill-down a livello di query, esaminare le tendenze cronologiche e individuare il momento esatto in cui la query è stata sottoposta a regressione. Advisor per database offre raccomandazioni su elementi che potrebbero migliorare in generale le prestazioni complessive, ad esempio indici mancanti, eliminazione degli indici, parametrizzazione delle query e così via. 

Con la risoluzione dei problemi delle prestazioni è importante stabilire se a incidere sulle prestazioni è solo l'applicazione o il database che la supporta. Spesso il problema delle prestazioni risiede a livello dell'applicazione. Potrebbe trattarsi dell'architettura o dei criteri di accesso ai dati. Si consideri ad esempio di avere un'applicazione "frammentata" sensibile alla latenza di rete. In questo caso, l'applicazione risente del traffico eccessivo di brevi richieste in entrata e in uscita ("frammentazione") tra l'applicazione e il server e, in una rete congestionata, questo traffico si accumula rapidamente. Per migliorare le prestazioni, è possibile usare gli [invii di query in batch](sql-database-performance-guidance.md#batch-queries). L'uso dei batch risulta particolarmente utile, in quanto ora le richieste vengono elaborate in un batch, consentendo di ridurre la latenza di andata e ritorno e migliorare le prestazioni dell'applicazione. 

Se poi si riscontra una riduzione generale delle prestazioni del database, è possibile monitorare le viste a gestione dinamica [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) per individuare il consumo di memoria, CPU e I/O. Le prestazioni potrebbero risentirne perché il database ha esaurito le risorse. Potrebbe essere necessario modificare il livello di prestazioni e/o del servizio in base all'aumento o alla riduzione delle esigenze di carico di lavoro. 

Per un set completo di raccomandazioni per la risoluzione dei problemi relativi alle prestazioni, vedere [Ottimizzare il database](sql-database-performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>Come verificare di usare il livello di servizio e il livello di prestazioni appropriati?
Il database SQL offre vari livelli di servizio: Basic, Standard e Premium. Per ogni livello di servizio si ottengono prestazioni prevedibili garantite a esso associate. A seconda del carico di lavoro, potrebbero verificarsi picchi di attività in cui l'uso delle risorse potrebbe raggiungere il limite massimo del livello di prestazioni corrente in cui si trova l'utente. In questi casi è utile valutare per prima cosa se eseguire o meno un'ottimizzazione (ad esempio, aggiungendo o modificando un indice e così via). Se i problemi di limite persistono, valutare la possibilità di passare a un livello superiore di prestazioni o di servizio. 

|**Livello di servizio**|**Scenari di casi di utilizzo comuni**|
|---|---|
|**Basic**|Applicazioni con alcuni utenti e un database senza requisiti elevati di concorrenza, scalabilità e prestazioni. |
|**Standard**|Applicazioni con requisiti elevati di concorrenza, scalabilità e prestazioni abbinati a richieste I/O medie o ridotte. |
|**Premium**|Applicazioni con numerosi utenti simultanei ed elevate richieste di CPU/memoria e I/O. Le app sensibili alla latenza con concorrenza e velocità effettiva elevate possono sfruttare il livello Premium. |
|||

Per accertarsi di essere nel livello di prestazioni ideale, è possibile monitorare il consumo di risorse di database e query con uno dei modi sopra indicati in "Come monitorare le prestazioni e l'uso delle risorse nel database SQL di Azure?" Se ci si accorge che le query e/o i database esauriscono costantemente CPU, memoria e così via, è opportuno valutare la scalabilità verticale a un livello di prestazioni superiore. In modo analogo, qualora si notasse che, anche nelle ore di punta, le risorse non vengono usate in modo eccessivo, prendere in considerazione la scalabilità verso il basso dal livello di prestazioni corrente. 

In presenza di criteri di app SaaS o uno scenario di consolidamento del database, considerare l'uso di un pool elastico per l'ottimizzazione dei costi. Il pool elastico è un ottimo modo per conseguire il consolidamento dei database e l'ottimizzazione dei costi. Per altre informazioni sulla gestione di più database con un pool elastico, vedere: [Manage pools and databases](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal) (Gestire pool e database). 

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Con quale frequenza è necessario eseguire controlli di integrità del database?
Il database SQL usa alcune tecniche intelligenti che consentono di gestire automaticamente specifiche classi di danneggiamento dei dati, senza alcuna perdita. Queste tecniche sono incorporate nel servizio e vengono usate in base alle esigenze. A intervalli regolari, i backup dei database nel servizio vengono testati ripristinandoli ed eseguendo CHECKDB di DBCC. Se si verificano problemi, il database SQL li gestisce in modo proattivo. La [correzione di pagina automatica](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) viene usata per correggere le pagine danneggiate o con problemi di integrità dei dati. Le pagine di database vengono sempre verificate con l'impostazione CHECKSUM predefinita che verifica l'integrità della pagina. Il database SQL monitora ed esamina in modo proattivo l'integrità dei dati del database e, se si verificano problemi, li gestisce con la massima priorità. È anche possibile eseguire altri controlli di integrità personalizzati.  Per altre informazioni, vedere [Data Integrity in SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) (Integrità dei dati nel database SQL)

## <a name="data-movement-after-migration"></a>Spostamento dei dati dopo la migrazione

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>Come esportare e importare i dati come file BACPAC dal database SQL?

- **Esportazione**: è possibile esportare il database SQL di Azure come file BACPAC dal portale di Azure

   ![esportazione di database](./media/sql-database-export/database-export.png)

- **Importazione**: è anche possibile importare dati come file BACPAC nel database dal portale di Azure.

   ![importazione di database](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Come sincronizzare dati tra il database SQL e SQL Server?
A tale scopo, è possibile procedere in diversi modi: 
- **[Sincronizzazione dei dati](sql-database-sync-data.md)**: questa funzionalità consente di sincronizzare i dati in modo bidirezionale tra più database SQL Server locali e il database SQL. Per eseguire la sincronizzazione con database SQL Server locali, è necessario installare e configurare l'agente di sincronizzazione in un computer locale e aprire la porta TCP in uscita 1433.
- **[Replica di tipo transazionale](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** : con la replica di tipo transazionale è possibile sincronizzare i dati dal server locale al database SQL di Azure, con il primo come database di pubblicazione e il database SQL di Azure come database sottoscrittore. Per il momento è supportata solo questa configurazione. Per altre informazioni su come eseguire la migrazione dei dati dal database locale al database SQL di Azure con tempi di inattività minimi, vedere: [Use Transaction Replication](sql-database-cloud-migrate.md#method-2-use-transactional-replication) (Uso della replica di tipo transazionale)

## <a name="next-steps"></a>Passaggi successivi
Informazioni sul [database SQL](sql-database-technical-overview.md).

