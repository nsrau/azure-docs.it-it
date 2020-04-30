---
title: PlayBook per soddisfare i requisiti di sicurezza comuni | Microsoft Docs
titleSuffix: Azure SQL Database
description: Questo articolo fornisce i requisiti di sicurezza comuni e le procedure consigliate nel database SQL di Azure.
ms.service: sql-database
ms.subservice: security
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 02/20/2020
ms.reviewer: ''
ms.openlocfilehash: 07e8d8690cb07cfde3e0def902bf6d8e7ba5b788
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232360"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database"></a>PlayBook per soddisfare i requisiti di sicurezza comuni con il database SQL di Azure

> [!NOTE]
> Questo documento illustra le procedure consigliate per risolvere i requisiti di sicurezza comuni. Non tutti i requisiti sono applicabili a tutti gli ambienti ed è necessario consultare il database e il team di sicurezza in cui verranno implementate le funzionalità.

## <a name="solving-common-security-requirements"></a>Risoluzione dei requisiti di sicurezza comuni

Questo documento fornisce indicazioni su come risolvere i requisiti di sicurezza comuni per le applicazioni nuove o esistenti usando il database SQL di Azure. È organizzato in base alle aree di sicurezza di alto livello. Per risolvere le minacce specifiche, vedere la sezione minacce per la [sicurezza comuni e potenziali mitigazioni](#common-security-threats-and-potential-mitigations) . Sebbene alcune delle raccomandazioni presentate siano applicabili durante la migrazione di applicazioni da locale ad Azure, gli scenari di migrazione non sono l'obiettivo di questo documento.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>Offerte di distribuzione del database SQL di Azure descritte in questa guida

- [Database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-single-index): [database singoli](sql-database-single-database.md) e [pool elastici](sql-database-elastic-pool.md) nei [server di database SQL di Azure](sql-database-servers.md)
- [Istanze gestite](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="sql-deployment-offers-not-covered-in-this-guide"></a>Le offerte per la distribuzione di SQL non sono descritte in questa guida

- Azure SQL Data Warehouse
- Macchine virtuali SQL di Azure (IaaS)
- SQL Server in locale

### <a name="audience"></a>Destinatari

I destinatari di questa guida sono i clienti che rivolti a domande su come proteggere il database SQL di Azure. I ruoli interessati a questo articolo di procedure consigliate includono, ma non limitati:

- Architetti della sicurezza
- Responsabili della sicurezza
- Responsabili della conformità
- Responsabili della privacy
- Ingegneri della sicurezza

### <a name="using-this-guide"></a><a id="using"></a>Utilizzo di questa guida

Questo documento è concepito come complemento alla documentazione [sulla sicurezza del database SQL di Azure](sql-database-security-overview.md) esistente.

Se non diversamente specificato, è consigliabile seguire tutte le procedure consigliate elencate in ogni sezione per ottenere il rispettivo obiettivo o requisito. Per soddisfare gli standard di conformità o le procedure consigliate di sicurezza specifici, i controlli di conformità normativi importanti sono elencati nella sezione requisiti o obiettivi, laddove applicabile. Questi sono gli standard di sicurezza e le normative a cui viene fatto riferimento in questo documento:

- [FedRAMP](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): cm-3, SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): controllo di accesso, crittografia
- [Procedure di Microsoft Operational Security Assurance (OSA)](https://www.microsoft.com/en-us/securityengineering/osa/practices): esercitazione #1-6 e #9
- [Pubblicazione speciale NIST 800-53 controlli di sicurezza](https://nvd.nist.gov/800-53): AC-5, AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library): 6.3.2, 6.4.2

### <a name="feedback"></a>Commenti e suggerimenti

Si prevede di continuare a aggiornare le raccomandazioni e le procedure consigliate elencate di seguito. Fornire l'input o qualsiasi correzione per questo documento usando il collegamento **feedback** nella parte inferiore di questo articolo.

## <a name="authentication"></a>Autenticazione

L'autenticazione è il processo atto a dimostrare che l'utente sia effettivamente chi dichiara di essere. Il database SQL di Azure supporta due tipi di autenticazione:

- Autenticazione SQL
- Autenticazione di Azure Active Directory

> [!NOTE]
> Azure Active Directory autenticazione potrebbe non essere supportata per tutti gli strumenti e le applicazioni di terze parti.

### <a name="central-management-for-identities"></a>Gestione centralizzata per le identità

Gestione delle identità centrale offre i vantaggi seguenti:

- Gestire gli account di gruppo e controllare le autorizzazioni utente senza duplicare gli account di accesso tra i server e i database SQL di Azure.
- Gestione semplificata e flessibile delle autorizzazioni.
- Gestione delle applicazioni su larga scala.

**Come implementare**:

- Usare l'autenticazione Azure Active Directory (Azure AD) per la gestione centralizzata delle identità.

**Procedure consigliate**:

- Creare un tenant di Azure AD e [creare utenti](../active-directory/fundamentals/add-users-azure-active-directory.md) per rappresentare gli utenti umani e creare [entità servizio](../active-directory/develop/app-objects-and-service-principals.md) per rappresentare app, servizi e strumenti di automazione. Le entità servizio sono equivalenti agli account del servizio in Windows e Linux. 

- Assegnare i diritti di accesso alle risorse Azure AD entità tramite assegnazione gruppo: creare gruppi di Azure AD, concedere l'accesso ai gruppi e aggiungere singoli membri ai gruppi. Nel database creare utenti del database indipendente che mappano i gruppi di Azure AD. Per assegnare autorizzazioni all'interno del database, inserire gli utenti associati ai gruppi di Azure AD nei ruoli del database con le autorizzazioni appropriate.
  - Vedere gli articoli, [configurare e gestire l'autenticazione Azure Active Directory con SQL](sql-database-aad-authentication-configure.md) e [usare Azure ad per l'autenticazione con SQL](sql-database-aad-authentication.md).
  > [!NOTE]
  > In un'istanza gestita è inoltre possibile creare account di accesso che esegue il mapping a Azure AD entità del database master. Vedere [Create Login (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

- L'uso di gruppi di Azure AD semplifica la gestione delle autorizzazioni e il proprietario del gruppo e il proprietario della risorsa può aggiungere/rimuovere membri da e verso il gruppo. 

- Creare un gruppo separato per gli amministratori Azure AD per ogni server di database SQL.

  - Vedere l'articolo eseguire il [provisioning di un amministratore Azure Active Directory per il server di database SQL di Azure](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

- Monitorare le modifiche dell'appartenenza a un gruppo Azure AD utilizzando Azure AD i report delle attività di controllo. 

- Per un'istanza gestita, è necessario un passaggio separato per creare Azure AD amministratore. 
  - Vedere l'articolo eseguire il [provisioning di un amministratore Azure Active Directory per l'istanza gestita](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). 

> [!NOTE]
> - Azure AD autenticazione viene registrata nei log di controllo SQL di Azure, ma non nei log di accesso Azure AD.
> - Le autorizzazioni RBAC concesse in Azure non si applicano alle autorizzazioni del database SQL di Azure. È necessario creare/mappare tali autorizzazioni manualmente nel database SQL utilizzando le autorizzazioni SQL esistenti.
> - Sul lato client, l'autenticazione Azure AD deve accedere a Internet o tramite route definita dall'utente (UDR) a una VNet.
> - Il token di accesso Azure AD viene memorizzato nella cache sul lato client e la relativa durata dipende dalla configurazione del token. Vedere l'articolo relativo alla [durata dei token configurabile in Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md)
> - Per indicazioni sulla risoluzione dei problemi di Autenticazione di Azure AD, vedere il blog seguente:<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA).

> Menzione: #2 di esercitazione OSA, controllo di accesso ISO (CA)

Azure Multi-Factor Authentication (multi-factor authentication) consente di ottenere una maggiore sicurezza richiedendo più di una forma di autenticazione.

**Come implementare**:

- [Abilitare](../active-directory/authentication/concept-mfa-howitworks.md) l'autenticazione a più fattori in Azure ad usando l'accesso condizionale e usare l'autenticazione interattiva. 

- In alternativa, è possibile abilitare l'autenticazione a più fattori per l'intero dominio Azure AD o AD.

**Procedure consigliate**:

- Attivare l'accesso condizionale in Azure AD (richiede una sottoscrizione Premium). 
  - Vedere l'articolo relativo all' [accesso condizionale in Azure ad](../active-directory/conditional-access/overview.md).  

- Creare gruppi di Azure AD e abilitare i criteri di autenticazione a più fattori per i gruppi selezionati usando Azure AD l'accesso condizionale. 
  - Vedere l'articolo [pianificare la distribuzione dell'accesso condizionale](../active-directory/conditional-access/plan-conditional-access.md). 

