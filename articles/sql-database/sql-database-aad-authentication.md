---
title: Autenticazione di Azure Active Directory - Azure SQL (Panoramica) | Documentazione Microsoft
description: Informazioni su come usare Azure Active Directory per l'autenticazione di un database SQL e di SQL Data Warehouse
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 7e2508a1-347e-4f15-b060-d46602c5ce7e
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/12/2017
ms.author: rickbyh
ms.openlocfilehash: 246c89be85a4ccd2e8f7fab0ae8128e7285123a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql-database-or-sql-data-warehouse"></a>Usare l'autenticazione di Azure Active Directory per l'autenticazione di un database SQL o di SQL Data Warehouse
L'autenticazione di Azure Active Directory è un meccanismo di connessione al database SQL di Microsoft Azure e a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) tramite le identità di Azure Active Directory (Azure AD). Con l'autenticazione di Azure AD è possibile gestire centralmente le identità degli utenti del database e altri servizi Microsoft. La gestione centrale degli ID consente di gestire gli utenti del database da un unico punto e semplifica la gestione delle autorizzazioni. Ecco alcuni vantaggi:

* Offre un'alternativa all'autenticazione di SQL Server.
* Contribuisce ad arrestare la proliferazione delle identità utente nei server di database.
* Consente la rotazione delle password in un'unica posizione.
* I clienti possono gestire le autorizzazioni del database tramite gruppi (AAD) esterni.
* Può eliminare l'archiviazione delle password abilitando l'autenticazione integrata di Windows e altre forme di autenticazione supportate da Azure Active Directory.
* L'autenticazione di Azure SD usa gli utenti di un database indipendente per autenticare le identità a livello di database.
* Azure AD supporta l'autenticazione basata su token per le applicazioni che si connettono a database SQL.
* L'autenticazione di Azure AD supporta la federazione dei domini di AD FS o l'autenticazione utente/password nativa per un'istanza locale di Azure Active Directory senza la sincronizzazione del dominio.  
* Azure AD supporta le connessioni da SQL Server Management Studio che utilizzano l'autenticazione universale di Active Directory, che include l'MFA (Multi-Factor Authentication).  L'MFA include funzionalità avanzate di autenticazione con una serie di semplici opzioni di verifica, tra cui: chiamata telefonica, SMS, smart card con pin o notifica tramite app per dispositivi mobili. Per altre informazioni [Supporto di SQL Server Management Studio (SSMS) per l'autenticazione MFA di Azure AD con il database SQL e SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).  

>  [!NOTE]  
>  La connessione a SQL Server in esecuzione su una macchina virtuale di Azure non è supportata con un account Azure Active Directory. Usare un account Active Directory di dominio.  

I passaggi di configurazione includono le procedure seguenti per configurare e usare l'autenticazione di Azure Active Directory.

