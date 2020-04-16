---
title: Abilitare AAD per il runtime di integrazione SSIS di AzureEnable AAD for Azure SSIS Integration Runtime
description: Questo articolo descrive come abilitare l'autenticazione di Azure Active Directory con l'identità gestita per Azure Data Factory per creare Azure-SSIS Integration Runtime.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 5/14/2019
ms.openlocfilehash: 2359b378b1f54cf6e03218f819b3a7c5740ba596
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416397"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Abilitare l'autenticazione di Azure Active Directory per Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo illustra come abilitare l'autenticazione di Azure Active Directory (Azure AD) con l'identità gestita per Azure Data Factory (ADF) e usarla al posto dei metodi di autenticazione convenzionali (ad esempio l'autenticazione SQL) per:This article shows you how to enable Azure Active Directory (Azure AD) authentication with the managed identity for your Azure Data Factory (ADF) and use it instead of conventional authentication methods (like SQL authentication) to:

- Creare un runtime di integrazione Azure-SSIS (IR, Azure-SSIS Integration Runtime) che a sua volta eseguirà il provisioning del database del catalogo SSIS (SSISDB) nel server di database SQL di Azure/Istanza gestita per conto dell'utente.

- Connettersi a varie risorse di Azure quando si eseguono pacchetti SSIS in Azure-SSIS IR.Connect to various Azure resources when running SSIS packages on Azure-SSIS IR.

Per altre informazioni sull'identità gestita per l'ADF, vedere [Identità gestita per Data Factory.](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)

> [!NOTE]
>-  In questo scenario, l'autenticazione di Azure AD con l'identità gestita per l'ADF viene usata solo nella creazione e nelle successive operazioni iniziali del provider di oggetti di vendita SSIS che a loro volta eseguirà il provisioning e si connetterà a SSISDB. Per le esecuzioni dei pacchetti SSIS, il provider di oggetti di archiviazione SSIS si connetterà comunque a SSISDB utilizzando l'autenticazione SQL con account completamente gestiti creati durante il provisioning di SSISDB.
>-  Se il runtime di accesso SSIS è già stato creato usando l'autenticazione SQL, non è possibile riconfigurarlo per usare l'autenticazione di Azure AD tramite PowerShell in questo momento, ma è possibile farlo tramite il portale di Azure/app ADF. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Abilitare Azure Active Directory nel database SQL di Azure

Il server di database SQL di Azure supporta la creazione di un database con un utente di Azure AD. È prima necessario creare un gruppo di Azure AD con l'identità gestita per ADF come membro. È poi necessario impostare un utente di Azure AD come amministratore di Active Directory per il server di database SQL di Azure e quindi connettersi a tale server in SQL Server Management Studio (SSMS) usando tale utente. È infine necessario creare un utente indipendente che rappresenta il gruppo di Azure AD, in modo che l'identità gestita per ADF possa essere usata da Azure-SSIS IR per creare SSISDB per conto dell'utente.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Creare un gruppo di Azure AD con l'identità gestita per ADF come membro

È possibile usare un gruppo di Azure AD esistente o crearne uno nuovo con Azure AD PowerShell.

1.  Installare il modulo [Azure AD PowerShell.Install](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) the Azure AD PowerShell module.

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

3.  Aggiungere l'identità gestita per Azure Data Factory al gruppo. È possibile seguire l'articolo [Managed identity for Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) per ottenere l'ID oggetto Identità gestita principale (ad esempio 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, ma non utilizzare l'ID applicazione identità gestita a questo scopo).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    È anche possibile controllare l'appartenenza a gruppi in un secondo momento.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Configurare l'autenticazione di Azure AD per il server di database SQL di Azure

