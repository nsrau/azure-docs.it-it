---
title: Azure Active Directory
description: Informazioni su come usare Azure Active Directory per l'autenticazione con il database SQL, l'istanza gestita e Azure Synapse Analytics
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 03/27/2020
ms.openlocfilehash: 58f40bc7406048df2b052bea799bcbf6466fbbae
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421098"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Usare l'autenticazione di Azure Active Directory per l'autenticazione con SQL

L'autenticazione di Azure Active Directory è un meccanismo di connessione al [database SQL](sql-database-technical-overview.md)di Azure, [all'istanza gestita](sql-database-managed-instance.md)e ad [Azure Synapse Analytics (in precedenza Azure SQL Data Warehouse)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) usando le identità in Azure Active Directory (Azure AD). 

> [!NOTE]
> Questo articolo si applica al server SQL di Azure e sia al database SQL che allo synapsi di Azure.This article applies to Azure SQL server, and both TO SQL Database and Azure Synapse. Per semplicità, il database SQL viene usato quando si fa riferimento sia al database SQL che allo synapsi di Azure.For simplicity, SQL Database is used when referring to both SQL Database and Azure Synapse.

Con l'autenticazione di Azure AD è possibile gestire centralmente le identità degli utenti del database e altri servizi Microsoft. La gestione centrale degli ID consente di gestire gli utenti del database da un unico punto e semplifica la gestione delle autorizzazioni. Ecco alcuni vantaggi:

- Offre un'alternativa all'autenticazione di SQL Server
- Contribuisce ad arrestare la proliferazione delle identità utente nei server di database.
- Consente la rotazione delle password in un'unica posizione.
- I clienti possono gestire le autorizzazioni del database usando gruppi (Azure AD) esterni
- Può eliminare l'archiviazione delle password abilitando l'autenticazione integrata di Windows e altre forme di autenticazione supportate da Azure Active Directory
- L'autenticazione di Azure SD usa gli utenti di un database indipendente per autenticare le identità a livello di database
- Azure AD supporta l'autenticazione basata su token per le applicazioni che si connettono a database SQL
- L'autenticazione di Azure AD supporta:Azure AD authentication supports:
  - Identità solo cloud di Azure ADAzure AD cloud-only identities
  - Identità ibride di Azure AD che supportano:Azure AD hybrid identities that support:
    - Autenticazione cloud con due opzioni abbinate all'accesso Single Sign-On (SSO) semplice: autenticazione **pass-through** e autenticazione **hash password**
    - Autenticazione federata
  - Per altre informazioni sui metodi di autenticazione di Azure AD e su quale scegliere, vedere l'articolo seguente:For more information on Azure AD authentication methods and which one to choose, see the following article:
    - [Scegliere il metodo di autenticazione appropriato per la soluzione ibrida di gestione delle identità di Azure AD](../active-directory/hybrid/choose-ad-authn.md)
- Azure AD supporta le connessioni da SQL Server Management Studio che utilizzano l'autenticazione universale di Active Directory, che include l'MFA (Multi-Factor Authentication).  L'MFA include funzionalità avanzate di autenticazione con una serie di semplici opzioni di verifica, tra cui: chiamata telefonica, SMS, smart card con pin o notifica tramite app per dispositivi mobili. Per altre informazioni, vedere Supporto di SSMS per l'autenticazione a più fattori di Azure AD con il database SQL e lo synapsi di AzureFor more information, see [SSMS support for Azure AD MFA with SQL Database and Azure Synapse](sql-database-ssms-mfa-authentication.md)
- Azure AD supporta connessioni analoghe da SQL Server Data Tools (SSDT) che usano l'autenticazione interattiva di Active Directory. Per altre informazioni, vedere [Supporto di Azure Active Directory in SQL Server Data Tools (SSDT)For more information, see Azure Active Directory support in SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory)

> [!NOTE]  
> La connessione a SQL Server in esecuzione in una VM di Azure non è supportata con un account Azure Active Directory. Usare un account Active Directory di dominio.  

I passaggi di configurazione includono le procedure seguenti per configurare e usare l'autenticazione di Azure Active Directory.

1. Creare e popolare un'istanza di Azure AD.
2. Facoltativo: associare o modificare l'istanza di Active Directory attualmente associata alla sottoscrizione di Azure.
3. Creare un amministratore di Azure Active Directory per il server di database SQL di Azure, l'istanza gestita o [Azure Synapse](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Configurare i computer client.
5. Creare gli utenti di database indipendente nel database di cui è stato eseguito il mapping alle identità di Azure AD.
6. Connettersi al database usando le identità di Azure AD.

