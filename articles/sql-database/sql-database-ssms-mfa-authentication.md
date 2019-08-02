---
title: Uso dell'autenticazione di AAD a più fattori con il database SQL di Azure e Azure SQL Data Warehouse | Microsoft Docs
description: Il database SQL di Azure e Azure SQL Data Warehouse supportano le connessioni da SQL Server Management Studio (SSMS) tramite l'autenticazione universale di Active Directory.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/08/2018
ms.openlocfilehash: 7add55380f2f7b3ef70db0603fe2c26127db8a78
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566454"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-sql-data-warehouse-ssms-support-for-mfa"></a>Uso dell'autenticazione di AAD a più fattori con il database SQL di Azure e Azure SQL Data Warehouse (supporto di SSMS per l'autenticazione a più fattori)
Il database SQL di Azure e Azure SQL Data Warehouse supportano le connessioni da SQL Server Management Studio (SSMS) tramite l'*autenticazione universale di Active Directory*. In questo articolo vengono illustrate le differenze tra le varie opzioni di autenticazione e anche le limitazioni associate all'utilizzo dell'autenticazione universale. 

**Scaricare la versione più recente di SSMS**: nel computer client scaricare la versione più recente di SSMS da [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 


Per tutte le funzionalità descritte in questo articolo, usare almeno il 2017 luglio, versione 17,2.  La finestra di dialogo connessione più recente dovrebbe avere un aspetto simile all'immagine seguente:
 
  ![1mfa-universal-connect](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "Viene completata la casella nome utente.")  

## <a name="the-five-authentication-options"></a>Le cinque opzioni di autenticazione  

Active Directory autenticazione universale supporta i due metodi di autenticazione non interattiva:
    - `Active Directory - Password`autenticazione
    - `Active Directory - Integrated`autenticazione

Sono disponibili anche due modelli di autenticazione non interattiva, che possono essere usati in molte applicazioni diverse (ADO.NET, JDCB, ODC e così via). Questi due metodi non generano mai finestre di dialogo popup: 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

Il metodo interattivo è che supporta anche l'autenticazione a più fattori di Azure: 
- `Active Directory - Universal with MFA` 


Azure MFA consente di proteggere l'accesso ai dati e alle applicazioni dell'utente, garantendo al tempo stesso una procedura di accesso semplice. Offre funzionalità avanzate di autenticazione con una serie di semplici opzioni di verifica, tra cui telefonata, SMS, smart card con pin o notifica tramite app per dispositivi mobili, in modo che ogni utente possa scegliere il metodo che preferisce. La convalida di MFA interattiva con Azure AD può avvenire attraverso una finestra popup.

Per una descrizione di Multi-Factor Authentication (autenticazione a più fattori), consultare [Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md).
Per la procedura di configurazione, vedere [Configurare Multi-Factor Authentication con database SQL di Azure per SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Parametro nome di dominio o ID tenant di Azure AD   

A partire dalla [versione 17 di SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), gli utenti che vengono importati come utenti guest nell'istanza corrente di Active Directory da altre istanze di Azure Active Directory possono specificare il nome di dominio o l'ID tenant di Azure AD al momento della connessione. Gli utenti guest includono quelli invitati da altre istanze di Azure AD, gli account Microsoft, ad esempio outlook.com, hotmail.com, live.com, o altri account come gmail.com. Queste informazioni consentono all'**autenticazione universale di Active Directory con MFA** di identificare l'autorità di autenticazione corretta. Questa opzione è necessaria anche per supportare gli account Microsoft (MSA), ad esempio outlook.com, hotmail.com e live.com, o altri account non MSA. Tutti gli utenti che desiderano essere autenticati usando l'autenticazione universale devono immettere il nome di dominio o l'ID tenant di AD Azure. Questo parametro rappresenta il nome di dominio/ID tenant di Azure AD a cui è collegato il server di Azure. Se ad esempio il server di Azure è associato al dominio di Azure AD `contosotest.onmicrosoft.com` in cui l'utente `joe@contosodev.onmicrosoft.com` è ospitato come utente importato dal dominio di Azure AD `contosodev.onmicrosoft.com`, il nome del dominio richiesto per l'autenticazione di questo utente è `contosotest.onmicrosoft.com`. Se l'utente è un utente nativo di Azure AD collegato al server di Azure e non è un account MSA, non è necessario usare l'ID tenant o il nome di dominio. Per specificare il parametro (a partire dalla versione 17.2 di SSMS), nella finestra di dialogo **Connetti al database** selezionare l'autenticazione **Active Directory - Universale con supporto MFA**, fare clic su **Opzioni**, immettere un nome nella casella **Nome utente** e quindi fare clic sulla scheda **Proprietà connessione**. Selezionare la casella **ID tenant o nome di dominio AD** e specificare l'autorità di autenticazione, ad esempio il nome di dominio (**contosotest.onmicrosoft.com**) o il GUID dell'ID tenant.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Supporto per Azure AD business-to-business   
Gli utenti di Azure AD supportati come guest per gli scenari di Azure AD B2B (vedere [Informazioni su Collaborazione B2B di Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) possono connettersi al database SQL e a SQL Data Warehouse solo come membri di un gruppo creato nell'istanza corrente di Azure AD e mappato manualmente tramite l'istruzione Transact-SQL `CREATE USER` in un determinato database. Se ad esempio `steve@gmail.com` viene invitato in `contosotest` di Azure AD (con dominio di Azure AD `contosotest.onmicrosoft.com`), è necessario creare un gruppo, ad esempio `usergroup`, nell'istanza di Azure AD che contiene il membro `steve@gmail.com`. Il gruppo deve quindi essere creato per un database specifico, ovvero un database, da Azure ad amministratore SQL o Azure ad dbo eseguendo un'istruzione Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` . Dopo che è stato creato l'utente del database, l'utente `steve@gmail.com` può accedere a `MyDatabase` usando l'opzione di autenticazione `Active Directory – Universal with MFA support` di SSMS. Per impostazione predefinita, al gruppo di utenti è concessa solo l'autorizzazione di connessione. Per qualsiasi altro tipo di accesso ai dati l'autorizzazione deve essere concessa secondo la normale procedura. Si noti che `steve@gmail.com`, in qualità di utente guest, deve selezionare la casella e aggiungere il nome di dominio AD `contosotest.onmicrosoft.com` nella finestra di dialogo **Proprietà connessione** di SSMS. L'opzione **ID tenant o nome di dominio AD** è supportata solo per le opzioni di connessione Universale con supporto MFA. Negli altri casi non è disponibile.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limitazioni dell'autenticazione universale per il database SQL e SQL Data Warehouse
- SSMS e SqlPackage.exe sono gli unici strumenti attualmente abilitati per MFA tramite l'autenticazione universale di Active Directory.
- La versione 17.2 di SSMS supporta l'accesso simultaneo di più utenti tramite l'autenticazione universale con supporto MFA. Nelle versioni 17.0 e 17.1 un solo account di Azure Active Directory può accedere a un'istanza di SSMS mediante l'autenticazione universale. Per accedere come un altro account di Azure AD, è necessario utilizzare un'altra istanza di SSMS. Questa restrizione è limitata all'autenticazione universale di Active Directory; è possibile accedere a server differenti tramite l'autenticazione con password di Active Directory, l'autenticazione integrata di Active Directory o autenticazione SQL Server.
- SSMS supporta l'autenticazione universale di Active Directory per la visualizzazione di Esplora oggetti, Editor di query e Archivio query.
- La versione 17.2 di SSMS fornisce il supporto di DacFx Wizard per le funzioni di esportazione, estrazione e distribuzione dei dati del database. Quando un utente ha eseguito l'autenticazione mediante la finestra di dialogo iniziale usando l'autenticazione universale, DacFx Wizard funziona esattamente come per tutti gli altri metodi di autenticazione.
- Progettazione tabelle di SSMS non supporta l'autenticazione universale.
- Non ci sono requisiti software aggiuntivi per l'autenticazione universale di Active Directory ad eccezione del fatto che si utilizzi una versione supportata di SSMS.  
- La versione di Active Directory Authentication Library (ADAL) per l'autenticazione universale è stata aggiornata alla versione rilasciata disponibile più recente, ovvero ADAL.dll 3.13.9. Vedere [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Passaggi successivi

- Per la procedura di configurazione, vedere [Configurare Multi-Factor Authentication con database SQL di Azure per SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Concedere ad altri utenti l'accesso al database: [Autenticazione e autorizzazione per il database SQL: Concessione dell'accesso](sql-database-manage-logins.md)  
- Verificare che altri utenti possano connettersi attraverso il firewall: [Configurare una regola firewall a livello di server di database SQL di Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md)  
- [Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL oppure con SQL Data Warehouse](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Importare un file BACPAC in un nuovo database SQL di Azure](../sql-database/sql-database-import.md)  
- [Esportare un database SQL di Azure in un file BACPAC](../sql-database/sql-database-export.md)  
- Interfaccia C# [IUniversalAuthProvider](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Quando si usa l'autenticazione **Active Directory - Universale con supporto MFA**, la traccia ADAL è disponibile a partire da [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Disattivata per impostazione predefinita, la traccia ADAL può essere attivata usando il menu **Strumenti**, **Opzioni** in **Servizi di Azure**, **Cloud di Azure**, **Livello di traccia della finestra di output di ADAL** e quindi abilitando **Output** nel menu **Visualizza**. Le tracce sono disponibili nella finestra di output quando si seleziona l'**opzione Azure Active Directory**.  
