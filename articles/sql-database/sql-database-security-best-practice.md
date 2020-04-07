---
title: Playbook per l'indirizzamento ai requisiti di sicurezza comuni Documenti Microsoft
titleSuffix: Azure SQL Database
description: Questo articolo fornisce requisiti di sicurezza comuni e procedure consigliate nel database SQL di Azure.This article provides common security requirements and best practices in Azure SQL Database.
ms.service: sql-database
ms.subservice: security
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 02/20/2020
ms.reviewer: ''
ms.openlocfilehash: 39747ac0a7133562bed526f44e30bf4a656127c0
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673599"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database"></a>Playbook per la risoluzione dei requisiti di sicurezza comuni con il database SQL di Azure

> [!NOTE]
> In questo documento vengono illustrate le procedure consigliate per la risoluzione dei requisiti di sicurezza comuni. Non tutti i requisiti sono applicabili a tutti gli ambienti ed è consigliabile consultare il database e il team di sicurezza in base alle funzionalità da implementare.

## <a name="solving-common-security-requirements"></a>Risoluzione dei requisiti di sicurezza comuni

Questo documento fornisce indicazioni su come risolvere i requisiti di sicurezza comuni per le applicazioni nuove o esistenti usando il database SQL di Azure.This document provides guidance on how to solve common security requirements for new or existing applications using Azure SQL Database. È organizzato per aree di sicurezza di alto livello. Per risolvere minacce specifiche, fare riferimento alla sezione Minacce comuni alla [sicurezza e potenziali attenuazioni.](#common-security-threats-and-potential-mitigations) Anche se alcuni dei consigli presentati sono applicabili quando si esegue la migrazione di applicazioni da locale ad Azure, gli scenari di migrazione non sono lo scopo di questo documento.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>Offerte di distribuzione del database SQL di Azure trattate in questa guidaAzure SQL Database deployment offers covered in this guide

- [Database SQL:](https://docs.microsoft.com/azure/sql-database/sql-database-single-index) [singoli database](sql-database-single-database.md) e [pool elastici](sql-database-elastic-pool.md) nei server di [database SQL](sql-database-servers.md) di Azure
- [Istanze gestite](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="sql-deployment-offers-not-covered-in-this-guide"></a>Offerte di distribuzione SQL non trattate in questa guida

- Azure SQL Data Warehouse
- Macchine virtuali SQL di Azure (IaaS)Azure SQL VMs (IaaS)
- SQL Server in locale

### <a name="audience"></a>Destinatari

I destinatari previsti per questa guida sono i clienti che devono affrontare domande su come proteggere il database SQL di Azure.The intended audiences for this guide are customers facing questions on how to secure Azure SQL Database. I ruoli interessati a questo articolo sulle procedure consigliate includono, ma non solo:

- Architetti della sicurezza
- Responsabili della sicurezza
- Responsabili della conformità
- Responsabili della privacy
- Ingegneri della sicurezza

### <a name="using-this-guide"></a><a id="using"></a>Utilizzo di questa guida

Questo documento è inteso come complemento alla documentazione sulla sicurezza del [database SQL](sql-database-security-overview.md) di Azure esistente.

Se non diversamente specificato, ti consigliamo di seguire tutte le best practice elencate in ogni sezione per raggiungere il rispettivo obiettivo o requisito. Per soddisfare specifici standard o procedure consigliate per la conformità alla sicurezza, i controlli di conformità alle normative sono elencati nella sezione Requisiti o obiettivi, ove applicabile. Questi sono gli standard di sicurezza e le normative a cui si fa riferimento in questo documento:

- [FedRAMP](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): CM-3, SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): Controllo degli accessi, crittografia
- [Procedure di Microsoft Operational Security Assurance (OSA):](https://www.microsoft.com/en-us/securityengineering/osa/practices)esercitazione #1-6 e #9
- [Pubblicazione speciale NIST 800-53 Controlli](https://nvd.nist.gov/800-53)di sicurezza : AC-5, AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library): 6.3.2, 6.4.2

### <a name="feedback"></a>Commenti e suggerimenti

Prevediamo di continuare ad aggiornare le raccomandazioni e le best practice elencate di seguito. Fornire input o eventuali correzioni per questo documento utilizzando il collegamento Commenti e **suggerimenti** nella parte inferiore di questo articolo.

## <a name="authentication"></a>Authentication

L'autenticazione è il processo atto a dimostrare che l'utente sia effettivamente chi dichiara di essere. Il database SQL di Azure supporta due tipi di autenticazione:

- Autenticazione SQL
- Autenticazione di Azure Active Directory

> [!NOTE]
> L'autenticazione di Azure Active Directory potrebbe non essere supportata per tutti gli strumenti e le applicazioni di terze parti.

### <a name="central-management-for-identities"></a>Gestione centralizzata delle identità

La gestione centralizzata delle identità offre i vantaggi seguenti:

- Gestire gli account di gruppo e controllare le autorizzazioni utente senza duplicare gli account di accesso tra i server e i database di database SQL di Azure.Manage group accounts and control user permissions without duplicating logins across Azure SQL Database servers and databases.
- Gestione semplificata e flessibile delle autorizzazioni.
- Gestione delle applicazioni su larga scala.

**Modalità di implementazione**:

- Usare l'autenticazione di Azure Active Directory (Azure AD) per la gestione centralizzata delle identità.

**Procedure consigliate**:

- Creare un tenant di Azure AD e [creare utenti](../active-directory/fundamentals/add-users-azure-active-directory.md) per rappresentare gli utenti umani e creare [entità servizio](../active-directory/develop/app-objects-and-service-principals.md) per rappresentare app, servizi e strumenti di automazione. Service principals are equivalent to service accounts in Windows and Linux. 

- Assegnare diritti di accesso alle risorse alle entità di Azure AD tramite l'assegnazione dei gruppi: creare gruppi di Azure AD, concedere l'accesso ai gruppi e aggiungere singoli membri ai gruppi. Nel database creare utenti di database indipendente che eseguono il mapping dei gruppi di Azure AD. Per assegnare autorizzazioni all'interno del database, inserire gli utenti nei ruoli del database con le autorizzazioni appropriate.
  - Vedere gli articoli [Configurare e gestire l'autenticazione](sql-database-aad-authentication-configure.md) di Azure Active Directory con SQL e [Usare Azure AD per l'autenticazione con SQL.](sql-database-aad-authentication.md)
  > [!NOTE]
  > In un'istanza gestita è anche possibile creare account di accesso mappati alle entità di Azure AD nel database master. Vedere [CREATE LOGIN (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

- L'uso dei gruppi di Azure AD semplifica la gestione delle autorizzazioni e sia il proprietario del gruppo, nonché il proprietario della risorsa può aggiungere/rimuovere membri dal gruppo. 

- Creare un gruppo separato per l'amministratore di Azure AD per i server di database SQL.

  - Vedere l'articolo [Effettuare il provisioning](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)di un amministratore di Azure Active Directory per il server di database SQL di Azure.See the article, Provision an Azure Active Directory administrator for your Azure SQL Database server .

- Monitorare le modifiche di appartenenza ai gruppi di Azure AD usando i report dell'attività di controllo di Azure AD. 

- Per un'istanza gestita, è necessario un passaggio separato per creare l'amministratore di Azure AD. 
  - Vedere l'articolo Effettuare il provisioning di un amministratore di [Azure Active Directory per l'istanza gestita.](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) 

> [!NOTE]
> - L'autenticazione di Azure AD viene registrata nei log di controllo SQL di Azure, ma non nei log di accesso di Azure AD.
> - Le autorizzazioni RBAC concesse in Azure non si applicano alle autorizzazioni del database SQL di Azure.RBAC RBAC permissions granted in Azure do not apply to Azure SQL DB permissions. Tali autorizzazioni devono essere create/mappate manualmente nel database SQL utilizzando le autorizzazioni SQL esistenti.
> - Sul lato client l'autenticazione di Azure AD deve accedere a Internet o tramite la route definita dall'utente (UDR) a una rete virtuale.
> - Il token di accesso di Azure AD viene memorizzato nella cache sul lato client e la durata dipende dalla configurazione del token. Vedere l'articolo Durata dei [token configurabili in Azure Active DirectorySee the](../active-directory/develop/active-directory-configurable-token-lifetimes.md) article, Configurable token lifetimes in Azure Active Directory
> - Per indicazioni sulla risoluzione dei problemi di autenticazione di Azure AD, vedere il blog seguente:For guidance on troubleshooting Azure AD Authentication issues, see the following blog:<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA).

> Menzionato in: OSA Practice #2, ISO Access Control (AC)

Azure Multi-Factor Authentication (MFA) offre ulteriore sicurezza richiedendo più di una forma di autenticazione.

**Modalità di implementazione**:

- [Abilitare l'autenticazione a](../active-directory/authentication/concept-mfa-howitworks.md) più fattori in Azure AD usando l'accesso condizionale e usare l'autenticazione interattiva. 

- L'alternativa consiste nell'abilitare l'autenticazione a più fattori per l'intero dominio Azure AD o AD.

**Procedure consigliate**:

- Attivare l'accesso condizionale in Azure AD (richiede la sottoscrizione Premium). 
  - Vedere l'articolo [Accesso condizionale in Azure AD](../active-directory/conditional-access/overview.md).  

- Creare gruppi di Azure AD e abilitare i criteri di autenticazione a più fattori per i gruppi selezionati usando l'accesso condizionale di Azure AD. 
  - Vedere l'articolo [Pianificare](../active-directory/conditional-access/plan-conditional-access.md)la distribuzione con accesso condizionale . 

- L'autenticazione a più fattori può essere abilitata per l'intero Azure AD o per l'intera federazione di Active Directory con Azure AD. 

- Usare la modalità di autenticazione interattiva di Azure AD per il database SQL in cui viene richiesta una password in modo interattivo, seguita dall'autenticazione MFA:Use Azure AD Interactive authentication mode for SQL DB where a password is requested interactively, followed by MFA authentication:      
  - Usare l'autenticazione universale in SSMS. Vedere l'articolo [Utilizzo dell'autenticazione AAD a più fattori con il database SQL di Azure e Azure SQL Data Warehouse (supporto SSMS per l'autenticazione a](sql-database-ssms-mfa-authentication.md)più fattori).
  - Usare l'autenticazione interattiva supportata in SQL Server Data Tools (SSDT). Vedere l'articolo [Supporto di Azure Active Directory in SQL Server Data Tools (SSDT).](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current)
  - Usare altri strumenti SQL che supportano l'autenticazione a più fattori. 
    - Supporto della procedura guidata SSMS per il database di esportazione/estrazione/distribuzione  
    - [sqlpackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage): opzione '/ua' 
    - [Utilità sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility): opzione -G (interattiva)
    - [Utilità bcp](https://docs.microsoft.com/sql/tools/bcp-utility): opzione -G (interattiva) 

- Implementare le applicazioni per connettersi al database SQL di Azure usando l'autenticazione interattiva con il supporto dell'autenticazione a più fattori. 
  - Vedere l'articolo Connettersi al database SQL di [Azure con Azure Multi-Factor Authentication.](active-directory-interactive-connect-azure-sql-db.md) 
  > [!NOTE]
  > Questa modalità di autenticazione richiede identità basate sull'utente. Nei casi in cui viene usato un modello di identità attendibile che ignora l'autenticazione utente di Azure AD (ad esempio usando l'identità gestita per le risorse di Azure), l'autenticazione a più fattori non viene applicata.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>Ridurre al minimo l'utilizzo dell'autenticazione basata su password per gli utenti

> Menzionato in: OSA Practice #4, ISO Access Control (AC)

I metodi di autenticazione basata su password sono una forma di autenticazione più debole. Le credenziali possono essere compromesse o erroneamente assegnate.

**Modalità di implementazione**:

- Usare un'autenticazione integrata di Azure AD che elimina l'uso delle password.

**Procedure consigliate**:

- Usare l'autenticazione Single Sign-On usando le credenziali di Windows.Use single sign-on authentication using Windows credentials. Federare il dominio di Active Directory locale con Azure AD e usare l'autenticazione integrata di Windows (per i computer aggiunti al dominio con Azure AD).
  - Vedere l'articolo [Supporto di SSMS per l'autenticazione integrata](sql-database-aad-authentication-configure.md#active-directory-integrated-authentication)di Azure AD.

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>Ridurre al minimo l'utilizzo dell'autenticazione basata su password per le applicazioni 

> Menzionato in: OSA Practice #4, ISO Access Control (AC)

**Modalità di implementazione**:

- Abilitare l'identità gestita di Azure.Enable Azure Managed Identity. È inoltre possibile utilizzare l'autenticazione integrata o basata su certificati. 

**Procedure consigliate**:

- Usare [le identità gestite per le risorse di Azure.Use managed identities for Azure resources](../active-directory/managed-identities-azure-resources/overview.md).
  - [Identità gestita assegnata dal sistema](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) 
  - [Identità gestita assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [Usare il database SQL di Azure dal servizio app con identità gestita (senza modifiche al codice)Use Azure SQL Database from app service with managed identity (without code changes)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- Utilizzare l'autenticazione basata su certificati per un'applicazione. 
  - Vedere questo [esempio di codice](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token). 

- Usare l'autenticazione di Azure AD per il dominio federato integrato e il computer aggiunto al dominio (vedere la sezione precedente).
  - Vedere [l'applicazione di esempio per l'autenticazione integrata](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated).

### <a name="protect-passwords-and-secrets"></a>Proteggere password e segreti

Per i casi in cui le password non sono evitabili, assicurati che siano protette.

**Modalità di implementazione**:

- Usare L'insieme di credenziali delle chiavi di Azure per archiviare password e segreti. Quando applicabile, usare l'autenticazione a più fattori per il database SQL di Azure con gli utenti di Azure AD.

**Procedure consigliate**:

- Se non è possibile evitare password o segreti, archiviare le password utente e i segreti dell'applicazione in Archiviazione delle chiavi di Azure e gestire l'accesso tramite i criteri di accesso dell'insieme di credenziali delle chiavi. 

- Vari framework di sviluppo di app possono anche offrire meccanismi specifici del framework per proteggere i segreti nell'app. Ad esempio: [ASP.NET'app principale](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows).

### <a name="use-sql-authentication-for-legacy-applications"></a>Usare l'autenticazione SQL per le applicazioni legacyUse SQL authentication for legacy applications 

L'autenticazione SQL si riferisce all'autenticazione di un utente quando ci si connette al database SQL di Azure usando nome utente e password. Sarà necessario creare un account di accesso in ogni server di database SQL o in un'istanza gestita e verrà creato un utente in ogni database.

**Modalità di implementazione**:

- Utilizzare l'autenticazione SQL.

**Procedure consigliate**:

- In qualità di amministratore del server, crea account di accesso e utenti. A meno che non si utilizzino utenti di database indipendenti con password, tutte le password vengono archiviate nel database master.
  - Vedere l'articolo [Controllo e concessione dell'accesso al database AL database SQL e](sql-database-manage-logins.md)al data warehouse SQL .

## <a name="access-management"></a>Gestione degli accessi

Access management is the process of controlling and managing authorized users' access and privileges to Azure SQL Database.

### <a name="implement-principle-of-least-privilege"></a>Implementare il principio dei privilegi minimi

> Menzionato in: FedRamp controlla AC-06, NIST: AC-6, OSA Practice #3

Il principio dei privilegi minimi indica che gli utenti non devono disporre di più privilegi del necessario per completare le attività. Per ulteriori informazioni, vedere l'articolo [Amministrazione sufficiente](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview).

**Modalità di implementazione**:

Assegnare solo le [autorizzazioni](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) necessarie per completare le attività necessarie:

- In SQL Data Plane: 
    - Utilizzare autorizzazioni granulari e ruoli del database definiti dall'utente (o ruoli del server in MI): 
        1. Creare i ruoli necessari
            - [CREA RUOLO](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)
            - [CREATE SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-server-role-transact-sql)
        1. Creare gli utenti necessari
            - [CREA UTENTE](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
        1. Aggiungere utenti come membri ai ruoli 
            - [ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)
            - [ALTERARE IL RUOLO DEL SERVER](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)
        1. Assegnare quindi le autorizzazioni ai ruoli. 
            - [Concedere](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 
    - Assicurarsi di non assegnare gli utenti a ruoli non necessari.

- In Azure Resource Manager:
  - Utilizzare i ruoli predefiniti, se disponibili o i ruoli RBAC personalizzati, e assegnare le autorizzazioni necessarie.
    - [Ruoli predefiniti per AzureBuilt-in roles for Azure](../role-based-access-control/built-in-roles.md) 
    - [Ruoli personalizzati per le risorse di Azure](../role-based-access-control/custom-roles.md)

**Procedure consigliate**:

Le procedure consigliate seguenti sono facoltative, ma si tradurrà in una migliore gestibilità e supportabilità della strategia di sicurezza: 

- Se possibile, iniziare con il minor numero possibile di autorizzazioni e iniziare ad aggiungere le autorizzazioni una per una se c'è una vera necessità (e giustificazione) - al contrario dell'approccio opposto: togliere le autorizzazioni passo dopo passo. 

- Astenersi dall'assegnare autorizzazioni a singoli utenti. Utilizzare invece i ruoli (ruoli del database o del server) in modo coerente. I ruoli sono molto soddisfatti delle autorizzazioni per la creazione di report e la risoluzione dei problemi. (Il controllo degli accessi in base al ruolo di Azure supporta solo l'assegnazione delle autorizzazioni tramite ruoli. 

- Creare e utilizzare ruoli personalizzati con le autorizzazioni esatte necessarie. Ruoli tipici utilizzati nella pratica: 
  - Distribuzione della sicurezza 
  - Amministratore 
  - Developer 
  - Personale di supporto 
  - Revisore 
  - Processi automatizzati 
  - End user (Utente finale) 
  
- Utilizzare i ruoli predefiniti solo quando le autorizzazioni dei ruoli corrispondono esattamente alle autorizzazioni necessarie per l'utente. È possibile assegnare gli utenti a più ruoli. 

- Tenere presente che le autorizzazioni nel Motore di database di SQL ServerSQL Server Database Engine possono essere applicate agli ambiti seguenti. Minore è l'ambito, minore è l'impatto delle autorizzazioni concesse: 
  - Server di database SQL di Azure (ruoli speciali nel database master)Azure SQL Database server (special roles in master database) 
  - Database 
  - SCHEMA
      - È consigliabile utilizzare gli schemi per concedere autorizzazioni all'interno di un database. (vedere anche: [Progettazione dello schema per SQL Server: consigli per la progettazione dello schema con la sicurezza in mente](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/))
  - Oggetto (tabella, vista, procedura, ecc.) 
  > [!NOTE]
  > Non è consigliabile applicare le autorizzazioni a livello di oggetto perché questo livello aggiunge complessità non necessaria all'implementazione complessiva. Se si decide di utilizzare le autorizzazioni a livello di oggetto, queste devono essere chiaramente documentate. Lo stesso vale per le autorizzazioni a livello di colonna, che sono ancora meno raccomandabili per gli stessi motivi. Tenere inoltre presente che per impostazione predefinita un [DENY](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql) a livello di tabella non esegue l'override di un GRANT a livello di colonna. Ciò richiederebbe l'attivazione della configurazione del server di [conformità dei criteri comuni.](https://docs.microsoft.com/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option)

- Eseguire controlli regolari utilizzando [Vulnerability Assessment (VA)](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) per verificare la presenza di troppe autorizzazioni.

### <a name="implement-separation-of-duties"></a>Implementare la separazione dei compiti

> Menzionato in: FedRamp: AC-04, NIST: AC-5, ISO: A.6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

La separazione dei doveri, denominata anche Separazione dei dazi, descrive la necessità di suddividere le attività sensibili in più attività assegnate a utenti diversi. La separazione dei compiti aiuta a prevenire le violazioni dei dati.

**Modalità di implementazione**:

- Identificare il livello richiesto di Separazione dei Doveri. Esempi: 
  - Tra ambienti di sviluppo/test e produzione 
  - Attività sensibili alla sicurezza rispetto alle attività del livello di gestione dell'amministratore di database (DBA) rispetto alle attività per gli sviluppatori. 
    - Esempi: auditor, creazione di criteri di sicurezza per la sicurezza a livello di ruolo (RLS), implementazione di oggetti di database SQL con autorizzazioni DDL.

- Identificare una gerarchia completa di utenti (e processi automatizzati) che accedono al sistema.

- Creare ruoli in base ai gruppi di utenti necessari e assegnare autorizzazioni ai ruoli. 
  - Per le attività a livello di gestione nel portale di Azure o tramite l'automazione di PowerShell, usare i ruoli RBAC. Trovare un ruolo predefinito corrispondente al requisito oppure creare un ruolo RBAC personalizzato usando le autorizzazioni disponibili 
  - Creare ruoli server per attività a livello di server (creazione di nuovi account di accesso, database) in un'istanza gestita. 
  - Creare ruoli del database per le attività a livello di database.

- Per alcune attività riservate, è consigliabile creare stored procedure speciali firmate da un certificato per eseguire le attività per conto degli utenti. 
  - Esempio: [Esercitazione: firma di stored procedure con un certificatoExample: Tutorial: Signing Stored Procedures with a Certificate](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate) 

- Implementare Transparent Data Encryption (TDE) con chiavi gestite dal cliente in Azure Key Vault per abilitare la separazione dei doveri tra il proprietario dei dati e il proprietario della sicurezza. 
  - Vedere l'articolo Configurare le chiavi gestite dal cliente per la crittografia di Archiviazione di Azure dal portale di Azure.See the article, [Configure customer-managed keys for Azure Storage encryption from the Azure portal](../storage/common/storage-encryption-keys-portal.md). 

- Per garantire che un amministratore di database non possa visualizzare dati considerati altamente sensibili e che possano comunque eseguire attività di amministratore di database, è possibile usare Always Encrypted con la separazione dei ruoli. 
  - Vedere gli [articoli, Panoramica della gestione delle chiavi per Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted), [Provisioning delle chiavi con separazione](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)dei ruoli e Rotazione della chiave master a colonne con [separazione](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation)dei ruoli . 

- Nei casi in cui non è fattibile almeno non senza grandi costi e sforzi che possono rendere il sistema quasi inutilizzabile, compromessi possono essere fatti e mitigati attraverso l'uso di controlli di compensazione come: 
  - Intervento umano nei processi. 
  - Audit trail: per ulteriori informazioni sul controllo, vedere [Audit critical security events](#audit-critical-security-events).

**Procedure consigliate**:

- Assicurarsi che vengano utilizzati account diversi per gli ambienti di sviluppo/test e produzione. Diversi account aiutano a rispettare la separazione dei sistemi di test & di produzione.

- Astenersi dall'assegnare autorizzazioni a singoli utenti. Utilizzare invece i ruoli (ruoli del database o del server) in modo coerente. La disponibilità di ruoli consente di risolvere notevolmente le autorizzazioni per la creazione di report e la risoluzione dei problemi.

- Utilizzare i ruoli predefiniti quando le autorizzazioni corrispondono esattamente alle autorizzazioni necessarie: se l'unione di tutte le autorizzazioni di più ruoli predefiniti ha una corrispondenza del 100%, è possibile assegnare più ruoli contemporaneamente. 

- Creare e utilizzare ruoli personalizzati quando i ruoli predefiniti concedono troppe autorizzazioni o autorizzazioni insufficienti. 

- Le assegnazioni di ruolo possono anche essere eseguite temporaneamente, noto anche come separazione dinamica dei doveri (DSD), all'interno dei passaggi del processo di SQL Agent in T-SQL o usando Azure PIM per i ruoli RBAC. 

- Assicurarsi che gli amministratori di database non abbiano accesso alle chiavi di crittografia o agli archivi chiavi e che gli amministratori della sicurezza con accesso alle chiavi non abbiano accesso al database. 

- Assicurarsi sempre di disporre di un audit trail per le azioni correlate alla sicurezza. 

- È possibile recuperare la definizione dei ruoli RBAC incorporati per visualizzare le autorizzazioni utilizzate e creare un ruolo personalizzato basato su estratti e cumuli di questi tramite PowerShell.You can retrieve the definition of the built-in RBAC roles to see the permissions used and create a custom role based on excerpts and cumulations of these via PowerShell.

- Poiché qualsiasi membro del ruolo del database db_owner può modificare le impostazioni di sicurezza, ad esempio Transparent Data Encryption (TDE), o modificare l'SLO, questa appartenenza deve essere concessa con attenzione. Tuttavia, esistono molte attività che richiedono privilegi di db_owner. Attività come la modifica di qualsiasi impostazione del database, ad esempio la modifica delle opzioni del database. Il controllo svolge un ruolo chiave in qualsiasi soluzione.

- Non è possibile limitare le autorizzazioni di un db_owner e quindi impedire a un account amministrativo di visualizzare i dati utente. Se sono presenti dati altamente sensibili in un database, Always Encrypted può essere utilizzato per impedire in modo sicuro db_owners o qualsiasi altro amministratore di database di visualizzarlo.

> [!NOTE]
> Il raggiungimento della separazione dei doveri (SoD) è complesso per le attività di risoluzione dei problemi o relative alla sicurezza. Altre aree come i ruoli di sviluppo e di utente finale sono più facili da separare. La maggior parte dei controlli correlati alla conformità consente l'uso di funzioni di controllo alternative, ad esempio il controllo quando altre soluzioni non sono pratiche.

Per i lettori che vogliono approfondire SoD, consigliamo le seguenti risorse: 

- Per il database SQL di Azure:  
  - [Controllo e concessione dell'accesso al database SQL e a SQL Data Warehouse](sql-database-manage-logins.md)
  - [Separazione dei compiti del motore per lo sviluppatore dell'applicazione](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100)) 
  - [Separazione dei doveri in SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=39269)
  - [Firma di stored procedure in SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- Per la gestione delle risorse di Azure:For Azure Resource Management:
  - [Ruoli predefiniti per AzureBuilt-in roles for Azure](../role-based-access-control/built-in-roles.md) 
  - [Ruoli personalizzati per le risorse di Azure](../role-based-access-control/custom-roles.md)
  - [Uso di Azure AD Privileged Identity Management per l'accesso con privilegiUsing Azure AD Privileged Identity Management for elevated access](https://www.microsoft.com/en-us/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>Eseguire revisioni regolari del codice

> Menzionato in: PCI: 6.3.2, SOC: SDL-3 

La separazione dei compiti non è limitata ai dati nel database, ma include il codice dell'applicazione. Il codice dannoso può potenzialmente aggirare i controlli di sicurezza. Prima di distribuire codice personalizzato nell'ambiente di produzione, è essenziale esaminare ciò che viene distribuito.

**Modalità di implementazione**:

- Usare uno strumento di database come Azure Data Studio che supporta il controllo del codice sorgente. 

- Implementare un processo di distribuzione del codice segregato.

- Prima di impegnarsi nel ramo principale, una persona (diversa dall'autore del codice stesso) deve controllare il codice per la potenziale elevazione dei rischi di privilegi, nonché modifiche ai dati dannosi per proteggersi da frodi e accesso non autorizzato. Questa operazione può essere eseguita utilizzando i meccanismi di controllo del codice sorgente.

**Procedure consigliate**:

- Standardizzazione: aiuta a implementare una procedura standard che deve essere seguita per eventuali aggiornamenti del codice. 

- La valutazione delle vulnerabilità contiene regole che controllano le autorizzazioni eccessive, l'utilizzo di algoritmi di crittografia precedenti e altri problemi di sicurezza all'interno di uno schema di database. 

- Ulteriori controlli possono essere eseguiti in un ambiente di controllo qualità o di test utilizzando Advanced Threat Protection che analizza il codice che è vulnerabile a SQL-injection.

- Esempi di cosa guardare fuori per: 
  - Creazione di un utente o modifica delle impostazioni di sicurezza dall'interno di una distribuzione automatica di aggiornamento del codice SQL. 
  - Una stored procedure che, a seconda dei parametri forniti, aggiorna un valore monetario in una cella in modo non conforme. 

- Assicurati che la persona che conduce la revisione sia un individuo diverso dall'autore del codice di origine e ben informato nelle revisioni del codice e nella codifica sicura.

- Assicurarsi di conoscere tutte le origini delle modifiche al codice. Il codice può essere in script T-SQL.Code can be in T-SQL Scripts. Può essere comandi ad hoc da eseguire o distribuiti in forme di viste, funzioni, trigger e stored procedure. Può far parte delle definizioni dei processi di SQL Agent (passaggi). Può anche essere eseguito dall'interno di pacchetti SSIS, Azure Data Factory e altri servizi.

## <a name="data-protection"></a>Protezione dei dati

La protezione dei dati è un insieme di funzionalità per la protezione di informazioni importanti dalla compromissione tramite crittografia o offuscamento.

> [!NOTE]
> Microsoft attesta al database SQL di Azure come compatibile con FIPS 140-2 Livello 1. Questa operazione viene eseguita dopo aver verificato l'utilizzo rigoroso degli algoritmi accettabili FIPS 140-2 Livello 1 e delle istanze convalidate di livello 1-1 FIPS 140-2 di tali algoritmi, inclusa la coerenza con le lunghezze di chiave necessarie, la gestione delle chiavi, la generazione delle chiavi e l'archiviazione delle chiavi. Questa attestazione ha lo scopo di consentire ai nostri clienti di rispondere alle esigenze o ai requisiti per l'uso di istanze convalidate FIPS 140-2 Livello 1 nell'elaborazione dei dati o nella distribuzione di sistemi o applicazioni. Definiamo i termini "FIPS 140-2 Level 1 compliant" e "FIPS 140-2 Level 1 compliance" utilizzati nella dichiarazione precedente per dimostrare la loro applicabilità prevista all'uso previsto del governo statunitense e canadese del diverso termine "FIPS 140-2 Level 1 validated". 


### <a name="encrypt-data-in-transit"></a>Crittografare i dati in transitoEncrypt data in transit

> Menzionato in: OSA Practice #6, ISO Control Family: Cryptography

Protegge i dati mentre i dati si spostano tra il client e il server. Fare riferimento a [Sicurezza di rete](#network-security).

### <a name="encrypt-data-at-rest"></a>Crittografare i dati inattivi

> Menzionato in: OSA Practice #6, ISO Control Family: Cryptography

La crittografia inattivi è la protezione crittografica dei dati quando viene mantenuta nei file di database, di log e di backup.

**Modalità di implementazione**:

- [Transparent Database Encryption (TDE)](transparent-data-encryption-azure-sql.md) con chiavi gestite del servizio sono abilitate per impostazione predefinita per tutti i database creati dopo il 2017 nel database SQL di Azure.Transparent Database Encryption (TDE) with service managed keys are enabled by default for any databases created after 2017 in Azure SQL Database.
- In un'istanza gestita, se il database viene creato da un'operazione di ripristino utilizzando un server locale, verrà rispettata l'impostazione TDE del database originale. Se nel database originale non è abilitato TDE, è consigliabile attivare manualmente TDE per l'istanza gestita.

**Procedure consigliate**:

- Non archiviare dati che richiedono la crittografia inattivi nel database master. Il database master non può essere crittografato con TDE.

- Usare le chiavi gestite dal cliente in Archiviazione delle chiavi di Azure se è necessaria una maggiore trasparenza e un controllo granulare sulla protezione TDE. L'insieme di credenziali delle chiavi di Azure consente di revocare le autorizzazioni in qualsiasi momento per rendere il database inaccessibile. È possibile gestire centralmente le protezioni TDE insieme ad altre chiavi oppure ruotare la protezione TDE in base alla propria pianificazione usando l'insieme di credenziali delle chiavi di Azure.You can centrally manage TDE protectors along with other keys, or rotate the TDE protector at your own schedule using Azure Key Vault.

- Se si usano chiavi gestite dal cliente in Archiviazione delle chiavi di Azure, seguire gli articoli Linee guida per la configurazione di [TDE con Azure Key Vault](transparent-data-encryption-byok-azure-sql.md#recommendations-when-configuring-akv) e How to configure [Geo-DR con Azure Key Vault](transparent-data-encryption-byok-azure-sql.md#geo-dr-and-customer-managed-tde).

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>Proteggere i dati sensibili in uso da utenti con privilegi elevati e non autorizzati

I dati in uso sono i dati archiviati in memoria del sistema di database durante l'esecuzione di query SQL. Se il database archivia dati sensibili, all'organizzazione potrebbe essere richiesto di garantire che agli utenti con privilegi elevati venga impedito di visualizzare i dati sensibili nel database. Gli utenti con privilegi elevati, ad esempio gli operatori Microsoft o gli amministratori di database nell'organizzazione, devono essere in grado di gestire il database, ma impedire la visualizzazione e l'esfiltrazione di dati sensibili dalla memoria del processo di SQL ServerSQL Server o eseguendo query sul database.

I criteri che determinano quali dati sono sensibili e se i dati sensibili devono essere crittografati in memoria e non accessibili agli amministratori in testo non crittografato, sono specifici per l'organizzazione e le normative di conformità che è necessario rispettare. Si prega di [consultare](#identify-and-tag-sensitive-data)il requisito correlato: Identificare e taggare i dati sensibili .

**Modalità di implementazione**:

- Usare [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) per garantire che i dati sensibili non vengano esposti in testo non crittografato nel database SQL di Azure, anche in memoria o in uso. Always Encrypted protegge i dati dagli amministratori di database (DBA) e dagli amministratori cloud (o da utenti non attivi che possono rappresentare utenti con privilegi elevati ma non autorizzati) e offre un maggiore controllo su chi può accedere ai dati.

**Procedure consigliate**:

- Always Encrypted non è un sostituto per crittografare i dati inattivi (TDE) o in transito (SSL/TLS). Always Encrypted non deve essere utilizzato per i dati non sensibili per ridurre al minimo l'impatto sulle prestazioni e sulle funzionalità. L'utilizzo di Always Encrypted in combinazione con TDE e Transport Layer Security (TLS) è consigliato per una protezione completa dei dati inattivi, in transito e in uso. 

- Valutare l'impatto della crittografia delle colonne di dati sensibili identificate prima di distribuire Always Encrypted in un database di produzione. In generale, Always Encrypted riduce la funzionalità delle query su colonne crittografate e presenta altre limitazioni, elencate in [Always Encrypted - Feature Details](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details). Pertanto, potrebbe essere necessario riprogettare l'applicazione per implementare nuovamente la funzionalità, una query non supporta, sul lato client o/e refactoring dello schema del database, incluse le definizioni di stored procedure, funzioni, viste e trigger. Le applicazioni esistenti potrebbero non funzionare con le colonne crittografate se non rispettano le restrizioni e le limitazioni di Always Encrypted. Mentre l'ecosistema di strumenti, prodotti e servizi Microsoft che supportano Always Encrypted è in crescita, alcuni di essi non funzionano con le colonne crittografate. La crittografia di una colonna può influire anche sulle prestazioni delle query, a seconda delle caratteristiche del carico di lavoro. 

- Gestire le chiavi Always Encrypted con separazione dei ruoli se si utilizza Always Encrypted per proteggere i dati da amministratori di database dannosi. Con la separazione dei ruoli, un amministratore della sicurezza crea le chiavi fisiche. L'amministratore di database crea gli oggetti dei metadati della chiave di crittografia di crittografia della colonna e della colonna, descrivendo le chiavi fisiche, nel database. Durante questo processo, l'amministratore della sicurezza non ha bisogno di accedere al database e l'amministratore di database non ha bisogno di accedere alle chiavi fisiche in testo non crittografato. 
  - Vedere l'articolo Gestione delle chiavi con la [separazione](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) dei ruoli per i dettagli. 

- Archiviare le chiavi master delle colonne in Archiviazione delle chiavi di Azure per semplificare la gestione. Evitare di usare l'archivio certificati di Windows (e, in generale, le soluzioni dell'archivio chiavi distribuite, in contrapposizione alle soluzioni di gestione delle chiavi centrali) che semplificano la gestione delle chiavi. 

- Esaminare attentamente i compromessi dell'utilizzo di più chiavi (chiavi di crittografia master di colonna o chiavi di crittografia di colonna). Mantenere il numero di chiavi ridotto per ridurre i costi di gestione delle chiavi. Una chiave master di una colonna e una chiave di crittografia di una colonna per database sono in genere sufficienti in ambienti a stato costante (non nel mezzo di una rotazione della chiave). Potrebbero essere necessarie chiavi aggiuntive se si dispone di gruppi di utenti diversi, ognuno con chiavi diverse e l'accesso a dati diversi.  

- Ruotare le chiavi master delle colonne in base ai requisiti di conformità. Se è necessario ruotare anche le chiavi di crittografia delle colonne, è consigliabile usare la crittografia online per ridurre al minimo i tempi di inattività dell'applicazione. 
  - Vedere l'articolo [Considerazioni sulle prestazioni e sulla disponibilità](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations). 

- Utilizzare la crittografia deterministica se è necessario supportare i calcoli (uguaglianza) sui dati. In caso contrario, utilizzare la crittografia casuale. Evitare di usare la crittografia deterministica per set di dati a bassa entropia o set di dati con distribuzione nota pubblicamente. 

- Se si è preoccupati che i dati di terze parti accedano legalmente senza il consenso dell'utente, assicurarsi che tutte le applicazioni e gli strumenti che hanno accesso alle chiavi e ai dati in testo non crittografato vengano eseguiti al di fuori di Microsoft Azure Cloud. Senza l'accesso alle chiavi, la terza parte non avrà modo di decrittografare i dati a meno che non bypassano la crittografia.

- Always Encrypted non supporta facilmente la concessione dell'accesso temporaneo alle chiavi (e ai dati protetti). Ad esempio, se è necessario condividere le chiavi con un amministratore di database per consentire all'amministratore di database di eseguire alcune operazioni di pulizia su dati sensibili e crittografati. L'unico modo per revocare l'accesso ai dati dall'amministratore di database consiste nel ruotare sia le chiavi di crittografia delle colonne che le chiavi master della colonna che proteggono i dati, operazione costosa. 

- Per accedere ai valori di testo non crittografato nelle colonne crittografate, un utente deve avere accesso alla CMK che protegge le colonne, configurata nell'archivio chiavi che contiene la chiave CMK. L'utente deve inoltre disporre delle autorizzazioni del database **VIEW ANY COLUMN MASTER KEY DEFINITION** e VIEW ANY COLUMN ENCRYPTION KEY **DEFINITION.**

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>Controllare l'accesso degli utenti dell'applicazione ai dati sensibili tramite la crittografia

La crittografia può essere utilizzata per garantire che solo gli utenti specifici dell'applicazione che hanno accesso alle chiavi di crittografia possano visualizzare o aggiornare i dati.

**Modalità di implementazione**:

- Utilizzare la crittografia a livello di cella (CLE). Vedere l'articolo [Crittografare una colonna di dati](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) per i dettagli. 
- Utilizzare Always Encrypted, ma essere consapevoli della sua limitazione. Le limitazioni sono elencate di seguito.

**Procedure consigliate**

Quando si utilizza CLE:

- Controllare l'accesso alle chiavi tramite autorizzazioni e ruoli SQL. 

- Utilizzare AES (consigliato AES 256) per la crittografia dei dati. Algoritmi, come RC4, DES e TripleDES, sono deprecati e non devono essere utilizzati a causa di vulnerabilità note. 

- Proteggere le chiavi simmetriche con chiavi/certificati asimmetrici (non password) per evitare l'utilizzo di 3DES. 

- Prestare attenzione quando si esegue la migrazione di un database utilizzando la crittografia a livello di cella tramite esportazione/importazione (file bacpac). 
  - Vedere l'articolo Consigli per l'uso della crittografia a livello di [cella nel database SQL](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/) di Azure su come evitare la perdita di chiavi durante la migrazione dei dati e per altre indicazioni sulle procedure consigliate.

Tenere presente che Always Encrypted è progettato principalmente per proteggere i dati sensibili in uso da utenti con privilegi elevati del database SQL di Azure (operatori cloud, amministratori di database) - vedere [Proteggere i dati sensibili in uso da utenti con privilegi elevati e non autorizzati](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users). Tenere presente le seguenti difficoltà quando si utilizza Always Encrypted per proteggere i dati dagli utenti dell'applicazione:

- Per impostazione predefinita, tutti i driver client Microsoft che supportano Always Encrypted gestiscono una cache globale (uno per applicazione) di chiavi di crittografia di colonna. Una volta che un driver client acquisisce una chiave di crittografia di colonna di testo non crittografato contattando un archivio chiavi che contiene una chiave master di colonna, la chiave di crittografia della colonna di testo non crittografato viene memorizzata nella cache. Ciò rende difficile l'isolamento dei dati da parte degli utenti di un'applicazione multiutente. Se l'applicazione rappresenta gli utenti finali quando interagisce con un archivio di chiavi (ad esempio L'insieme di credenziali delle chiavi di Azure), dopo che la query di un utente popola la cache con una chiave di crittografia della colonna, una query successiva che richiede la stessa chiave ma viene attivata da un altro utente utilizzerà la chiave memorizzata nella cache. Il driver non chiamerà l'archivio chiavi e non controlleranno se il secondo utente dispone di un'autorizzazione per accedere alla chiave di crittografia della colonna. Di conseguenza, l'utente potrà visualizzare i dati crittografati anche se l'utente non ha accesso alle chiavi. Per ottenere l'isolamento degli utenti all'interno di un'applicazione multiutente, è possibile disabilitare la memorizzazione nella cache della chiave di crittografia delle colonne. La disabilitazione della memorizzazione nella cache causerà ulteriori sovraccarichi delle prestazioni, poiché il driver dovrà contattare l'archivio chiavi per ogni operazione di crittografia o decrittografia dei dati.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>Proteggere i dati dalla visualizzazione non autorizzata da parte degli utenti dell'applicazione, preservando il formato dei dati
Un'altra tecnica per impedire agli utenti non autorizzati di visualizzare i dati consiste nell'offuscare o mascherare i dati conservando i tipi di dati e i formati per garantire che le applicazioni utente possano continuare a gestire e visualizzare i dati.

**Modalità di implementazione**:

- Utilizzare [Dynamic Data Masking](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) per offuscare le colonne della tabella.

> [!NOTE]
> Always Encrypted non funziona con Dynamic Data Masking. Non è possibile crittografare e mascherare la stessa colonna, il che implica che è necessario assegnare priorità alla protezione dei dati in uso e mascherare i dati per gli utenti dell'app tramite Dynamic Data Masking.

**Procedure consigliate**:

> [!NOTE]
> Dynamic Data Masking non può essere utilizzato per proteggere i dati da utenti con privilegi elevati. I criteri di mascheramento non si applicano agli utenti con accesso amministrativo come db_owner.

- Non consentire agli utenti dell'app di eseguire query ad hoc (poiché potrebbero essere in grado di aggirare Dynamic Data Masking).  
  - Vedere l'articolo [Bypassing masking utilizzando l'inferenza o tecniche](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) di forza bruta per i dettagli.  

- Utilizzare criteri di controllo di accesso appropriati (tramite autorizzazioni SQL, ruoli, RLS) per limitare le autorizzazioni utente per apportare aggiornamenti nelle colonne mascherate. La creazione di una maschera in una colonna non impedisce gli aggiornamenti a tale colonna. Gli utenti che ricevono dati mascherati durante l'esecuzione di query sulla colonna mascherata possono aggiornare i dati se dispongono delle autorizzazioni di scrittura.    

-  Dynamic Data Masking non mantiene le proprietà statistiche dei valori mascherati. Ciò potrebbe influire sui risultati delle query (ad esempio, query contenenti predicati di filtro o join sui dati mascherati).

## <a name="network-security"></a>Sicurezza di rete
Per sicurezza di rete si intende i controlli di accesso e le procedure consigliate per proteggere i dati in transito nel database SQL di Azure.Network security refers to access controls and best practices to secure your data in transit to Azure SQL Database.

### <a name="configure-my-client-to-connect-securely-to-azure-sql-database"></a>Configurare il client per la connessione sicura al database SQL di AzureConfigure my client to connect securely to Azure SQL Database 
Procedure consigliate su come impedire a computer client e applicazioni con vulnerabilità note (ad esempio, utilizzando protocolli TLS e suite di crittografia precedenti) di connettersi al database SQL di Azure.Best practices on how to prevent client machines and applications with well-known vulnerabilities (for example, using older TLS protocols and cipher suites) from connecting to Azure SQL Database.

**Modalità di implementazione**:

- Verificare che i computer client che si connettono al database SQL di Azure utilizzino [Transport Layer Security (TLS).](sql-database-security-overview.md#transport-layer-security-tls-encryption-in-transit)

**Procedure consigliate**:

- Configurare tutte le app e gli strumenti per connettersi al database SQL con la crittografia abilitata 
  - Crittografa, Attivato, TrustServerCertificate: disattivato (o equivalente con driver non Microsoft). 

- Se l'app usa un driver che non supporta TLS o supporta una versione precedente di TLS, sostituisci il driver, se possibile. Se non è possibile, valutare attentamente i rischi per la sicurezza. 

- Ridurre i vettori di attacco tramite vulnerabilità in SSL 2.0, SSL 3.0, TLS 1.0 e TLS 1.1 disabilitandoli nei computer client che si connettono alle impostazioni del Registro di sistema di Azure SQL Database per [Transport Layer Security (TLS).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10) 

- Controllare i pacchetti di crittografia disponibili sul client: [Suite di crittografia in TLS/SSL (SSP Schannel)](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel). In particolare, disabilitare 3DES per [Configurazione dell'ordine](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order)di suite di crittografia TLS . 

- Per il database SQL di Azure, la crittografia viene applicata per i tipi di connessione Proxy e Reindirizza.For Azure SQL Database, encryption is enforced for both Proxy and Redirect connection types. Se si usa un'istanza gestita, usare il tipo di connessione **Proxy** (impostazione predefinita) in quanto applica la crittografia dal lato server. Il tipo di connessione **Reindirizza** attualmente non supporta l'imposizione della crittografia ed è disponibile solo per le connessioni IP private. 

- Per altre informazioni, vedere Architettura della [connettività SQL](sql-database-connectivity-architecture.md#connection-policy)di Azure - Criteri di connessione.


### <a name="minimize-attack-surface"></a>Riduci al minimo la superficie di attacco
Ridurre al minimo il numero di funzionalità che possono essere attaccate da un utente malintenzionato. Implementare i controlli di accesso di rete per il database SQL di Azure.Implement network access controls for Azure SQL Database.

> Menzionato in: OSA Practice #5

**Modalità di implementazione**:

In un server di database SQL di Azure (contenente database singleton o pool elastici):In an Azure SQL Database server (containing singleton database or elastic pools):
- Impostare Consenti accesso ai servizi di Azure su OFF.

- Usare gli endpoint del servizio VNet e le regole del firewall della rete virtuale.

- Usa private Link (anteprima).

In un'istanza gestita:In a managed instance:
- Seguire le linee guida in [Requisiti di rete](sql-database-managed-instance-connectivity-architecture.md#network-requirements). 

**Procedure consigliate**:

- Limitazione dell'accesso al database SQL di Azure tramite connessione in un endpoint privato (ad esempio, usando un percorso dati privato):Restricting access to Azure SQL Database by connecting on a private endpoint (for example, using a private data path): 
  - Un'istanza gestita può essere isolata all'interno di una rete virtuale per impedire l'accesso esterno. Le applicazioni e gli strumenti che si trovano nella stessa rete virtuale o con mapping nella stessa area possono accedervi direttamente. Le applicazioni e gli strumenti che si trovano in un'area diversa potrebbero usare la connessione da VNet a VNet o il peering del circuito ExpressRoute per stabilire la connessione. Il cliente deve usare i gruppi di sicurezza di rete (NSG) per limitare l'accesso sulla porta 1433 solo alle risorse che richiedono l'accesso a un'istanza gestita 
  - Per un server di database SQL (contenente singoli database o pool elastici), usare la funzionalità [Di collegamento](sql-database-private-endpoint-overview.md) privato che fornisce un indirizzo IP privato dedicato per il server di database SQL all'interno della rete virtuale. È inoltre possibile utilizzare gli endpoint del [servizio VNet con le regole del firewall Della rete virtuale](sql-database-vnet-service-endpoint-rule-overview.md) per limitare l'accesso ai server di database SQL.
  - Gli utenti mobili devono utilizzare connessioni VPN da punto a sito per connettersi tramite il percorso dati.
  - Gli utenti connessi alla rete locale devono usare la connessione VPN da sito a sito o ExpressRoute per connettersi tramite il percorso dati.

- È possibile accedere al database SQL di Azure connettendosi a un endpoint pubblico, ad esempio usando un percorso dati pubblico. Devono essere prese in considerazione le seguenti procedure consigliate: 
  - Per un server di database SQL, utilizzare [le regole del firewall IP](sql-database-firewall-configure.md) per limitare l'accesso solo agli indirizzi IP autorizzati.
  - Per un'istanza gestita, usare Gruppi di sicurezza di rete per limitare l'accesso tramite la porta 3342 solo alle risorse necessarie. Per altre informazioni, vedere [Usare un'istanza gestita del database SQL](sql-database-managed-instance-public-endpoint-securely.md)di Azure in modo sicuro con endpoint pubblici. 

> [!NOTE]
> Un endpoint pubblico di istanza gestita non è abilitato per impostazione predefinita e deve essere abilitato in modo esplicito. Se i criteri aziendali non consentono l'uso di endpoint pubblici, usare Criteri di [Azure](../governance/policy/overview.md) per impedire in primo luogo l'abilitazione di endpoint pubblici.

- Configurare i componenti di rete di Azure:Set up Azure Networking components: 
  - Seguire [le procedure consigliate di Azure per la sicurezza](../security/fundamentals/network-best-practices.md)di rete .
  - Pianificare la configurazione della rete virtuale (VNet) in base alle procedure consigliate descritte nelle [domande frequenti (FAQ)](../virtual-network/virtual-networks-faq.md) della rete virtuale di Azure e pianificare. 
  - Segmentare una rete virtuale in più subnet e assegnare le risorse per un ruolo simile alla stessa subnet (ad esempio, risorse front-end o back-end).
  - Usare i gruppi di sicurezza di rete per controllare il traffico tra subnet all'interno del limite della rete virtuale di Azure.Use [Network Security Groups (NSGs)](../virtual-network/security-overview.md) to control traffic between subnets within the Azure VNet boundary.
  - Abilitare [Controllo rete di Azure](../network-watcher/network-watcher-monitoring-overview.md) per la sottoscrizione per monitorare il traffico di rete in ingresso e in uscita.

### <a name="configure-power-bi-for-secure-connections-to-azure-sql-database"></a>Configurare Power BI per le connessioni protette al database SQL di AzureConfigure Power BI for secure connections to Azure SQL Database

**Procedure consigliate**:

- Per Power BI Desktop, usare il percorso dati privato quando possibile. 

- Verificare che Power BI Desktop si connetta utilizzando TLS1.2 impostando la chiave del Registro di sistema nel computer client in base alle impostazioni del Registro di sistema [TLS (Transport Layer Security).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) 

- Limitare l'accesso ai dati per utenti specifici tramite la sicurezza a [livello di riga con Power BI.](https://docs.microsoft.com/power-bi/service-admin-rls) 

- Per il servizio Power BI, usare il [gateway dati locale,](https://docs.microsoft.com/power-bi/service-gateway-onprem)tenendo presente [limitazioni e considerazioni.](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway)

### <a name="configure-app-service-for-secure-connections-to-azure-sql-database"></a>Configurare il servizio app per le connessioni protette al database SQL di AzureConfigure App Service for secure connections to Azure SQL Database

**Procedure consigliate**:

- Per un'app Web semplice, la connessione tramite endpoint pubblico richiede l'impostazione **Consenti servizi di Azure** su ON. 

- [Integrare l'app con](../app-service/web-sites-integrate-with-vnet.md) una rete virtuale di Azure per la connettività dei percorsi dati privati a un'istanza gestita. Facoltativamente, è anche possibile distribuire un'app Web con ambienti del [servizio app (ASE)](../app-service/environment/intro.md). 

- Per Web App con ambiente app o VNet Integrated Web App che si connette a un database nel server di database SQL, è possibile utilizzare gli endpoint del [servizio VNet e le regole del firewall della rete virtuale](sql-database-vnet-service-endpoint-rule-overview.md) per limitare l'accesso da una rete virtuale e una subnet specifiche. Impostare **quindi Consenti servizi di Azure** su OFF. È anche possibile connettere l'asino di impostazioni del servizio app a un'istanza gestita tramite un percorso di dati privato.  

- Verificare che l'app Web sia configurata in base all'articolo Procedure consigliate per la protezione delle [applicazioni Web e per dispositivi mobili PaaS tramite](../security/security-paas-applications-using-app-services.md)il servizio app di Azure . 

- Installare [Web Application Firewall (WAF)](../application-gateway/waf-overview.md) per proteggere l'app Web da exploit e vulnerabilità comuni.

### <a name="configure-azure-vm-hosting-for-secure-connections-to-azure-sql-database"></a>Configurare l'hosting delle macchine virtuali di Azure per le connessioni sicure al database SQL di AzureConfigure Azure VM hosting for secure connections to Azure SQL Database

**Procedure consigliate**:

- Usare una combinazione di regole Consenti e Nega nei gruppi di sicurezza di rete delle macchine virtuali di Azure per controllare quali aree sono accessibili dalla macchina virtuale.

- Verificare che la macchina virtuale sia configurata in base all'articolo Procedure consigliate per la sicurezza per i carichi di lavoro IaaS in Azure.Ensure that your VM is configured in the article, [Security best practices for IaaS workloads in Azure.](../security/azure-security-iaas.md)

- Assicurarsi che tutte le macchine virtuali siano associate a una rete virtuale e una subnet specifiche. 

- Valutare se è necessaria la route predefinita 0.0.0.0/Internet per le linee guida relative al [tunneling forzato](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling). 
  - Se sì, ad esempio una subnet front-end, mantenere la route predefinita.
  - Se nessuna, ad esempio, livello intermedio o subnet back-end, abilitare il tunneling forzato in modo che nessun traffico passi tramite Internet per raggiungere in locale (noto anche come cross-premise). 

- Implementare [route predefinite facoltative](../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) se si usa il peering o la connessione a locale. 

- Implementare [route definite dall'utente](../virtual-network/virtual-networks-udr-overview.md#user-defined) se è necessario inviare tutto il traffico nella rete virtuale a un accessorio virtuale di rete per l'ispezione dei pacchetti. 

- Usare gli endpoint del servizio VNet per l'accesso sicuro ai servizi PaaS come Archiviazione di Azure tramite la rete backbone di Azure.Use [VNet Service endpoints](sql-database-vnet-service-endpoint-rule-overview.md) for secure access to PaaS services like Azure Storage via the Azure backbone network. 

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>Protezione dagli attacchi DDoS (Distributed Denial of Service)
Gli attacchi DDoS (Distributed Denial of Service) sono tentativi da parte di un utente malintenzionato di inviare un'inondazione di traffico di rete al database SQL di Azure con l'obiettivo di sovraccaricare l'infrastruttura di Azure e causare il rifiuto di account di accesso e carico di lavoro validi.

> Menzionato in: OSA Practice #9

**Modalità di implementazione**:

DDoS protection is automatically enabled as part of the Azure Platform. Include il monitoraggio del traffico sempre in onda e la mitigazione in tempo reale degli attacchi a livello di rete agli endpoint pubblici. 

- Usare [Protezione DDoS di Azure](../virtual-network/ddos-protection-overview.md) per monitorare gli indirizzi IP pubblici associati alle risorse distribuite nelle reti virtuali.

- Usare [Advanced Threat Protection per](sql-database-threat-detection-overview.md) il database SQL di Azure per rilevare gli attacchi DoS (Denial of Service) sui database.

**Procedure consigliate**:

- Seguire le procedure descritte in Ridurre al minimo la superficie di [attacco](#minimize-attack-surface) consente di ridurre al minimo le minacce agli attacchi DDoS. 

- L'avviso **credenziali SQL forza bruta** protezione avanzata minaccia consente di rilevare gli attacchi di forza bruta. In alcuni casi, l'avviso può anche distinguere i carichi di lavoro di test di penetrazione. 

- Per le applicazioni di hosting vm di Azure che si connettono al database SQL:For Azure VM hosting applications connecting to SQL Database: 
  - Seguire le raccomandazioni per limitare l'accesso tramite endpoint con connessione Internet nel Centro sicurezza di Azure.Follow recommendation to Restrict access through Internet-facing endpoints in Azure Security Center. 
  - Usare i set di scalabilità delle macchine virtuali per eseguire più istanze dell'applicazione nelle macchine virtuali di Azure.Use virtual machine scale sets to run multiple instances of your application on Azure VMs. 
  - Disabilitare RDP e SSH da Internet per evitare attacchi di forza bruta. 

## <a name="monitoring-logging-and-auditing"></a>Monitoraggio, registrazione e controllo  
In questa sezione si fa riferimento alle funzionalità che consentono di rilevare attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database. Vengono inoltre descritte le procedure consigliate per configurare il controllo del database per tenere traccia e acquisire gli eventi del database.

### <a name="protect-databases-against-attacks"></a>Proteggere i database dagli attacchi 
La protezione avanzata dalle minacce consente di rilevare e rispondere alle potenziali minacce non appena si verificano fornendo avvisi di sicurezza per le attività anomale.

**Modalità di implementazione**:

- Utilizzare [Advanced Threat Protection per SQL](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts) per rilevare tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database, tra cui:
  - Attacco SQL injection.
  - Furto/perdita di credenziali.
  - Abuso di privilegi.
  - Esfiltrazione dei dati.

**Procedure consigliate**:

- Configurare [Advanced Data Security (ADS)](sql-database-advanced-data-security.md#getting-started-with-ads) per il database SQL di Azure per un server di database SQL specifico o un'istanza gestita. È anche possibile configurare ADS per tutti i server di database SQL e le istanze gestite in una sottoscrizione passando al [livello Standard del Centro sicurezza di Azure.](../security-center/security-center-pricing.md) 

- Per un'esperienza di analisi completa, è consigliabile abilitare il [controllo del database SQL.](sql-database-auditing.md) Con il controllo è possibile tenere traccia degli eventi del database e scriverli in un log di controllo in un account di Archiviazione di Azure o nell'area di lavoro di Azure Log Analytics.With auditing, you can track database events and write them to an audit log in an Azure Storage account or Azure Log Analytics workspace. 

### <a name="audit-critical-security-events"></a>Controllare gli eventi di sicurezza criticiAudit critical security events
Il rilevamento degli eventi del database consente di comprendere l'attività del database. È possibile ottenere informazioni su discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza. Consente inoltre e facilita l'aderenza agli standard di conformità. 

**Modalità di implementazione**:

- Abilitare il [controllo del database SQL](sql-database-auditing.md) per tenere traccia degli eventi del database e scriverli in un log di controllo nell'account di Archiviazione di Azure, nell'area di lavoro log Analytics (anteprima) o negli hub eventi (anteprima). 

- I log di controllo possono essere scritti in un account di Archiviazione di Azure, in un'area di lavoro di Log Analytics per l'utilizzo da parte dei log di Monitoraggio di Azure o nell'hub eventi per l'utilizzo tramite l'hub eventi. È possibile configurare qualsiasi combinazione di queste opzioni e verranno scritti i log di controllo per ognuno. 

**Procedure consigliate**:

- Configurando il [controllo del database SQL](sql-database-auditing.md) nel server di database per controllare gli eventi, verranno controllati tutti i database esistenti e appena creati in tale server.
- Per impostazione predefinita, i criteri di controllo includono tutte le azioni (query, stored procedure e account di accesso riusciti e non riusciti) sui database, che possono comportare un volume elevato di log di controllo. È consigliabile per i clienti configurare il [controllo per diversi tipi di azioni e gruppi di azioni tramite PowerShell](sql-database-auditing.md#manage-auditing). La configurazione consente di controllare il numero di azioni controllate e di ridurre al minimo il rischio di perdita di eventi. La configurazione di controllo personalizzata consente ai clienti di acquisire solo i dati di controllo necessari.
- I log di controllo possono essere utilizzati direttamente nel portale di [Azure](https://portal.azure.com/)o dal percorso di archiviazione configurato. 


> [!NOTE]
> L'abilitazione del controllo a Log Analytics comporta costi basati sui tassi di inserimento. Tenere presente il costo associato con l'uso di questa opzione o archiviare i log di controllo in un account di archiviazione di Azure.Please be aware of the associated cost with using this [option](https://azure.microsoft.com/pricing/details/monitor/), or consider storing the audit logs in an Azure storage account. 

**Ulteriori risorse**:

- [Controllo del database SQL](sql-database-auditing.md)
- [Controllo di SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) 

### <a name="secure-audit-logs"></a>Registri di controllo protetti
Limitare l'accesso all'account di archiviazione per supportare la separazione dei doveri e separare l'amministratore di database dai revisori. 

**Modalità di implementazione**:

- Quando si salvano i log di controllo in Archiviazione di Azure, assicurarsi che l'accesso all'account di archiviazione sia limitato ai principi di sicurezza minimi. Controllare chi ha accesso all'account di archiviazione.
    - Per altre informazioni, vedere [Autorizzazione dell'accesso ad Archiviazione di Azure.For](../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)more information, see Authorizing access to Azure Storage.

**Procedure consigliate**:

- Il controllo dell'accesso alla destinazione di controllo è un concetto chiave nella separazione dell'amministratore di database dai revisori. 

- Quando si controlla l'accesso ai dati sensibili, è consigliabile proteggere i dati con la crittografia dei dati per evitare perdite di informazioni al revisore. Per ulteriori informazioni, vedere la sezione [Proteggere i dati sensibili in uso da utenti con privilegi elevati e non autorizzati](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users).

## <a name="security-management"></a>Gestione della sicurezza

In questa sezione vengono descritti i diversi aspetti e procedure consigliate per la gestione dei requisiti di sicurezza dei database. Include procedure consigliate per garantire che i database siano configurati per soddisfare gli standard di sicurezza, per l'individuazione e la classificazione e il rilevamento dell'accesso a dati potenzialmente sensibili nei database. 

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>Verificare che i database siano configurati per soddisfare le procedure consigliate per la sicurezzaEnsure that the database(s) are configured to meet security best practices 

Migliora in modo proattivo la sicurezza del database individuando e correggindo potenziali vulnerabilità del database.

**Modalità di implementazione**:

- Abilitare [SQL Vulnerability Assessment](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) (VA) per eseguire la scansione del database alla ricerca di problemi di sicurezza e per l'esecuzione periodica nei database.

**Procedure consigliate**:

- Inizialmente, eseguire VA sui database e iterare correggiando i controlli non riusciti che si oppongono alle procedure consigliate per la sicurezza. Impostare le linee di base per le configurazioni accettabili fino a quando la scansione non viene fuori _pulito_o tutti i controlli sono stati superati.  

- Configurare scansioni periodiche ricorrenti in modo che vengano eseguite una volta alla settimana e configurare la persona pertinente per ricevere messaggi di posta elettronica di riepilogo. 

- Esaminare il riepilogo VA dopo ogni scansione settimanale. Per eventuali vulnerabilità rilevate, valutare la deriva dal risultato dell'analisi precedente e determinare se il controllo deve essere risolto. Verificare se esiste un motivo legittimo per la modifica della configurazione.   

- Risolvere i controlli e aggiornare le previsioni, se pertinenti. Creare elementi di ticket per la risoluzione delle azioni e tenere traccia di questi fino a quando non vengono risolti. 

**Ulteriori risorse**:

- [Valutazione della vulnerabilità SQL](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 
- [Servizio Valutazione della vulnerabilità di SQL per identificare le vulnerabilità dei database](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>Identificare e contrassegnare i dati sensibili 

Individuare le colonne che potenzialmente contengono dati sensibili. I dati considerati sensibili dipendono in larga misura dal cliente, dalla normativa di conformità e, ecc., e devono essere valutati dagli utenti responsabili di tali dati. Classificare le colonne per utilizzare scenari avanzati di controllo e protezione basati sulla riservatezza. 

**Modalità di implementazione**:

- Usare [L'individuazione e](sql-database-data-discovery-and-classification.md) la classificazione dei dati SQL per individuare, classificare, etichettare e proteggere i dati sensibili nei database. 
  - Visualizzare i suggerimenti di classificazione creati dall'individuazione automatica nel dashboard Individuazione e classificazione dati SQL. Accettare le classificazioni pertinenti, in modo che i dati sensibili vengano contrassegnati in modo permanente con etichette di classificazione. 
  - Aggiungere manualmente le classificazioni per eventuali campi dati sensibili aggiuntivi non individuati dal meccanismo automatico. 
- Per ulteriori informazioni, vedere [Classificazione & di individuazione dati SQL](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification).

**Procedure consigliate**:

- Monitorare regolarmente il dashboard di classificazione per una valutazione accurata dello stato di classificazione del database. Un report sullo stato di classificazione del database può essere esportato o stampato per la conformità e il controllo.

- Monitorare continuamente lo stato dei dati sensibili consigliati in SQL Vulnerability Assessment. Tenere traccia della regola di individuazione dei dati sensibili e identificare eventuali deriva nelle colonne consigliate per la classificazione.  

- Utilizzare la classificazione in modo personalizzato in base alle esigenze specifiche dell'organizzazione. Personalizzare i criteri di Information Protection (etichette di riservatezza, tipi di informazioni, logica di individuazione) nei criteri di SQL Information Protection nel Centro sicurezza di Azure.Customize your Information Protection policy (sensitivity labels, information types, discovery logic) in the [SQL Information Protection](../security-center/security-center-info-protection-policy.md) policy in Azure Security Center. 

### <a name="track-access-to-sensitive-data"></a>Tenere traccia dell'accesso ai dati sensibili 
Monitorare chi accede ai dati sensibili e acquisire query su dati sensibili nei log di controllo.

**Modalità di implementazione**:

- Utilizzare il controllo SQL e la classificazione dei dati in combinazione. 
  - Nel log di [controllo del database SQL](sql-database-auditing.md) è possibile tenere traccia dell'accesso specifico ai dati sensibili. È inoltre possibile visualizzare informazioni quali i dati a cui è stato effettuato l'accesso, nonché l'etichetta di riservatezza. Per ulteriori informazioni, vedere [Individuazione dei dati & Classificazione](sql-database-data-discovery-and-classification.md) e Controllo [dell'accesso ai dati sensibili.](sql-database-data-discovery-and-classification.md#audit-sensitive-data) 

**Procedure consigliate**:

- Vedere le procedure consigliate per le sezioni Controllo e Classificazione dei dati:See best practices for the Auditing and Data Classification sections: 
  - [Controllare gli eventi di sicurezza criticiAudit critical security events](#audit-critical-security-events) 
  - [Identificare e contrassegnare i dati sensibili](#identify-and-tag-sensitive-data) 

### <a name="visualize-security-and-compliance-status"></a>Visualizza lo stato di sicurezza e conformità 

Utilizzare un sistema di gestione della sicurezza dell'infrastruttura unificata che rafforzi il livello di sicurezza dei data center (inclusi i database SQL). Visualizzare un elenco di suggerimenti relativi alla sicurezza dei database e allo stato di conformità.

**Modalità di implementazione**:

- Monitorare i consigli di sicurezza correlati a SQL e le minacce attive nel Centro sicurezza di [Azure.](https://azure.microsoft.com/documentation/services/security-center/)

## <a name="common-security-threats-and-potential-mitigations"></a>Minacce alla sicurezza comuni e potenziali attenuazioni

Questa sezione consente di trovare misure di sicurezza per la protezione da determinati vettori di attacco. Si prevede che la maggior parte delle attenuazioni può essere raggiunto seguendo una o più delle linee guida di sicurezza di cui sopra.

### <a name="security-threat-data-exfiltration"></a>Minaccia alla sicurezza: esfiltrazione dei dati

L'esfiltrazione dei dati è la copia, il trasferimento o il recupero non autorizzato di dati da un computer o server. Vedi una definizione per [l'esfiltrazione dei dati](https://en.wikipedia.org/wiki/Data_exfiltration) su Wikipedia.

La connessione al server di database SQL di Azure tramite un endpoint pubblico presenta un rischio di esfiltrazione dei dati in quanto richiede ai clienti di aprire i firewall agli indirizzi IP pubblici.  

**Scenario 1:** un'applicazione in una macchina virtuale di Azure si connette a un database in un server di database SQL di Azure.Scenario 1: An application on an Azure VM connects to a database in an Azure SQL Database server. Un attore canaglia ottiene l'accesso alla macchina virtuale e la compromette. In questo scenario, l'esfiltrazione dei dati significa che un'entità esterna che usa la macchina virtuale non autorizzata si connette al database, copia i dati personali e li archivia in un archivio BLOB o in un database SQL diverso in una sottoscrizione diversa.

**Scenario 2**: Un amministratore di database Rouge. Questo scenario viene spesso generato da clienti sensibili alla sicurezza provenienti da settori regolamentati. In questo scenario, un utente con privilegi elevati potrebbe copiare i dati dal database SQL di Azure a un'altra sottoscrizione non controllata dal proprietario dei dati.

**Potenziali attenuazioni**:

Oggi, il database SQL di Azure offre le tecniche seguenti per attenuare le minacce di esfiltrazione dei dati:Today, Azure SQL Database offers the following techniques for mitigating data exfiltration threats: 

- Usare una combinazione di regole Consenti e Nega nei gruppi di sicurezza di rete delle macchine virtuali di Azure per controllare quali aree sono accessibili dalla macchina virtuale. 
- Se si usa un server di database SQL di Azure (contenente database singleton o pool elastici), impostare le opzioni seguenti:If using an Azure SQL Database server (containing singleton databases or elastic pools), set the following options:
  - Consentire ai servizi di Azure di OFF.
  - Consentire il traffico dalla subnet contenente la macchina virtuale di Azure configurando una regola del firewall della rete virtuale.
  - Usa [collegamento privato](sql-database-private-endpoint-overview.md)
- Per un'istanza gestita, l'utilizzo dell'accesso IP privato per impostazione predefinita risolve gli indirizzi del primo problema di esfiltrazione dei dati di una macchina virtuale non autorizzata. Attivare la funzionalità di delega della subnet in una subnet per impostare automaticamente i criteri più restrittivi in una subnet dell'istanza gestita.
- Il problema dell'amministratore di database Rogue è più esposto con un'istanza gestita in quanto ha una superficie più ampia e i requisiti di rete sono visibili ai clienti. La migliore attenuazione per questo è l'applicazione di tutte le procedure in questa guida alla sicurezza per evitare lo scenario di amministratore di database Rogue in primo luogo (non solo per l'esfiltrazione dei dati). Always Encrypted è un metodo per proteggere i dati sensibili crittografandoli e mantenendo la chiave inaccessibile per l'amministratore di database.

## <a name="security-aspects-of-business-continuity-and-availability"></a>Aspetti di sicurezza della continuità e della disponibilità aziendale

La maggior parte degli standard di sicurezza riguarda la disponibilità dei dati in termini di continuità operativa, ottenuta implementando funzionalità di ridondanza e failover per evitare singoli punti di errore. Per gli scenari di emergenza, è prassi comune mantenere i backup dei file di dati e di log.The following section provides a high-level overview of the capabilities that are built-into Azure. Fornisce inoltre opzioni aggiuntive che possono essere configurate per soddisfare esigenze specifiche:It also provides additional options that can be configured to meet specific needs: 

- Azure offre disponibilità elevata incorporata: disponibilità elevata e database SQL di AzureAzure offers built-in high-availability: [High-availability and Azure SQL Database](sql-database-high-availability.md) 

- Il livello Business Critical include gruppi di failover, zone a disponibilità multipla, backup del log completi e differenziali e backup di ripristino temporizzato abilitati per impostazione predefinita:  
  - [Database SQL di Azure e a disponibilità elevata - Configurazione ridondante dell'areaHigh-availability and Azure SQL Database - Zone redundant configuration](sql-database-high-availability.md#zone-redundant-configuration)
  - [Backup automatizzati](sql-database-automated-backups.md)
  - [Ripristinare un database SQL di Azure usando backup automatici del database - ripristino temporizzatoRecover an Azure SQL Database using automated database backups - Point-in-time restore](sql-database-recovery-using-backups.md#point-in-time-restore)

- Altre funzionalità di continuità aziendale, ad esempio i gruppi di failover automatico in diversi geo di Azure, possono essere configurate come descritto di seguito: [Panoramica della continuità aziendale con](sql-database-business-continuity.md) il database SQL di AzureAdditional business continuity features such as auto-failover groups across different Azure geos can be configured as described here: Overview of business continuity with Azure SQL Database

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica delle funzionalità di sicurezza del database SQL di AzureSee An overview of Azure SQL Database security capabilities](sql-database-security-overview.md)
