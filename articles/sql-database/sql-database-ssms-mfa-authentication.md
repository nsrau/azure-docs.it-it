---
title: Supporto di SSMS per l&quot;autenticazione MFA di Azure AD con database SQL ed SQL Data Warehouse | Documentazione Microsoft
description: Utilizzare Multi-Factored Authentication con SSMS per il database SQL e SQL Data Warehouse.
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: fbd6e644-0520-439c-8304-2e4fb6d6eb91
ms.service: sql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 10/04/2016
ms.author: rick.byham@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3281ce8eba82c2cbfcb34415aa2145419c91332d


---
# <a name="ssms-support-for-azure-ad-mfa-with-sql-database-and-sql-data-warehouse"></a>Supporto di SQL Server Management Studio (SSMS) per l'autenticazione MFA di Azure AD con database SQL e SQL Data Warehouse
Il database SQL e SQL Data Warehouse di Azure adesso supportano le connessioni da SQL Server Management Studio (SSMS) tramite l' *autenticazione universale di Active Directory*. L'autenticazione universale di Active Directory è un flusso di lavoro interattivo che supporta *Azure Multi-Factor Authentication* (MFA). Azure MFA consente di proteggere l'accesso ai dati e alle applicazioni dell'utente, garantendo al tempo stesso una procedura di accesso semplice. Offre funzionalità avanzate di autenticazione con una serie di semplici opzioni di verifica, tra cui telefonata, SMS, smart card con pin o notifica tramite app per dispositivi mobili, in modo che ogni utente possa scegliere il metodo che preferisce. Per una descrizione di Multi-Factor Authentication (autenticazione a più fattori), consultare [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).

SSMS ora supporta:

* MFA interattiva con Azure AD con il potenziale per la convalida della casella di dialogo popup.
* I metodi di autenticazione integrata di Active Directory e tramite password di Active Directory non interattivi che possono essere utilizzati in svariate applicazioni diverse (ADO.NET, JDBC, ODBC, e così via). Questi due metodi non aprono mai finestre di dialogo popup.

Quando l'account utente è configurato per l'MFA, il flusso di lavoro dell'autenticazione interattiva richiede l'interazione supplementare dell'utente tramite l'uso di finestre di dialogo popup, smart card, ecc. Quando l'account utente è configurato per l'MFA, l'utente deve selezionare l'autenticazione universale di Azure per connettersi. Se l'account utente non richiede l'MFA, l'utente può comunque utilizzare le altre due opzioni di autenticazione di Azure Active Directory.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limitazioni dell'autenticazione universale per il database SQL e SQL Data Warehouse
* SSMS è l'unico strumento attualmente abilitato per l'MFA tramite l'autenticazione universale di Active Directory.
* Solo un singolo account di Azure Active Directory può accedere a un'istanza di SSMS mediante l'autenticazione universale. Per accedere come un altro account di Azure AD, è necessario utilizzare un'altra istanza di SSMS. Questa restrizione è limitata all'autenticazione universale di Active Directory; è possibile accedere a server differenti tramite l'autenticazione con password di Active Directory, l'autenticazione integrata di Active Directory o autenticazione SQL Server.
* SSMS supporta l'autenticazione universale di Active Directory per la visualizzazione di Esplora oggetti, Editor di query e Archivio query.
* Né DacFx né Progettazione schema supportano l'autenticazione universale.
* Gli account MSA non sono supportati per l'autenticazione universale di Active Directory.
* L'autenticazione universale di Active Directory non è supportata in SSMS per gli utenti importati nell'istanza di Active Directory corrente da altre istanze di Active Directory di Azure. Questi utenti non sono supportati, in quanto per convalidare gli account è necessario un ID tenant e non esiste alcun meccanismo per fornire tale ID.
* Non ci sono requisiti software aggiuntivi per l'autenticazione universale di Active Directory ad eccezione del fatto che si utilizzi una versione supportata di SSMS.

## <a name="configuration-steps"></a>Procedura di configurazione
Per implementare il metodo Multi-Factor Authentication sono necessari quattro passaggi fondamentali.

1. **Configurare Azure Active Directory**: per altre informazioni, consultare [Integrazione delle identità locali con Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Add your own domain name to Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Aggiungere un nome di dominio personalizzato ad Azure Active Directory), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Nuovo supporto per la federazione con Active Directory di Windows Server in Microsoft Azure), [Amministrazione della directory di Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx) e [Manage Azure AD using Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) (Gestire Azure AD tramite Windows PowerShell).
2. **Configurare MFA** : per le istruzioni dettagliate, vedere [Configurazione di Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-whats-next.md). 
3. **Configurare database SQL o SQL Data Warehouse per l'autenticazione Azure AD** : per le istruzioni dettagliate, vedere [Connessione al database SQL oppure a SQL Data Warehouse con l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md).
4. **Scaricare SSMS** : nel computer client, scaricare la versione più recente di SSMS (risalente almeno ad agosto 2016), da [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Connessione tramite l'autenticazione universale con SSMS
La procedura seguente illustra la modalità di connessione al database SQL o a SQL Data Warehouse tramite la versione più recente di SSMS.

1. Per connettersi usando l'autenticazione universale, nella finestra di dialogo **Connetti al Server**, selezionare **Autenticazione universale di Active Directory**.
   ![1mfa-universal-connect][1]
2. Come di consueto per il database SQL ed SQL Data Warehouse è necessario fare clic su **Opzioni** e specificare il database nella finestra di dialogo **Opzioni**. Fare clic su **Connetti**.
3. Quando appare la finestra di dialogo **Accedi al tuo account** , fornire l'account e la password dell'identità di Azure Active Directory.
   ![2mfa-sign-in][2]
   
   > [!NOTE]
   > Dopodiché l'utente viene connesso, se si tratta dell'autenticazione universale con un account che non richiede l'MFA. Per gli utenti che richiedono l'MFA, continuare con la procedura seguente.
   > 
   > 
4. Potrebbero aprirsi due finestre di dialogo per la configurazione del metodo MFA. Questa operazione una tantum dipende l'impostazione dell'amministratore dell'MFA e potrebbe, quindi, essere facoltativa. Per un dominio abilitato per l'MFA si tratta di una procedura predefinita (ad esempio, il dominio richiede agli utenti di utilizzare una smart card con pin).  
   ![3mfa-setup][3]
5. La seconda finestra di dialogo consente di selezionare i dettagli del metodo di autenticazione. Le opzioni possibili sono configurate dall'amministratore.
   ![4mfa-verify-1][4]
6. Azure Active Directory invia le informazioni di conferma all'utente. Quando si riceve il codice di verifica, immetterlo nella casella **Immettere il codice di verifica** e fare clic su **Accedi**.
   ![5mfa-verify-2][5]

Al termine della procedura di verifica, di norma SSMS stabilisce la connessione se le credenziali sono valide e se il firewall lo consente.

## <a name="next-steps"></a>Passaggi successivi
Concedere ad altri utenti l'accesso al database: [Autenticazione e autorizzazione per il database SQL: concessione dell'accesso](sql-database-manage-logins.md)  
Verificare che altri utenti possano connettersi tramite il firewall: [Configurare una regola firewall a livello di server per il database SQL di Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md)

[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png




<!--HONumber=Nov16_HO3-->


