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
ms.openlocfilehash: 2012ccf4d9fd3e62ba248f29f922f868077e4061
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2018
ms.locfileid: "42141579"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Configurare Azure-SSIS Integration Runtime con la replica geografica del Database SQL di Azure e il failover

Questo articolo descrive come configurare Azure-SSIS Integration Runtime eseguendo la replica geografica del Database SQL di Azure per il database SSISDB. Quando si verifica un failover, è possibile verificare che Azure-SSIS Integration Runtime continui a funzionare con il database secondario.

Per altre informazioni sulla replica geografica e il failover per il Database SQL, vedere [Panoramica: Gruppi di failover e replica geografica attiva](../sql-database/sql-database-geo-replication-overview.md).

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenario 1 - Azure-SSIS Integration Runtime fa riferimento all'endpoint listener di lettura/scrittura

### <a name="conditions"></a>Condizioni

Questa sezione si applica quando vengono soddisfatte le condizioni seguenti:

- Azure-SSIS Integration Runtime fa riferimento all'endpoint listener di lettura/scrittura del gruppo di failover.

  AND

- Il server di Database SQL *non* è configurato con la regola dell'endpoint di servizio di rete virtuale.

### <a name="solution"></a>Soluzione

Quando si verifica il failover, è trasparente per Azure-SSIS Integration Runtime. Azure-SSIS Integration Runtime si connette automaticamente alla nuova replica primaria del gruppo di failover.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenario 2 - Azure-SSIS Integration Runtime fa riferimento all'endpoint del server primario

### <a name="conditions"></a>Condizioni

Questa sezione si applica quando una delle condizioni seguenti è vera:

- Azure-SSIS Integration Runtime fa riferimento all'endpoint del server primario del gruppo di failover. Questo endpoint viene modificato quando si verifica il failover.

  Oppure

- Il server di Database SQL di Azure è configurato con la regola dell'endpoint di servizio di rete virtuale.

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
    Set-AzureRmDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Per altre informazioni su questo comando di PowerShell, vedere [Creare il runtime di integrazione Azure-SSIS in Azure Data Factory](create-azure-ssis-integration-runtime.md)

3. Avviare nuovamente il runtime di integrazione.

## <a name="next-steps"></a>Passaggi successivi

Considerare queste altre opzioni di configurazione per il runtime di integrazione Azure-SSIS:

- [Configurare il runtime di integrazione Azure-SSIS per garantire prestazioni elevate](configure-azure-ssis-integration-runtime-performance.md)

- [Personalizzare l'installazione del runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Provisioning della Enterprise Edition per il runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
