---
title: Configurare Multi-Factor Authentication - Azure SQL | Microsoft Docs
description: Informazioni su come usare Multi-Factored Authentication con SSMS per il database SQL e SQL Data Warehouse.
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/27/2017
ms.author: rickbyh
ms.openlocfilehash: 89a5ee969cf4337dbc0521027f36f80e357c54e5
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Configurare Multi-Factor Authentication per SQL Server Management Studio e Azure AD

Questo argomento illustra come usare Azure Active Directory Multi-Factor Authentication (MFA) con SQL Server Management Studio. È possibile usare Azure AD MFA quando si connette SSMS o SqlPackage.exe al database SQL di Azure e ad Azure SQL Data Warehouse.

Per una panoramica dell'autenticazione a più fattori per il database SQL di Azure, vedere [Autenticazione universale con database SQL e SQL Data Warehouse (supporto SSMS per MFA)](sql-database-ssms-mfa-authentication.md).

## <a name="configuration-steps"></a>Procedura di configurazione

1. **Configurare un'istanza di Azure Active Directory**: per altre informazioni, vedere [Amministrazione della directory di Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [Integrare le directory locali con Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Aggiungere un nome di dominio personalizzato ad Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Nuovo supporto per la federazione con Active Directory di Windows Server in Microsoft Azure) e [Gestire Azure AD con Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Configurare MFA**: per istruzioni dettagliate, vedere [Informazioni su Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) e [Accesso condizionale (MFA) con il database SQL di Azure e Azure SQL Data Warehouse](sql-database-conditional-access.md). Per l'accesso condizionale completo è necessaria l'edizione Premium di Azure Active Directory (Azure AD). Con l'edizione standard di Azure AD è disponibile l'autenticazione a più fattori limitata.
3. **Configurare un database SQL o SQL Data Warehouse per l'autenticazione di Azure AD**: per istruzioni dettagliate, vedere [Connessione al database SQL oppure a SQL Data Warehouse con l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md).
4. **Scaricare SSMS**: nel computer client scaricare la versione più recente di SSMS da [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Per tutte le funzionalità illustrate in questo argomento, usare almeno la versione 17.2 di luglio 2017.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Connessione tramite l'autenticazione universale con SSMS

La procedura seguente illustra la modalità di connessione al database SQL o a SQL Data Warehouse tramite la versione più recente di SSMS.

1. Per connettersi usando l'autenticazione universale, nella finestra di dialogo **Connetti al server** selezionare **Active Directory - Universale con supporto MFA**. Se viene visualizzata l'opzione **Autenticazione universale di Active Directory** significa che non si sta usando la versione più recente di SSMS.  
   ![1mfa-universal-connect][1]  
2. Nella casella **Nome utente** immettere le credenziali di Azure Active Directory nel formato `user_name@domain.com`.  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Se si esegue la connessione come utente guest, è necessario fare clic su **Opzioni** e nella finestra di dialogo **Proprietà connessione** completare la casella **ID tenant o nome di dominio AD**. Per altre informazioni, vedere [Autenticazione universale con database SQL e SQL Data Warehouse (supporto SSMS per MFA)](sql-database-ssms-mfa-authentication.md).
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   
4. Come di consueto per il database SQL e SQL Data Warehouse, è necessario fare clic su **Opzioni** e specificare il database nella finestra di dialogo **Opzioni**. Se l'utente connesso è un utente guest, ad esempio joe@outlook.com, è necessario selezionare la casella e aggiungere il nome di dominio AD o l'ID tenant corrente nelle opzioni. Vedere [Autenticazione universale con database SQL e SQL Data Warehouse (supporto SSMS per MFA)]()(sql-database-ssms-mfa-authentication.md. Fare clic su **Connetti**.  
5. Quando appare la finestra di dialogo **Accedi al tuo account** , fornire l'account e la password dell'identità di Azure Active Directory. Se un utente fa parte di un dominio federato con Azure AD, non è necessario specificare la password.  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > L'utente viene connesso in questa fase nel caso dell'autenticazione universale con un account che non richiede l'MFA. Per gli utenti che richiedono l'MFA, continuare con la procedura seguente:
   >  
   
6. Potrebbero aprirsi due finestre di dialogo per la configurazione del metodo MFA. Questa operazione una tantum dipende l'impostazione dell'amministratore dell'MFA e potrebbe, quindi, essere facoltativa. Per un dominio abilitato per l'MFA si tratta di una procedura predefinita (ad esempio, il dominio richiede agli utenti di utilizzare una smart card con pin).  
   ![3mfa-setup][3]  
7. La seconda finestra di dialogo consente di selezionare i dettagli del metodo di autenticazione. Le opzioni possibili sono configurate dall'amministratore.  
   ![4mfa-verify-1][4]  
8. Azure Active Directory invia le informazioni di conferma all'utente. Quando si riceve il codice di verifica, immetterlo nella casella **Immettere il codice di verifica** e fare clic su **Accedi**.  
   ![5mfa-verify-2][5]  

Al termine della procedura di verifica, di norma SSMS stabilisce la connessione se le credenziali sono valide e se il firewall lo consente.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dell'autenticazione a più fattori per il database SQL di Azure, vedere [Autenticazione universale con database SQL e SQL Data Warehouse (supporto SSMS per MFA)](sql-database-ssms-mfa-authentication.md).  
- Concedere ad altri utenti l'accesso al database: [Autenticazione e autorizzazione per il database SQL: concessione dell'accesso](sql-database-manage-logins.md)  
- Verificare che altri utenti possano connettersi tramite il firewall: [Configurare una regola firewall a livello di server per il database SQL di Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md)  
- Quando si usa l'autenticazione **Active Directory - Universale con supporto MFA**, la traccia ADAL è disponibile a partire da [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Disattivata per impostazione predefinita, la traccia ADAL può essere attivata usando il menu **Strumenti**, **Opzioni** in **Servizi di Azure**, **Cloud di Azure**, **Livello di traccia della finestra di output di ADAL** e quindi abilitando **Output** nel menu **Visualizza**. Le tracce sono disponibili nella finestra di output quando si seleziona l'**opzione Azure Active Directory**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

