---
title: Accesso condizionale
description: Informazioni su come configurare l'accesso condizionale per database SQL di Azure, Azure SQL Istanza gestita e Azure sinapsi Analytics.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 04/28/2020
tag: azure-synpase
ms.openlocfilehash: ad80f68c1ab3b3583c5a22de49b77211571f345e
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91443985"
---
# <a name="conditional-access-with-azure-sql-database-and-azure-synapse-analytics"></a>Accesso condizionale con il database SQL di Azure e Azure sinapsi Analytics

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Il [database SQL di Azure](sql-database-paas-overview.md), [Azure SQL istanza gestita](../managed-instance/sql-managed-instance-paas-overview.md)e [Azure sinapsi Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) supportano l'accesso condizionale Microsoft.

I passaggi seguenti illustrano come configurare il database SQL di Azure, SQL Istanza gestita o la sinapsi di Azure per applicare un criterio di accesso condizionale (CA).  

## <a name="prerequisites"></a>Prerequisiti

- Per supportare l'autenticazione Azure Active Directory (Azure AD), è necessario configurare il database SQL di Azure, il Istanza gestita SQL di Azure o il pool SQL di Azure in sinapsi di Azure. Per i passaggi specifici, vedere [configurare e gestire l'autenticazione di Azure Active Directory con il database SQL o la sinapsi di Azure](authentication-aad-configure.md).  
- Quando Multi-Factor Authentication è abilitato, è necessario connettersi con uno strumento supportato, ad esempio l'ultimo SQL Server Management Studio (SSMS). Per altre informazioni, vedere [Configurare Multi-Factor Authentication con database SQL di Azure per SQL Server Management Studio](authentication-mfa-ssms-configure.md).  

## <a name="configure-conditional-access"></a>Configurare l'accesso condizionale

> [!NOTE]
> Nell'esempio seguente viene usato il database SQL di Azure, ma è necessario selezionare il prodotto appropriato per cui si vuole configurare l'accesso condizionale.

1. Accedere al portale di Azure, selezionare **Azure Active Directory**, quindi selezionare **accesso condizionale**. Per altre informazioni, vedere [Documentazione tecnica sull'accesso condizionale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Pannello accesso condizionale](./media/conditional-access-configure/conditional-access-blade.png)

2. Nel pannello **Accesso condizionale - Criteri** fare clic su **Nuovo criterio**, specificare un nome e quindi fare clic su **Configura regole**.  
3. In **assegnazioni**selezionare **utenti e gruppi**, **selezionare utenti e gruppi**, quindi selezionare l'utente o il gruppo per l'accesso condizionale. Fare clic su **Seleziona** e quindi su **Fatto** per accettare la selezione.  
   ![seleziona utenti e gruppi](./media/conditional-access-configure/select-users-and-groups.png)  

4. Selezionare **App cloud** e fare clic su **Seleziona app**. Vengono visualizzate tutte le app disponibili per l'accesso condizionale. Selezionare **Database SQL di Azure**, nella parte inferiore fare clic su **Seleziona** e quindi fare clic su **Fatto**.  
   ![selezionare il database SQL](./media/conditional-access-configure/select-sql-database.png)  
   Se non è possibile trovare il **database SQL di Azure** elencato nel terzo screenshot seguente, completare i passaggi seguenti:
   - Connettersi al database nel database SQL di Azure usando SSMS con un account amministratore Azure AD.  
   - Eseguire `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Accedere a Azure AD e verificare che il database SQL di Azure, SQL Istanza gestita o la sinapsi di Azure siano elencati nelle applicazioni nell'istanza di Azure AD.  

5. Selezionare **Controlli di accesso**, selezionare **Concedi** e quindi selezionare il criterio che si vuole applicare. Per questo esempio, selezionare **Richiedi autenticazione a più fattori**.  
   ![selezionare l'opzione per concedere l'accesso](./media/conditional-access-configure/grant-access.png)  

## <a name="summary"></a>Summary

L'applicazione selezionata (database SQL di Azure) che usa Azure AD Premium, ora applica i criteri di accesso condizionale selezionati, l' **autenticazione a più fattori necessaria.**

Per domande sul database SQL di Azure e sulle sinapsi di Azure per l'autenticazione a più fattori, contattare <MFAforSQLDB@microsoft.com> .  

## <a name="next-steps"></a>Passaggi successivi  

Per un'esercitazione, vedere [proteggere il database nel database SQL](secure-database-tutorial.md).
