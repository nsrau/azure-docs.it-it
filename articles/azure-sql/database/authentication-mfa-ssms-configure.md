---
title: Configurare l'autenticazione a più fattori
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Informazioni su come usare l'autenticazione a più fattori con SSMS per database SQL di Azure, Azure SQL Istanza gestita e Azure sinapsi Analytics.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: has-adal-ref, sqldbrb=3
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 1fb90c106c334073cea18cf014edce491029edec
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91596180"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Configurare Multi-Factor Authentication per SQL Server Management Studio e Azure AD
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Questo articolo illustra come usare Azure Active Directory (Azure AD) multi-factor authentication con SQL Server Management Studio (SSMS). Azure AD autenticazione a più fattori può essere usata per la connessione di SSMS o SqlPackage.exe a [database SQL di Azure](sql-database-paas-overview.md), [Azure SQL istanza gestita](../managed-instance/sql-managed-instance-paas-overview.md) e [Azure sinapsi Analytics (in precedenza SQL Data Warehouse)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Per una panoramica dell'autenticazione a più fattori, vedere [autenticazione universale con database SQL, sql istanza gestita e sinapsi di Azure (supporto di SSMS per](../database/authentication-mfa-ssms-overview.md)l'autenticazione a più fattori).

> [!IMPORTANT]
> I database nel database SQL di Azure, Azure SQL Istanza gestita e la sinapsi di Azure (in precedenza SQL Data Warehouse) sono definiti collettivamente nel resto di questo articolo come database e il server fa riferimento al [Server](logical-servers.md) che ospita i database per il database SQL di Azure e la sinapsi di Azure.

## <a name="configuration-steps"></a>Passaggi di configurazione

