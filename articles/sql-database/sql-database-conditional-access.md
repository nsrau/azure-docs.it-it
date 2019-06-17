---
title: Accesso condizionale - Database SQL di Azure e Azure SQL Data Warehouse | Microsoft Docs
description: Informazioni su come configurare l'accesso condizionale per il database SQL di Azure e Azure SQL Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 03/29/2019
ms.openlocfilehash: 2b2a4a8f7de7e23997b2d8ba0c1c35dfd97f2541
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118774"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Accesso condizionale (MFA) con il database SQL di Azure e Azure SQL Data Warehouse  

Azure [Database SQL](sql-database-technical-overview.md), [istanza gestita](sql-database-managed-instance.md), e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) supportano l'accesso condizionale di Microsoft. 

> [!NOTE]
> Questo argomento è applicabile al server SQL di Azure e ai database SQL e di SQL Data Warehouse creati nel server SQL di Azure. Per semplicità, "database SQL" viene usato per fare riferimento sia al database SQL che al database di SQL Data Warehouse.

La procedura seguente mostra come configurare il database SQL per applicare un criterio di accesso condizionale.  

## <a name="prerequisites"></a>Prerequisiti  
- È necessario configurare il database SQL oppure SQL Data Warehouse per il supporto dell'autenticazione di Azure Active Directory. Per informazioni sulla procedura specifica, vedere [Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL oppure con SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- Quando è abilitata l'autenticazione a più fattori, è necessario connettersi con uno strumento supportato, ad esempio la versione più recente di SQL Server Management Studio. Per altre informazioni, vedere [Configurare Multi-Factor Authentication con database SQL di Azure per SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Configurare l'autorità di certificazione per il database SQL di Azure o Azure SQL Data Warehouse  
1. Accedere al portale, selezionare **Azure Active Directory**, quindi selezionare **accesso condizionale**. Per altre informazioni, vedere [Documentazione tecnica sull'accesso condizionale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Pannello di accesso condizionale](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. Nel pannello **Accesso condizionale - Criteri** fare clic su **Nuovo criterio**, specificare un nome e quindi fare clic su **Configura regole**.  
3. Sotto **assegnazioni**, selezionare **utenti e gruppi**, selezionare **selezionare utenti e gruppi**e quindi selezionare l'utente o gruppo per l'accesso condizionale. Fare clic su **Seleziona** e quindi su **Fatto** per accettare la selezione.  
   ![seleziona utenti e gruppi](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Selezionare **App cloud** e fare clic su **Seleziona app**. Noterete che tutte le app disponibili per l'accesso condizionale. Selezionare **Database SQL di Azure**, nella parte inferiore fare clic su **Seleziona** e quindi fare clic su **Fatto**.  
   ![selezionare il database SQL](./media/sql-database-conditional-access/select-sql-database.png)  
   Se non si trova **Database SQL di Azure** elencata nel terzo screenshot seguente, completare i passaggi seguenti:   
   - Accedere all'istanza del database SQL di Azure o di Azure SQL Data Warehouse usando SQL Server Management Studio con un account amministratore di AAD.  
   - Eseguire `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Accedere ad AAD e verificare che il database SQL di Azure o Azure SQL Data Warehouse siano elencati tra le applicazioni in AAD.  

5. Selezionare **Controlli di accesso**, selezionare **Concedi** e quindi selezionare il criterio che si vuole applicare. Per questo esempio, selezionare **Richiedi autenticazione a più fattori**.  
   ![selezionare l'opzione per concedere l'accesso](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Riepilogo  
L'applicazione selezionata (database SQL di Azure) che consente di connettersi al database SQL di Azure o ad Azure SQL Data Warehouse con Azure AD Premium, applica ora il criterio di accesso condizionale selezionato, **Richiedi autenticazione a più fattori**  
Per domande sul database SQL di Azure o su Azure SQL Data Warehouse in relazione all'autenticazione a più fattori, contattare MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Passaggi successivi  

Per un'esercitazione, vedere [Proteggere il database SQL di Azure](sql-database-security-tutorial.md).
