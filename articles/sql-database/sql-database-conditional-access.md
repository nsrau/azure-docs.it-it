---
title: Accesso condizionale - Database SQL di Azure e Azure SQL Data Warehouse | Microsoft Docs
description: Informazioni su come configurare l'accesso condizionale per il database SQL di Azure e Azure SQL Data Warehouse.
services: sql-database
author: BYHAM
manager: jhubbard
ms.custom: security
ms.service: sql-database
ms.topic: article
ms.date: 06/07/2017
ms.author: rickbyh
ms.openlocfilehash: 0dcec61c03a84197e2c351761c743683caa98a06
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Accesso condizionale (MFA) con il database SQL di Azure e Azure SQL Data Warehouse  

Sia il database SQL che SQL Data Warehouse supportano l'accesso condizionale Microsoft. La procedura seguente mostra come configurare il database SQL per applicare un criterio di accesso condizionale.  

## <a name="prerequisites"></a>Prerequisiti  
- È necessario configurare il database SQL oppure SQL Data Warehouse per il supporto dell'autenticazione di Azure Active Directory. Per informazioni sulla procedura specifica, vedere [Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL oppure con SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- Quando è abilitata l'autenticazione a più fattori, è necessario connettersi con uno strumento supportato, ad esempio la versione più recente di SQL Server Management Studio. Per altre informazioni, vedere [Configurare Multi-Factor Authentication con database SQL di Azure per SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Configurare l'autorità di certificazione per il database SQL di Azure o Azure SQL Data Warehouse  
1.  Accedere al portale, selezionare **Azure Active Directory** e quindi selezionare **Accesso condizionale**. Per altre informazioni, vedere [Documentazione tecnica sull'accesso condizionale di Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference).  
  ![pannello dell'accesso condizionale](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2.  Nel pannello **Accesso condizionale - Criteri** fare clic su **Nuovo criterio**, specificare un nome e quindi fare clic su **Configura regole**.  
3.  In **Assegnazioni** selezionare **Utenti e gruppi**, selezionare **Seleziona utenti e gruppi** e quindi selezionare l'utente o il gruppo per l'accesso condizionale. Fare clic su **Seleziona** e quindi su **Fatto** per accettare la selezione.  
  ![seleziona utenti e gruppi](./media/sql-database-conditional-access/select-users-and-groups.png)  

4.  Selezionare **App cloud** e fare clic su **Seleziona app**. Verranno visualizzate tutte le app disponibili per l'accesso condizionale. Selezionare **Database SQL di Azure**, nella parte inferiore fare clic su **Seleziona** e quindi fare clic su **Fatto**.  
  ![selezionare il database SQL](./media/sql-database-conditional-access/select-sql-database.png)  
  Se l'opzione **Database SQL di Azure** indicata nel terzo screenshot di seguito non è disponibile, seguire questa procedura:   
  - Accedere all'istanza del database SQL di Azure o di Azure SQL Data Warehouse usando SQL Server Management Studio con un account amministratore di AAD.  
  - Eseguire `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
  - Accedere ad AAD e verificare che il database SQL di Azure o Azure SQL Data Warehouse siano elencati tra le applicazioni in AAD.  

5.  Selezionare **Controlli di accesso**, selezionare **Concedi** e quindi selezionare il criterio che si vuole applicare. Per questo esempio, selezionare **Richiedi autenticazione a più fattori**.  
  ![selezionare l'opzione per concedere l'accesso](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Riepilogo  
L'applicazione selezionata (database SQL di Azure) che consente di connettersi al database SQL di Azure o ad Azure SQL Data Warehouse con Azure AD Premium, applica ora il criterio di accesso condizionale selezionato, **Richiedi autenticazione a più fattori**  
Per domande sul database SQL di Azure o su Azure SQL Data Warehouse in relazione all'autenticazione a più fattori, contattare MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Passaggi successivi  

Per un'esercitazione, vedere [Proteggere il database SQL di Azure](sql-database-security-tutorial.md).
