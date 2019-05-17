---
title: Abilitare l'autenticazione di Azure Active Directory per Azure-SSIS Integration Runtime | Microsoft Docs
description: Questo articolo descrive come abilitare l'autenticazione di Azure Active Directory con l'identità gestita per Azure Data Factory per creare Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 5/14/2019
author: swinarko
ms.author: sawinark
manager: craigg
ms.openlocfilehash: a67436f09d6e28db8d19679e446ac4cf98383709
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65593793"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Abilitare l'autenticazione di Azure Active Directory per Azure-SSIS Integration Runtime

Questo articolo illustra come abilitare l'autenticazione di Azure Active Directory (Azure AD) con l'identità gestita per l'Azure Data Factory (ADF) e usarlo invece l'autenticazione di SQL per creare un Azure-SSIS Integration Runtime (IR) che a sua volta effettua il provisioning Database del catalogo SSIS (SSISDB) in Azure SQL server gestita/Database istanza per tuo conto.

Per altre informazioni relative all'identità gestita per ADF, vedere [identiy gestito per Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  In questo scenario, l'autenticazione di Azure AD con l'identità gestita per ADF viene usato solo per la creazione e le operazioni successive iniziale del runtime di integrazione SSIS che verrà in abilitare il provisioning e connettersi al database SSISDB. Per esecuzioni di pacchetti SSIS, runtime di integrazione SSIS comunque si connetterà al database SSISDB tramite l'autenticazione di SQL con account completamente gestito che vengono creati durante il provisioning del database SSISDB.
>-  Se è già stato creato il runtime di integrazione SSIS Usa l'autenticazione SQL, non è possibile riconfigurare in modo che usi l'autenticazione di Azure AD tramite PowerShell in questo momento, ma è possibile farlo tramite app di portale o Azure Data factory di Azure. 

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

3.  Aggiungere l'identità gestita per Azure Data Factory al gruppo. È possibile seguire l'articolo [identiy gestito per Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) per ottenere l'ID dell'entità gestita identità oggetto (ad esempio 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, ma non usano Managed Identity Application ID per questo scopo).

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

5.  Selezionare un account utente di Azure AD per renderla amministratore del server e quindi selezionare **selezionare.**

6.  Sulla barra dei comandi selezionare **Salva**.

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Creare un utente indipendente nel server di database SQL di Azure che rappresenta il gruppo di Azure AD

Per il passaggio successivo è necessario  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1. Avviare SSMS.

2. Nel **Connetti al Server** finestra di dialogo immettere il nome del server di Database SQL di Azure nel **nome Server** campo.

