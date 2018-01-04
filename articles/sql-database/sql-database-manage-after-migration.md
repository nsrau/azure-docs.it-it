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
ms.workload: Inactive
ms.date: 12/06/2016
ms.author: Joe.Sack
ms.suite: sql
ms.prod_service: sql-database
ms.component: migration
ms.openlocfilehash: b65236fb2d11473d626ee2602237ed4a49380702
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="new-dba-in-the-cloud--managing-your-database-in-azure-sql-database"></a>Nuovo amministratore del database nel cloud, la gestione del database nel Database di SQL Azure

Lo spostamento da tradizionale gestito autonomo, un ambiente autonomo controllato in un ambiente PaaS può apparire piuttosto complesso inizialmente. Come uno sviluppatore di app o un amministratore di database, si desidera conoscere le funzionalità principali della piattaforma che consentono di mantenere l'applicazione disponibile, ad alte prestazioni, sicura e flessibile - sempre. In questo articolo mira a esattamente a tale scopo. L'articolo sinteticamente organizza le risorse e è fornite indicazioni su come utilizzare al meglio le principali funzionalità di Database SQL per gestire e mantenere l'applicazione in esecuzione in modo efficiente e ottenere risultati ottimali nel cloud. Destinatari tipici per l'articolo sarebbe quelli che:-
- Valutazione migrazione delle rispettive applicazioni al database di SQL Azure, modernizzazione una o più applicazioni.
- Sono in corso la migrazione delle rispettive applicazioni: scenario di migrazione in corso.
- Di recente è stata completata la migrazione al database SQL di Azure – nuovo amministratore del database nel cloud.

In questo articolo vengono descritte alcune delle caratteristiche principali di database SQL di Azure come piattaforma che è possibile sfruttare facilmente. Sono le operazioni seguenti:- 
- Business continuità e ripristino di emergenza (BCDR)
- Sicurezza e conformità
- Monitoraggio database intelligente e la manutenzione
- Spostamento dati


## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Business continuità e ripristino di emergenza (BCDR)
Capacità di ripristino di emergenza e continuità business consentono di continuare l'attività aziendale, come di consueto, in caso di emergenza. L'emergenza potrebbe essere un evento di livello database (ad esempio, un utente erroneamente Elimina una tabella fondamentale) o un evento di livello del centro dati (internazionali della situazione di emergenza, ad esempio un tsunami). 

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Come creare e gestire i backup nel database SQL?
Non è possibile creare i backup nel database SQL di Azure e ciò avviene perché non è necessario. Database SQL esegue automaticamente il backup dei database, pertanto non è più necessario preoccuparsi di pianificazione, accettando e la gestione dei backup. La piattaforma richiede un backup completo ogni settimana, backup differenziale che backup intervalli di poche ore e un log di backup ogni 5 minuti per garantire il ripristino di emergenza è efficiente e la perdita di dati minima. Il primo backup completo avviene non appena si crea un database. Questi backup sono disponibili per un determinato periodo chiamato "Periodo di conservazione" e varia a seconda del livello di prestazioni scelto.  Database SQL offre la possibilità di ripristinare in qualsiasi punto nel tempo all'interno di questo periodo di memorizzazione utilizzando [punto di ripristino ora ripristino (Temporizzato) in](sql-database-recovery-using-backups.md#point-in-time-restore).

|Livello di prestazioni|Periodo di memorizzazione in giorni|
|---|:---:|
|Basic|7|
|Standard|35|
|Premium|35|
|||

Inoltre, il [conservazione a lungo termine (LTR)](sql-database-long-term-retention.md) funzionalità consente di conservare i file di backup per un periodo più lungo in particolare, per un massimo di 10 anni e ripristinare i dati da questi backup in un punto qualsiasi all'interno di tale periodo. Inoltre, i backup del database vengono mantenuti in un archivio di replica geografica per garantire la resilienza da calamità internazionali. È inoltre possibile ripristinare tali backup in qualsiasi area di Azure in qualsiasi momento di tempo entro il periodo di memorizzazione. Vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Come è possibile garantire la continuità aziendale in caso di emergenza a livello di Data Center o internazionali della situazione di emergenza?
Poiché i backup dei database sono archiviati in un sottosistema di archiviazione replicata geograficamente per garantire che in caso di situazioni di emergenza, è possibile ripristinare il backup in un'altra area di Azure. Si tratta del ripristino a livello geografico. Il valore RPO (Recovery Point Objective) per questo oggetto è in genere < 1 ora ed ERT (ripristino tempo stimato: ore alcuni minuti).

Per i database di importanza critica, database SQL di Azure offre, replica geografica attiva. In questo modo essenzialmente è una copia secondaria replica geografica del database originale viene creato in un'altra area. Ad esempio, se il database è inizialmente ospitato in Azure ovest area Stati Uniti e si desidera resilienza situazioni di emergenza. Negli Stati Uniti occidentali, ad esempio Stati Uniti orientali, è necessario creare una replica geografica attiva del database. Quando il calamità colpisce su Stati Uniti occidentali, è possibile eseguire il failover nell'area Stati Uniti orientali. La configurazione in un gruppo con Failover automatico è ancora migliore perché in questo modo si garantisce che il database automaticamente eseguito il failover al database secondario in Stati Uniti orientali in caso di emergenza. Il valore RPO per questo è < 5 secondi e secondi ERT < 30.