> [!NOTE]
> Per informazioni su come creare e popolare Azure AD e quindi configurare Azure AD con il database SQL di Azure, l'istanza gestita e lo synapse di Azure, vedere [Configurare Azure AD con](sql-database-aad-authentication-configure.md)il database SQL di Azure.

## <a name="trust-architecture"></a>Architettura di attendibilità

- Per supportare la password utente nativa di Azure AD, viene considerata solo la parte cloud e Azure AD o il database SQL di Azure.
- Per supportare le credenziali Single Sign-On di Windows (o credenziali utente/password per Windows), usare le credenziali di Azure Active Directory da un dominio federato o gestito configurato per l'accesso Single Sign-On senza problemi per l'autenticazione hash pass-through e password. Per altre informazioni, vedere [Azure Active Directory Seamless Single Sign-On](../active-directory/hybrid/how-to-connect-sso.md).
- Per supportare l'autenticazione federata o l'autenticazione utente/password per le credenziali di Windows, è necessaria la comunicazione con il blocco AD FS.

Per altre informazioni sulle identità ibride di Azure AD, la configurazione e la sincronizzazione, vedere gli articoli seguenti:For more information on Azure AD hybrid identities, the setup, and synchronization, see the following articles:

- Autenticazione hash password- Implementare la [sincronizzazione dell'hash delle password con](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) la sincronizzazione di Azure AD ConnectPassword hash authentication - Implement password hash synchronization with Azure AD Connect sync
- Autenticazione pass-through - [Autenticazione pass-through di Azure Active](../active-directory/hybrid/how-to-connect-pta-quick-start.md) DirectoryPass-through authentication - Azure Active Directory Pass-through Authentication
- Autenticazione federata - [Distribuzione di Active Directory Federation Services in Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) e Azure AD Connect e [federazione](../active-directory/hybrid/how-to-connect-fed-whatis.md)

Per un'autenticazione federata di esempio con l'infrastruttura ADFS (o utente/password per le credenziali di Windows), vedere il diagramma seguente. Le frecce indicano i percorsi di comunicazione.

![diagramma di autenticazione di aad][1]

Il diagramma seguente indica le relazioni federative, di trust e di hosting che consentono a un client di connettersi a un database inviando un token, che viene autenticato da Azure Active Directory e considerato attendibile dal database. Il cliente 1 può rappresentare un'istanza di Azure Active Directory con utenti nativi o con utenti federati. Il cliente 1 può rappresentare una Azure Active Directory con utenti nativi o una Azure AD con utenti federati. In questo esempio provengono da un'istanza federata di Azure Active Directory con AD FS sincronizzato con Azure Active Directory. È importante comprendere che l'accesso a un database con l'autenticazione di Azure AD richiede che la sottoscrizione di hosting sia associata ad Azure AD. The same subscription must be used to create the SQL Server hosting the Azure SQL Database or Azure Synapse.

![relazione di sottoscrizione][2]

## <a name="administrator-structure"></a>Struttura dell'account amministratore

Quando si usa l'autenticazione di Azure AD, sono disponibili due account amministratore per il server di database SQL e l'istanza gestita. l'amministratore originale di SQL Server e l'amministratore di Azure AD. Gli stessi concetti si applicano ad Azure Synapse.The same concepts apply to Azure Synapse. Solo l'amministratore basato su un account Azure AD può creare il primo utente di database indipendente di Azure AD in un database utente. L'account di accesso dell'amministratore di Azure AD può essere un utente o un gruppo di Azure AD. Quando l'amministratore è un account di gruppo, può essere usato da qualsiasi membro del gruppo, abilitando quindi più amministratori di Azure AD per l'istanza di SQL Server. L'uso dell'account di gruppo come amministratore migliora la gestibilità, perché consente di aggiungere e rimuovere a livello centrale i membri del gruppo in Azure AD senza apportare modifiche a utenti o autorizzazioni nel database SQL. È possibile configurare un solo amministratore di Azure AD (utente o gruppo) alla volta.

![struttura di amministrazione][3]

## <a name="permissions"></a>Autorizzazioni

Per creare nuovi utenti, è necessario avere l'autorizzazione `ALTER ANY USER` sul database. L'autorizzazione `ALTER ANY USER` può esser concessa a qualsiasi utente di database. L'autorizzazione `ALTER ANY USER` è assegnata anche agli account amministratore del server, agli utenti di database con l'autorizzazione `CONTROL ON DATABASE` o `ALTER ON DATABASE` per tale database e ai membri del ruolo del database `db_owner`.

Per creare un utente del database indipendente nel database SQL di Azure, nell'istanza gestita o in Azure Synapse, è necessario connettersi al database o all'istanza usando un'identità di Azure AD. Per creare il primo utente di database indipendente, è necessario connettersi al database tramite un amministratore di Azure AD (che corrisponde al proprietario del database). Ciò è illustrato in [Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL o Azure Synapse](sql-database-aad-authentication-configure.md). Qualsiasi autenticazione di Azure AD è possibile solo se l'amministratore di Azure AD è stato creato per il database SQL di Azure o Azure Synapse.Any Azure AD authentication is only possible if the Azure AD admin was created for Azure SQL Database or Azure Synapse. Se l'amministratore di Azure Active Directory è stato rimosso dal server, gli utenti di Azure Active Directory esistenti creati in precedenza all'interno di SQL Server non possono più connettersi al database con le credenziali di Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Funzionalità e limitazioni di Azure AD

- È possibile eseguire il provisioning dei membri seguenti di Azure AD nel server SQL di Azure o in Azure Synapse:The following members of Azure AD can be provisioned in Azure SQL server or Azure Synapse:

  - Membri nativi: un membro creato in Azure AD nel dominio gestito o in un dominio del cliente. Per altre informazioni, vedere [Aggiungere un nome di dominio personalizzato ad Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
  - Membri di un dominio di Active Directory federati con Azure Active Directory in un dominio gestito configurato per l'accesso Single Sign-On senza soluzione di continuità con autenticazione hash pass-through o password. Per altre informazioni, vedere [Microsoft Azure supporta ora la federazione con Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) e Azure Active Directory Seamless Single [Sign-On](../active-directory/hybrid/how-to-connect-sso.md).
  - Membri importati da altre istanze di Azure AD che sono membri nativi o del dominio federato.
  - Gruppi di Active Directory creati come gruppi di sicurezza.

- Gli utenti di Azure AD che `db_owner` fanno parte di un gruppo con ruolo del server non possono usare la sintassi CREATE DATABASE SCOPED CREDENTIAL nel database SQL di Azure e nello synapsi di Azure.Azure AD users that are part of a group that has server role cannot use the **[CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** syntax against Azure SQL Database and Azure Synapse. Verrà visualizzato l'errore seguente:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Assegnare il ruolo `db_owner` direttamente all'utente di Azure AD per mitigare il problema **CREATE DATABASE SCOPED CREDENTIAL**.

- Queste funzioni di sistema restituiscono valori NULL quando vengono eseguite nell'ambito di entità di sicurezza di Azure AD:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>Istanze gestite

- Le entità server di Azure AD (account di accesso) e gli utenti sono supportati come funzionalità di anteprima per [le istanze gestite.](sql-database-managed-instance.md)
- L'impostazione delle entità server di Azure AD (account di accesso) mappata a un gruppo di Azure AD come proprietario del database non è supportata nelle [istanze gestite.](sql-database-managed-instance.md)
    - Un'estensione di questo è che quando un `dbcreator` gruppo viene aggiunto come parte del ruolo del server, gli utenti di questo gruppo possono connettersi all'istanza gestita e creare nuovi database, ma non saranno in grado di accedere al database. Il nuovo proprietario del database, infatti, è SA e non l'utente di Azure AD. Questo problema non si verifica se al ruolo del server `dbcreator` si aggiunge il singolo utente.
- La gestione di SQL Agent e l'esecuzione dei processi sono supportate per le entità server di Azure AD (account di accesso).
- Le operazioni di backup e ripristino del database possono essere eseguite dalle entità server (account di accesso) di Azure AD.
- È supportato il controllo di tutte le istruzioni relative agli eventi di autenticazione e alle entità server (account di accesso) di Azure AD.
- È supportata la connessione amministrativa dedicata per le entità server (account di accesso) di Azure AD membri del ruolo del server sysadmin.
    - È supportata tramite l'utilità SQLCMD e SQL Server Management Studio.
- Sono supportati i trigger di accesso per gli eventi di accesso provenienti dalle entità server (account di accesso) di Azure AD.
- Usando un'entità server (account di accesso) di Azure AD è possibile configurare Service Broker e la posta elettronica del database.


## <a name="connecting-using-azure-ad-identities"></a>Connettersi usando le identità di Azure AD

L'autenticazione di Azure Active Directory supporta i metodi seguenti per la connessione a un database con le identità di Azure AD:

- Password di Azure Active Directory
- Autenticazione integrata di Azure Active Directory
- Autenticazione universale di Azure Active Directory con MFA
- Con l'autenticazione del token dell'applicazione

I metodi di autenticazione seguenti sono supportati per le entità server (account di accesso di Azure AD):

- Password di Azure Active Directory
- Autenticazione integrata di Azure Active Directory
- Autenticazione universale di Azure Active Directory con MFA


### <a name="additional-considerations"></a>Altre considerazioni

- Per migliorare la gestibilità, è consigliabile effettuare il provisioning di un gruppo di Azure AD dedicato come amministratore.   
- Solo un amministratore di Azure AD (un utente o un gruppo) può essere configurato per un server di database SQL di Azure o Azure Synapse in qualsiasi momento.
  - L'aggiunta di entità server di Azure AD (account di accesso) per le istanze gestite consente `sysadmin` di creare più entità server di Azure AD (account di accesso) che possono essere aggiunte al ruolo.
- Solo un amministratore di Azure AD per SQL Server può inizialmente connettersi al server di database SQL di Azure, all'istanza gestita o ad Azure Synapse usando un account Azure Active Directory.Only an Azure AD administrator for SQL Server can initially connect to the Azure SQL Database server, managed instance, or Azure Synapse using an Azure Active Directory account. L'amministratore di Active Directory può configurare gli utenti del database di Azure AD successivi.   
- È consigliabile impostare il timeout di connessione su 30 secondi.   
- SQL Server 2016 Management Studio e SQL Server Data Tools per Visual Studio 2015, versione 14.0.60311.1 di aprile 2016 o successiva, supportano l'autenticazione di Azure Active Directory. L'autenticazione di Azure AD è supportata dal **provider di dati .NET Framework per server SQL** a partire da .NET Framework versione 4.6. Di conseguenza, le versioni più recenti di questi strumenti e applicazioni del livello dati (DAC e BACPAC) possono usare l'autenticazione di Azure AD.   
- A partire dalla versione 15.0.1, l'[utilità sqlcmd](/sql/tools/sqlcmd-utility) e l'[utilità bcp](/sql/tools/bcp-utility) supportano l'autenticazione interattiva di Active Directory con MFA.
- SQL Server Data Tools per Visual Studio 2015 richiede almeno la versione 14.0.60311.1 di aprile 2016. Gli utenti di Azure AD non sono attualmente visualizzati in Esplora oggetti di SSDT. Come soluzione alternativa è possibile visualizzare gli utenti in [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
- [Microsoft JDBC Driver 6.0 per server SQL](https://www.microsoft.com/download/details.aspx?id=11774) supporta l'autenticazione di Azure AD. Vedere anche l'argomento su come [impostare le proprietà della connessione](https://msdn.microsoft.com/library/ms378988.aspx).   
- PolyBase non può eseguire l'autenticazione di Azure AD.   
- L'autenticazione di Azure AD è supportata per il database SQL nei pannelli **Importa database** ed **Esporta database** del portale di Azure. L'importazione e l'esportazione tramite l'autenticazione di Azure AD è supportata anche dal comando PowerShell.   
- L'autenticazione di Azure AD è supportata per il database SQL, l'istanza gestita e lo synapse di Azure tramite cli. Per altre informazioni, vedere [Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL o Azure Synapse](sql-database-aad-authentication-configure.md) e [SQL Server - az sql server.](https://docs.microsoft.com/cli/azure/sql/server)

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come creare e popolare Azure AD e quindi configurare Azure AD con il database SQL di Azure o Azure Synapse, vedere [Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL, l'istanza gestita o Azure Synapse.](sql-database-aad-authentication-configure.md)
- Per un'esercitazione sull'uso delle entità server di Azure AD (account di accesso) con le istanze gestite, vedere Entità server di Azure AD (account di accesso) con istanze gestiteFor a tutorial of using Azure AD server principals (logins) with managed instances, see [Azure AD server principals (logins) with managed instances](sql-database-managed-instance-aad-security-tutorial.md)
- Per una panoramica di account di accesso, utenti, ruoli del database e autorizzazioni nel database SQL, vedere [Account di accesso, utenti, ruoli del database e autorizzazioni](sql-database-manage-logins.md).
- Per altre informazioni sulle entità di database, vedere [Entità](https://msdn.microsoft.com/library/ms181127.aspx).
- Per altre informazioni sui ruoli del database, vedere [Ruoli a livello di database](https://msdn.microsoft.com/library/ms189121.aspx).
- Per la sintassi sulla creazione di entità server di Azure AD (account di accesso) per le istanze gestite, vedere [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).
- Per informazioni generali sulle regole del firewall, vedere l'articolo relativo alle [regole del firewall per il database SQL](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png
