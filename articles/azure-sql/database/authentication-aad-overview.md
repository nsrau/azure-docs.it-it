---
title: Autenticazione di Azure Active Directory
description: Informazioni su come usare Azure Active Directory per l'autenticazione con il database SQL di Azure, Azure SQL Istanza gestita e Azure sinapsi Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 04/23/2020
ms.openlocfilehash: e1f97b1a29681a65cddf3a5904020555e903fe63
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85984776"
---
# <a name="use-azure-active-directory-authentication"></a>Usa autenticazione Azure Active Directory

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

L'autenticazione Azure Active Directory (Azure AD) è un meccanismo per la connessione al [database SQL](sql-database-paas-overview.md)di Azure, ad [Azure SQL istanza gestita](../managed-instance/sql-managed-instance-paas-overview.md)e ad [Azure sinapsi Analytics (in precedenza Azure SQL Data Warehouse)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) usando le identità in Azure ad.

> [!NOTE]
> Questo articolo si applica al database SQL di Azure, a SQL Istanza gestita e ad Azure sinapsi Analytics.

Con l'autenticazione di Azure AD è possibile gestire centralmente le identità degli utenti del database e altri servizi Microsoft. La gestione centrale degli ID consente di gestire gli utenti del database da un unico punto e semplifica la gestione delle autorizzazioni. Ecco alcuni vantaggi:

- Offre un'alternativa all'autenticazione di SQL Server.
- Consente di arrestare la proliferazione di identità utente tra i server.
- Consente la rotazione delle password in un'unica posizione.
- I clienti possono gestire le autorizzazioni del database usando gruppi (Azure AD) esterni.
- Può eliminare l'archiviazione delle password abilitando l'autenticazione integrata di Windows e altre forme di autenticazione supportate da Azure Active Directory.
- L'autenticazione di Azure SD usa gli utenti di un database indipendente per autenticare le identità a livello di database.
- Azure AD supporta l'autenticazione basata su token per le applicazioni che si connettono al database SQL e a SQL Istanza gestita.
- Azure AD autenticazione supporta:
  - Azure AD le identità solo cloud.
  - Azure AD identità ibride che supportano:
    - Autenticazione cloud con due opzioni abbinate all'autenticazione **pass-through** seamless Single Sign-on (SSO) e all'autenticazione con **hash delle password** .
    - Autenticazione federata.
  - Per ulteriori informazioni sui metodi di autenticazione Azure AD e su quale scegliere, vedere l'articolo seguente:
    - [Scegliere il metodo di autenticazione appropriato per la soluzione ibrida di gestione delle identità di Azure AD](../../active-directory/hybrid/choose-ad-authn.md)

- Azure AD supporta le connessioni da SQL Server Management Studio che usano l'autenticazione universale di Active Directory, che include Multi-Factor Authentication. Multi-Factor Authentication include l'autenticazione avanzata con una gamma di semplici opzioni di verifica, ad esempio telefonata, SMS, smart card con pin o notifica tramite app per dispositivi mobili. Per altre informazioni, vedere [supporto di SSMS per Azure AD multi-factor authentication con il database SQL di Azure, sql istanza gestita e la sinapsi di Azure](authentication-mfa-ssms-overview.md)

- Azure AD supporta connessioni analoghe da SQL Server Data Tools (SSDT) che usano l'autenticazione interattiva di Active Directory. Per ulteriori informazioni, vedere [supporto Azure Active Directory in SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory)

> [!NOTE]  
> La connessione a un'istanza di SQL Server in esecuzione in una macchina virtuale (VM) di Azure non è supportata con un account di Azure Active Directory. Usare un account Active Directory di dominio.  

I passaggi di configurazione includono le procedure seguenti per configurare e usare l'autenticazione di Azure Active Directory.

1. Creare e popolare un'istanza di Azure AD.
2. Facoltativo: associare o modificare l'istanza di Active Directory attualmente associata alla sottoscrizione di Azure.
3. Creare un amministratore Azure Active Directory.
4. Configurare i computer client.
5. Creare gli utenti di database indipendente nel database di cui è stato eseguito il mapping alle identità di Azure AD.
6. Connettersi al database usando le identità di Azure AD.

> [!NOTE]
> Per informazioni su come creare e popolare Azure AD e quindi configurare Azure AD con il database SQL di Azure, SQL Istanza gestita e la sinapsi di Azure, vedere [configurare Azure ad con il database SQL di Azure](authentication-aad-configure.md).