È possibile  [configurare e gestire l'autenticazione di Azure AD con SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) seguendo questa procedura:

1.  Nel portale di Azure selezionare **Tutti i server** -> **SQL** dei servizi nel riquadro di spostamento sinistro.

2.  Selezionare il server di database SQL di Azure da configurare con l'autenticazione di Azure AD.

3.  Nella sezione **Impostazioni** del pannello selezionare **Amministratore di Active Directory**.

4.  Sulla barra dei comandi selezionare **Imposta amministratore**.

5.  Selezionare un account utente di Azure AD da rendere amministratore del server e quindi selezionare **Seleziona.Select** an Azure AD user account to be made administrator of the server, and then select Select.

6.  Sulla barra dei comandi selezionare **Salva**.

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Creare un utente indipendente nel server di database SQL di Azure che rappresenta il gruppo di Azure AD

Per questo passaggio successivo, è necessario [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1. Avviare SSMS.

2. Nella finestra di dialogo **Connetti al server** immettere il nome del server di database SQL di Azure nel campo Nome **server.**

3. Nel campo **Autenticazione** selezionare **Active Directory - Universale con supporto MFA** (è anche possibile usare gli altri due tipi di autenticazione di Active Directory, vedere [Configurare e gestire l'autenticazione](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)di Azure AD con SQL).

4. Nel campo **Nome utente** immettere il nome dell'account Azure AD impostato testuser@xxxonline.comcome amministratore del server, ad esempio .

5. Selezionare **Connetti** e completare il processo di accesso.

6. In **Esplora oggetti** espandere la cartella **Database** -> **Database di sistema**.

7. Fare clic con il pulsante destro del mouse sul database **master** e scegliere **Nuova query**.

8. Nella finestra della query, immettere il comando T-SQL seguente e selezionare **Esegui** sulla barra degli strumenti.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   Il comando dovrebbe completare l'operazione correttamente, creando un utente indipendente per rappresentare il gruppo.

9. Cancellare il contenuto della finestra di query, immettere il comando T-SQL seguente e selezionare **Esegui** sulla barra degli strumenti.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   Il comando dovrebbe completare l'operazione correttamente, concedendo all'utente indipendente la possibilità di creare un database (SSISDB).

10. Se il file SSISDB è stato creato con l'autenticazione SQL e si vuole usare l'autenticazione di Azure AD per il provider di oggetti di gestione e aggiornamento di Azure-SSIS per accedervi, fare clic con il pulsante destro del mouse sul database **SSISDB** e scegliere **Nuova query**.

11. Nella finestra della query, immettere il comando T-SQL seguente e selezionare **Esegui** sulla barra degli strumenti.

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

Seguire i passaggi descritti in Effettuare il provisioning di un amministratore di [Azure Active Directory per l'istanza gestita.](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance)

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Aggiungere l'identità gestita per ADF come utente nell'istanza gestita di database SQL di Azure

Per questo passaggio successivo, è necessario [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Avviare SSMS.

2.  Connettersi all'istanza gestita utilizzando un account di SQL Server che sia un **sysadmin**. Si tratta di una limitazione temporanea che verrà rimossa una volta che le entità server di Azure AD (account di accesso) per l'istanza gestita del database SQL di Azure diventano GA. Se si tenta di usare un account amministratore di Azure AD per creare l'account di accesso, verrà visualizzato il seguente errore: Msg 15247, livello 16, stato 1, utente di riga 1 non dispone dell'autorizzazione per eseguire questa azione.

3.  In **Esplora oggetti** espandere la cartella **Database** -> **Database di sistema**.

4.  Fare clic con il pulsante destro del mouse sul database **master** e scegliere **Nuova query**.

5.  Nella finestra della query, eseguire lo script T-SQL seguente per aggiungere l'identità gestita per il file ADF come utente

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    Il comando dovrebbe completare l'operazione correttamente, concedendo all'identità gestita per ADF la possibilità di creare un database (SSISDB).

6.  Se il file SSISDB è stato creato con l'autenticazione SQL e si vuole usare l'autenticazione di Azure AD per il provider di oggetti di gestione e aggiornamento di Azure-SSIS per accedervi, fare clic con il pulsante destro del mouse sul database **SSISDB** e scegliere **Nuova query**.

7.  Nella finestra della query, immettere il comando T-SQL seguente e selezionare **Esegui** sulla barra degli strumenti.

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

1.  Installare il modulo [di Azure PowerShell.Install Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) module.

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

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>Eseguire pacchetti SSIS con l'autenticazione dell'identità gestitaRun SSIS Packages with Managed Identity Authentication

Quando si eseguono pacchetti SSIS nel runtime di ir Azure-SSIS, è possibile usare l'autenticazione dell'identità gestita per connettersi a varie risorse di Azure.When you run SSIS packages on Azure-SSIS IR, you can use managed identity authentication to connect to various Azure resources. Attualmente abbiamo già supportato l'autenticazione delle identità gestite nelle gestioni connessioni seguenti.

- [gestione connessione OLE DB](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [Gestione connessione ADO.NET](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Gestione connessione dell'archiviazione di Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