1. **Configurare un'istanza di Azure Active Directory**: per altre informazioni, vedere [Amministrazione della directory di Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [Integrare le directory locali con Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md), [Aggiungere un nome di dominio personalizzato ad Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Nuovo supporto per la federazione con Active Directory di Windows Server in Microsoft Azure) e [Gestire Azure AD con Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Configurare MFA**: per istruzioni dettagliate, vedere [Informazioni su Azure Multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md) e [Accesso condizionale (MFA) con il database SQL di Azure e Azure SQL Data Warehouse](conditional-access-configure.md). Per l'accesso condizionale completo è necessario un Azure Active Directory Premium. Con l'edizione standard di Azure AD è disponibile l'autenticazione a più fattori limitata.
3. **Configurare autenticazione di Azure ad** : per istruzioni dettagliate, vedere [connessione al database SQL, a SQL istanza gestita o a una sinapsi di Azure con l'autenticazione di Azure Active Directory](authentication-aad-overview.md).
4. **Scaricare SSMS**: nel computer client scaricare la versione più recente di SSMS da [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Connessione tramite l'autenticazione universale con SSMS

La procedura seguente illustra come connettersi usando la versione più recente di SSMS.

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]

1. Per connettersi usando l'autenticazione universale, nella finestra di dialogo **Connetti al server** in SQL Server Management Studio (SSMS) selezionare **Active Directory universale con supporto di**autenticazione a più fattori. Se viene visualizzata l'opzione **Autenticazione universale di Active Directory** significa che non si sta usando la versione più recente di SSMS.

   ![Screenshot della scheda Proprietà connessione nella finestra di dialogo Connetti al server in S S M. "database" è selezionato nell'elenco a discesa Connetti al database.](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  
2. Nella casella **Nome utente** immettere le credenziali di Azure Active Directory nel formato `user_name@domain.com`.

   ![Screenshot delle impostazioni della finestra di dialogo Connetti al server per il tipo di server, il nome del server, l'autenticazione e il nome utente.](./media/authentication-mfa-ssms-configure/1mfa-universal-connect-user.png)
3. Se si sta effettuando la connessione come utente Guest, non è più necessario completare il campo nome di dominio AD o ID tenant per gli utenti guest perché SSMS 18. x o versione successiva lo riconosce automaticamente. Per altre informazioni, vedere [autenticazione universale con database SQL, sql istanza gestita e sinapsi di Azure (supporto di SSMS per l'autenticazione a](../database/authentication-mfa-ssms-overview.md)più fattori).

   ![Screenshot della scheda Proprietà connessione nella finestra di dialogo Connetti al server in S S M. "database" è selezionato nell'elenco a discesa Connetti al database.](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)

   Tuttavia, se si esegue la connessione come utente guest usando SSMS 17. x o versione precedente, è necessario fare clic su **Opzioni**e nella finestra di dialogo **Proprietà connessione** , quindi completare la casella **nome dominio ad o ID tenant** .

   ![Screenshot della scheda Proprietà connessione nella finestra di dialogo Connetti al server in S S M. l'opzione nome di dominio AD o proprietà ID tenant è compilata.](./media/authentication-mfa-ssms-configure/mfa-tenant-ssms.png)

4. Selezionare **Opzioni** e specificare il database nella finestra di dialogo **Opzioni** . Se l'utente connesso è un utente Guest, ad esempio joe@outlook.com , è necessario selezionare la casella e aggiungere il nome di dominio ad o l'ID tenant corrente come parte delle opzioni. Vedere [autenticazione universale con database SQL e Azure sinapsi Analytics (supporto di SSMS per l'autenticazione a](../database/authentication-mfa-ssms-overview.md)più fattori). Fare clic su **Connetti**.  
5. Quando appare la finestra di dialogo **Accedi al tuo account** , fornire l'account e la password dell'identità di Azure Active Directory. Se un utente fa parte di un dominio federato con Azure AD, non è necessario specificare la password.

   ![Screenshot della finestra di dialogo Accedi al tuo account per il database SQL di Azure e data warehouse. L'account e la password sono compilati.](./media/authentication-mfa-ssms-configure/2mfa-sign-in.png)  

   > [!NOTE]
   > L'utente viene connesso in questa fase nel caso dell'autenticazione universale con un account che non richiede l'MFA. Per gli utenti che richiedono l'MFA, continuare con la procedura seguente:
   >  

6. Potrebbero aprirsi due finestre di dialogo per la configurazione del metodo MFA. Questa operazione una tantum dipende l'impostazione dell'amministratore dell'MFA e potrebbe, quindi, essere facoltativa. Per un dominio abilitato per l'MFA si tratta di una procedura predefinita (ad esempio, il dominio richiede agli utenti di utilizzare una smart card con pin).

   ![Screenshot della finestra di dialogo Accedi al tuo account per il database SQL di Azure e data warehouse con un prompt per configurare la verifica aggiuntiva di sicurezza.](./media/authentication-mfa-ssms-configure/3mfa-setup.png)
  
7. La seconda finestra di dialogo consente di selezionare i dettagli del metodo di autenticazione. Le opzioni possibili sono configurate dall'amministratore.

   ![Screenshot della finestra di dialogo verifica aggiuntiva di sicurezza con le opzioni per la selezione e la configurazione di un metodo di autenticazione.](./media/authentication-mfa-ssms-configure/4mfa-verify-1.png)  
8. Azure Active Directory invia le informazioni di conferma all'utente. Quando si riceve il codice di verifica, immetterlo nella casella **Immettere il codice di verifica** e fare clic su **Accedi**.

   ![Screenshot della finestra di dialogo Accedi al tuo account per il database SQL di Azure e data warehouse con la richiesta di immettere un codice di verifica.](./media/authentication-mfa-ssms-configure/5mfa-verify-2.png)  

Al termine della procedura di verifica, di norma SSMS stabilisce la connessione se le credenziali sono valide e se il firewall lo consente.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dell'autenticazione a più fattori, vedere [autenticazione universale con database SQL, sql istanza gestita e sinapsi di Azure (supporto di SSMS per](../database/authentication-mfa-ssms-overview.md)l'autenticazione a più fattori).  
- Concedere ad altri utenti l'accesso al database: [Autenticazione e autorizzazione per il database SQL: Concessione dell'accesso](logins-create-manage.md)  
- Assicurarsi che altri utenti possano connettersi tramite il firewall: [configurare una regola del firewall a livello di server usando il portale di Azure](https://docs.microsoft.com/azure/azure-sql/database/firewall-configure)  
- Quando si usa l'autenticazione **Active Directory - Universale con supporto MFA**, la traccia ADAL è disponibile a partire da [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Disattivata per impostazione predefinita, la traccia ADAL può essere attivata usando il menu **Strumenti**, **Opzioni** in **Servizi di Azure**, **Cloud di Azure**, **Livello di traccia della finestra di output di ADAL** e quindi abilitando **Output** nel menu **Visualizza**. Le tracce sono disponibili nella finestra di output quando si seleziona l'**opzione Azure Active Directory**.
