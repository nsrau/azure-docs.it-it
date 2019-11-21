---
title: Enable Azure Active Directory for Azure-SSIS Integration Runtime
description: Questo articolo descrive come abilitare l'autenticazione di Azure Active Directory con l'identità gestita per Azure Data Factory per creare Azure-SSIS Integration Runtime.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.date: 5/14/2019
ms.openlocfilehash: 6973e72b06d51241e883038936270fd0931365d7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217704"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Abilitare l'autenticazione di Azure Active Directory per Azure-SSIS Integration Runtime

This article shows you how to enable Azure Active Directory (Azure AD) authentication with the managed identity for your Azure Data Factory (ADF) and use it instead of conventional authentication methods (like SQL authentication) to:

- Create an Azure-SSIS Integration Runtime (IR) that will in turn provision SSIS catalog database (SSISDB) in Azure SQL Database server/Managed Instance on your behalf.

- Connect to various Azure resources when running SSIS packages on Azure-SSIS IR.

For more info about the managed identity for your ADF, see [Managed identiy for Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  In this scenario, Azure AD authentication with the managed identity for your ADF is only used in the creation and subsequent starting operations of your SSIS IR that will in turn provision and connect to SSISDB. For SSIS package executions, your SSIS IR will still connect to SSISDB using SQL authentication with fully managed accounts that are created during SSISDB provisioning.
>-  If you have already created your SSIS IR using SQL authentication, you can not reconfigure it to use Azure AD authentication via PowerShell at this time, but you can do so via Azure portal/ADF app. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Abilitare Azure Active Directory nel database SQL di Azure

Il server di database SQL di Azure supporta la creazione di un database con un utente di Azure AD. È prima necessario creare un gruppo di Azure AD con l'identità gestita per ADF come membro. È poi necessario impostare un utente di Azure AD come amministratore di Active Directory per il server di database SQL di Azure e quindi connettersi a tale server in SQL Server Management Studio (SSMS) usando tale utente. È infine necessario creare un utente indipendente che rappresenta il gruppo di Azure AD, in modo che l'identità gestita per ADF possa essere usata da Azure-SSIS IR per creare SSISDB per conto dell'utente.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Creare un gruppo di Azure AD con l'identità gestita per ADF come membro

È possibile usare un gruppo di Azure AD esistente o crearne uno nuovo con Azure AD PowerShell.

1.  Installare il [modulo Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2).

2.  Accedere usando  `Connect-AzureAD`, eseguire il cmdlet seguente per creare un gruppo e salvarlo in una variabile:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Il risultato sarà analogo all'esempio seguente, che visualizza anche il valore della variabile:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Aggiungere l'identità gestita per Azure Data Factory al gruppo. You can follow the article [Managed identiy for Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) to get the principal Managed Identity Object ID (e.g. 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, but do not use Managed Identity Application ID for this purpose).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    È anche possibile controllare l'appartenenza a gruppi in un secondo momento.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Configurare l'autenticazione di Azure AD per il server di database SQL di Azure

È possibile  [configurare e gestire l'autenticazione di Azure AD con SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) seguendo questa procedura:

1.  Nel portale di Azure selezionare **Tutti i servizi** -> **SQL Server** dal menu a sinistra.

2.  Selezionare il server di database SQL di Azure da configurare con l'autenticazione di Azure AD.

3.  Nella sezione **Impostazioni** del pannello selezionare **Amministratore di Active Directory**.

4.  Sulla barra dei comandi selezionare **Imposta amministratore**.

5.  Select an Azure AD user account to be made administrator of the server, and then select **Select.**

6.  Sulla barra dei comandi selezionare **Salva**.

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Creare un utente indipendente nel server di database SQL di Azure che rappresenta il gruppo di Azure AD

Per il passaggio successivo è necessario  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1. Avviare SSMS.

2. In the **Connect to Server** dialog, enter your Azure SQL Database server name in the **Server name** field.

3. In the **Authentication** field, select **Active Directory - Universal with MFA support** (you can also use the other two Active Directory authentication types, see [Configure and manage Azure AD authentication with SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4. In the **User name** field, enter the name of Azure AD account that you set as the server administrator, e.g. testuser@xxxonline.com.

5. select **Connect** and complete the sign-in process.

6. In **Esplora oggetti** espandere la cartella **Database** -> **Database di sistema**.

7. Fare clic con il pulsante destro del mouse sul database **master** e scegliere **Nuova query**.

8. In the query window, enter the following T-SQL command, and select **Execute** on the toolbar.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   Il comando dovrebbe completare l'operazione correttamente, creando un utente indipendente per rappresentare il gruppo.

9. Cancellare il contenuto della finestra di query, immettere il comando T-SQL seguente e selezionare **Esegui** sulla barra degli strumenti.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   Il comando dovrebbe completare l'operazione correttamente, concedendo all'utente indipendente la possibilità di creare un database (SSISDB).

10. If your SSISDB was created using SQL authentication and you want to switch to use Azure AD authentication for your Azure-SSIS IR to access it, right-click on **SSISDB** database and select **New query**.

11. In the query window, enter the following T-SQL command, and select **Execute** on the toolbar.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Il comando dovrebbe completare l'operazione correttamente, creando un utente indipendente per rappresentare il gruppo.

12. Cancellare il contenuto della finestra di query, immettere il comando T-SQL seguente e selezionare **Esegui** sulla barra degli strumenti.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    Il comando dovrebbe completare l'operazione correttamente, concedendo all'utente indipendente la possibilità di creare database SSISDB.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Abilitare Azure AD in Istanza gestita di database SQL di Azure

L'istanza gestita di database SQL di Azure supporta la creazione di un database direttamente con identità gestita per ADF. Non è necessario aggiungere l'identità gestita per ADF a un gruppo di Azure AD né creare un utente indipendente che rappresenta tale gruppo nell'istanza gestita.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Configurare l'autenticazione di Azure AD per l'istanza gestita di database SQL di Azure

Follow the steps in [Provision an Azure Active Directory administrator for your Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Aggiungere l'identità gestita per ADF come utente nell'istanza gestita di database SQL di Azure

Per il passaggio successivo è necessario  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Avviare SSMS.

2.  Connect to your Managed Instance using a SQL Server account that is a **sysadmin**. This is a temporary limitation that will be removed once Azure AD server principals (logins) for Azure SQL Database Managed Instance becomes GA. You will see the following error if you try to use an Azure AD admin account to create the login: Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.

3.  In **Esplora oggetti** espandere la cartella **Database** -> **Database di sistema**.

4.  Fare clic con il pulsante destro del mouse sul database **master** e scegliere **Nuova query**.

5.  In the query window, execute the following T-SQL script to add the managed identity for your ADF as a user

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    Il comando dovrebbe completare l'operazione correttamente, concedendo all'identità gestita per ADF la possibilità di creare un database (SSISDB).

6.  If your SSISDB was created using SQL authentication and you want to switch to use Azure AD authentication for your Azure-SSIS IR to access it, right-click on **SSISDB** database and select **New query**.

7.  In the query window, enter the following T-SQL command, and select **Execute** on the toolbar.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    Il comando dovrebbe completare l'operazione correttamente, concedendo all'identità gestita per ADF la possibilità di accedere a SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Effettuare il provisioning di Azure-SSIS IR nel portale di Azure o nell'app ADF

Quando si effettua il provisioning di Azure-SSIS IR nel portale di Azure o nell'app ADF, nella pagina **Impostazioni SQL** selezionare l'opzione **Use AAD authentication with the managed identity for your ADF** (Usa autenticazione AAD con l'identità gestita per Azure Data Factory). La schermata seguente mostra le impostazioni per il runtime di integrazione con il server di database SQL di Azure che ospita SSISDB. Per il runtime di integrazione con l'istanza gestita che ospita SSISDB, le impostazioni **Catalog Database Service Tier** (Livello di servizio del database di catalogo) e **Allow Azure services to access** (Consenti ai servizi di Azure di eseguire l'accesso) non sono applicabili, mentre le altre impostazioni sono uguali.

Per altre informazioni su come creare Azure-SSIS IR, vedere [Creare un runtime di integrazione SSIS di Azure in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Impostazioni per il runtime di integrazione Azure-SSIS](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Effettuare il provisioning di Azure-SSIS IR con PowerShell

Per eseguire il provisioning del runtime di integrazione Azure-SSIS con PowerShell, procedere come segue:

1.  Installare il modulo [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) .

2.  Nello script non impostare il parametro `CatalogAdminCredential`. ad esempio:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
    ```

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>Run SSIS Packages with Managed Identity Authentication

When you run SSIS packages on Azure-SSIS IR, you can use managed identity authentication to connect to various Azure resources. Currently we have already supported managed identity authentication in the following connection managers.

- [OLE DB Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure Storage Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
