---
title: Riconfigurare il runtime di integrazione SSIS di Azure
description: Informazioni su come riconfigurare un runtime di integrazione SSIS di Azure nella Azure Data Factory dopo che è stato già effettuato il provisioning.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: f609cfb0945d79cfa8ae21b786a5761b92b9dabb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84324624"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Riconfigurare il runtime di integrazione SSIS di Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo descrive come configurare un runtime di integrazione Azure-SSIS esistente. Creare un runtime di integrazione Azure-SSIS (IR) in Azure Data Factory, vedere [Creare un runtime di integrazione SSIS di Azure in Azure Data Factory](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>Interfaccia utente di Data Factory 
È possibile usare l'interfaccia utente di Data Factory per arrestare, modificare, riconfigurare o eliminare un runtime di integrazione Azure-SSIS. 

1. Aprire Data Factory interfaccia utente selezionando il riquadro **autore & monitoraggio** sul home page del data factory.
2. Selezionare l'hub **Gestisci** sotto **Home**, **modificare**e **monitorare** gli hub per visualizzare il riquadro **connessioni** .

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Per riconfigurare un runtime di integrazione Azure-SSIS
Nel riquadro **Connessioni** dell'hub **Gestione**, passare alla pagina **Integration runtimes** (Runtime di integrazione) e selezionare **Aggiorna**. 

   ![Riquadro Connessioni](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   È possibile modificare o riconfigurare l'istanza di Azure-SSIS IR selezionando il nome corrispondente. È anche possibile selezionare i pulsanti pertinenti per monitorare/avviare/arrestare/eliminare l'istanza di Azure-SSIS IR, generare automaticamente una pipeline di Azure Data Factory con l'attività Esegui pacchetto SSIS per l'esecuzione nell'istanza di Azure-SSIS IR e visualizzare il codice o il payload JSON di tale istanza.  È possibile modificare o eliminare l'istanza di Azure-SSIS IR solo quando è arrestata.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dopo il provisioning e l’avvio di un'istanza di runtime di integrazione SSIS di Azure, è possibile riconfigurarlo eseguendo una sequenza di cmdlet PowerShell `Stop` - `Set` - `Start` consecutivamente. Ad esempio, lo script di PowerShell seguente modifica il numero di nodi allocato per l'istanza di runtime di integrazione SSIS di Azure a 5.

### <a name="reconfigure-an-azure-ssis-ir"></a>Riconfigurare un runtime di integrazione SSIS di Azure

1. Arrestare prima di tutto il runtime di integrazione SSIS di Azure usando il cmdlet [Stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) . Questo comando rilascia tutti i nodi e arresta la fatturazione.

   ```powershell
   Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
   ```
2. Riconfigurare quindi il Azure-SSIS IR usando il cmdlet [set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) . Il comando di esempio seguente consente applicare la scalabilità orizzontale a un runtime di integrazione SSIS di Azure per ottenere cinque nodi.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
   ```  
3. Avviare quindi il runtime di integrazione SSIS di Azure usando il cmdlet [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) . Questo comando alloca tutti i nodi per i pacchetti SSIS in esecuzione.   

   ```powershell
   Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
   ```

### <a name="delete-an-azure-ssis-ir"></a>Eliminare un runtime di integrazione SSIS di Azure
1. In primo luogo elencare tutti i runtime di integrazione SSIS di Azure esistenti nella data factory.

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
   ```
2. Arrestare quindi tutti i runtime di integrazione SSIS di Azure esistenti nella data factory.

   ```powershell
   Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
   ```
3. Rimuovere tutti i runtime di integrazione SSIS di Azure esistenti nella data factory singolarmente.

   ```powershell
   Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
   ```
4. Rimuovere infine la data factory.

   ```powershell
   Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
   ```
5. Se è stato creato un nuovo gruppo di risorse, rimuovere il gruppo di risorse.

   ```powershell
   Remove-AzResourceGroup -Name $ResourceGroupName -Force 
   ```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui runtime SSIS di Azure, vedere gli argomenti seguenti: 

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo vengono fornite le informazioni sui runtime di integrazione in generale incluso il runtime di integrazione SSIS di Azure. 
- [Esercitazione: distribuire i pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md). Questo articolo fornisce istruzioni dettagliate per creare un Azure-SSIS IR e usa il database SQL di Azure per ospitare il catalogo SSIS. 
- [Procedura: come creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md). Questo articolo amplia l'esercitazione e fornisce istruzioni sull'uso di Azure SQL Istanza gestita e sull'aggiunta del runtime di integrazione a una rete virtuale. 
- [Aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md). Questo articolo offre informazioni concettuali sull'aggiunta di un runtime di integrazione Azure-SSIS a una rete virtuale di Azure. Indica inoltre i passaggi per usare il portale di Azure per configurare la rete virtuale in modo che il runtime di integrazione Azure-SSIS possa essere aggiunto alla rete virtuale. 
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo viene illustrato come recuperare informazioni su un runtime di integrazione SSIS di Azure e le descrizioni degli stati nelle informazioni restituite.
