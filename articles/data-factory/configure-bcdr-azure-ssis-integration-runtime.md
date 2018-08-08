---
title: Consigli per la continuità aziendale e ripristino di emergenza (BCDR) per il runtime di integrazione Azure-SSIS | Microsoft Docs
description: Questo articolo presenta i consigli per la continuità aziendale e ripristino di emergenza per il runtime di integrazione Azure-SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/26/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 37347df2d543116085f52fed76c692b60fac2ad6
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295311"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-recommendations-for-azure-ssis-integration-runtime"></a>Consigli per la continuità aziendale e ripristino di emergenza (BCDR) per il runtime di integrazione Azure-SSIS

Per il ripristino di emergenza, è possibile arrestare il runtime di integrazione Azure-SSIS nell'area in cui è in esecuzione e passare a un'altra area di per avviarlo nuovamente. È consigliabile usare le [aree associate di Azure](../best-practices-availability-paired-regions.md) per questo scopo.

## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi di aver abilitato il ripristino di emergenza per il server di database SQL di Azure nel caso di un'interruzione del servizio del server nello stesso momento. Per altre informazioni, vedere [Panoramica della continuità aziendale del database SQL di Azure](../sql-database/sql-database-business-continuity.md).

- Se si usa una rete virtuale nell'area corrente, è necessario optare per un'altra rete virtuale nella nuova area per connettere il runtime di integrazione Azure-SSIS. Per altre informazioni, vedere [Aggiungere un runtime di integrazione Azure-SSIS a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md).

- Se si usa un'installazione personalizzata, potrebbe essere necessario preparare un altro URI di firma di accesso condiviso per il contenitore BLOB in cui è archiviato lo script di installazione personalizzato e i file associati, in modo che continui a essere accessibile durante un'interruzione del servizio. Per altre informazioni, vedere [Personalizzare l'installazione del runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="steps"></a>Passaggi

Seguire questi passaggi per arrestare il runtime di integrazione Azure-SSIS, passare il runtime di integrazione in una nuova area e avviarlo nuovamente.

1. Arrestare il runtime di integrazione nell'area originale.

2. Eseguire il comando seguente in PowerShell per aggiornare il runtime di integrazione

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
