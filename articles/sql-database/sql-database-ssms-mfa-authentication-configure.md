---
title: Configurare Multi-Factor Authentication - Azure SQL | Microsoft Docs
description: Utilizzare Multi-Factored Authentication con SSMS per il database SQL e SQL Data Warehouse.
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: security-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/23/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: b134999d407195aaf44babb3e4862b96cc1dc1ed
ms.openlocfilehash: b36f0cf8cbf0dfb310d6dd534906ee5391ce4cd5
ms.lasthandoff: 03/02/2017


---
# <a name="configure-azure-sql-database-multi-factor-authentication-for-sql-server-management-studio"></a>Configurare Multi-Factor Authentication con database SQL di Azure per SQL Server Management Studio

Questo argomento illustra come configurare Multi-Factor Authentication con database SQL di Azure per SQL Server Management Studio. 

Per una panoramica di Multi-Factor Authentication con database SQL di Azure, vedere [Panoramica di Multi-Factor Authentication con database SQL di Azure per SQL Server Management Studio](sql-database-ssms-mfa-authentication.md).

## <a name="configuration-steps"></a>Procedura di configurazione

1. **Configurare Azure Active Directory**: per altre informazioni, vedere gli articoli relativi all'[integrazione delle identità locali con Azure Active Directory](../active-directory/active-directory-aadconnect.md), all'[aggiunta di un nome di dominio personalizzato ad Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), al [nuovo supporto per la federazione con Active Directory di Windows Server in Microsoft Azure](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), all'[amministrazione della directory di Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx) e alla [gestione di Azure AD con Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Configurare MFA**: per istruzioni dettagliate, vedere [Configurazione di Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-whats-next.md). 
3. **Configurare un database SQL o SQL Data Warehouse per l'autenticazione di Azure AD**: per istruzioni dettagliate, vedere [Connessione al database SQL oppure a SQL Data Warehouse con l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md).
4. **Scaricare SSMS**: nel computer client scaricare la versione più recente di SSMS (risalente almeno ad agosto 2016) da [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

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

* Per una panoramica di Multi-Factor Authentication con database SQL di Azure, vedere [Panoramica di Multi-Factor Authentication con database SQL di Azure per SQL Server Management Studio](sql-database-ssms-mfa-authentication.md).
* Concedere ad altri utenti l'accesso al database: [Autenticazione e autorizzazione per il database SQL: concessione dell'accesso](sql-database-manage-logins.md)  
Verificare che altri utenti possano connettersi tramite il firewall: [Configurare una regola firewall a livello di server per il database SQL di Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md)

[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png