3. Nel **Authentication** campi, selezionare **Active Directory - universale con supporto MFA** (è anche possibile usare le altre due tipi di autenticazione di Active Directory, vedere [configurare e gestire Autenticazione di Azure AD con SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4. Nel **nome utente** immettere il nome dell'account Azure AD che è possibile impostare come amministratore del server, ad esempio testuser@xxxonline.com.

5. Selezionare **Connect** e completare il processo di accesso.

6. In **Esplora oggetti** espandere la cartella **Database** -> **Database di sistema**.

7. Fare clic con il pulsante destro del mouse sul database **master** e scegliere **Nuova query**.

8. Nella finestra di query, immettere il comando T-SQL seguente e selezionare **Execute** sulla barra degli strumenti.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   Il comando dovrebbe completare l'operazione correttamente, creando un utente indipendente per rappresentare il gruppo.

9. Cancellare il contenuto della finestra di query, immettere il comando T-SQL seguente e selezionare **Esegui** sulla barra degli strumenti.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   Il comando dovrebbe completare l'operazione correttamente, concedendo all'utente indipendente la possibilità di creare un database (SSISDB).

10. Se il database SSISDB è stato creato utilizzando l'autenticazione di SQL e si desidera passare per usare l'autenticazione di Azure AD per il runtime di integrazione Azure-SSIS per accedervi, fare clic su **SSISDB** del database e selezionare **nuova query**.

11. Nella finestra di query, immettere il comando T-SQL seguente e selezionare **Execute** sulla barra degli strumenti.

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

1.   Nel portale di Azure selezionare **Tutti i servizi** -> **SQL Server** dal menu a sinistra.

2.   Selezionare l'istanza gestita da configurare con l'autenticazione di Azure AD.

3.   Nella sezione **Impostazioni** del pannello selezionare **Amministratore di Active Directory**.

4.   Sulla barra dei comandi selezionare **Imposta amministratore**.

5.   Selezionare un account utente di Azure AD da rendere amministratore del server e quindi scegliere **Seleziona**.

6.   Sulla barra dei comandi selezionare **Salva**.

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Aggiungere l'identità gestita per ADF come utente nell'istanza gestita di database SQL di Azure

Per il passaggio successivo è necessario  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Avviare SSMS.

2.  Connettersi all'istanza gestita usando l'account amministratore di SQL/Active Directory.

3.  In **Esplora oggetti** espandere la cartella **Database** -> **Database di sistema**.

4.  Fare clic con il pulsante destro del mouse sul database **master** e scegliere **Nuova query**.

5.  Ottenere l'identità gestita per ADF. È possibile seguire l'articolo [identiy gestito per Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) per ottenere l'ID dell'entità gestita identità applicazione (ma non usare ID di oggetto identità gestita per questo scopo).

6.  Nella finestra di query eseguire lo script T-SQL seguente per convertire l'identità gestita per ADF in tipo binario:

    ```sql
    DECLARE @applicationId uniqueidentifier = '{your Managed Identity Application ID}'
    select CAST(@applicationId AS varbinary)
    ```
    
    Il comando dovrebbe essere completato correttamente, visualizzando l'identità gestita per ADF come tipo binario.

7.  Cancellare il contenuto della finestra di query ed eseguire lo script T-SQL seguente per aggiungere l'identità gestita per ADF come utente

    ```sql
    CREATE LOGIN [{a name for the managed identity}] FROM EXTERNAL PROVIDER with SID = {your Managed Identity Application ID as binary}, TYPE = E
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{the managed identity name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{the managed identity name}]
    ```
    
    Il comando dovrebbe completare l'operazione correttamente, concedendo all'identità gestita per ADF la possibilità di creare un database (SSISDB).

8.  Se il database SSISDB è stato creato utilizzando l'autenticazione di SQL e si desidera passare per usare l'autenticazione di Azure AD per il runtime di integrazione Azure-SSIS per accedervi, fare clic su **SSISDB** del database e selezionare **nuova query**.

9.  Nella finestra di query, immettere il comando T-SQL seguente e selezionare **Execute** sulla barra degli strumenti.

    ```sql
    CREATE USER [{the managed identity name}] FOR LOGIN [{the managed identity name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{the managed identity name}]
    ```

    Il comando dovrebbe completare l'operazione correttamente, concedendo all'identità gestita per ADF la possibilità di accedere a SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Effettuare il provisioning di Azure-SSIS IR nel portale di Azure o nell'app ADF

Quando si effettua il provisioning di Azure-SSIS IR nel portale di Azure o nell'app ADF, nella pagina **Impostazioni SQL** selezionare l'opzione **Use AAD authentication with the managed identity for your ADF** (Usa autenticazione AAD con l'identità gestita per Azure Data Factory). La schermata seguente mostra le impostazioni per il runtime di integrazione con il server di database SQL di Azure che ospita SSISDB. Per il runtime di integrazione con l'istanza gestita che ospita SSISDB, le impostazioni **Catalog Database Service Tier** (Livello di servizio del database di catalogo) e **Allow Azure services to access** (Consenti ai servizi di Azure di eseguire l'accesso) non sono applicabili, mentre le altre impostazioni sono uguali.

Per altre informazioni su come creare Azure-SSIS IR, vedere [Creare un runtime di integrazione SSIS di Azure in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Impostazioni per il runtime di integrazione Azure-SSIS](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Effettuare il provisioning di Azure-SSIS IR con PowerShell

Per eseguire il provisioning del runtime di integrazione Azure-SSIS con PowerShell, procedere come segue:

1.  Installare il modulo [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) .

2.  Nello script non impostare il parametro `CatalogAdminCredential`. Ad esempio:

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
