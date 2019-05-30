---
title: Configurare Azure-SSIS Integration Runtime per eseguire il failover di database SQL di Azure | Microsoft Docs
description: Questo articolo descrive come configurare Azure-SSIS Integration Runtime eseguendo il failover e la replica geografica del Database SQL di Azure per il database SSISDB
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/14/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f0612a688bb1e0fd79325b9a1f9b43731a210d10
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399230"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Configurare Azure-SSIS Integration Runtime con la replica geografica del Database SQL di Azure e il failover

Questo articolo descrive come configurare Azure-SSIS Integration Runtime eseguendo la replica geografica del Database SQL di Azure per il database SSISDB. Quando si verifica un failover, è possibile verificare che Azure-SSIS Integration Runtime continui a funzionare con il database secondario.

Per altre informazioni sulla replica geografica e failover per il Database SQL, vedere [Panoramica: I gruppi di failover automatico e la replica geografica attivi](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenario 1 - Azure-SSIS Integration Runtime fa riferimento all'endpoint listener di lettura/scrittura

### <a name="conditions"></a>Condizioni

Questa sezione si applica quando vengono soddisfatte le condizioni seguenti:

- Azure-SSIS Integration Runtime fa riferimento all'endpoint listener di lettura/scrittura del gruppo di failover.

  AND

- Il server di database SQL *non* è configurato con la regola dell'endpoint servizio di rete virtuale.

### <a name="solution"></a>Soluzione

Quando si verifica il failover, è trasparente per Azure-SSIS Integration Runtime. Azure-SSIS Integration Runtime si connette automaticamente alla nuova replica primaria del gruppo di failover.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenario 2 - Azure-SSIS Integration Runtime fa riferimento all'endpoint del server primario

### <a name="conditions"></a>Condizioni

Questa sezione si applica quando una delle condizioni seguenti è vera:

- Azure-SSIS Integration Runtime fa riferimento all'endpoint del server primario del gruppo di failover. Questo endpoint viene modificato quando si verifica il failover.

  Oppure

- Il server di database SQL di Azure è configurato con la regola dell'endpoint servizio di rete virtuale.

  Oppure

- Il server di database è un'Istanza gestita di database SQL configurata con una rete virtuale.

### <a name="solution"></a>Soluzione

Quando si verifica il failover, è necessario eseguire le operazioni seguenti:

1. Arrestare il runtime di integrazione Azure-SSIS.

2. Riconfigurare il runtime di integrazione in modo che faccia riferimento al nuovo endpoint primario e a una rete virtuale nella nuova area.

3. Riavviare il runtime di integrazione.

Le sezioni seguenti descrivono questi passaggi in modo più dettagliato.

### <a name="prerequisites"></a>Prerequisiti

- Assicurarsi di aver abilitato il ripristino di emergenza per il server di database SQL di Azure nel caso di un'interruzione del servizio del server nello stesso momento. Per altre informazioni, vedere [Panoramica della continuità aziendale del database SQL di Azure](../sql-database/sql-database-business-continuity.md).

- Se si usa una rete virtuale nell'area corrente, è necessario optare per un'altra rete virtuale nella nuova area per connettere il runtime di integrazione Azure-SSIS. Per altre informazioni, vedere [Aggiungere un runtime di integrazione Azure-SSIS a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md).

- Se si usa un'installazione personalizzata, potrebbe essere necessario preparare un altro URI di firma di accesso condiviso per il contenitore BLOB in cui è archiviato lo script di installazione personalizzato e i file associati, in modo che continui a essere accessibile durante un'interruzione del servizio. Per altre informazioni, vedere [Personalizzare l'installazione del runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Passaggi

Seguire questi passaggi per arrestare il runtime di integrazione Azure-SSIS, passare il runtime di integrazione in una nuova area e avviarlo nuovamente.

1. Arrestare il runtime di integrazione nell'area originale.

2. Eseguire il comando seguente in PowerShell per aggiornare il runtime di integrazione con le nuove impostazioni.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Per altre informazioni su questo comando di PowerShell, vedere [Creare il runtime di integrazione Azure-SSIS in Azure Data Factory](create-azure-ssis-integration-runtime.md)

3. Avviare nuovamente il runtime di integrazione.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenario 3: collegare un database esistente SSISDB (catalogo SSIS) per un nuovo runtime di integrazione Azure-SSIS

Quando si verifica un'emergenza runtime di integrazione SSIS di Azure o Azure Data Factory nell'area corrente, è possibile rendere la mantiene SSISDB utilizzo di un nuovo runtime di integrazione Azure-SSIS in una nuova area.

### <a name="prerequisites"></a>Prerequisiti

- Se si usa una rete virtuale nell'area corrente, è necessario optare per un'altra rete virtuale nella nuova area per connettere il runtime di integrazione Azure-SSIS. Per altre informazioni, vedere [Aggiungere un runtime di integrazione Azure-SSIS a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md).

- Se si usa un'installazione personalizzata, potrebbe essere necessario preparare un altro URI di firma di accesso condiviso per il contenitore BLOB in cui è archiviato lo script di installazione personalizzato e i file associati, in modo che continui a essere accessibile durante un'interruzione del servizio. Per altre informazioni, vedere [Personalizzare l'installazione del runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Passaggi

Seguire questi passaggi per arrestare il runtime di integrazione Azure-SSIS, passare il runtime di integrazione in una nuova area e avviarlo nuovamente.

1. Eseguire stored procedure per rendere SSISDB collegata **\<new_data_factory_name\>** oppure  **\<new_integration_runtime_name\>** .
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Creare una nuova data factory denominata **\<new_data_factory_name\>** nella nuova area. Per altre informazioni, vedere creare una data factory.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    Per altre informazioni su questo comando di PowerShell, vedere [creare una data factory di Azure con PowerShell](quickstart-create-data-factory-powershell.md)

3. Creare un nuovo runtime di integrazione di Azure-SSIS denominato **\<new_integration_runtime_name\>** nella nuova area tramite Azure PowerShell.

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

4. Avviare nuovamente il runtime di integrazione.

## <a name="next-steps"></a>Passaggi successivi

Considerare queste altre opzioni di configurazione per il runtime di integrazione Azure-SSIS:

- [Configurare il runtime di integrazione Azure-SSIS per garantire prestazioni elevate](configure-azure-ssis-integration-runtime-performance.md)

- [Personalizzare l'installazione del runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Provisioning della Enterprise Edition per il runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
