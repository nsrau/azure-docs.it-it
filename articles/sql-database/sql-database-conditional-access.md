---
title: Accesso condizionale
description: Informazioni su come configurare l'accesso condizionale per il database SQL di Azure e Azure Synapse.Learn how to configure Conditional Access for Azure SQL Database and Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sql-data-warehouse
ms.date: 02/06/2020
tag: azure-synpase
ms.openlocfilehash: cd56ccf2e6a4ceb0d81c25b5b9e795176be66b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124898"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Accesso condizionale (MFA) con il database SQL di Azure e Azure Synapse AnalyticsConditional Access (MFA) with Azure SQL Database and Azure Synapse Analytics

[Il database SQL di](sql-database-technical-overview.md)Azure, [l'istanza gestita](sql-database-managed-instance.md)e [lo synapsi](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) di Azure supportano Microsoft Conditional Access. 

> [!NOTE]
> Questo argomento si applica al server SQL di Azure e sia al database SQL che alla protezione di Azure creati nel server SQL di Azure.This topic applies to Azure SQL server, and both SQL Database and Azure Synapse that are created on the Azure SQL server. Per semplicità, il database SQL viene usato quando si fa riferimento sia al database SQL che allo synapsi di Azure.For simplicity, SQL Database is used when referring to both SQL Database and Azure Synapse.

La procedura seguente mostra come configurare il database SQL per applicare un criterio di accesso condizionale.  

## <a name="prerequisites"></a>Prerequisiti  
- È necessario configurare il database SQL o il pool SQL in Azure Synapse per supportare l'autenticazione di Azure Active Directory.You must configure your SQL Database or SQL pool in Azure Synapse to support Azure Active Directory authentication. Per la procedura specifica, vedere [Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL o Azure Synapse.](sql-database-aad-authentication-configure.md)  
- Quando è abilitata l'autenticazione a più fattori, è necessario connettersi con uno strumento supportato, ad esempio la versione più recente di SQL Server Management Studio. Per altre informazioni, vedere [Configurare Multi-Factor Authentication con database SQL di Azure per SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Configurare l'autorità di certificazione per il database SQL di Azure o Azure SQL Data Warehouse  
1. Accedere al portale, selezionare **Azure Active Directory**e quindi Accesso **condizionale**. Per altre informazioni, vedere [Documentazione tecnica sull'accesso condizionale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Pannello Accesso condizionale](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. Nel pannello **Accesso condizionale - Criteri** fare clic su **Nuovo criterio**, specificare un nome e quindi fare clic su **Configura regole**.  
3. In **Assegnazioni**selezionare **Utenti e gruppi**, **selezionare Seleziona utenti e gruppi**e quindi selezionare l'utente o il gruppo per Accesso condizionale. Fare clic su **Seleziona** e quindi su **Fatto** per accettare la selezione.  
   ![seleziona utenti e gruppi](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Selezionare **App cloud** e fare clic su **Seleziona app**. Vengono visualizzate tutte le app disponibili per l'accesso condizionale. Selezionare **Database SQL di Azure**, nella parte inferiore fare clic su **Seleziona** e quindi fare clic su **Fatto**.  
   ![selezionare il database SQL](./media/sql-database-conditional-access/select-sql-database.png)  
   Se non è possibile trovare il database SQL di Azure elencato nella terza schermata seguente, completare i passaggi seguenti:If you can't find **Azure SQL Database** listed in the following third screenshot, complete the following steps:   
   - Accedere all'istanza del database SQL di Azure o di Azure SQL Data Warehouse usando SQL Server Management Studio con un account amministratore di AAD.  
   - Eseguire `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Accedere ad AAD e verificare che il database SQL di Azure e lo synapsi di Azure siano elencati nelle applicazioni in AAD.  

5. Selezionare **Controlli di accesso**, selezionare **Concedi** e quindi selezionare il criterio che si vuole applicare. Per questo esempio, selezionare **Richiedi autenticazione a più fattori**.  
   ![selezionare l'opzione per concedere l'accesso](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Riepilogo  
L'applicazione selezionata (database SQL di Azure) che consente di connettersi al database SQL di Azure o ad Azure SQL Data Warehouse con Azure AD Premium, applica ora il criterio di accesso condizionale selezionato, **Richiedi autenticazione a più fattori**  
Per domande sul database SQL di Azure e su Azure MFAforSQLDB@microsoft.comSynapse relative all'autenticazione a più fattori, contact .  

## <a name="next-steps"></a>Passaggi successivi  

Per un'esercitazione, vedere [Proteggere il database SQL di Azure](sql-database-security-tutorial.md).
