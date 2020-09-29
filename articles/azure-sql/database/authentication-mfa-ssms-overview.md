---
title: Uso dell'autenticazione a più fattori Azure Active Directory
description: Il database SQL di Azure, Azure SQL Istanza gestita e Azure sinapsi Analytics supportano le connessioni da SQL Server Management Studio (SSMS) usando Active Directory l'autenticazione universale.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
ms.custom: seoapril2019, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/28/2020
tags: azure-synapse
ms.openlocfilehash: e526b8e2e4f31187bb958ec37c2ffa4d30f0265b
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91461156"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>Uso dell'autenticazione a più fattori Azure Active Directory
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Il database SQL di Azure, Azure SQL Istanza gestita e Azure sinapsi Analytics supportano le connessioni da [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) usando *Azure Active Directory universale con autenticazione a* più fattori. Questo articolo descrive le differenze tra le varie opzioni di autenticazione e anche le limitazioni associate all'uso dell'autenticazione universale in Azure Active Directory (Azure AD) per Azure SQL.

**Scaricare la versione più recente di SSMS**: nel computer client scaricare la versione più recente di SSMS da [Scaricare SQL Server Management Studio (SSMS)](https://aka.ms/ssms). 

> [!Note]
> Nel 2021 dicembre, le versioni di SSMS precedenti alla 18,6 non saranno più autenticate tramite Azure Active Directory con autenticazione a più fattori. Per continuare a usare l'autenticazione Azure Active Directory con l'autenticazione a più fattori, installare o aggiornare a SSMS 18,6 o versione successiva.

Per tutte le funzionalità descritte in questo articolo, usare almeno la versione 17.2 di luglio 2017. La finestra di dialogo di connessione più recente dovrebbe avere un aspetto simile al seguente:

  ![Screenshot della finestra di dialogo Connetti al server in SQL Server Management Studio, che mostra le impostazioni per il tipo di server, il nome del server e l'autenticazione.](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png)
  
    

## <a name="authentication-options"></a>Opzioni di autenticazione

Esistono due modelli di autenticazione non interattiva per Azure AD, che possono essere usati in molte applicazioni diverse (ADO.NET, JDCB, ODC e così via). Questi due metodi non aprono mai finestre di dialogo popup:

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

Il metodo interattivo che supporta anche Azure Multi-Factor Authentication (multi-factor authentication) è: 

- `Azure Active Directory - Universal with MFA`

Azure MFA consente di salvaguardare l'accesso a dati e applicazioni soddisfacendo l'aspettativa dell'utente all'uso di un processo di accesso semplice. Offre funzionalità avanzate di autenticazione con una serie di semplici opzioni di verifica, tra cui telefonata, SMS, smart card con pin o notifica tramite app per dispositivi mobili, in modo che ogni utente possa scegliere il metodo che preferisce. La convalida di MFA interattiva con Azure AD può avvenire attraverso una finestra popup.

Per una descrizione del Multi-Factor Authentication di Azure, vedere [multi-factor authentication](../../active-directory/authentication/multi-factor-authentication.md).
Per la procedura di configurazione, vedere [Configurare Multi-Factor Authentication con database SQL di Azure per SQL Server Management Studio](authentication-mfa-ssms-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Parametro nome di dominio o ID tenant di Azure AD

A partire da [SSMS versione 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), gli utenti che vengono importati nella Azure ad corrente da altre Azure Active Directory come utenti guest possono fornire l'Azure ad nome di dominio o l'ID tenant quando si connettono. Gli utenti guest includono quelli invitati da altre istanze di Azure AD, gli account Microsoft, ad esempio outlook.com, hotmail.com, live.com, o altri account come gmail.com. Queste informazioni consentono `Azure Active Directory - Universal with MFA` all'autenticazione di identificare l'autorità di autenticazione corretta. Questa opzione è necessaria anche per supportare gli account Microsoft (MSA), ad esempio outlook.com, hotmail.com e live.com, o altri account non MSA. 

Tutti gli utenti guest che vogliono essere autenticati tramite l'autenticazione universale devono immettere il nome di dominio Azure AD o l'ID tenant. Questo parametro rappresenta il nome di dominio Azure AD o l'ID tenant corrente a cui è associato il server logico SQL di Azure. Se ad esempio il server logico SQL è associato al dominio Azure AD, in `contosotest.onmicrosoft.com` cui l'utente `joe@contosodev.onmicrosoft.com` è ospitato come utente importato dal dominio Azure ad `contosodev.onmicrosoft.com` , il nome di dominio necessario per autenticare l'utente è `contosotest.onmicrosoft.com` . Quando l'utente è un utente nativo del Azure AD associato al server logico SQL e non è un account MSA, non è necessario alcun nome di dominio o ID tenant. Per immettere il parametro (a partire dalla versione 17,2 di SSMS):


1. Aprire una connessione in SSMS. Immettere il nome del server e selezionare **Azure Active Directory universale con autenticazione a** più fattori. Aggiungere il **nome utente** con cui si vuole accedere.
1. Selezionare la casella **Opzioni** e passare alla scheda **Proprietà connessione** . Nella finestra di dialogo **Connetti al database** completare la finestra di dialogo per il database. Selezionare la casella **ID tenant o nome di dominio AD** e specificare l'autorità di autenticazione, ad esempio il nome di dominio (**contosotest.onmicrosoft.com**) o il GUID dell'ID tenant. 

   ![Screenshot della scheda delle proprietà della connessione che evidenzia le impostazioni per Connetti al database e al nome di dominio di Active Directory o all'ID tenant.](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

Se si esegue SSMS 18. x o versione successiva, l'ID tenant o il nome di dominio di Active Directory non è più necessario per gli utenti guest perché 18. x o versione successiva lo riconosce automaticamente.

   ![Screenshot della scheda Proprietà connessione nella finestra di dialogo Connetti al server in S S M. "database" è selezionato nel campo Connetti al database.](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Supporto per Azure AD business-to-business

> [!IMPORTANT]
> Il supporto per gli utenti guest per la connessione al database SQL di Azure, a SQL Istanza gestita e a sinapsi di Azure senza che sia necessario far parte di un gruppo è attualmente disponibile in **anteprima pubblica**. Per altre informazioni, vedere [creare Azure ad utenti guest e impostare come amministratore Azure ad](authentication-aad-guest-users.md).

Azure AD gli utenti supportati per Azure AD scenari B2B come utenti Guest (vedere informazioni su [collaborazione B2B di Azure](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) possono connettersi al database SQL e alla sinapsi di Azure solo come parte dei membri di un gruppo creato nel Azure ad associato ed essere mappati manualmente usando l'istruzione [Create User (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) in un database specifico. Se ad esempio `steve@gmail.com` viene invitato in `contosotest` di Azure AD (con dominio di Azure AD `contosotest.onmicrosoft.com`), è necessario creare un gruppo, ad esempio `usergroup`, nell'istanza di Azure AD che contiene il membro `steve@gmail.com`. Questo gruppo deve quindi essere creato per un database specifico, ad esempio, `MyDatabase` da un Azure ad amministratore SQL o Azure ad dbo, eseguendo l'istruzione Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` . 

Dopo aver creato l'utente del database, l'utente `steve@gmail.com` può accedere `MyDatabase` usando l'opzione di autenticazione di SSMS `Azure Active Directory – Universal with MFA` . Per impostazione predefinita, `usergroup` dispone solo dell'autorizzazione Connect. Ogni ulteriore accesso ai dati dovrà essere [concesso](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) nel database da un utente con privilegi sufficienti. 

> [!NOTE]
> Per SSMS 17. x, l'uso `steve@gmail.com` di come utente Guest è necessario controllare la casella **nome di dominio ad o ID tenant** e aggiungere il nome di dominio ad nella finestra `contosotest.onmicrosoft.com` di dialogo **Proprietà connessione** . L'opzione **nome di dominio ad o ID tenant** è supportata solo per la **Azure Active Directory universale con autenticazione a** più fattori. In caso contrario, la casella di controllo è disattivata.

## <a name="universal-authentication-limitations"></a>Limitazioni dell'autenticazione universale

- SSMS e SqlPackage.exe sono gli unici strumenti attualmente abilitati per MFA tramite l'autenticazione universale di Active Directory.
- SSMS versione 17,2 supporta l'accesso simultaneo multiutente usando l'autenticazione universale con multi-factor authentication. Per SSMS versione 17,0 e 17,1, lo strumento limita un account di accesso per un'istanza di SSMS usando l'autenticazione universale per un singolo account Azure Active Directory. Per accedere con un altro account di Azure AD, è necessario usare un'altra istanza di SSMS. Questa restrizione è limitata all'autenticazione universale di Active Directory; è possibile accedere a un server diverso usando `Azure Active Directory - Password` l'autenticazione, `Azure Active Directory - Integrated` l'autenticazione o `SQL Server Authentication` .
- SSMS supporta l'autenticazione universale di Active Directory per la visualizzazione di Esplora oggetti, Editor di query e Archivio query.
- La versione 17.2 di SSMS fornisce il supporto di DacFx Wizard per le funzioni di esportazione, estrazione e distribuzione dei dati del database. Quando un utente ha eseguito l'autenticazione mediante la finestra di dialogo iniziale usando l'autenticazione universale, DacFx Wizard funziona esattamente come per tutti gli altri metodi di autenticazione.
- Progettazione tabelle di SSMS non supporta l'autenticazione universale.
- Non ci sono requisiti software aggiuntivi per l'autenticazione universale di Active Directory ad eccezione del fatto che si utilizzi una versione supportata di SSMS.  
- Vedere il collegamento seguente per la versione più recente di Active Directory Authentication Library (ADAL) per l'autenticazione universale: [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Passaggi successivi

- Per la procedura di configurazione, vedere [Configurare Multi-Factor Authentication con database SQL di Azure per SQL Server Management Studio](authentication-mfa-ssms-configure.md).
- Concedere ad altri utenti l'accesso al database: [Autenticazione e autorizzazione per il database SQL: Concessione dell'accesso](logins-create-manage.md)  
- Assicurarsi che altri utenti possano connettersi tramite il firewall: [configurare una regola del firewall a livello di server usando il portale di Azure](firewall-configure.md)  
- [Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL o Azure Synapse](authentication-aad-configure.md)
- [Creare utenti guest di Azure AD e impostarli come amministratore di Azure AD](authentication-aad-guest-users.md) 
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Importare un file BACPAC in un nuovo database](database-import.md)  
- [Esportare un database in un file BACPAC](database-export.md)  
- Interfaccia C# [IUniversalAuthProvider](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Quando si usa **Azure Active Directory universale con autenticazione a** più fattori, la traccia adal è disponibile a partire da [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Disattivata per impostazione predefinita, la traccia ADAL può essere attivata usando il menu **Strumenti**, **Opzioni** in **Servizi di Azure**, **Cloud di Azure**, **Livello di traccia della finestra di output di ADAL** e quindi abilitando **Output** nel menu **Visualizza**. Le tracce sono disponibili nella finestra di output quando si seleziona l'**opzione Azure Active Directory**.  
