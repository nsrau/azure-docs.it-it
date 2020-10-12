---
title: Configurare il runtime di integrazione SSIS di Azure per il failover del database SQL
description: Questo articolo descrive come configurare il runtime di integrazione Azure-SSIS con la replica geografica del database SQL di Azure e il failover per il database SSISDB
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
ms.openlocfilehash: e1b70e0e3eb54253972afded1bd37363d1a868e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84195722"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-sql-database-geo-replication-and-failover"></a>Configurare il runtime di integrazione Azure-SSIS con la replica geografica e il failover del database SQL

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo descrive come configurare il runtime di integrazione SSIS di Azure con la replica geografica del database SQL di Azure per il database SSISDB. Quando si verifica un failover, è possibile verificare che Azure-SSIS Integration Runtime continui a funzionare con il database secondario.

Per altre informazioni sulla replica geografica e il failover per il Database SQL, vedere [Panoramica: Gruppi di failover e replica geografica attiva](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-a-sql-managed-instance"></a>Azure-SSIS IR failover con SQL Istanza gestita

### <a name="prerequisites"></a>Prerequisiti

Un Istanza gestita SQL di Azure usa una *chiave master del database (DMK)* per proteggere i dati, le credenziali e le informazioni di connessione archiviate in un database. Per abilitare la decrittografia automatica di DMK, una copia della chiave viene crittografata tramite la *chiave master del server (SMK)*. 

SMK non viene replicato in un gruppo di failover. È necessario aggiungere una password sia nelle istanze primarie che secondarie per la decrittografia DMK dopo il failover.

1. Eseguire il comando seguente per SSISDB nell'istanza primaria. Questo passaggio consente di aggiungere una nuova password di crittografia.

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. Creare un gruppo di failover in un Istanza gestita SQL.

3. Eseguire **sp_control_dbmasterkey_password** nell'istanza secondaria usando la nuova password di crittografia.

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Scenario 1: Azure-SSIS IR punta a un endpoint del listener di lettura/scrittura

Se si desidera che il Azure-SSIS IR punti a un endpoint del listener di lettura/scrittura, è necessario innanzitutto puntare all'endpoint del server primario. Dopo aver inserito SSISDB in un gruppo di failover, è possibile passare all'endpoint del listener di lettura/scrittura e riavviare il Azure-SSIS IR.

#### <a name="solution"></a>Soluzione

Quando si verifica il failover, seguire questa procedura:

1. Arrestare il Azure-SSIS IR nell'area primaria.

2. Modificare la Azure-SSIS IR con le informazioni sull'URI della nuova area, della rete virtuale e della firma di accesso condiviso per l'installazione personalizzata nell'istanza secondaria. Poiché il Azure-SSIS IR punta a un listener di lettura/scrittura e l'endpoint è trasparente per il Azure-SSIS IR, non è necessario modificare l'endpoint.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Riavviare il Azure-SSIS IR.

### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Scenario 2: Azure-SSIS IR punta a un endpoint server primario

Questo scenario è adatto se il Azure-SSIS IR punta a un endpoint server primario.

#### <a name="solution"></a>Soluzione

Quando si verifica il failover, seguire questa procedura:

1. Arrestare il Azure-SSIS IR nell'area primaria.

2. Modificare la Azure-SSIS IR con le informazioni sulla nuova area, sull'endpoint e sulla rete virtuale per l'istanza secondaria.

    ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database endpoint" `
                    -CatalogAdminCredential "Azure SQL Database admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
        ```

3. Restart the Azure-SSIS IR.

### Scenario 3: Azure-SSIS IR is pointing to a public endpoint of a SQL Managed Instance

This scenario is suitable if the Azure-SSIS IR is pointing to a public endpoint of a Azure SQL Managed Instance and it doesn't join to a virtual network. The only difference from scenario 2 is that you don't need to edit virtual network information for the Azure-SSIS IR after failover.

#### Solution

When failover occurs, take the following steps:

1. Stop the Azure-SSIS IR in the primary region.

2. Edit the Azure-SSIS IR with the new region and endpoint information for the secondary instance.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Riavviare il Azure-SSIS IR.

### <a name="scenario-4-attach-an-existing-ssisdb-instance-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenario 4: associazione di un'istanza di SSISDB esistente (catalogo SSIS) a una nuova Azure-SSIS IR

Questo scenario è adatto se si desidera che SSISDB funzioni con un nuovo Azure-SSIS IR in una nuova area quando si verifica un'emergenza Azure Data Factory o Azure-SSIS IR nell'area corrente.

#### <a name="solution"></a>Soluzione

Quando si verifica il failover, seguire questa procedura.

> [!NOTE]
> Usare PowerShell per il passaggio 4 (creazione del runtime di integrazione). In caso contrario, il portale di Azure segnalerà un errore che indica che SSISDB esiste già.

1. Arrestare il Azure-SSIS IR nell'area primaria.

2. Eseguire un stored procedure per aggiornare i metadati in SSISDB in modo da accettare le connessioni da **\<new_data_factory_name\>** e **\<new_integration_runtime_name\>** .
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Creare un nuovo data factory denominato **\<new_data_factory_name\>** nella nuova area.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    Per altre informazioni su questo comando di PowerShell, vedere [creare un data factory di Azure con PowerShell](quickstart-create-data-factory-powershell.md).

4. Creare un nuovo Azure-SSIS IR denominato **\<new_integration_runtime_name\>** nella nuova area usando Azure PowerShell.

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

    Per altre informazioni su questo comando di PowerShell, vedere [creare il runtime di integrazione SSIS di Azure in Azure Data Factory](create-azure-ssis-integration-runtime.md).



## <a name="azure-ssis-ir-failover-with-sql-database"></a>Failover di Azure-SSIS IR con il database SQL

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Scenario 1: Azure-SSIS IR punta a un endpoint del listener di lettura/scrittura

Questo scenario è adatto nei casi seguenti:

- Il Azure-SSIS IR fa riferimento all'endpoint del listener di lettura/scrittura del gruppo di failover.
- Il server di database SQL *non* è configurato con la regola per l'endpoint del servizio rete virtuale.

Se si desidera che il Azure-SSIS IR punti a un endpoint del listener di lettura/scrittura, è necessario innanzitutto puntare all'endpoint del server primario. Dopo aver inserito SSISDB in un gruppo di failover, è possibile passare a un endpoint del listener di lettura/scrittura e riavviare il Azure-SSIS IR.

#### <a name="solution"></a>Soluzione

Quando si verifica il failover, è trasparente per il Azure-SSIS IR. Azure-SSIS Integration Runtime si connette automaticamente alla nuova replica primaria del gruppo di failover. 

Se si desidera aggiornare l'area o altre informazioni nella Azure-SSIS IR, è possibile arrestarla, modificarla e riavviarla.


### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Scenario 2: Azure-SSIS IR punta a un endpoint server primario

Questo scenario è adatto se il Azure-SSIS IR punta a un endpoint server primario.

#### <a name="solution"></a>Soluzione

Quando si verifica il failover, seguire questa procedura:

1. Arrestare il Azure-SSIS IR nell'area primaria.

2. Modificare la Azure-SSIS IR con le informazioni sulla nuova area, sull'endpoint e sulla rete virtuale per l'istanza secondaria.

    ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                        -CatalogServerEndpoint "Azure SQL Database endpoint" `
                        -CatalogAdminCredential "Azure SQL Database admin credentials" `
                        -VNetId "new VNet" `
                        -Subnet "new subnet" `
                        -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Riavviare il Azure-SSIS IR.

### <a name="scenario-3-attach-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenario 3: associazione di un database SSISDB esistente (catalogo SSIS) a una nuova Azure-SSIS IR

Questo scenario è appropriato se si vuole eseguire il provisioning di un nuovo Azure-SSIS IR in un'area secondaria. È inoltre appropriato se si desidera che il database SSISDB continui a usare un nuovo Azure-SSIS IR in una nuova area quando si verifica un'emergenza Azure Data Factory o Azure-SSIS IR nell'area corrente.

#### <a name="solution"></a>Soluzione

Quando si verifica il failover, seguire questa procedura.

> [!NOTE]
> Usare PowerShell per il passaggio 4 (creazione del runtime di integrazione). In caso contrario, il portale di Azure segnalerà un errore che indica che SSISDB esiste già.

1. Arrestare il Azure-SSIS IR nell'area primaria.

2. Eseguire un stored procedure per aggiornare i metadati in SSISDB in modo da accettare le connessioni da **\<new_data_factory_name\>** e **\<new_integration_runtime_name\>** .
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Creare un nuovo data factory denominato **\<new_data_factory_name\>** nella nuova area.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    Per altre informazioni su questo comando di PowerShell, vedere [creare un data factory di Azure con PowerShell](quickstart-create-data-factory-powershell.md).

4. Creare un nuovo Azure-SSIS IR denominato **\<new_integration_runtime_name\>** nella nuova area usando Azure PowerShell.

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

    Per altre informazioni su questo comando di PowerShell, vedere [creare il runtime di integrazione SSIS di Azure in Azure Data Factory](create-azure-ssis-integration-runtime.md).


## <a name="next-steps"></a>Passaggi successivi

Considerare queste altre opzioni di configurazione per il runtime di integrazione Azure-SSIS:

- [Configurare il runtime di integrazione Azure-SSIS per prestazioni elevate](configure-azure-ssis-integration-runtime-performance.md)

- [Personalizzare l'installazione del runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Eseguire il provisioning di Enterprise Edition per il runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