Se non è configurato un gruppo di failover automatico, l'applicazione deve monitorare attivamente la presenza di una situazione di emergenza e avviare un failover al database secondario. È possibile creare fino a 4 tali active repliche geo attive in diverse aree di Azure. Ottiene ancora migliore. È inoltre possibile accedere a questi active geo-repliche secondarie per l'accesso in sola lettura. Questo risulta molto utile in per ridurre la latenza per uno scenario di applicazione distribuita geograficamente. 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Come è modifica il piano di ripristino di emergenza locale al Database SQL?
In breve, è necessaria l'installazione di SQL Server locale tradizionali di gestire la propria disponibilità utilizzando le funzionalità, ad esempio il Clustering di Failover, il mirroring del Database, replica transazionale, Log Shipping e così via e mantenere e gestire i backup per garantire attivamente Continuità aziendale. Con il Database SQL, la piattaforma gestisce questi, pertanto è possibile concentrarsi sullo sviluppo e ottimizzare l'applicazione di database e non comporta problemi di gestione di emergenza lo stesso. È possibile disporre di backup e i piani di ripristino di emergenza configurati e funzionante con pochi clic nel portale di Azure (o alcuni comandi utilizzando le APIs PowerShell). 

Per ulteriori informazioni sul ripristino di emergenza, vedere: [101 ripristino di emergenza di Azure SQL database](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Sicurezza e conformità
Database SQL richiede molto seri sicurezza e Privacy. Sicurezza in Database SQL è disponibile a livello di database e a livello di piattaforma e risulta più semplice comprendere quando suddivisi in livelli diversi. A ogni livello è possibile controllare e garantire una sicurezza ottimale per l'applicazione. I livelli sono:
- Identità e autenticazione ([l'autenticazione di Windows/SQL e l'autenticazione di Azure Active Directory [AAD]](sql-database-control-access.md)).
- Monitoraggio dell'attività ([controllo](sql-database-auditing.md) e [rilevamento delle minacce](sql-database-threat-detection.md)).
- Protezione dei dati effettivi ([Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) e [Always Encrypted [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)). 
- Controllo dell'accesso ai dati sensibili e con privilegi ([protezione a livello di riga](/sql/relational-databases/security/row-level-security) e [maschera dati dinamica](/sql/relational-databases/security/dynamic-data-masking)).

Il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) offre la gestione centralizzata della sicurezza per carichi di lavoro in esecuzione in Azure, in locale e in altri cloud. È possibile visualizzare se essenziali protezione del Database SQL, ad esempio [controllo](sql-database-auditing.md) e [Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) sono configurati in tutte le risorse e creare i criteri in base alle proprie esigenze.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>I metodi di autenticazione utente sono disponibili nel Database SQL?
Esistono [due metodi di autenticazione](sql-database-control-access.md#authentication) disponibili nel Database SQL: 
- [Autenticazione di Azure Active Directory](sql-database-aad-authentication.md)
- Autenticazione di SQL. 

Non è supportata l'autenticazione di windows tradizionale. Azure Active Directory (AD) è un servizio di gestione centralizzato di identità e accessi. Con questo è possibile fornire in modo molto un Single Sign-on accesso (SSO) per tutto il personale dell'organizzazione. Ciò significa che le credenziali vengono condivise tra tutti i servizi di Azure per l'autenticazione più semplice. Supporta AAD [MFA (multi-Factor Authentication)](sql-database-ssms-mfa-authentication.md) e un oggetto con un [poche](../active-directory/connect/active-directory-aadconnect-get-started-express.md) AAD può essere integrato con Windows Server Active Directory. Autenticazione di SQL Server funziona esattamente come si usano già nel passato. Fornire un nome utente/password e per autenticare gli utenti a qualsiasi database in un determinato server logico. In questo modo, il database SQL e SQL Data Warehouse possono anche offrire l'autenticazione a più fattori e gli account utente guest all'interno di un dominio di Azure AD. Se Active Directory è già disponibile in locale, è possibile attuare la federazione della directory con Azure Active Directory per estendere la directory ad Azure.

|**Se si...**|**Database SQL / SQL Data Warehouse**|
|---|---|
|Si preferisce non usare Azure Active Directory (AD) in Azure|Usare l'[autenticazione in SQL](sql-database-security-overview.md)|
|È stato usato Active Directory in SQL Server in locale|[Attuare la federazione di Active Directory con Azure AD](../active-directory/connect/active-directory-aadconnect.md) e usare l'autenticazione di Azure AD. In questo caso è possibile usare Single Sign-On.|
|È necessario applicare l'autenticazione a più fattori (MFA)|Richiedere MFA come criterio mediante l'[accesso condizionale di Microsoft](sql-database-conditional-access.md) e usare l'[autenticazione universale di Azure AD con il supporto di MFA](sql-database-ssms-mfa-authentication.md).|
|Si dispone di account guest Microsoft (live.com, outlook.com) o di altri domini (gmail.com)|Usare l'[autenticazione universale di Azure AD](sql-database-ssms-mfa-authentication.md) nel database SQL o in Data Warehouse, che sfrutta la [collaborazione B2B di Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
|Si è connessi a Windows con le credenziali di Azure AD da un dominio federato|Usare l'[autenticazione integrata di Azure AD](sql-database-aad-authentication-configure.md).|
|Si è connessi a Windows con le credenziali di un dominio non federato con Azure|Usare l'[autenticazione integrata di Azure AD](sql-database-aad-authentication-configure.md).|
|Servizi di livello intermedio che devono connettersi al Database SQL o SQL Data Warehouse|Usare l'[autenticazione integrata di Azure AD](sql-database-aad-authentication-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Come limitare o controllare l'accesso di connettività al database?
Esistono diverse tecniche a disposizione che è possibile utilizzare per ottenere l'organizzazione di connettività ottimale per l'applicazione. 
- Regole del firewall
- Endpoint del servizio di rete virtuale
- IP riservati

#### <a name="firewall"></a>Firewall
Un firewall impedisce l'accesso al server da un'entità esterna, consentendo solo l'accesso entità specifiche per il server logico. Per impostazione predefinita, tutte le connessioni e i database all'interno del server logico non sono consentiti, ad eccezione delle connessioni provenienti da altri servizi di Azure. Con una regola del firewall è possibile aprire l'accesso al server solo per le entità (ad esempio, una macchina per sviluppatori) da approvare, consentendo l'indirizzo IP del computer attraverso il firewall. Consente inoltre di specificare un intervallo di indirizzi IP che si desidera consentire l'accesso al server logico. Ad esempio, indirizzi IP per sviluppatori all'interno dell'organizzazione possono essere aggiunto in una sola volta specificando un intervallo nella pagina delle impostazioni di Firewall. 

È possibile creare regole del firewall a livello di server o a livello di database. Le regole firewall a livello di server possono creato tramite il portale o tramite SQL Server Management Studio. Per altre informazioni su come impostare un server e una regola del firewall a livello di database, vedere: [creare regole del firewall nel Database SQL](sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal).

#### <a name="service-endpoints"></a>Endpoint di servizio
Per impostazione predefinita, il database SQL è configurato per "Consenti a tutti i servizi di Azure", ovvero qualsiasi macchina virtuale in Azure può tentare di connettersi al database. Questi tentativi ancora ottenere l'autenticazione. Tuttavia, se non si desidera il database deve essere accessibile da tutti gli indirizzi IP di Azure, è possibile disabilitare "Consenti a tutti i servizi di Azure". Inoltre, è possibile configurare [gli endpoint del servizio di rete virtuale](sql-database-vnet-service-endpoint-rule-overview.md).

Gli endpoint del servizio (SE) consentono di esporre le risorse di Azure critico solo alla propria rete privata virtuale in Azure. In questo modo, è essenzialmente eliminare accesso pubblico alle risorse. Il traffico tra la rete virtuale in Azure resta attiva la backbone di Azure. Senza SE è possibile ottenere il tunneling forzato routing dei pacchetti. La rete virtuale forza il traffico internet per l'organizzazione e il traffico del servizio di Azure per elencare le route stesso. Con gli endpoint del servizio, è possibile ottimizzare questo perché il flusso di pacchetti direttamente dalla rete virtuale al servizio di rete backbone di Azure.

![Endpoint del servizio di rete virtuale](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

#### <a name="reserved-ips"></a>IP riservati
È anche possibile eseguire il provisioning [riservato di indirizzi IP](../virtual-network/virtual-networks-reserved-public-ip.md) per le macchine virtuali e whitelist parametri specifici indirizzi IP della VM nel server le impostazioni del firewall. L'assegnazione degli indirizzi IP riservati evita di dover aggiornare le regole firewall quando si modificano gli indirizzi IP.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>La porta ci si connette al Database SQL in?

Porta 1433. Database SQL comunica attraverso questa porta. Per connettersi da una rete aziendale, è necessario aggiungere una regola in uscita nelle impostazioni del firewall dell'organizzazione. Come indicazione generale, evitare di esporre la porta 1433 all'esterno dei limiti di Azure. È possibile eseguire SQL Server Management Studio in Azure tramite [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps). Questo non è necessario aprire le connessioni in uscita per la porta 1433, l'indirizzo IP statico, in modo che il database può essere aperto per solo RemoteApp e supporta più l'autenticazione a più fattori.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Come è possibile monitorare e regolare l'attività in my server e database nel Database SQL?
#### <a name="sql-database-auditing"></a>Controllo del Database SQL
Con il Database SQL, è possibile attivare il controllo su ON per tenere traccia degli eventi di database. [Controllo del Database SQL](sql-database-auditing.md) registra gli eventi di database e li scrive in un file di log di controllo nell'Account di archiviazione di Azure. Il controllo è particolarmente utile se si desidera ottenere informazioni su potenziali violazioni di sicurezza e i criteri, mantenere la conformità ai requisiti normativi e così via. Consente di definire e configurare determinate categorie di eventi che si ritiene necessario il controllo e in base che è possibile ottenere i report preconfigurati e un dashboard per ottenere una panoramica degli eventi che si verificano sul database. È possibile applicare questi criteri di controllo a livello di database o a livello di server. Una Guida su come attivare il controllo per il server/database, vedere: [abilitare il controllo dei Database di SQL](sql-database-security-tutorial.md#enable-sql-database-auditing-if-necessary).

#### <a name="threat-detection"></a>Rilevamento delle minacce
Con [rilevamento delle minacce](sql-database-threat-detection.md), si ottiene la capacità di agire le violazioni di sicurezza o criteri individuate per il controllo in modo molto semplice. Non è necessario essere un esperto di dominio per risolvere potenziali minacce o violazioni nel sistema di sicurezza. Rilevamento minacce dispone anche di alcune funzionalità incorporate come il rilevamento di attacchi SQL Injection. Attacco intrusivo nel codice SQL viene eseguito un tentativo di modificare o compromettere i dati e un modo molto comune per attaccare in genere un'applicazione di database. Il rilevamento delle minacce del database SQL esegue vari set di algoritmi che rilevano potenziali vulnerabilità e attacchi SQL injection, nonché modelli anomali di accesso al database (ad esempio, accesso da una posizione insolita o da un'entità di sicurezza sconosciuta). I responsabili della sicurezza o altri amministratori designati ricevono una notifica e-mail se viene rilevata una minaccia nel database. Ogni notifica contiene dettagli sull'attività sospetta e consigli su come eseguire altre indagini e mitigare la minaccia. Per informazioni su come attivare la funzionalità di rilevamento minacce, vedere: [Abilita rilevamento minacce del Database SQL](sql-database-security-tutorial.md#enable-sql-database-threat-detection). 
### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Modo è possibile proteggere i dati in generale nel Database SQL?
La crittografia fornisce un meccanismo sicuro per proteggere i dati riservati da intrusioni. I dati crittografati sono di non utilizzare per l'utente non autorizzato senza la chiave di decrittografia. In questo modo, viene aggiunto un ulteriore livello di protezione sopra i livelli di protezione creato nel Database di SQL esistente. Sono disponibili due opzioni per la protezione dei dati nel Database SQL: 
- I dati che sono a riposo nei file di dati e di log 
- I dati in transito. 

Nel Database SQL, per impostazione predefinita, i dati inattivi nei file di dati e di log nel sottosistema di archiviazione sono completamente e sempre crittografati tramite [Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). I backup vengono crittografati anche. Con TDE è non sono presenti modifiche necessarie sul lato applicazione che accede a questi dati. Crittografia e decrittografia si verifica in modo trasparente; pertanto il nome. Per proteggere i dati sensibili in transito e inattivi, Database SQL fornisce una funzionalità denominata [Always Encrypted (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine). AE è un tipo di crittografia lato client che crittografa colonne sensibili nel database (in modo che siano in testo crittografato per gli amministratori di database e gli utenti non autorizzati). Il server riceve i dati crittografati per iniziare. La chiave di crittografia sempre attiva verrà memorizzata anche sul lato client, pertanto solo i client autorizzati in grado di decrittografare le colonne sensibili. Il server e gli amministratori dei dati non è possibile visualizzare i dati sensibili poiché le chiavi di crittografia vengono archiviate sul client. AE crittografa colonne riservate nella tabella to end, da un client non autorizzati per il disco fisico. AE supporta i confronti di uguaglianza oggi, gli amministratori di database consentire query sulle colonne crittografate come parte dei comandi SQL. Always Encrypted può essere usato con un'ampia gamma di opzioni di archiviazione di chiavi, ad esempio [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), l'archivio certificati di Windows e i moduli di protezione hardware locali.

|**Caratteristiche**|**Always Encrypted**|**Transparent Data Encryption**|
|---|---|---|
|**Intervallo di crittografia**|End-to-end|Dati a riposo|
|**Server di database può accedere a dati sensibili**|No |Sì, poiché la crittografia per i dati inattivi|
|**Operazioni di T-SQL consentite**|Confronto di uguaglianza|Tutti della superficie di attacco di T-SQL è disponibile|
|**Modifiche all'applicazione è necessarie utilizzare la funzionalità**|Minima|Minimo|
|**Granularità di crittografia**|Livello di colonna|Livello di database|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Come limitare l'accesso ai dati sensibili nel database?
Ogni applicazione dispone di un determinato bit dei dati sensibili nel database che devono essere protetti sia visibile a tutti gli utenti. Determinate personale all'interno dell'organizzazione è necessario per visualizzare questi dati, tuttavia altri utenti non devono essere in grado di visualizzare i dati. Un esempio è retribuzioni dei dipendenti. Una gestione è necessario accedere alle informazioni di retribuzione di proprie diretti, tuttavia, i singoli membri del team non devono avere accesso alle informazioni di retribuzione di peer. Un altro scenario è agli sviluppatori di dati che potrebbero essere l'interazione con i dati sensibili durante le fasi di sviluppo o test, ad esempio, SSNs dei clienti. Queste informazioni non devono necessariamente essere esposti allo sviluppatore. In questi casi, i dati riservati devono sia da mascherare o non essere esposti. Database SQL offre due questi approcci per impedire a utenti non autorizzati di visualizzare i dati sensibili:

[La maschera dati dinamica](sql-database-dynamic-data-masking-get-started.md) è una funzionalità di maschera dati che consente di limitare l'esposizione dei dati sensibili nascondendoli agli utenti senza privilegi sul livello dell'applicazione. Definire una regola di maschera che è possibile creare un criterio di maschera (ad esempio, da mostrare solo ultime quattro cifre di un esempio di ID national SSN: XXX-XX-0000 e contrassegnare la maggior parte di esso come Xs) e identificare gli utenti che devono essere escluse dalla regola di maschera. Il mascheramento avviene in tempo reale e sono disponibili varie funzioni di maschera per diverse categorie di dati. La maschera dati dinamica consente di rilevare i dati sensibili nel database e applicare la maschera automaticamente.

[Sicurezza a livello di riga](/sql/relational-databases/security/row-level-security) consente di controllare l'accesso a livello di riga. Pertanto, sono nascosti alcune righe in una tabella di database in base all'utente l'esecuzione della query (appartenenza o l'esecuzione del contesto del gruppo). La limitazione dell'accesso viene eseguita a livello di database anziché a livello di applicazione, per semplificare la logica dell'app. Iniziare creando un predicato del filtro per escludere le righe che non vengono esposti e di sicurezza criteri successiva definizione chi ha accesso a queste righe. Infine, l'utente finale esegue le query e, a seconda dei privilegi dell'utente, consente di visualizzare le righe con restrizioni o non è possibile visualizzarli tutti.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Come è possibile gestire le chiavi di crittografia nel cloud?

Sono disponibili opzioni di gestione delle chiavi per Always Encrypted (crittografia lato client) e Transparent Data Encryption (crittografia dei dati inattivi). È consigliabile ruoti regolarmente le chiavi di crittografia. La frequenza di rotazione deve essere allineate con le normative organizzazione interna e i requisiti di conformità.

**Transparent Data Encryption (TDE)**: in TDE è presente una gerarchia a due chiavi. I dati in ogni database utente vengono crittografati con una chiave di crittografia simmetrica AES 256 univoca per il database, che a sua volta viene crittografata da una chiave master asimmetrica RSA 2048 univoca per il server. La chiave master può essere gestito uno:
- Automaticamente dalla piattaforma - Database SQL.
- Oppure è necessario utilizzare [insieme credenziali chiavi Azure](sql-database-always-encrypted-azure-key-vault.md) come archivio delle chiavi.

Per impostazione predefinita, la chiave master per Transparent Data Encryption viene gestita dal servizio di database SQL per praticità. Se l'organizzazione desidera controllo tramite la chiave master, è disponibile un'opzione per utilizzare Vault](sql-database-always-encrypted-azure-key-vault.md) chiave di Azure come archivio delle chiavi. Con Azure Key Vault l'organizzazione assume il controllo sul provisioning delle chiavi, sulla rotazione e sulle autorizzazioni. [La rotazione o il cambio del tipo di una chiave master TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) è un processo rapido, poiché ripete solo la crittografia della chiave di crittografia del database. Per le organizzazioni con separazione dei ruoli tra sicurezza e gestione dei dati, un amministratore responsabile della sicurezza potrebbe eseguire il provisioning di materiale della chiave per la chiave master di Transparent Data Encryption nell'insieme di credenziali chiave di Azure e fornire un identificatore di chiave dell'insieme di credenziali di Azure chiave per l'amministratore del database da utilizzare per crittografia in un server. Insieme di credenziali chiave è progettato in modo che Microsoft non vedere o estrarre le chiavi di crittografia. Anche possibile ottenere una gestione centralizzata delle chiavi per l'organizzazione. 

**Always Encrypted**: è inoltre disponibile un [gerarchia combinazioni di tasti](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) in Always Encrypted - una colonna di dati sensibili verrà crittografata mediante una chiave di crittografia di colonna di AES 256 (CEK), che a sua volta viene crittografata dalla chiave master della colonna (CMK). I driver del client forniti per Always Encrypted non presentano limitazioni di lunghezza per le chiavi master di colonna. Il valore crittografato della chiave di crittografia di colonna viene archiviato nel database e quello della chiave master di colonna in un archivio di chiavi attendibile, ad esempio l'archivio certificati di Windows, Azure Key Vault e i moduli di protezione hardware locali. 
- È possibile alternare [sia la chiave di crittografia di colonna sia la chiave master di colonna](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell). 
- Rotazione CEK è un'operazione di dimensionamento dei dati e può essere molto tempo a seconda delle dimensioni delle tabelle contenenti colonne crittografate. Pertanto è consigliabile pianificare di conseguenza le rotazioni CEK. 
- Rotazione di CMK, tuttavia, non interferisce con le prestazioni del database e può essere eseguita con i ruoli separati.
Il diagramma seguente illustra le opzioni di archivio per le chiavi master di colonna in Always Encrypted

![Provider di archivi per chiavi master di colonna Always Encrypted](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Come è possibile ottimizzare e proteggere il traffico tra l'organizzazione e il Database SQL?
Il traffico di rete tra l'organizzazione e il Database SQL in genere sarebbe instradato sulla rete pubblica. Tuttavia, se si sceglie di ottimizzare il percorso e renderlo più sicuro, è possibile esaminare Express Route. Express route essenzialmente consente di estendere la rete aziendale nella piattaforma Azure tramite una connessione privata. In questo modo, non si accede sulla rete Internet pubblica. Anche possibile ottenere una maggiore sicurezza, affidabilità e ottimizzazione di routing che si traduce in minori latenze di rete e velocità molto più veloce rispetto a quelle normalmente verifica una situazione trasmessi tramite la rete internet pubblica. Se si intende trasferire una notevole quantità di dati tra l'organizzazione e Azure, l'uso di Express Route può produrre vantaggi di costo. Sono disponibili tre modelli diversi di connettività per la connessione della propria organizzazione in Azure: 
- [Cloud Exchange condivisione percorso](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Qualsiasi per qualsiasi](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Point to Point](../expressroute/expressroute-connectivity-models.md#Ethernet)

Express Route consente inoltre di potenziamento fino a 2x il limite di larghezza di banda acquistato senza alcun costo aggiuntivo. È anche possibile configurare la connettività tra più aree con ExpressRoute. Per visualizzare un elenco di provider di connettività di Expressroute, vedere: [Express Route partner e località di Peering](../expressroute/expressroute-locations.md). Gli articoli seguenti descrivono Express Route in modo più dettagliato:
- [Introduzione a ExpressRoute](../expressroute/expressroute-introduction.md)
- [Prerequisiti](../expressroute/expressroute-prerequisites.md)
- [Flussi di lavoro](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Il database SQL è conforme ai requisiti normativi e in che modo questo facilita la conformità dell'organizzazione?
Database SQL è conforme a un intervallo delle conformità ai requisiti normativi. Per visualizzare il set più recente delle conformità che sono stati soddisfatti, visitare il [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings) e drill-down le di conformità che sono importanti per l'organizzazione per vedere se il Database SQL è incluso in servizi di Azure conformi. È importante notare che anche se il Database SQL possono essere certificato come un servizio conforme, facilita la conformità del servizio dell'organizzazione ma non lo garantiscono automaticamente.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Monitoraggio database intelligente e la manutenzione dopo la migrazione

Dopo aver eseguito la migrazione di database al Database SQL, verranno per monitorare il database (ad esempio, come l'utilizzo delle risorse è come controllo o DBCC controlla) e di eseguire la manutenzione regolare (ad esempio, ricompilare o riorganizzare gli indici, statistiche e così via). Fortunatamente, Database SQL è intelligente nel senso che usa le tendenze cronologiche metriche registrate e statistiche che in modo proattivo consentono di monitorare e gestire il database, in modo che l'applicazione viene eseguita sempre in modo ottimale. In alcuni casi, i database SQL di Azure può eseguire automaticamente le attività di manutenzione a seconda delle impostazioni di configurazione. Esistono tre facet per il monitoraggio del database nel Database SQL:
- Monitoraggio delle prestazioni e ottimizzazione.
- Ottimizzazione della sicurezza.
- Ottimizzazione dei costi.

**Monitoraggio delle prestazioni e ottimizzazione**: con informazioni dettagliate prestazioni Query, è possibile ottenere indicazioni personalizzate per il carico di lavoro del database in modo che le applicazioni possono mantenere in esecuzione su un livello ottimale, sempre. È possibile anche configurarlo in modo che questi suggerimenti viene applicati automaticamente e non è necessario preoccuparsi di eseguire attività di manutenzione. Con Index Advisor, è possibile implementare automaticamente le indicazioni relative agli indici in base al carico di lavoro - si tratta di ottimizzazione automatica. Le indicazioni evolvono come le modifiche del carico di lavoro dell'applicazione per fornire i suggerimenti più rilevanti. Anche possibile ottenere la possibilità di esaminare queste indicazioni manualmente e applicarli a propria discrezione.  

**Ottimizzazione della sicurezza**: Database SQL vengono forniti consigli di sicurezza utilizzabili per consentire di proteggere i dati di rilevamento minacce per identificare e valutare le attività sospette database che possono rappresentare un potenziale thread per il database. [Valutazione delle vulnerabilità SQL](sql-vulnerability-assessment.md) è un database di analisi e di reporting del servizio che consente di monitorare lo stato di sicurezza dei database su larga scala e identificare i rischi di sicurezza e passare da una linea di base di sicurezza definito dall'utente. Al termine di ogni analisi vengono forniti un elenco personalizzato di passaggi eseguibili e gli script di correzione, nonché un report di valutazione che può essere usato per consentire di rispettare la conformità.

Con il centro di sicurezza di Azure, si identificano le indicazioni di sicurezza nella Lavagna e applicarle con un solo clic. 

**Ottimizzazione dei costi**: piattaforma SQL Azure consente di analizzare la cronologia di utilizzo tra i database in un server per valutare e opzioni di ottimizzazione dei costi è consigliabile. Questa analisi richiede in genere un due settimane per analizzare e compilare i suggerimenti pratici. Pool elastico è una di queste opzioni. L'indicazione viene visualizzato nel portale di come un banner di:

![raccomandazioni per i pool elastici](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png) 

È inoltre possibile visualizzare l'analisi della sezione "Advisor":

![indicazioni-advisor pool elastico](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Come monitorare le prestazioni e utilizzo delle risorse nel Database SQL?

Nel Database SQL è possibile sfruttare le informazioni dettagliate intelligente della piattaforma per monitorare le prestazioni e ottimizzare di conseguenza. È possibile monitorare le prestazioni e utilizzo delle risorse nel Database SQL tramite i metodi seguenti:
- **Portale di Azure**: il portale di Azure mostra l'uso di un database singolo selezionandolo e facendo clic sul grafico nel riquadro Panoramica. È possibile modificare il grafico per visualizzare varie metriche, tra cui percentuale di CPU, percentuale di DTU, percentuale I/O dei dati, percentuale di sessioni e percentuale delle dimensioni del database.

   ![Grafico di monitoraggio](./media/sql-database-manage-after-migration/monitoring-chart.png)

   ![Monitoraggio chart2](./media/sql-database-manage-after-migration/chart.png)

Da questo grafico è anche possibile configurare gli avvisi dalla risorsa. Questi avvisi consentono di rispondere alle condizioni di risorse con un messaggio di posta elettronica, a un endpoint HTTPS o HTTP o eseguire un'azione. Vedere il [monitoraggio delle prestazioni del database nel Database SQL](sql-database-single-database-monitor.md) per istruzioni dettagliate.

- **Viste a gestione dinamica**: È possibile eseguire una query di [Sys.dm db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) vista a gestione dinamica per restituire cronologia statistiche di utilizzo risorse dall'ultima ora e [Sys. resource_stats ](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) vista del catalogo di sistema per restituire una cronologia per gli ultimi 14 giorni.
- **Informazioni dettagliate prestazioni query**: [Informazioni dettagliate prestazioni query](sql-database-query-performance.md) consente di visualizzare una cronologia delle query principali a livello di uso delle risorse e le query con esecuzione prolungata per un database specifico. È possibile identificare rapidamente prime query per l'utilizzo delle risorse, la durata e frequenza di esecuzione. È possibile tenere traccia delle query e rilevare regressione. Questa funzionalità richiede che [Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) sia abilitato e attivo per il database.

   ![Informazioni dettagliate prestazioni query](./media/sql-database-manage-after-migration/query-performance-insight.png)

- **Analitica di SQL Azure (anteprima) nel Log Analitica**: [Azure Log Analitica](../log-analytics/log-analytics-azure-sql.md) consente di raccogliere e visualizzare metriche di prestazioni chiave SQL di Azure, che supporta fino a 150.000 database SQL e 5.000 pool elastico SQL per area di lavoro. È possibile usare il servizio per monitorare e ricevere notifiche. È possibile monitorare i Database SQL e le metriche di pool elastico in più sottoscrizioni di Azure e i pool elastici e può essere usato per identificare i problemi relativi a ogni livello dello stack di un'applicazione.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Sono notato problemi di prestazioni: il Database SQL i problemi che impediscono la differenza da SQL Server?
Una parte importante delle tecniche di risoluzione dei problemi che si utilizzerebbero per la diagnosi di query e i problemi di prestazioni del database rimangono invariati. Dopo avere tutte lo stesso SQL Server motore consente di creare il cloud. Tuttavia, la piattaforma - database SQL di Azure è compilato in "intelligence". Consentono di risolvere e diagnosticare i problemi di prestazioni anche più facilmente. Può inoltre eseguire alcune di queste azioni correttive per conto dell'utente e in alcuni casi, in modo proattivo correggere - automaticamente. 

L'approccio per la risoluzione dei problemi di prestazioni in modo significativo può trarre vantaggio da usare la funzionalità intelligente come [Insight(QPI) prestazioni Query](sql-database-query-performance.md) e [di Database proporrà](sql-database-advisor.md) insieme e in modo che la differenza di metodologia diverso in proposito: non è più necessario eseguire il lavoro manuale di intercettazione di informazioni essenziali che consentono di risolvere il problema in questione. La piattaforma esegue il lavoro. Un esempio di tale è QPI. Con QPI, è possibile eseguire il drill-fino a livello di query ed esaminare le tendenze cronologiche e capire quando esattamente la query regredite. Guidata Database consente di ottenere suggerimenti sulle operazioni che potrebbero migliorare le prestazioni complessive in generale come - indici mancanti, l'eliminazione degli indici, la parametrizzazione delle query e così via. 

Risoluzione dei problemi di prestazioni, è importante stabilire se è solo l'applicazione o il database di sostegno, che sta influenzando le prestazioni dell'applicazione. Spesso il problema di prestazioni si trova nel livello dell'applicazione. Potrebbe essere l'architettura o il modello di accesso ai dati. Si consideri ad esempio che si dispone di un'applicazione "frammentate" è sensibile alla latenza di rete. In questo caso, l'applicazione si verifica perché non vi sarà un numero di richieste short in avanti e indietro i round trip ("chatty") tra l'applicazione e il server e in una rete congestionata, aggiungere rapidamente. Per migliorare le prestazioni in questo caso, è possibile utilizzare [query Batch](sql-database-performance-guidance.md#batch-queries). Utilizzo dei batch consente notevole perché ora elaborare le richieste in un batch; Pertanto, consentono di ridurre la latenza di andata e ritorno e migliorare le prestazioni dell'applicazione. 

Inoltre, se si nota un calo delle prestazioni complessive del database, è possibile monitorare il [Sys.dm db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) al fine di viste a gestione dinamica comprendere il consumo di CPU, i/o e memoria. Le prestazioni interessati forse perché il database è rimasto in scadenza delle risorse. È possibile che potrebbe essere necessario modificare il livello di prestazioni e/o del servizio di livello in base l'espansione e riduzione del carico di lavoro. 

Per un set completo di indicazioni per l'ottimizzazione di problemi di prestazioni, vedere: [ottimizzare il database](sql-database-performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>Come verificare di usare il livello di servizio e il livello di prestazioni appropriati?
Database SQL offre vari livelli di servizio Basic, Standard e Premium. Ogni livello di servizio si ottiene un garantisce prestazioni prevedibili associato a tale livello di servizio. A seconda del carico di lavoro, è possibile picchi di attività in cui l'utilizzo delle risorse potrebbe essere raggiunto il limite massimo del livello di prestazioni corrente in cui si trova. In questi casi, è utile al primo avvio valutando se qualsiasi ottimizzazione consente (ad esempio, aggiunta o modifica di un indice, ecc.). Se si verificano ancora problemi di limite, si consiglia di spostare a un livello di prestazioni superiore o a livello di servizio. 

|**Livello di servizio**|**Scenari di casi di utilizzo comuni**|
|---|---|
|**Basic**|Applicazioni con gli utenti di un numero limitato e un database che non hanno requisiti di concorrenza, scalabilità e prestazioni elevati. |
|**Standard**|Applicazioni con un considerevole requisiti di concorrenza, scalabilità e prestazioni associato a bassa per le richieste dei / o medie. |
|**Premium**|Le richieste dei / o applicazioni con un numero elevato di utenti simultanei ed elevato della CPU o memoria ad alta. Concorrenza elevata velocità effettiva elevata e latenza sensibili App può sfruttare il livello Premium. |
|||

Per assicurarsi che si è a livello di prestazioni idoneo, è possibile monitorare il consumo di risorse di query e il database tramite uno dei modi sopra indicati in "Come monitorare l'utilizzo di risorse e delle prestazioni nel Database SQL". In caso di rilevamento che/database di query in modo coerente in esecuzione frequente su CPU/memoria ECC è possibile prendere in considerazione la scalabilità verticale a un livello di prestazioni superiore. In modo analogo, se si nota che anche durante le ore di punta, si è autorizzati a usare le risorse come la maggior parte; prendere in considerazione la scalabilità verso il basso dal livello di prestazioni corrente. 

Se si dispone di un modello di app SaaS o per uno scenario di consolidamento di database, considerare l'utilizzo di un Pool elastico per l'ottimizzazione dei costi. Pool elastico è un ottimo modo per ottenere il consolidamento di database e di ottimizzazione dei costi. Per altre informazioni sulla gestione di più database Usa un Pool elastico, vedere: [gestire i database e i pool](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal). 

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>La frequenza con cui è necessario eseguire controlli di integrità del database per il database?
Database SQL utilizza alcune tecniche intelligenti che consentono di gestire alcune classi di danneggiamento dei dati automaticamente, senza alcuna perdita di dati. Queste tecniche sono incorporate nel servizio e vengono utilizzate dal servizio necessario quando si verifica. A intervalli regolari, i backup dei database tra il servizio vengono testati ripristinarli e l'esecuzione di DBCC CHECKDB su di esso. Se si verificano problemi, Database SQL in modo proattivo vengono risolti. [Correzione automatica della pagina](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) viene utilizzata per la correzione di pagine che sono danneggiati oppure con problemi di integrità dei dati. Le pagine di database sono sempre verificate con l'impostazione del CHECKSUM predefinita che verifica l'integrità della pagina. Database SQL in modo proattivo consente di monitorare e verifica l'integrità dei dati del database e, se si verificano problemi, con cui vengono risolti con la priorità più alta. Oltre a queste, è possibile, facoltativamente, eseguire i controlli di integrità del necessario.  Per ulteriori informazioni, vedere [l'integrità dei dati nel Database SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Spostamento dei dati dopo la migrazione

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>Come esportare e importare i dati come file BACPAC dal Database SQL?

- **Esportare**: È possibile esportare il database SQL di Azure come file BACPAC dal portale di Azure

   ![Esportazione del database](./media/sql-database-export/database-export.png)

- **Importare**: È anche possibile importare dati in un file BACPAC nel database di mediante il portale di Azure.

   ![Importazione di database](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Come sincronizzare dati tra Database SQL e SQL Server?
Esistono diversi modi per ottenere questo risultato: 
- **[Sincronizzazione dati](sql-database-sync-data.md)**  : questa funzionalità consente di sincronizzare i dati bidirezionale tra più database di SQL Server locale e il Database SQL. Per eseguire la sincronizzazione con i database di SQL Server locale, è necessario installare e configurare l'agente di sincronizzazione in un computer locale e aprire la porta TCP in uscita 1433.
- **[Replica transazionale](https://azure.microsoft.com/en-us/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)**  : con la replica delle transazioni è possibile sincronizzare i dati locali al database di SQL Azure con locale da server di pubblicazione e il database SQL di Azure da server di sottoscrizione. Per il momento, il programma di installazione è supportata. Per ulteriori informazioni su come eseguire la migrazione dei dati da locale ad Azure SQL con tempi di inattività minimi, vedere: [utilizzare transazioni replica](sql-database-cloud-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Passaggi successivi
Informazioni su [Database SQL](sql-database-technical-overview.md).