- L'autenticazione a più fattori può essere abilitata per l'intera Azure AD o per l'intero Active Directory federato con Azure AD. 

- Usare Azure AD modalità di autenticazione interattiva per il database SQL dove viene richiesta una password in modo interattivo, seguita dall'autenticazione a più fattori:      
  - Usare l'autenticazione universale in SSMS. Vedere l'articolo [uso dell'autenticazione di AAD a più fattori con il database SQL di Azure e Azure SQL data warehouse (supporto di SSMS per l'autenticazione a più fattori)](sql-database-ssms-mfa-authentication.md).
  - Usare l'autenticazione interattiva supportata in SQL Server Data Tools (SSDT). Vedere l'articolo [supporto Azure Active Directory in SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current).
  - Usare altri strumenti SQL che supportano l'autenticazione a più fattori. 
    - Supporto della procedura guidata di SSMS per il database di esportazione/estrazione/distribuzione  
    - [SqlPackage. exe](https://docs.microsoft.com/sql/tools/sqlpackage): opzione '/ua ' 
    - [utilità sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility): opzione-G (interattiva)
    - [utilità bcp](https://docs.microsoft.com/sql/tools/bcp-utility): opzione-G (interattiva) 

- Implementare le applicazioni per connettersi al database SQL di Azure usando l'autenticazione interattiva con il supporto dell'autenticazione a più fattori. 
  - Vedere l'articolo [connettersi al database SQL di Azure con multi-factor authentication di Azure](active-directory-interactive-connect-azure-sql-db.md). 
  > [!NOTE]
  > Questa modalità di autenticazione richiede identità basate sull'utente. Nei casi in cui viene usato un modello di identità attendibile che ignora le singole Azure AD autenticazione utente, ad esempio usando l'identità gestita per le risorse di Azure, l'autenticazione a più fattori non è applicabile.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>Ridurre al minimo l'uso dell'autenticazione basata su password per gli utenti

> Menzione: #4 di esercitazione OSA, controllo di accesso ISO (CA)

I metodi di autenticazione basati su password sono una forma di autenticazione più debole. Le credenziali possono essere compromesse o abbandonate erroneamente.

**Come implementare**:

- Utilizzare un Azure AD autenticazione integrata che elimini l'utilizzo delle password.

**Procedure consigliate**:

- Usare l'autenticazione Single Sign-On usando le credenziali di Windows. Federare il dominio AD locale con Azure AD e usare l'autenticazione integrata di Windows (per i computer aggiunti a un dominio con Azure AD).
  - Vedere l'articolo [supporto di SSMS per Azure ad l'autenticazione integrata](sql-database-aad-authentication-configure.md#active-directory-integrated-authentication).

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>Ridurre al minimo l'uso dell'autenticazione basata su password per le applicazioni 

> Menzione: #4 di esercitazione OSA, controllo di accesso ISO (CA)

**Come implementare**:

- Abilitare l'identità gestita di Azure. È anche possibile usare l'autenticazione integrata o basata sui certificati. 

**Procedure consigliate**:

- Usare [le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).
  - [Identità gestita assegnata dal sistema](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) 
  - [Identità gestita assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [Usare il database SQL di Azure dal servizio app con identità gestita (senza modifiche al codice)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- Usare l'autenticazione basata su certificato per un'applicazione. 
  - Vedere questo [esempio di codice](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token). 

- Usare Azure AD autenticazione per il dominio federato integrato e il computer aggiunto al dominio (vedere la sezione precedente).
  - Vedere l' [applicazione di esempio per l'autenticazione integrata](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated).

### <a name="protect-passwords-and-secrets"></a>Proteggi le password e i segreti

Per i casi in cui le password non possono essere evitate, assicurarsi che siano protette.

**Come implementare**:

- Usare Azure Key Vault per archiviare le password e i segreti. Quando applicabile, usare l'autenticazione a più fattori per il database SQL di Azure con Azure AD utenti.

**Procedure consigliate**:

- Se non è possibile evitare password o segreti, archiviare le password degli utenti e i segreti dell'applicazione in Azure Key Vault e gestire l'accesso tramite Key Vault criteri di accesso. 

- Vari Framework di sviluppo di app possono anche offrire meccanismi specifici del Framework per la protezione dei segreti nell'app. Ad esempio: [ASP.NET Core app](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows).

### <a name="use-sql-authentication-for-legacy-applications"></a>Usare l'autenticazione SQL per le applicazioni legacy 

L'autenticazione SQL fa riferimento all'autenticazione di un utente durante la connessione al database SQL di Azure tramite nome utente e password. Sarà necessario creare un account di accesso in ogni server di database SQL o in un'istanza gestita e un utente creato in ogni database.

**Come implementare**:

- Usare l'autenticazione SQL.

**Procedure consigliate**:

- Come amministratore del server, creare account di accesso e utenti. A meno che non vengano utilizzati utenti del database indipendente con password, tutte le password vengono archiviate nel database master.
  - Vedere l'articolo [controllo e concessione dell'accesso al database SQL e SQL data warehouse](sql-database-manage-logins.md).

## <a name="access-management"></a>Gestione degli accessi

La gestione degli accessi (detta anche autorizzazione) è il processo di controllo e gestione dei privilegi e dell'accesso degli utenti autorizzati al database SQL di Azure.

### <a name="implement-principle-of-least-privilege"></a>Implementare il principio dei privilegi minimi

> Menzione in: FedRamp Controls AC-06, NIST: AC-6, OSA Practice #3

Il principio dei privilegi minimi indica che gli utenti non devono avere più privilegi del necessario per completare le attività. Per ulteriori informazioni, vedere l'articolo relativo all' [amministrazione sufficiente](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview).

**Come implementare**:

Assegnare solo le [autorizzazioni](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) necessarie per completare le attività richieste:

- Nei database SQL: 
    - Usare autorizzazioni granulari e ruoli del database definiti dall'utente (o ruoli del server in MI): 
        1. Creare i ruoli necessari
            - [CREATE ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)
            - [CREATE SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-server-role-transact-sql)
        1. Creare gli utenti necessari
            - [CREA UTENTE](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
        1. Aggiungere utenti come membri ai ruoli 
            - [ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)
            - [ALTER SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)
        1. Assegnare quindi le autorizzazioni ai ruoli. 
            - [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 
    - Assicurarsi di non assegnare utenti a ruoli non necessari.

- In Azure Resource Manager:
  - Usare i ruoli predefiniti se disponibili o i ruoli RBAC personalizzati e assegnare le autorizzazioni necessarie.
    - [Ruoli predefiniti per Azure](../role-based-access-control/built-in-roles.md) 
    - [Ruoli personalizzati per le risorse di Azure](../role-based-access-control/custom-roles.md)

**Procedure consigliate**:

Le procedure consigliate seguenti sono facoltative, ma comporteranno una migliore gestibilità e supporto della strategia di sicurezza: 

- Se possibile, iniziare con il set di autorizzazioni meno possibile e iniziare ad aggiungere le autorizzazioni una alla volta se esiste una reale necessità (e giustificazione), anziché l'approccio opposto: prendendo le autorizzazioni in modo graduale. 

- Evitare di assegnare autorizzazioni a singoli utenti. Usare i ruoli (ruoli del database o del server) in modo coerente. I ruoli contribuiscono significativamente alla creazione di report e alla risoluzione dei problemi relativi alle autorizzazioni. Azure RBAC supporta solo l'assegnazione delle autorizzazioni tramite ruoli. 

- Creare e utilizzare ruoli personalizzati con le autorizzazioni esatte necessarie. Ruoli tipici usati in pratica: 
  - Distribuzione della sicurezza 
  - Amministratore 
  - Sviluppatore 
  - Personale di supporto 
  - Revisore 
  - Processi automatizzati 
  - End user (Utente finale) 
  
- Usare i ruoli predefiniti solo quando le autorizzazioni dei ruoli corrispondono esattamente alle autorizzazioni necessarie per l'utente. È possibile assegnare utenti a più ruoli. 

- Tenere presente che le autorizzazioni in SQL Server motore di database possono essere applicate agli ambiti seguenti. Minore è l'ambito, minore è l'effetto delle autorizzazioni concesse: 
  - Server di database SQL di Azure (ruoli speciali nel database master) 
  - Database 
  - SCHEMA
      - È consigliabile utilizzare gli schemi per concedere autorizzazioni all'interno di un database. (vedere anche: [progettazione schema per SQL Server: suggerimenti per la progettazione dello schema tenendo presente la sicurezza](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/))
  - Oggetto (tabella, vista, procedura e così via) 
  > [!NOTE]
  > Non è consigliabile applicare le autorizzazioni a livello di oggetto perché questo livello aggiunge complessità superflua all'implementazione complessiva. Se si decide di utilizzare le autorizzazioni a livello di oggetto, queste devono essere chiaramente documentate. Lo stesso vale per le autorizzazioni a livello di colonna, che sono ancora meno raccomandabili per gli stessi motivi. Tenere inoltre presente che, per impostazione predefinita, un'istruzione [Deny](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql) a livello di tabella non esegue l'override di una concessione a livello di colonna. Questa operazione richiede l'attivazione della [configurazione del server common criteria compliance](https://docs.microsoft.com/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option) .

- Eseguire verifiche regolari usando la [valutazione della vulnerabilità (va)](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) per verificare la presenza di un numero eccessivo di autorizzazioni.

### <a name="implement-separation-of-duties"></a>Implementare la separazione dei compiti

> Indicato in: FedRamp: AC-04, NIST: AC-5, ISO: A. 6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

La separazione dei compiti, detta anche separazione dei compiti, descrive la necessità di suddividere le attività riservate in più attività assegnate a utenti diversi. La separazione dei compiti contribuisce a impedire violazioni dei dati.

**Come implementare**:

- Identificare il livello necessario di separazione dei compiti. Esempi: 
  - Tra ambienti di sviluppo/test e produzione 
  - Attività sensibili per la sicurezza e attività del livello di gestione dell'amministratore del database (DBA) rispetto alle attività di sviluppo. 
    - Esempi: Auditor, creazione di criteri di sicurezza per la sicurezza a livello di ruolo (RLS), implementazione di oggetti di database SQL con autorizzazioni DDL.

- Identificare una gerarchia completa di utenti (e processi automatizzati) che accedono al sistema.

- Creare ruoli in base ai gruppi di utenti necessari e assegnare autorizzazioni ai ruoli. 
  - Per le attività a livello di gestione in portale di Azure o tramite l'automazione di PowerShell usare i ruoli RBAC. Trovare un ruolo predefinito corrispondente al requisito oppure creare un ruolo personalizzato RBAC usando le autorizzazioni disponibili 
  - Creare ruoli del server per attività a livello di server (creazione di nuovi account di accesso, database) in un'istanza gestita. 
  - Creare ruoli del database per le attività a livello di database.

- Per alcune attività riservate, provare a creare stored procedure speciali firmate da un certificato per eseguire le attività per conto degli utenti. Un importante vantaggio delle stored procedure con firma digitale è che se la procedura viene modificata, le autorizzazioni concesse alla versione precedente della procedura vengono immediatamente rimosse.
  - Esempio: [esercitazione: firma di stored procedure con un certificato](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate) 

- Implementare Transparent Data Encryption (Transparent Data Encryption) con chiavi gestite dal cliente in Azure Key Vault per consentire la separazione dei compiti tra il proprietario dei dati e il proprietario della sicurezza. 
  - Vedere l'articolo [configurare le chiavi gestite dal cliente per la crittografia di archiviazione di Azure dalla portale di Azure](../storage/common/storage-encryption-keys-portal.md). 

- Per assicurarsi che un amministratore di database non possa visualizzare i dati considerati sensibili ed è comunque in grado di eseguire attività DBA, è possibile usare Always Encrypted con la separazione dei ruoli. 
  - Vedere gli articoli [Panoramica della gestione delle chiavi per la always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted), il [provisioning](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)delle chiavi con la separazione dei ruoli e la rotazione delle [chiavi master della colonna con la separazione dei ruoli](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation). 

- Nei casi in cui l'uso di Always Encrypted non è fattibile o almeno non senza costi e sforzi maggiori che potrebbero persino rendere il sistema quasi inutilizzabile, è possibile rendere possibili e mitigare i compromessi tramite l'uso di controlli di compensazione, ad esempio: 
  - Intervento umano nei processi. 
  - Audit Trails: per ulteriori informazioni sul controllo, vedere [Audit Critical Security Events](#audit-critical-security-events).

**Procedure consigliate**:

- Assicurarsi che vengano usati account diversi per gli ambienti di sviluppo, test e produzione. Account diversi consentono di rispettare la separazione dei sistemi di produzione di test &.

- Evitare di assegnare autorizzazioni a singoli utenti. Usare i ruoli (ruoli del database o del server) in modo coerente. La presenza di ruoli contribuisce significativamente alla creazione di report e alla risoluzione dei problemi di autorizzazioni.

- Usare i ruoli predefiniti quando le autorizzazioni corrispondono esattamente alle autorizzazioni necessarie: se l'Unione di tutte le autorizzazioni di più ruoli predefiniti comporta una corrispondenza del 100%, è possibile assegnare più ruoli contemporaneamente. 

- Creare e utilizzare i ruoli definiti dall'utente quando i ruoli predefiniti concedono un numero eccessivo di autorizzazioni o autorizzazioni insufficienti. 

- Le assegnazioni di ruolo possono anche essere eseguite temporaneamente, note anche come separazione dinamica dei compiti (DSD), nei passaggi del processo di SQL Agent in T-SQL o usando Azure PIM per i ruoli RBAC. 

- Verificare che DBA non dispongano dell'accesso alle chiavi di crittografia o agli archivi di chiavi e che gli amministratori della sicurezza con accesso alle chiavi non dispongano dell'accesso al database a sua volta. L'uso di [Extensible Key Management (EKM)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-ekm) può rendere la separazione più semplice da raggiungere. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) può essere utilizzato per implementare EKM. 

- Assicurarsi sempre di avere un audit trail per le azioni correlate alla sicurezza. 

- È possibile recuperare la definizione dei ruoli controllo degli accessi in base al ruolo predefiniti per visualizzare le autorizzazioni usate e creare un ruolo personalizzato basato su estratti e cumulativi di questi ruoli tramite PowerShell.

- Poiché qualsiasi membro del ruolo del database db_owner può modificare le impostazioni di sicurezza come Transparent Data Encryption (Transparent Data Encryption) o modificare il SLO, questa appartenenza deve essere concessa con cautela. Tuttavia, esistono molte attività che richiedono privilegi di db_owner. Attività come la modifica delle impostazioni del database, ad esempio la modifica delle opzioni di database. Il controllo svolge un ruolo chiave in qualsiasi soluzione.

- Non è possibile limitare le autorizzazioni di un db_owner e quindi impedire a un account amministrativo di visualizzare i dati utente. Se in un database sono presenti dati estremamente sensibili, è possibile usare Always Encrypted per impedire che db_owners o qualsiasi altro amministratore di database lo visualizzi.

> [!NOTE]
> Il raggiungimento della separazione dei compiti (SoD) è difficile per le attività relative alla sicurezza o alla risoluzione dei problemi. Altre aree come lo sviluppo e i ruoli degli utenti finali sono più facili da separare. La maggior parte dei controlli correlati alla conformità consente l'uso di funzioni di controllo alternative, ad esempio il controllo quando altre soluzioni non sono pratiche.

Per i lettori che desiderano approfondire il proprio SoD, è consigliabile usare le risorse seguenti: 

- Per il database SQL di Azure:  
  - [Controllo e concessione dell'accesso al database SQL e a SQL Data Warehouse](sql-database-manage-logins.md)
  - [Separazione del motore dei compiti per lo sviluppatore di applicazioni](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100)) 
  - [Separazione dei compiti in SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=39269)
  - [Firma di stored procedure in SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- Per gestione risorse di Azure:
  - [Ruoli predefiniti per Azure](../role-based-access-control/built-in-roles.md) 
  - [Ruoli personalizzati per le risorse di Azure](../role-based-access-control/custom-roles.md)
  - [Uso di Azure AD Privileged Identity Management per l'accesso con privilegi elevati](https://www.microsoft.com/en-us/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>Eseguire revisioni del codice regolari

> Indicato in: PCI: 6.3.2, SOC: SDL-3 

La separazione dei compiti non è limitata ai dati nel database, ma include il codice dell'applicazione. Il codice dannoso può potenzialmente aggirare i controlli di sicurezza. Prima di distribuire codice personalizzato nell'ambiente di produzione, è fondamentale esaminare gli elementi distribuiti.

**Come implementare**:

- Usare uno strumento di database come Azure Data Studio che supporta il controllo del codice sorgente. 

- Implementare un processo di distribuzione del codice separato.

- Prima di eseguire il commit nel branch principale, una persona (a parte l'autore del codice stesso) deve esaminare il codice per individuare potenziali rischi di elevazione dei privilegi, nonché apportare modifiche ai dati dannosi per la protezione da frodi e accessi non autorizzati. Questa operazione può essere eseguita utilizzando meccanismi di controllo del codice sorgente.

**Procedure consigliate**:

- Standardizzazione: consente di implementare una procedura standard da seguire per gli eventuali aggiornamenti del codice. 

- La valutazione della vulnerabilità contiene regole che verificano la presenza di autorizzazioni eccessive, l'utilizzo di algoritmi di crittografia obsoleti e altri problemi di sicurezza all'interno di uno schema del database. 

- Ulteriori controlli possono essere eseguiti in un ambiente di controllo di qualità o di test usando Advanced Threat Protection che esegue l'analisi del codice vulnerabile a SQL injection.

- Esempi di elementi da cercare: 
  - Creazione di un utente o modifica delle impostazioni di sicurezza all'interno di una distribuzione automatizzata di SQL-Code-Update. 
  - Una stored procedure, che, a seconda dei parametri forniti, aggiorna un valore monetario in una cella in modo non conforme. 

- Assicurarsi che la persona che esegue la revisione sia un utente diverso dall'autore del codice di origine e che abbia una conoscenza approfondita delle revisioni del codice e della codifica sicura.

- Assicurarsi di essere a conoscenza di tutte le origini di modifiche del codice. Il codice può essere in script T-SQL. Può essere l'esecuzione o la distribuzione di comandi ad hoc in forme di viste, funzioni, trigger e stored procedure. Può far parte delle definizioni dei processi di SQL Agent (passaggi). Può anche essere eseguita dall'interno di pacchetti SSIS, Azure Data Factory e altri servizi.

## <a name="data-protection"></a>Protezione dati

La protezione dei dati è un set di funzionalità per salvaguardare le informazioni importanti dalla compromissione mediante crittografia o offuscamento.

> [!NOTE]
> Microsoft dichiara il database SQL di Azure come conforme allo standard FIPS 140-2 livello 1. Questa operazione viene eseguita dopo la verifica del rigoroso utilizzo di algoritmi FIPS 140-2 di livello 1 accettabili e di istanze convalidate FIPS 140-2 Level 1 di tali algoritmi, inclusa la coerenza con le lunghezze di chiave obbligatorie, la gestione delle chiavi, la generazione di chiavi e l'archiviazione delle chiavi. Questa attestazione ha lo scopo di consentire ai clienti di rispondere alle necessità o ai requisiti per l'utilizzo di istanze convalidate FIPS 140-2 di livello 1 nell'elaborazione dei dati o del recapito di sistemi o applicazioni. I termini "FIPS 140-2 Level 1 compliant" e "FIPS 140-2 Level 1 Compliance" vengono definiti nell'istruzione precedente per illustrare l'applicabilità prevista per gli Stati Uniti e il governo canadese che usano il diverso termine "FIPS 140-2 Level 1 convalidato". 


### <a name="encrypt-data-in-transit"></a>Crittografare i dati in transito

> Menzione: procedura OSA #6, famiglia di controlli ISO: Cryptography

Protegge i dati quando i dati vengono spostati tra il client e il server. Vedere [sicurezza di rete](#network-security).

### <a name="encrypt-data-at-rest"></a>Crittografare i dati inattivi

> Menzione: procedura OSA #6, famiglia di controlli ISO: Cryptography

La crittografia dei dati inattivi è la protezione crittografica dei dati quando viene resa permanente nei file di database, di log e di backup.

**Come implementare**:

- [Transparent Database Encryption (Transparent Database Encryption)](transparent-data-encryption-azure-sql.md) con chiavi gestite dal servizio sono abilitate per impostazione predefinita per qualsiasi database creato dopo 2017 nel database SQL di Azure.
- In un'istanza gestita, se il database viene creato da un'operazione di ripristino utilizzando un server locale, viene rispettata l'impostazione di Transparent Data Encryption del database originale. Se per il database originale non è abilitata la funzionalità Transparent Data Encryption, è consigliabile attivarla manualmente per l'istanza gestita.

**Procedure consigliate**:

- Non archiviare i dati che richiedono la crittografia dei dati inattivi nel database master. Il database master non può essere crittografato con Transparent Data Encryption.

- Usare le chiavi gestite dal cliente in Azure Key Vault se è necessaria una maggiore trasparenza e un controllo granulare sulla protezione di Transparent Data Encryption. Azure Key Vault consente di revocare le autorizzazioni in qualsiasi momento per rendere inaccessibile il database. È possibile gestire centralmente le protezioni Transparent Data Encryption insieme ad altre chiavi oppure ruotare la protezione Transparent Data Encryption con una pianificazione personalizzata usando Azure Key Vault.

- Se si usano chiavi gestite dal cliente in Azure Key Vault, seguire gli articoli, [linee guida per la configurazione](transparent-data-encryption-byok-azure-sql.md#recommendations-when-configuring-akv) di Transparent Data encryption con Azure Key Vault e [come configurare il ripristino di emergenza geografico con Azure Key Vault](transparent-data-encryption-byok-azure-sql.md#geo-dr-and-customer-managed-tde).

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>Proteggere i dati sensibili in uso da utenti con privilegi elevati e non autorizzati

I dati in uso sono i dati archiviati in memoria del sistema del database durante l'esecuzione di query SQL. Se il database archivia dati sensibili, è possibile che l'organizzazione sia necessaria per garantire che agli utenti con privilegi elevati venga impedito di visualizzare dati sensibili nel database. Gli utenti con privilegi elevati, ad esempio gli operatori Microsoft o DBA nell'organizzazione, devono essere in grado di gestire il database, ma non hanno potuto visualizzare e potenzialmente divulgare dati sensibili dalla memoria del processo di SQL Server o eseguendo una query sul database.

I criteri che determinano quali dati sono sensibili e se i dati sensibili devono essere crittografati in memoria e non accessibili agli amministratori in testo non crittografato, sono specifici dell'organizzazione e delle normative di conformità a cui è necessario attenersi. Vedere il requisito correlato: [identificare e contrassegnare i dati sensibili](#identify-and-tag-sensitive-data).

**Come implementare**:

- Usare [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) per assicurarsi che i dati sensibili non siano esposti in testo non crittografato nel database SQL di Azure, anche in memoria/in uso. Always Encrypted protegge i dati dagli amministratori del database (DBA) e dagli amministratori del cloud (o da attori malintenzionati che possono rappresentare utenti con privilegi elevati ma non autorizzati) e offre maggiore controllo su chi può accedere ai dati.

**Procedure consigliate**:

- Always Encrypted non sostituisce la crittografia dei dati inattivi (Transparent Data Encryption) o in transito (SSL/TLS). Always Encrypted non deve essere usato per i dati non sensibili per ridurre al minimo le prestazioni e l'effetto sulle funzionalità. È consigliabile usare Always Encrypted insieme a Transparent Data Encryption e Transport Layer Security (TLS) per una protezione completa dei dati inattivi, in transito e in uso. 

- Valutare l'effetto della crittografia delle colonne di dati sensibili identificate prima di distribuire Always Encrypted in un database di produzione. In generale, Always Encrypted riduce la funzionalità delle query sulle colonne crittografate e presenta altre limitazioni, elencate nei [Dettagli della funzionalità Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details). Potrebbe pertanto essere necessario riprogettare l'applicazione per implementare nuovamente la funzionalità, una query non supporta sul lato client o/ed effettuare il refactoring dello schema del database, incluse le definizioni di stored procedure, funzioni, viste e trigger. Le applicazioni esistenti potrebbero non funzionare con le colonne crittografate se non rispettano le restrizioni e le limitazioni del Always Encrypted. Sebbene l'ecosistema degli strumenti, dei prodotti e dei servizi Microsoft che supportano Always Encrypted stia crescendo, molti di essi non funzionano con le colonne crittografate. La crittografia di una colonna può influito anche sulle prestazioni delle query, a seconda delle caratteristiche del carico di lavoro. 

- Gestire Always Encrypted chiavi con la separazione dei ruoli se si usa Always Encrypted per proteggere i dati da DBA dannosi. Con la separazione dei ruoli, un amministratore della sicurezza crea le chiavi fisiche. Il DBA crea gli oggetti di metadati della chiave master della colonna e della chiave di crittografia della colonna, che descrivono le chiavi fisiche, nel database. Durante questo processo, l'amministratore della sicurezza non necessita dell'accesso al database e l'amministratore di database non necessita dell'accesso alle chiavi fisiche in testo non crittografato. 
  - Per informazioni dettagliate, vedere l'articolo [gestione delle chiavi con separazione dei ruoli](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) . 

- Archiviare le chiavi master della colonna in Azure Key Vault per facilitarne la gestione. Evitare di usare l'archivio certificati di Windows (e, in generale, le soluzioni di archivio chiavi distribuite, anziché le soluzioni di gestione delle chiavi centrali) che rendono difficile la gestione delle chiavi. 

- Valutare con attenzione i compromessi dell'uso di più chiavi (chiave master della colonna o chiavi di crittografia della colonna). Mantieni il numero di chiavi ridotte per ridurre i costi di gestione delle chiavi. Una chiave master della colonna e una chiave di crittografia della colonna per ogni database sono in genere sufficienti negli ambienti a stato stabile (non nel mezzo di una rotazione della chiave). Potrebbero essere necessarie chiavi aggiuntive se sono presenti gruppi di utenti diversi, ognuno dei quali USA chiavi diverse e accede a dati diversi.  

- Ruotare le chiavi master della colonna in base ai requisiti di conformità. Se è necessario anche ruotare le chiavi di crittografia della colonna, provare a usare la crittografia online per ridurre al minimo i tempi di inattività dell'applicazione. 
  - Vedere l'articolo [considerazioni sulle prestazioni e sulla disponibilità](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations). 

- Utilizzare la crittografia deterministica se è necessario supportare calcoli (uguaglianza) sui dati. In caso contrario, usare la crittografia casuale. Evitare di usare la crittografia deterministica per i set di dati a bassa entropia o i set di dati con distribuzione nota pubblica. 

- Se si è preoccupati per le terze parti che accedono ai dati legalmente senza il consenso dell'utente, assicurarsi che tutte le applicazioni e gli strumenti che hanno accesso alle chiavi e ai dati in testo non crittografato siano eseguiti al di fuori di Microsoft Azure cloud. Senza l'accesso alle chiavi, la terza parte non avrà alcun modo per decrittografare i dati, a meno che non vengano ignorati dalla crittografia.

- Always Encrypted non supporta facilmente la concessione dell'accesso temporaneo alle chiavi e ai dati protetti. Se, ad esempio, è necessario condividere le chiavi con un amministratore di database per consentire all'amministratore di database di eseguire alcune operazioni di pulizia sui dati sensibili e crittografati. L'unico modo per garantire l'affidabilità, revocare l'accesso ai dati dell'amministratore di database, consiste nel ruotare sia le chiavi di crittografia della colonna sia le chiavi master della colonna che proteggono i dati, che è un'operazione costosa. 

- Per accedere ai valori di testo non crittografato nelle colonne crittografate, è necessario che l'utente abbia accesso alla chiave master della colonna (CMK) che protegge le colonne, che è configurata nell'archivio chiavi che contiene CMK. L'utente deve inoltre disporre delle autorizzazioni di database **View any Column Master Key** Definition e **View any Column Encryption Key Definition** .

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>Controllare l'accesso degli utenti dell'applicazione ai dati sensibili tramite la crittografia

La crittografia può essere usata come metodo per garantire che solo gli utenti specifici dell'applicazione che hanno accesso alle chiavi crittografiche possano visualizzare o aggiornare i dati.

**Come implementare**:

- Usare la crittografia a livello di cella (CLE). Per informazioni dettagliate, vedere l'articolo [crittografare una colonna di dati](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) . 
- Utilizzare Always Encrypted, ma tenere presente la relativa limitazione. Di seguito sono elencate le limitazioni.

**Procedure consigliate**

Quando si usa CLE:

- Controllare l'accesso alle chiavi tramite autorizzazioni e ruoli SQL. 

- Usare AES (AES 256 consigliato) per la crittografia dei dati. Gli algoritmi, ad esempio RC4, DES e TripleDES, sono deprecati e non devono essere usati a causa di vulnerabilità note. 

- Proteggere le chiavi simmetriche con chiavi/certificati asimmetrici (non password) per evitare di utilizzare 3DES. 

- Prestare attenzione quando si esegue la migrazione di un database usando la crittografia a livello di cella tramite esportazione/importazione (file BACPAC). 
  - Vedere l'articolo [Suggerimenti per l'uso della crittografia a livello di cella nel database SQL di Azure](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/) per informazioni su come evitare la perdita di chiavi durante la migrazione dei dati e per altre procedure consigliate.

Tenere presente che Always Encrypted è progettato principalmente per proteggere i dati sensibili in uso dagli utenti con privilegi elevati del database SQL di Azure (operatori cloud, DBA). vedere [proteggere i dati sensibili in uso da utenti con privilegi elevati e non autorizzati](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users). Quando si utilizza Always Encrypted per proteggere i dati dagli utenti delle applicazioni, tenere presente i seguenti problemi:

- Per impostazione predefinita, tutti i driver client Microsoft che supportano Always Encrypted gestiscono una cache globale (uno per applicazione) delle chiavi di crittografia della colonna. Quando un driver client acquisisce una chiave di crittografia della colonna non crittografata contattando un archivio chiavi contenente una chiave master della colonna, la chiave di crittografia della colonna non crittografata viene memorizzata nella cache. Questo consente di isolare i dati dagli utenti di un'applicazione multiutente complessa. Se l'applicazione rappresenta gli utenti finali durante l'interazione con un archivio chiavi (ad esempio Azure Key Vault), dopo che la query di un utente ha popolato la cache con una chiave di crittografia della colonna, una query successiva che richiede la stessa chiave ma viene attivata da un altro utente utilizzerà la chiave memorizzata nella cache. Il driver non chiamerà l'archivio chiavi e non verificherà se il secondo utente dispone di un'autorizzazione per accedere alla chiave di crittografia della colonna. Di conseguenza, l'utente può visualizzare i dati crittografati anche se l'utente non ha accesso alle chiavi. Per ottenere l'isolamento degli utenti all'interno di un'applicazione multiutente, è possibile disabilitare la memorizzazione nella cache della chiave di crittografia della colonna. La disabilitazione della memorizzazione nella cache provocherà un sovraccarico delle prestazioni aggiuntivo, in quanto il driver dovrà contattare l'archivio chiavi per ogni operazione di crittografia o decrittografia dei dati.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>Proteggi i dati dalla visualizzazione non autorizzata da parte degli utenti dell'applicazione mantenendo il formato dei dati
Un'altra tecnica per impedire agli utenti non autorizzati di visualizzare i dati è di offuscare o mascherare i dati mantenendo i tipi di dati e i formati per garantire che le applicazioni utente possano continuare a gestire e visualizzare i dati.

**Come implementare**:

- Utilizzare [Dynamic Data Masking](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) per offuscare le colonne della tabella.

> [!NOTE]
> Always Encrypted non funziona con Dynamic Data Masking. Non è possibile crittografare e mascherare la stessa colonna, che implica la necessità di assegnare priorità alla protezione dei dati in uso e di mascherare i dati per gli utenti dell'app tramite Dynamic Data Masking.

**Procedure consigliate**:

> [!NOTE]
> Non è possibile usare Dynamic Data Masking per proteggere i dati da utenti con privilegi elevati. I criteri di maschera non si applicano agli utenti con accesso amministrativo, ad esempio db_owner.

- Non consentire agli utenti dell'app di eseguire query ad hoc, in quanto potrebbero aggirare Dynamic Data Masking).  
  - Per informazioni dettagliate, vedere l'articolo [ignorare la maschera usando le tecniche di inferenza o forza bruta](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) .  

- Usare un criterio di controllo di accesso appropriato (tramite autorizzazioni SQL, ruoli, RLS) per limitare le autorizzazioni utente per eseguire aggiornamenti nelle colonne mascherate. La creazione di una maschera in una colonna non impedisce gli aggiornamenti a tale colonna. Utenti che ricevono dati mascherati quando eseguono una query sulla colonna mascherata, possono aggiornare i dati se dispongono di autorizzazioni di scrittura.    

-  Dynamic Data Masking non mantiene le proprietà statistiche dei valori mascherati. Questo può influisca sui risultati della query, ad esempio query contenenti predicati di filtro o join sui dati mascherati.

## <a name="network-security"></a>Sicurezza di rete
La sicurezza di rete si riferisce ai controlli di accesso e alle procedure consigliate per proteggere i dati in transito nel database SQL di Azure.

### <a name="configure-my-client-to-connect-securely-to-azure-sql-database"></a>Configurare il client per la connessione sicura al database SQL di Azure 
Procedure consigliate su come impedire la connessione al database SQL di Azure a computer e applicazioni con vulnerabilità note, ad esempio l'uso di protocolli TLS e pacchetti di crittografia precedenti.

**Come implementare**:

- Assicurarsi che i computer client che si connettono al database SQL di Azure utilizzino [Transport Layer Security (TLS)](sql-database-security-overview.md#transport-layer-security-tls-encryption-in-transit).

**Procedure consigliate**:

- Configurare tutte le app e gli strumenti per connettersi al database SQL con la crittografia abilitata 
  - Encrypt = on, TrustServerCertificate = off (o equivalente con driver non Microsoft). 

- Se l'app usa un driver che non supporta TLS o supporta una versione precedente di TLS, sostituire il driver, se possibile. Se non è possibile, valutare con attenzione i rischi per la sicurezza. 

- Ridurre i vettori di attacco tramite vulnerabilità in SSL 2,0, SSL 3,0, TLS 1,0 e TLS 1,1 disabilitando i vettori nei computer client che si connettono alle [impostazioni del registro di sistema del](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10)database SQL di Azure per Transport Layer Security (TLS). 

- Controllare i pacchetti di crittografia disponibili nel client: pacchetti [di crittografia in TLS/SSL (SSP Schannel)](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel). In particolare, disabilitare 3DES per la [configurazione dell'ordine del pacchetto di crittografia TLS](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order). 

- Per il database SQL di Azure, la crittografia viene applicata sia per i tipi di connessione proxy che per quelli di reindirizzamento. Se si usa un'istanza gestita, usare il tipo di connessione **proxy** (impostazione predefinita), in quanto impone la crittografia dal lato server. Il tipo di connessione di **Reindirizzamento** attualmente non supporta l'imposizione della crittografia ed è disponibile solo per le connessioni IP private. 

- Per altre informazioni, vedere [architettura di connettività SQL di Azure-criteri di connessione](sql-database-connectivity-architecture.md#connection-policy).


### <a name="minimize-attack-surface"></a>Riduzione della superficie di attacco
Ridurre al minimo il numero di funzionalità che possono essere attaccate da un utente malintenzionato. Implementare i controlli di accesso alla rete per il database SQL di Azure.

> Menzione: procedura OSA #5

**Come implementare**:

In un server di database SQL di Azure (che contiene il database Singleton o i pool elastici):
- Impostare Consenti accesso ai servizi di Azure su disattivato.

- Usare gli endpoint del servizio VNet e le regole del firewall VNet.

- Usare il collegamento privato (anteprima).

In un'istanza gestita:
- Seguire le linee guida in [requisiti di rete](sql-database-managed-instance-connectivity-architecture.md#network-requirements). 

**Procedure consigliate**:

- Limitazione dell'accesso al database SQL di Azure tramite la connessione a un endpoint privato (ad esempio, usando un percorso dati privato): 
  - Un'istanza gestita può essere isolata all'interno di una VNet per impedire l'accesso esterno. Le applicazioni e gli strumenti che si trovano nella stessa VNet o con peering nella stessa area possono accedervi direttamente. Le applicazioni e gli strumenti in un'area diversa possono usare la connessione da VNet a VNet o il peering del circuito ExpressRoute per stabilire la connessione. Il cliente deve usare i gruppi di sicurezza di rete (NSG) per limitare l'accesso sulla porta 1433 solo alle risorse che richiedono l'accesso a un'istanza gestita 
  - Per un server di database SQL (che contiene singoli database o pool elastici), usare la funzionalità di [collegamento privato](sql-database-private-endpoint-overview.md) che fornisce un indirizzo IP privato dedicato per il server di database SQL all'interno della VNet. È anche possibile usare gli [endpoint del servizio VNet con le regole del firewall VNet](sql-database-vnet-service-endpoint-rule-overview.md) per limitare l'accesso ai server del database SQL.
  - Gli utenti mobili devono usare connessioni VPN da punto a sito per la connessione tramite il percorso dati.
  - Gli utenti connessi alla rete locale devono usare la connessione VPN da sito a sito o ExpressRoute per la connessione al percorso dati.

- È possibile accedere al database SQL di Azure connettendosi a un endpoint pubblico, ad esempio usando un percorso dati pubblico. È consigliabile prendere in considerazione le procedure consigliate seguenti: 
  - Per un server di database SQL, usare [le regole del firewall IP](sql-database-firewall-configure.md) per limitare l'accesso solo agli indirizzi IP autorizzati.
  - Per un'istanza gestita, usare i gruppi di sicurezza di rete (NSG) per limitare l'accesso sulla porta 3342 solo alle risorse necessarie. Per altre informazioni, vedere [usare un'istanza gestita di database SQL di Azure in modo sicuro con gli endpoint pubblici](sql-database-managed-instance-public-endpoint-securely.md). 

> [!NOTE]
> Un endpoint pubblico dell'istanza gestita non è abilitato per impostazione predefinita e deve essere abilitato in modo esplicito. Se i criteri aziendali non consentono l'uso di endpoint pubblici, usare [criteri di Azure](../governance/policy/overview.md) per impedire l'abilitazione di endpoint pubblici in primo luogo.

- Configurare i componenti di rete di Azure: 
  - Seguire le [procedure consigliate di Azure per la sicurezza di rete](../security/fundamentals/network-best-practices.md).
  - Pianificare la configurazione della rete virtuale (VNet) per le procedure consigliate descritte in domande frequenti sulla [rete virtuale di Azure](../virtual-network/virtual-networks-faq.md) e pianificare. 
  - Segmentare un VNet in più subnet e assegnare le risorse per un ruolo simile alla stessa subnet (ad esempio, le risorse di back-end di Visual Studio front-end).
  - Usare i [gruppi di sicurezza di rete (gruppi)](../virtual-network/security-overview.md) per controllare il traffico tra le subnet all'interno del limite di VNet di Azure.
  - Abilitare [Network Watcher di Azure](../network-watcher/network-watcher-monitoring-overview.md) per la sottoscrizione per monitorare il traffico di rete in ingresso e in uscita.

### <a name="configure-power-bi-for-secure-connections-to-azure-sql-database"></a>Configurare Power BI per le connessioni sicure al database SQL di Azure

**Procedure consigliate**:

- Per Power BI Desktop, usare il percorso dati privato quando possibile. 

- Verificare che Power BI Desktop stia eseguendo la connessione utilizzando TLS 1.2 impostando la chiave del registro di sistema nel computer client in base alle impostazioni del registro di sistema di [Transport Layer Security (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) . 

- Limitare l'accesso ai dati per utenti specifici tramite [la sicurezza a livello di riga (RLS) con Power bi](https://docs.microsoft.com/power-bi/service-admin-rls). 

- Per Power BI servizio, usare il [gateway dati locale](https://docs.microsoft.com/power-bi/service-gateway-onprem), tenendo presenti le [limitazioni e le considerazioni](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway).

### <a name="configure-app-service-for-secure-connections-to-azure-sql-database"></a>Configurare il servizio app per le connessioni sicure al database SQL di Azure

**Procedure consigliate**:

- Per una semplice app Web, per la connessione tramite endpoint pubblico è necessario impostare **Consenti servizi di Azure** su on. 

- [Integrare l'app con una rete virtuale di Azure](../app-service/web-sites-integrate-with-vnet.md) per la connettività del percorso dati privato a un'istanza gestita. Facoltativamente, è anche possibile distribuire un'app Web con gli [ambienti del servizio app (ASE)](../app-service/environment/intro.md). 

- Per l'app Web con ambiente del servizio app o un'app Web integrata VNet che si connette a un database nel server di database SQL, è possibile usare gli [endpoint del servizio VNet e le regole del firewall VNet](sql-database-vnet-service-endpoint-rule-overview.md) per limitare l'accesso da un VNet e una subnet specifici. Impostare quindi **Consenti servizi di Azure** su disattivato. È anche possibile connettere l'ambiente del servizio app a un'istanza gestita tramite un percorso dati privato.  

- Verificare che l'app Web sia configurata in base all'articolo, procedure consigliate [per la protezione di applicazioni Web e per dispositivi mobili PaaS usando app Azure servizio](../security/security-paas-applications-using-app-services.md). 

- Installare [Web Application Firewall (WAF)](../application-gateway/waf-overview.md) per proteggere l'app Web da exploit e vulnerabilità comuni.

### <a name="configure-azure-vm-hosting-for-secure-connections-to-azure-sql-database"></a>Configurare la macchina virtuale di Azure che ospita per le connessioni sicure al database SQL di Azure

**Procedure consigliate**:

- Usare una combinazione di regole Allow e Deny per la gruppi di macchine virtuali di Azure per controllare le aree a cui è possibile accedere dalla macchina virtuale.

- Assicurarsi che la macchina virtuale sia configurata in base all'articolo procedure consigliate [per la sicurezza per i carichi di lavoro IaaS in Azure](../security/azure-security-iaas.md).

- Verificare che tutte le macchine virtuali siano associate a una VNet e una subnet specifiche. 

- Valutare se è necessaria la route predefinita 0.0.0.0/Internet per le linee guida disponibili in [informazioni sul tunneling forzato](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling). 
  - In caso affermativo, ad esempio una subnet front-end, Mantieni la route predefinita.
  - Se no, ad esempio il livello intermedio o la subnet back-end, abilitare il tunneling forzato in modo che nessun traffico venga trasferito su Internet per raggiungere l'ambiente locale (a. k. a cross-premise). 

- Implementare [route predefinite facoltative](../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) se si usa il peering o la connessione a un'istanza locale. 

- Implementare le [route definite dall'utente](../virtual-network/virtual-networks-udr-overview.md#user-defined) se è necessario inviare tutto il traffico nella VNet a un'appliance virtuale di rete per l'ispezione dei pacchetti. 

- Usare gli [endpoint del servizio VNet](sql-database-vnet-service-endpoint-rule-overview.md) per l'accesso sicuro ai servizi PaaS come archiviazione di Azure tramite la rete backbone di Azure. 

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>Proteggi dagli attacchi Distributed Denial of Service (DDoS)
Gli attacchi Distributed Denial of Service (DDoS) sono tentativi da parte di un utente malintenzionato di inviare un'alluvione del traffico di rete al database SQL di Azure con l'obiettivo di sovraccaricare l'infrastruttura di Azure e di rifiutare accessi e carichi di lavoro validi.

> Menzione: procedura OSA #9

**Come implementare**:

La protezione DDoS viene abilitata automaticamente come parte della piattaforma Azure. Include il monitoraggio del traffico always on e la mitigazione in tempo reale degli attacchi a livello di rete sugli endpoint pubblici. 

- Usare la [protezione DDoS di Azure](../virtual-network/ddos-protection-overview.md) per monitorare gli indirizzi IP pubblici associati alle risorse distribuite in reti virtuali.

- Usare [Advanced Threat Protection per il database SQL di Azure](sql-database-threat-detection-overview.md) per rilevare gli attacchi Denial of Service (DOS) contro i database.

**Procedure consigliate**:

- Seguire le procedure descritte in [ridurre la superficie di attacco](#minimize-attack-surface) aiuta a ridurre al minimo le minacce di attacco DDoS. 

- L'avviso relativo alla **forza bruta** di Advanced Threat Protection per le credenziali SQL consente di rilevare attacchi di forza bruta. In alcuni casi, l'avviso può anche distinguere i carichi di lavoro di test di penetrazione. 

- Per macchine virtuali di Azure che ospitano applicazioni che si connettono al database SQL 
  - Seguire le indicazioni per limitare l'accesso tramite endpoint con connessione Internet nel centro sicurezza di Azure. 
  - Usare i set di scalabilità di macchine virtuali per eseguire più istanze dell'applicazione in macchine virtuali di Azure. 
  - Disabilitare RDP e SSH da Internet per evitare attacchi di forza bruta. 

## <a name="monitoring-logging-and-auditing"></a>Monitoraggio, registrazione e controllo  
Questa sezione si riferisce alle funzionalità che consentono di rilevare attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database. Vengono inoltre descritte le procedure consigliate per configurare il controllo del database per tenere traccia degli eventi di database e per acquisirli.

### <a name="protect-databases-against-attacks"></a>Proteggere i database dagli attacchi 
Advanced Threat Protection consente di rilevare e rispondere alle minacce potenziali non appena si verificano, fornendo avvisi di sicurezza sulle attività anomale.

**Come implementare**:

- Utilizzare [Advanced Threat Protection per SQL](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts) per rilevare tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database, tra cui:
  - Attacco SQL injection.
  - Furto/perdita di credenziali.
  - Abuso dei privilegi.
  - Exfiltration di dati.

**Procedure consigliate**:

- Configurare [Advanced Data Security (ADS)](sql-database-advanced-data-security.md#getting-started-with-ads) per il database SQL di Azure per un server di database SQL specifico o un'istanza gestita. È anche possibile configurare annunci per tutti i server di database SQL e le istanze gestite in una sottoscrizione passando al [livello standard del Centro sicurezza di Azure](../security-center/security-center-pricing.md). 

- Per un'esperienza di analisi completa, è consigliabile abilitare il [controllo del database SQL](sql-database-auditing.md). Con il controllo è possibile tenere traccia degli eventi di database e scriverli in un log di controllo in un account di archiviazione di Azure o in un'area di lavoro di Azure Log Analytics. 

### <a name="audit-critical-security-events"></a>Controlla eventi di sicurezza critici
Il rilevamento degli eventi di database consente di comprendere l'attività del database. È possibile ottenere informazioni su discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza. Consente inoltre di migliorare la conformità agli standard di conformità. 

**Come implementare**:

- Abilitare il [controllo del database SQL](sql-database-auditing.md) per tenere traccia degli eventi di database e scriverli in un log di controllo nell'account di archiviazione di Azure, log Analytics area di lavoro (anteprima) o hub eventi (anteprima). 

- I log di controllo possono essere scritti in un account di archiviazione di Azure, in un'area di lavoro Log Analytics per l'uso da log di monitoraggio di Azure o nell'hub eventi per l'uso con hub eventi. È possibile configurare qualsiasi combinazione di queste opzioni e verranno scritti i log di controllo per ognuno. 

**Procedure consigliate**:

- Configurando il [controllo del database SQL](sql-database-auditing.md) nel server di database per controllare gli eventi, verranno controllati tutti i database esistenti e appena creati nel server.
- Per impostazione predefinita, i criteri di controllo includono tutte le azioni (query, stored procedure e accessi riusciti e non riusciti) sui database, che possono comportare un volume elevato di log di controllo. È consigliabile che i clienti [configurino il controllo per diversi tipi di azioni e gruppi di azioni usando PowerShell](sql-database-auditing.md#manage-auditing). La configurazione di questo consente di controllare il numero di azioni controllate e ridurre al minimo il rischio di perdita di eventi. La configurazione di controllo personalizzato consente ai clienti di acquisire solo i dati di controllo necessari.
- I log di controllo possono essere utilizzati direttamente nel [portale di Azure](https://portal.azure.com/)o dal percorso di archiviazione configurato. 


> [!NOTE]
> L'abilitazione del controllo per Log Analytics comporterà costi in base ai tassi di inserimento. Per conoscere i costi associati, usare questa [opzione](https://azure.microsoft.com/pricing/details/monitor/)oppure prendere in considerazione l'archiviazione dei log di controllo in un account di archiviazione di Azure. 

**Altre risorse**:

- [Controllo del database SQL](sql-database-auditing.md)
- [Controllo di SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) 

### <a name="secure-audit-logs"></a>Log di controllo protetti
Limitare l'accesso all'account di archiviazione per supportare la separazione dei compiti e separare l'amministratore di database dai revisori. 

**Come implementare**:

- Quando si salvano i log di controllo in archiviazione di Azure, assicurarsi che l'accesso all'account di archiviazione sia limitato ai principi di protezione minimi. Controllare chi ha accesso all'account di archiviazione.
    - Per altre informazioni, vedere [autorizzazione dell'accesso ad archiviazione di Azure](../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

**Procedure consigliate**:

- Il controllo dell'accesso alla destinazione di controllo è un concetto chiave nella separazione dei DBA dai revisori. 

- Quando si controlla l'accesso ai dati sensibili, valutare la possibilità di proteggere i dati con la crittografia dei dati per evitare la perdita di informazioni da parte del revisore. Per ulteriori informazioni, vedere la sezione [proteggere i dati sensibili in uso da utenti con privilegi elevati e non autorizzati](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users).

## <a name="security-management"></a>Gestione della sicurezza

In questa sezione vengono descritti i diversi aspetti e le procedure consigliate per la gestione del comportamento di sicurezza dei database. Sono incluse le procedure consigliate per garantire che i database siano configurati per soddisfare gli standard di sicurezza, per l'individuazione e la classificazione e la verifica dell'accesso a dati potenzialmente sensibili nei database. 

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>Assicurarsi che i database siano configurati per soddisfare le procedure di sicurezza consigliate 

Per migliorare in modo proattivo la sicurezza del database, è possibile individuare e correggere potenziali vulnerabilità del database.

**Come implementare**:

- Abilitare la [valutazione della vulnerabilità di SQL](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) (va) per analizzare il database per individuare eventuali problemi di sicurezza e per l'esecuzione periodica automatica nei database.

**Procedure consigliate**:

- Eseguire inizialmente VA sui database ed eseguire l'iterazione monitorando e aggiornando i controlli con esito negativo che si oppongono alle procedure di sicurezza consigliate. Configurare le basi di riferimento per le configurazioni accettabili fino a quando l'analisi non risulta _pulita_oppure tutti i controlli sono stati superati.  

- Configurare analisi periodiche periodiche da eseguire una volta alla settimana e configurare la persona interessata per la ricezione di messaggi di posta elettronica di riepilogo. 

- Esaminare il riepilogo di VA dopo ogni analisi settimanale. Per eventuali vulnerabilità rilevate, valutare la deriva dal risultato dell'analisi precedente e determinare se il controllo deve essere risolto. Verificare se esiste un motivo legittimo per la modifica nella configurazione.   

- Risolvere i controlli e aggiornare le basi di riferimento laddove pertinente. Creare gli elementi del ticket per la risoluzione delle azioni e tenerne traccia fino a quando non vengono risolti. 

**Altre risorse**:

- [Valutazione della vulnerabilità SQL](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 
- [Servizio Valutazione della vulnerabilità di SQL per identificare le vulnerabilità dei database](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>Identificare e contrassegnare i dati sensibili 

Individuare le colonne che potenzialmente contengono dati sensibili. Gli elementi considerati dati sensibili dipendono spesso dal cliente, dal regolamento di conformità e così via e devono essere valutati dagli utenti responsabili di tali dati. Classificare le colonne in modo da usare scenari avanzati di controllo e protezione basati sulla sensibilità. 

**Come implementare**:

- Usare l' [individuazione e la classificazione dei dati SQL](sql-database-data-discovery-and-classification.md) per individuare, classificare, etichettare e proteggere i dati sensibili nei database. 
  - Visualizzare le raccomandazioni di classificazione create dall'individuazione automatica nel dashboard individuazione dati SQL e classificazione. Accettare le classificazioni rilevanti, in modo che i dati sensibili siano contrassegnati in modo permanente con le etichette di classificazione. 
  - Aggiungere manualmente le classificazioni per eventuali campi dati sensibili aggiuntivi che non sono stati individuati dal meccanismo automatizzato. 
- Per ulteriori informazioni, vedere [SQL Data Discovery & Classification](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification).

**Procedure consigliate**:

- Monitorare regolarmente il dashboard di classificazione per una valutazione accurata dello stato di classificazione del database. È possibile esportare o stampare un report sullo stato di classificazione del database per condividerlo a scopo di controllo e conformità.

- Monitorare continuamente lo stato dei dati sensibili consigliati nella valutazione della vulnerabilità di SQL. Tenere traccia della regola di individuazione dei dati sensibili e identificare eventuali deviazioni nelle colonne consigliate per la classificazione.  

- Usare la classificazione in modo che sia adattata alle esigenze specifiche della propria organizzazione. Personalizzare i criteri di Information Protection (etichette di riservatezza, tipi di informazioni e logica di individuazione) nei criteri di [Information Protection SQL](../security-center/security-center-info-protection-policy.md) nel centro sicurezza di Azure. 

### <a name="track-access-to-sensitive-data"></a>Tenere traccia dell'accesso ai dati sensibili 
Consente di monitorare l'accesso ai dati sensibili e l'acquisizione di query sui dati sensibili nei log di controllo.

**Come implementare**:

- Usare il controllo SQL e la classificazione dei dati in combinazione. 
  - Nel log di [controllo del database SQL](sql-database-auditing.md) è possibile tenere traccia dell'accesso in modo specifico ai dati sensibili. È anche possibile visualizzare informazioni quali i dati a cui è stato eseguito l'accesso, nonché l'etichetta di riservatezza. Per altre informazioni, vedere [individuazione dei dati & classificazione](sql-database-data-discovery-and-classification.md) e [controllo dell'accesso ai dati sensibili](sql-database-data-discovery-and-classification.md#audit-sensitive-data). 

**Procedure consigliate**:

- Vedere le procedure consigliate per le sezioni controllo e classificazione dei dati: 
  - [Controlla eventi di sicurezza critici](#audit-critical-security-events) 
  - [Identificare e contrassegnare i dati sensibili](#identify-and-tag-sensitive-data) 

### <a name="visualize-security-and-compliance-status"></a>Visualizzare lo stato di sicurezza e conformità 

Usare un sistema di gestione della sicurezza dell'infrastruttura unificata che rafforzi il comportamento di sicurezza dei Data Center (inclusi i database SQL). Visualizzare un elenco di consigli relativi alla sicurezza dei database e allo stato di conformità.

**Come implementare**:

- Monitorare le raccomandazioni di sicurezza relative a SQL e le minacce attive nel [Centro sicurezza di Azure](https://azure.microsoft.com/documentation/services/security-center/).

## <a name="common-security-threats-and-potential-mitigations"></a>Minacce di sicurezza comuni e potenziali mitigazioni

Questa sezione consente di trovare misure di sicurezza da proteggere da determinati vettori di attacco. Si prevede che la maggior parte delle mitigazioni possa essere eseguita attenendosi a una o più delle linee guida di sicurezza riportate sopra.

### <a name="security-threat-data-exfiltration"></a>Minaccia per la sicurezza: dati exfiltration

I dati exfiltration sono la copia, il trasferimento o il recupero di dati non autorizzati da un computer o un server. Vedere una definizione per [i dati exfiltration](https://en.wikipedia.org/wiki/Data_exfiltration) in Wikipedia.

La connessione al server di database SQL di Azure tramite un endpoint pubblico presenta un rischio per i exfiltration di dati perché richiede ai clienti di aprire i firewall negli indirizzi IP pubblici.  

**Scenario 1**: un'applicazione in una macchina virtuale di Azure si connette a un database in un server di database SQL di Azure. Un attore malintenzionato ottiene l'accesso alla macchina virtuale e lo compromette. In questo scenario, data exfiltration significa che un'entità esterna che usa la VM non autorizzato si connette al database, copia i dati personali e li archivia in un archivio BLOB o in un altro database SQL in una sottoscrizione diversa.

**Scenario 2**: DBA di Rouge. Questo scenario è spesso generato da clienti sensibili alla sicurezza da settori regolamentati. In questo scenario un utente con privilegi elevati potrebbe copiare dati dal database SQL di Azure a un'altra sottoscrizione non controllata dal proprietario dei dati.

**Possibili mitigazioni**:

Attualmente, il database SQL di Azure offre le tecniche seguenti per attenuare le minacce per il exfiltration dei dati: 

- Usare una combinazione di regole Allow e Deny per la gruppi di macchine virtuali di Azure per controllare le aree a cui è possibile accedere dalla macchina virtuale. 
- Se si usa un server di database SQL di Azure (contenente i database Singleton o i pool elastici), impostare le opzioni seguenti:
  - Consentire ai servizi di Azure di disattivare.
  - Consentire solo il traffico dalla subnet che contiene la macchina virtuale di Azure impostando una regola del firewall VNet.
  - USA [collegamento privato](sql-database-private-endpoint-overview.md)
- Per un'istanza gestita, l'uso dell'accesso IP privato per impostazione predefinita si riferisce al primo problema di exfiltration dei dati di una macchina virtuale non autorizzati. Attivare la funzionalità di delega della subnet in una subnet per impostare automaticamente i criteri più restrittivi in una subnet dell'istanza gestita.
- Il problema dell'amministratore di database non autorizzato è più esposto a un'istanza gestita poiché presenta una superficie di attacco più ampia e i requisiti di rete sono visibili ai clienti. La migliore mitigazione è l'applicazione di tutte le procedure illustrate in questa guida alla sicurezza per evitare lo scenario di DBA non autorizzato (non solo per i dati exfiltration). Always Encrypted è un metodo per proteggere i dati sensibili mediante la crittografia e l'inaccessibilità della chiave per l'amministratore di database.

## <a name="security-aspects-of-business-continuity-and-availability"></a>Aspetti di sicurezza della continuità aziendale e della disponibilità

La maggior parte degli standard di sicurezza consente di gestire la disponibilità dei dati in termini di continuità operativa, implementando funzionalità di ridondanza e failover per evitare singoli punti di errore. Per gli scenari di emergenza, è prassi comune eseguire i backup dei file di dati e di log.La sezione seguente offre una panoramica di alto livello delle funzionalità integrate in Azure. Fornisce inoltre opzioni aggiuntive che possono essere configurate per soddisfare specifiche esigenze: 

- Azure offre disponibilità elevata incorporata: [disponibilità elevata e database SQL di Azure](sql-database-high-availability.md) 

- Il livello business critical include gruppi di failover, zone con più disponibilità, backup del log completi e differenziali e backup di ripristino temporizzato abilitati per impostazione predefinita:  
  - [Disponibilità elevata e database SQL di Azure-configurazione con ridondanza della zona](sql-database-high-availability.md#zone-redundant-configuration)
  - [Backup automatizzati](sql-database-automated-backups.md)
  - [Ripristinare un database SQL di Azure usando i backup automatici del database, ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore)

- Altre funzionalità di continuità aziendale, ad esempio i gruppi di failover automatico in diversi servizi di Azure GEOS, possono essere configurate come descritto qui: [Panoramica della continuità aziendale con il database SQL di Azure](sql-database-business-continuity.md)

## <a name="next-steps"></a>Passaggi successivi

- Vedere [una panoramica delle funzionalità di sicurezza del database SQL di Azure](sql-database-security-overview.md)