## <a name="trust-architecture"></a>Architettura di attendibilità

- Solo la parte cloud di Azure AD, database SQL, SQL Istanza gestita e la sinapsi di Azure viene considerata per supportare Azure AD password utente Native.
- Per supportare le credenziali di Windows Single Sign-On (o utente/password per le credenziali di Windows), usare Azure Active Directory le credenziali di un dominio federato o gestito configurato per l'Single Sign-On trasparente per l'autenticazione pass-through e hash delle password. Per altre informazioni, vedere [Accesso Single Sign-On facile di Azure Active Directory](../../active-directory/hybrid/how-to-connect-sso.md).
- Per supportare l'autenticazione federata o l'autenticazione utente/password per le credenziali di Windows, è necessaria la comunicazione con il blocco AD FS.

Per ulteriori informazioni su Azure AD identità ibride, l'installazione e la sincronizzazione, vedere gli articoli seguenti:

- Autenticazione dell'hash delle password: [implementare la sincronizzazione dell'hash delle password con Azure ad Connect sincronizzazione](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Autenticazione pass-through: [autenticazione pass-through Azure Active Directory](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Autenticazione federata- [distribuzione di Active Directory Federation Services in Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) e [Azure ad Connect e Federazione](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

Per un'autenticazione federata di esempio con l'infrastruttura ADFS (oppure utente/password per le credenziali di Windows), vedere il diagramma seguente. Le frecce indicano i percorsi di comunicazione.

![diagramma di autenticazione di aad][1]

Il diagramma seguente indica le relazioni federative, di trust e di hosting che consentono a un client di connettersi a un database inviando un token, che viene autenticato da Azure Active Directory e considerato attendibile dal database. Il cliente 1 può rappresentare un'istanza di Azure Active Directory con utenti nativi o con utenti federati. Il cliente 1 può rappresentare una Azure Active Directory con utenti nativi o una Azure AD con utenti federati. Il cliente 2 rappresenta una possibile soluzione, inclusi gli utenti importati, in questo esempio proveniente da un Azure Active Directory federato con ADFS sincronizzato con Azure Active Directory. È importante comprendere che l'accesso a un database con l'autenticazione di Azure AD richiede che la sottoscrizione di hosting sia associata ad Azure AD. È necessario usare la stessa sottoscrizione per creare il database SQL di Azure, SQL Istanza gestita o le risorse di Azure sinapsi.

![relazione di sottoscrizione][2]

## <a name="administrator-structure"></a>Struttura dell'account amministratore

Quando si usa l'autenticazione di Azure AD, sono disponibili due account amministratore: l'amministratore del database SQL di Azure originale e l'amministratore di Azure AD. Gli stessi concetti si applicano alla sinapsi di Azure. Solo l'amministratore basato su un account Azure AD può creare il primo utente di database indipendente di Azure AD in un database utente. L'account di accesso dell'amministratore di Azure AD può essere un utente o un gruppo di Azure AD. Quando l'amministratore è un account di gruppo, può essere usato da qualsiasi membro del gruppo, abilitando più Azure AD amministratori per il server. L'uso di un account di gruppo come amministratore migliora la gestibilità consentendo di aggiungere e rimuovere in modo centralizzato i membri del gruppo in Azure AD senza modificare gli utenti o le autorizzazioni nel database SQL o nella sinapsi di Azure. È possibile configurare un solo amministratore di Azure AD (utente o gruppo) alla volta.

![struttura di amministrazione][3]

## <a name="permissions"></a>Autorizzazioni

Per creare nuovi utenti, è necessario avere l'autorizzazione `ALTER ANY USER` sul database. L'autorizzazione `ALTER ANY USER` può esser concessa a qualsiasi utente di database. L'autorizzazione `ALTER ANY USER` è assegnata anche agli account amministratore del server, agli utenti di database con l'autorizzazione `CONTROL ON DATABASE` o `ALTER ON DATABASE` per tale database e ai membri del ruolo del database `db_owner`.

Per creare un utente di database indipendente nel database SQL di Azure, in SQL Istanza gestita o in una sinapsi di Azure, è necessario connettersi al database o all'istanza usando un'identità Azure AD. Per creare il primo utente di database indipendente, è necessario connettersi al database tramite un amministratore di Azure AD (che corrisponde al proprietario del database). Questa operazione è illustrata in [configurare e gestire l'autenticazione Azure Active Directory con il database SQL o la sinapsi di Azure](authentication-aad-configure.md). Azure AD autenticazione è possibile solo se è stato creato l'amministratore Azure AD per il database SQL di Azure, SQL Istanza gestita o la sinapsi di Azure. Se l'amministratore di Azure Active Directory è stato rimosso dal server, gli utenti di Azure Active Directory esistenti creati in precedenza all'interno di SQL Server non possono più connettersi al database con le credenziali di Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Funzionalità e limitazioni di Azure AD

- È possibile eseguire il provisioning dei seguenti membri di Azure AD per il database SQL di Azure:

  - Membri nativi: un membro creato in Azure AD nel dominio gestito o in un dominio del cliente. Per altre informazioni, vedere [Aggiungere un nome di dominio personalizzato ad Azure AD](../../active-directory/fundamentals/add-custom-domain.md).
  - I membri di un dominio Active Directory federati con Azure Active Directory in un dominio gestito configurato per Single Sign-On Seamless con l'autenticazione pass-through o di hash della password. Per ulteriori informazioni, vedere [Microsoft Azure supporta ora la Federazione con Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) e [Azure Active Directory accesso Single Sign-on](../../active-directory/hybrid/how-to-connect-sso.md)facile.
  - Membri importati da altre istanze di Azure AD che sono membri nativi o del dominio federato.
  - Gruppi di Active Directory creati come gruppi di sicurezza.

- Azure AD utenti che fanno parte di un gruppo che dispone di un `db_owner` ruolo del server non possono usare la sintassi di **[creazione di credenziali con ambito database](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** per il database SQL di Azure e la sinapsi di Azure. Verrà visualizzato l'errore seguente:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Assegnare il ruolo `db_owner` direttamente all'utente di Azure AD per mitigare il problema **CREATE DATABASE SCOPED CREDENTIAL**.

- Queste funzioni di sistema restituiscono valori NULL quando vengono eseguite nell'ambito di entità di sicurezza di Azure AD:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="sql-managed-instance"></a>Istanza gestita di SQL

- Azure AD entità server (account di accesso) e gli utenti sono supportati per [SQL istanza gestita](../managed-instance/sql-managed-instance-paas-overview.md).
- L'impostazione di Azure AD entità server (account di accesso) con mapping a un gruppo di Azure AD come proprietario del database non è supportata in [SQL istanza gestita](../managed-instance/sql-managed-instance-paas-overview.md).
  - Un'estensione è che quando un gruppo viene aggiunto come parte del `dbcreator` ruolo del server, gli utenti di questo gruppo possono connettersi al istanza gestita SQL e creare nuovi database, ma non potranno accedere al database. Il nuovo proprietario del database, infatti, è SA e non l'utente di Azure AD. Questo problema non si verifica se al ruolo del server `dbcreator` si aggiunge il singolo utente.
- La gestione e l'esecuzione di processi di SQL Agent sono supportate per Azure AD entità server (account di accesso).
- Le operazioni di backup e ripristino del database possono essere eseguite dalle entità server (account di accesso) di Azure AD.
- È supportato il controllo di tutte le istruzioni relative agli eventi di autenticazione e alle entità server (account di accesso) di Azure AD.
- È supportata la connessione amministrativa dedicata per le entità server (account di accesso) di Azure AD membri del ruolo del server sysadmin.
  - È supportata tramite l'utilità SQLCMD e SQL Server Management Studio.
- Sono supportati i trigger di accesso per gli eventi di accesso provenienti dalle entità server (account di accesso) di Azure AD.
- Usando un'entità server (account di accesso) di Azure AD è possibile configurare Service Broker e la posta elettronica del database.

## <a name="connect-by-using-azure-ad-identities"></a>Connettersi usando le identità di Azure AD

L'autenticazione di Azure Active Directory supporta i metodi seguenti per la connessione a un database con le identità di Azure AD:

- Password di Azure Active Directory
- Autenticazione integrata di Azure Active Directory
- Azure Active Directory universale con Multi-Factor Authentication
- Con l'autenticazione del token dell'applicazione

Per Azure AD entità server (account di accesso) sono supportati i metodi di autenticazione seguenti:

- Password di Azure Active Directory
- Autenticazione integrata di Azure Active Directory
- Azure Active Directory universale con Multi-Factor Authentication

### <a name="additional-considerations"></a>Altre considerazioni

- Per migliorare la gestibilità, è consigliabile effettuare il provisioning di un gruppo di Azure AD dedicato come amministratore.
- È possibile configurare un solo amministratore di Azure AD (utente o gruppo) per un server nel database SQL o in una sinapsi di Azure in qualsiasi momento.
  - L'aggiunta di Azure AD entità server (account di accesso) per SQL Istanza gestita consente di creare più entità di Azure AD server (account di accesso) che possono essere aggiunte al `sysadmin` ruolo.
- Solo un amministratore Azure AD per il server può connettersi inizialmente al server o all'istanza gestita usando un account di Azure Active Directory. L'amministratore di Active Directory può configurare gli utenti del database di Azure AD successivi.
- È consigliabile impostare il timeout di connessione su 30 secondi.
- SQL Server 2016 Management Studio e SQL Server Data Tools per Visual Studio 2015, versione 14.0.60311.1 di aprile 2016 o successiva, supportano l'autenticazione di Azure Active Directory. L'autenticazione di Azure AD è supportata dal **provider di dati .NET Framework per server SQL** a partire da .NET Framework versione 4.6. Pertanto, le versioni più recenti di questi strumenti e applicazioni livello dati (DAC e BACPAC) possono usare l'autenticazione Azure AD.
- A partire dalla versione 15.0.1, l' [utilità sqlcmd](/sql/tools/sqlcmd-utility) e l' [utilità bcp](/sql/tools/bcp-utility) supportano Active Directory autenticazione interattiva con multi-factor authentication.
- SQL Server Data Tools per Visual Studio 2015 richiede almeno la versione 14.0.60311.1 di aprile 2016. Attualmente, gli utenti Azure AD non vengono visualizzati nella Esplora oggetti di SSDT. Come soluzione alternativa è possibile visualizzare gli utenti in [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
- [Microsoft JDBC Driver 6.0 per server SQL](https://www.microsoft.com/download/details.aspx?id=11774) supporta l'autenticazione di Azure AD. Vedere anche l'argomento su come [impostare le proprietà della connessione](/sql/connect/jdbc/setting-the-connection-properties).
- PolyBase non può eseguire l'autenticazione di Azure AD.
- Azure AD autenticazione è supportata per il database SQL di Azure e la sinapsi di Azure usando il portale di Azure **importare database** ed esportare i pannelli del **database** . L'importazione e l'esportazione tramite l'autenticazione Azure AD è supportata anche da un comando di PowerShell.
- Azure AD autenticazione è supportata per il database SQL, SQL Istanza gestita e la sinapsi di Azure tramite l'interfaccia della riga di comando. Per altre informazioni, vedere [configurare e gestire l'autenticazione di Azure ad con il database SQL o la sinapsi di Azure](authentication-aad-configure.md) e [SQL Server-AZ SQL Server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come creare e popolare un'istanza di Azure AD e quindi configurarla con il database SQL di Azure, SQL Istanza gestita o la sinapsi di Azure, vedere [configurare e gestire l'autenticazione di Azure Active Directory con il database SQL, il istanza gestita SQL o la sinapsi di Azure](authentication-aad-configure.md).
- Per un'esercitazione sull'utilizzo di Azure AD entità server (account di accesso) con SQL Istanza gestita, vedere [Azure ad entità server (account di accesso) con sql istanza gestita](../managed-instance/aad-security-configure-tutorial.md)
- Per una panoramica degli account di accesso, degli utenti, dei ruoli del database e delle autorizzazioni nel database SQL, vedere [account di accesso, utenti, ruoli del database e autorizzazioni](logins-create-manage.md).
- Per altre informazioni sulle entità di database, vedere [Entità](https://msdn.microsoft.com/library/ms181127.aspx).
- Per altre informazioni sui ruoli del database, vedere [Ruoli a livello di database](https://msdn.microsoft.com/library/ms189121.aspx).
- Per la sintassi per la creazione di Azure AD entità server (account di accesso) per SQL Istanza gestita, vedere [creare un account di accesso](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).
- Per informazioni generali sulle regole del firewall, vedere l'articolo relativo alle [regole del firewall per il database SQL](firewall-configure.md).

<!--Image references-->
[1]: ./media/authentication-aad-overview/1aad-auth-diagram.png
[2]: ./media/authentication-aad-overview/2subscription-relationship.png
[3]: ./media/authentication-aad-overview/3admin-structure.png
