---
title: Configurare Azure-SSIS Integration Runtime per il failover del database SQL
description: Questo articolo descrive come configurare Azure-SSIS Integration Runtime eseguendo il failover e la replica geografica del Database SQL di Azure per il database SSISDB
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: b4b679b15092531ff9553d221f23d7f030fc1def
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592092"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Configurare Azure-SSIS Integration Runtime con la replica geografica del Database SQL di Azure e il failover

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo descrive come configurare Azure-SSIS Integration Runtime eseguendo la replica geografica del Database SQL di Azure per il database SSISDB. Quando si verifica un failover, è possibile verificare che Azure-SSIS Integration Runtime continui a funzionare con il database secondario.

Per altre informazioni sulla replica geografica e il failover per il Database SQL, vedere [Panoramica: Gruppi di failover e replica geografica attiva](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-azure-sql-database-managed-instance"></a>Failover Azure-SSIS IR con Istanza gestita di database SQL di Azure

### <a name="prerequisites"></a>Prerequisiti

Istanza gestita di database SQL di Azure usa la **chiave master del database (DMK)** per proteggere i dati, le credenziali e le informazioni di connessione archiviate nel database. Per abilitare la decrittografia automatica di DMK, una copia della chiave viene crittografata con la **chiave master del server (SMK)**. Tuttavia, SMK non viene replicato in un gruppo di failover, pertanto è necessario aggiungere una password aggiuntiva nelle istanze primarie e secondarie per la decrittografia DMK dopo il failover.

1. Eseguire il comando seguente nell'istanza primaria di SSISDB. Questo passaggio aggiunge una nuova password di crittografia.

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. Creare il gruppo di failover su Istanza gestita di database SQL di Azure.

3. Eseguire **sp_control_dbmasterkey_password** nell'istanza secondaria usando la nuova password di crittografia.

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenario 1 - Azure-SSIS Integration Runtime fa riferimento all'endpoint listener di lettura/scrittura

Se si desidera Azure-SSIS IR puntare all'endpoint del listener di lettura/scrittura, è necessario innanzitutto puntare all'endpoint del server primario. Dopo aver inserito SSISDB nel gruppo di failover, è possibile passare all'endpoint del listener di lettura/scrittura e riavviare Azure-SSIS IR.

#### <a name="solution"></a>Soluzione

Quando si verifica il failover, è necessario eseguire le operazioni seguenti:

1. Arrestare Azure-SSIS IR nell'area primaria.

2. Modificare Azure-SSIS IR con la nuova area, VNET e le informazioni sull'URI della firma di accesso condiviso personalizzato dell'istanza secondaria. Poiché Azure-SSIS IR fa riferimento al listener di lettura/scrittura e l'endpoint è trasparente per Azure-SSIS IR, non è necessario modificare l'endpoint.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Riavviare Azure-SSIS IR.

### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenario 2 - Azure-SSIS Integration Runtime fa riferimento all'endpoint del server primario

Lo scenario è adatto se Azure-SSIS IR fa riferimento all'endpoint del server primario.

#### <a name="solution"></a>Soluzione

Quando si verifica il failover, è necessario eseguire le operazioni seguenti:

1. Arrestare Azure-SSIS IR nell'area primaria.

2. Modificare Azure-SSIS IR con le nuove informazioni relative a Region, endpoint e VNET dell'istanza secondaria.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Riavviare Azure-SSIS IR.

### <a name="scenario-3---azure-ssis-ir-is-pointing-to-public-endpoint-of-azure-sql-database-managed-instance"></a>Scenario 3: Azure-SSIS IR punta all'endpoint pubblico di Istanza gestita di database SQL di Azure

Lo scenario è adatto se il Azure-SSIS IR punta all'endpoint pubblico di Istanza gestita di database SQL di Azure e non viene aggiunto a VNET. L'unica differenza tra lo scenario 2 e questi scenari è che non è necessario modificare le informazioni VNET di Azure-SSIS IR dopo il failover.

#### <a name="solution"></a>Soluzione

Quando si verifica il failover, è necessario eseguire le operazioni seguenti:

1. Arrestare Azure-SSIS IR nell'area primaria.

2. Modificare Azure-SSIS IR con le nuove informazioni sull'area e sull'endpoint dell'istanza secondaria.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Riavviare Azure-SSIS IR.

### <a name="scenario-4---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenario 4: associazione di un database SSISDB esistente (catalogo SSIS) a una nuova Azure-SSIS IR

Questo scenario è appropriato se si vuole eseguire il provisioning di un nuovo Azure-SSIS IR nell'area secondaria o si vuole che il database SSISDB continui a usare un nuovo Azure-SSIS IR in una nuova area quando si verifica un'emergenza di ADF o Azure-SSIS IR nell'area corrente.

#### <a name="solution"></a>Soluzione

Quando si verifica il failover, è necessario eseguire le operazioni seguenti:

> [!NOTE]
> Il passaggio 4 (creazione di IR) deve essere eseguito tramite PowerShell. Portale di Azure segnalerà un errore che informa che il database SSISDB esiste già.

1. Arrestare Azure-SSIS IR nell'area primaria.

2. Eseguire stored procedure per aggiornare i metadati in SSISDB in modo da accettare le connessioni da ** \<new_data_factory_name\> ** e ** \<new_integration_runtime_name\>**.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Creare un nuovo data factory denominato ** \<new_data_factory_name\> ** nella nuova area. Per altre informazioni, vedere creare un data factory.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    Per altre informazioni su questo comando di PowerShell, vedere [creare un data factory di Azure con PowerShell](quickstart-create-data-factory-powershell.md)

4. Creare un nuovo Azure-SSIS IR denominato ** \<new_integration_runtime_name\> ** nella nuova area utilizzando Azure PowerShell.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Per altre informazioni su questo comando di PowerShell, vedere [Creare il runtime di integrazione Azure-SSIS in Azure Data Factory](create-azure-ssis-integration-runtime.md)



## <a name="azure-ssis-ir-failover-with-azure-sql-database"></a>Failover di Azure-SSIS IR con il database SQL di Azure

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenario 1 - Azure-SSIS Integration Runtime fa riferimento all'endpoint listener di lettura/scrittura

Questo scenario è adatto Azure-SSIS IR punta all'endpoint del listener di lettura/scrittura del gruppo di failover e il server di database SQL *non* è configurato con la regola dell'endpoint del servizio di rete virtuale. Se si desidera Azure-SSIS IR puntare all'endpoint del listener di lettura/scrittura, è necessario innanzitutto puntare all'endpoint del server primario. Dopo aver inserito SSISDB nel gruppo di failover, è possibile passare all'endpoint del listener di lettura/scrittura e riavviare Azure-SSIS IR.

#### <a name="solution"></a>Soluzione

Quando si verifica il failover, è trasparente per Azure-SSIS Integration Runtime. Azure-SSIS Integration Runtime si connette automaticamente alla nuova replica primaria del gruppo di failover. Se si desidera aggiornare l'area o altre informazioni in Azure-SSIS IR, è possibile arrestarla, modificarla e riavviarla.


### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenario 2 - Azure-SSIS Integration Runtime fa riferimento all'endpoint del server primario

Lo scenario è adatto se Azure-SSIS IR fa riferimento all'endpoint del server primario.

#### <a name="solution"></a>Soluzione

Quando si verifica il failover, è necessario eseguire le operazioni seguenti:

1. Arrestare Azure-SSIS IR nell'area primaria.

2. Modificare Azure-SSIS IR con le nuove informazioni Region, endpoint e VNET dell'istanza secondaria.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Riavviare Azure-SSIS IR.

### <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenario 3: associazione di un database SSISDB esistente (catalogo SSIS) a una nuova Azure-SSIS IR

Questo scenario è appropriato se si vuole eseguire il provisioning di un nuovo Azure-SSIS IR nell'area secondaria o si vuole che il database SSISDB continui a usare un nuovo Azure-SSIS IR in una nuova area quando si verifica un'emergenza di ADF o Azure-SSIS IR nell'area corrente.

#### <a name="solution"></a>Soluzione

> [!NOTE]
> Il passaggio 4 (creazione di IR) deve essere eseguito tramite PowerShell. Portale di Azure segnalerà un errore che informa che il database SSISDB esiste già.

1. Arrestare Azure-SSIS IR nell'area primaria.

2. Eseguire stored procedure per aggiornare i metadati in SSISDB in modo da accettare le connessioni da ** \<new_data_factory_name\> ** e ** \<new_integration_runtime_name\>**.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Creare un nuovo data factory denominato ** \<new_data_factory_name\> ** nella nuova area. Per altre informazioni, vedere creare un data factory.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    Per altre informazioni su questo comando di PowerShell, vedere [creare un data factory di Azure con PowerShell](quickstart-create-data-factory-powershell.md)

4. Creare un nuovo Azure-SSIS IR denominato ** \<new_integration_runtime_name\> ** nella nuova area utilizzando Azure PowerShell.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Per altre informazioni su questo comando di PowerShell, vedere [Creare il runtime di integrazione Azure-SSIS in Azure Data Factory](create-azure-ssis-integration-runtime.md)


## <a name="next-steps"></a>Passaggi successivi

Considerare queste altre opzioni di configurazione per il runtime di integrazione Azure-SSIS:

- [Configurare il runtime di integrazione Azure-SSIS per garantire prestazioni elevate](configure-azure-ssis-integration-runtime-performance.md)

- [Personalizzare l'installazione del runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Provisioning della Enterprise Edition per il Runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