1. Creare e popolare un'istanza di Azure AD.
2. Facoltativo: associare o modificare l'istanza di Active Directory attualmente associata alla sottoscrizione di Azure.
3. Creare un amministratore di Azure Active Directory per il server di Azure SQL o per [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Configurare i computer client.
5. Creare gli utenti di database indipendente nel database di cui è stato eseguito il mapping alle identità di Azure AD.
6. Connettersi al database usando le identità di Azure AD.

> [!NOTE]
> Per informazioni su come creare e popolare Azure AD e quindi configurare Azure AD con il database SQL di Azure e SQL Data Warehouse, vedere [Configurare Azure AD con il database SQL di Azure](sql-database-aad-authentication-configure.md).
>

## <a name="trust-architecture"></a>Architettura di attendibilità
Il diagramma generale seguente riepiloga l'architettura della soluzione relativa all'uso dell'autenticazione di Azure AD con il database SQL di Azure SQL. Gli stessi concetti sono validi per SQL Data Warehouse. Per supportare la password utente nativa di Azure AD, viene considerata solo la parte cloud e Azure AD o il database SQL di Azure. Per supportare l'autenticazione federata o l'autenticazione utente/password per le credenziali di Windows, è necessaria la comunicazione con il blocco AD FS. Le frecce indicano i percorsi di comunicazione.

![diagramma di autenticazione di aad][1]

Il diagramma seguente indica le relazioni federative, di trust e di hosting che consentono a un client di connettersi a un database inviando un token, che viene autenticato da Azure Active Directory e considerato attendibile dal database. Il cliente 1 può rappresentare un'istanza di Azure Active Directory con utenti nativi o con utenti federati. Il cliente 1 può rappresentare una Azure Active Directory con utenti nativi o una Azure AD con utenti federati. In questo esempio provengono da un'istanza federata di Azure Active Directory con AD FS sincronizzato con Azure Active Directory. È importante comprendere che l'accesso a un database con l'autenticazione di Azure AD richiede che la sottoscrizione di hosting sia associata ad Azure AD. La stessa sottoscrizione deve essere utilizzata per creare il Server SQL che ospita l'Azure SQL Data Warehouse o il database SQL di Azure.

![relazione di sottoscrizione][2]

## <a name="administrator-structure"></a>Struttura dell'account amministratore
Quando si usa l'autenticazione di Azure AD, sono disponibili due account amministratore per il server di database SQL, l'amministratore del server SQL originale e l'amministratore di Azure AD. Gli stessi concetti sono validi per SQL Data Warehouse. Solo l'amministratore basato su un account Azure AD può creare il primo utente di database indipendente di Azure AD in un database utente. L'account di accesso dell'amministratore di Azure AD può essere un utente o un gruppo di Azure AD. Quando l'amministratore è un account di gruppo, può essere usato da qualsiasi membro del gruppo, abilitando quindi più amministratori di Azure AD per l'istanza di SQL Server. L'uso dell'account di gruppo come amministratore migliora la gestibilità, perché consente di aggiungere e rimuovere a livello centrale i membri del gruppo in Azure AD senza apportare modifiche a utenti o autorizzazioni nel database SQL. È possibile configurare un solo amministratore di Azure AD (utente o gruppo) alla volta.

![struttura di amministrazione][3]

## <a name="permissions"></a>Autorizzazioni
Per creare nuovi utenti, è necessario avere l'autorizzazione `ALTER ANY USER` sul database. L'autorizzazione `ALTER ANY USER` può esser concessa a qualsiasi utente di database. L'autorizzazione `ALTER ANY USER` è assegnata anche agli account amministratore del server, agli utenti di database con l'autorizzazione `CONTROL ON DATABASE` o `ALTER ON DATABASE` per tale database e ai membri del ruolo del database `db_owner`.

Per creare un utente di database indipendente nel database SQL di Azure o in SQL Data Warehouse, è necessario connettesi al database con un'identità di Azure AD. Per creare il primo utente di database indipendente, è necessario connettersi al database tramite un amministratore di Azure AD (che corrisponde al proprietario del database). Questa procedura è illustrata in [Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL oppure con SQL Data Warehouse](sql-database-aad-authentication-configure.md). L'autenticazione di Azure AD è possibile unicamente se l'amministratore di Azure AD è stato creato per il server di database SQL di Azure o SQL Data Warehouse. Se l'amministratore di Azure Active Directory è stato rimosso dal server, gli utenti di Azure Active Directory esistenti creati in precedenza all'interno di SQL Server non possono più connettersi al database con le credenziali di Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Funzionalità e limitazioni di Azure AD
È possibile eseguire il provisioning dei membri di Azure AD seguenti nel server di Azure SQL o in SQL Data Warehouse:

* Membri nativi: un membro creato in Azure AD nel dominio gestito o in un dominio del cliente. Per altre informazioni, vedere [Aggiungere un nome di dominio personalizzato ad Azure AD](../active-directory/active-directory-add-domain.md).
* Membri del dominio federato: un membro creato in Azure AD con un dominio federato. Per altre informazioni, vedere il post di blog relativo al [nuovo supporto per la federazione in Microsoft Azure con Active Directory di Windows Server](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/).
* Membri importati da altre istanze di Azure AD che sono membri nativi o del dominio federato.
* Gruppi di Active Directory creati come gruppi di sicurezza.


## <a name="connecting-using-azure-ad-identities"></a>Connettersi usando le identità di Azure AD

L'autenticazione di Azure Active Directory supporta i metodi seguenti per la connessione a un database con le identità di Azure AD:

* Con l'autenticazione integrata di Windows
* Con un nome di entità e una password di Azure AD
* Con l'autenticazione del token dell'applicazione

### <a name="additional-considerations"></a>Ulteriori considerazioni

* Per migliorare la gestibilità, è consigliabile effettuare il provisioning di un gruppo di Azure AD dedicato come amministratore.   
* È possibile configurare un solo amministratore di Azure AD, utente o gruppo, per un server di Azure SQL o Azure SQL Data Warehouse in qualsiasi momento.   
* Inizialmente solo un amministratore di Azure AD per server SQL può connettersi a SQL Server di Azure o a SQL Data Warehouse di Azure con un account Azure Active Directory. L'amministratore di Active Directory può configurare gli utenti del database di Azure AD successivi.   
* È consigliabile impostare il timeout di connessione su 30 secondi.   
* SQL Server 2016 Management Studio e SQL Server Data Tools per Visual Studio 2015, versione 14.0.60311.1 di aprile 2016 o successiva, supportano l'autenticazione di Azure Active Directory. L'autenticazione di Azure AD è supportata dal **provider di dati .NET Framework per server SQL** a partire da .NET Framework versione 4.6. Di conseguenza, le versioni più recenti di questi strumenti e applicazioni del livello dati (DAC e file con estensione bacpac) possono usare l'autenticazione di Azure AD.   
* [ODBC versione 13.1](https://www.microsoft.com/download/details.aspx?id=53339) supporta l'autenticazione di Azure Active Directory, tuttavia `bcp.exe` non può connettersi mediante l'autenticazione di Azure Active Directory perché usa un provider ODBC meno recente.   
* `sqlcmd` supporta l'autenticazione di Azure Active Directory iniziando con la versione 13.1 disponibile in [Download Center](http://go.microsoft.com/fwlink/?LinkID=825643).   
* SQL Server Data Tools per Visual Studio 2015 richiede almeno la versione 14.0.60311.1 di aprile 2016. Gli utenti di Azure AD non sono attualmente visualizzati in Esplora oggetti di SSDT. Come soluzione alternativa è possibile visualizzare gli utenti in [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
* [Microsoft JDBC Driver 6.0 per server SQL](https://www.microsoft.com/download/details.aspx?id=11774) supporta l'autenticazione di Azure AD. Vedere anche l'argomento su come [impostare le proprietà della connessione](https://msdn.microsoft.com/library/ms378988.aspx).   
* PolyBase non può eseguire l'autenticazione di Azure AD.   
* L'autenticazione di Azure AD è supportata per il database SQL nei pannelli **Importa database** ed **Esporta database** del portale di Azure. L'importazione e l'esportazione tramite l'autenticazione di Azure AD è supportata anche dal comando PowerShell.   
* L'autenticazione di Azure AD è supportata per il database SQL e SQL Data Warehouse usando l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL oppure con SQL Data Warehouse](sql-database-aad-authentication-configure.md) e [SQL Server - az sql server](https://docs.microsoft.com/en-us/cli/azure/sql/server).

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni su come creare e popolare Azure AD e quindi configurare Azure AD con il database SQL di Azure o Azure SQL Data Warehouse, vedere [Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL oppure con SQL Data Warehouse](sql-database-aad-authentication-configure.md).
- Per una panoramica dell'accesso e del controllo nel database SQL, vedere l'articolo relativo al [controllo dell'accesso al database SQL](sql-database-control-access.md).
- Per una panoramica degli account di accesso, degli utenti e dei ruoli del database nel database SQL, vedere l'articolo relativo ad [account di accesso, utenti e ruoli del database](sql-database-manage-logins.md).
- Per altre informazioni sulle entità di database, vedere [Entità](https://msdn.microsoft.com/library/ms181127.aspx).
- Per altre informazioni sui ruoli del database, vedere [Ruoli a livello di database](https://msdn.microsoft.com/library/ms189121.aspx).
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

