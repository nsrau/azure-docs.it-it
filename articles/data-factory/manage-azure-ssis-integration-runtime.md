---
title: Riconfigurare il runtime di integrazione SSIS di Azure | Microsoft Docs
description: "Informazioni su come riconfigurare un runtime di integrazione SSIS di Azure nella Azure Data Factory dopo che è stato già effettuato il provisioning."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: spelluru
ms.openlocfilehash: b4b777a858febb4b601c038508e4fc313c189ac2
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="manage-an-azure-ssis-integration-runtime"></a>Gestire un runtime di integrazione SSIS di Azure
L'articolo [Creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md) illustra come creare un runtime di integrazione SSIS di Azure usando Azure Data Factory. Questo articolo fornisce informazioni su come riconfigurare un runtime di integrazione SSIS di Azure esistente.  

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-introduction.md).


## <a name="data-factory-ui"></a>Interfaccia utente di Data Factory 
È possibile usare l'interfaccia utente di Data Factory per arrestare, modificare, riconfigurare o eliminare un runtime di integrazione Azure-SSIS. 

1. Nell'**interfaccia utente di Data Factory** passare alla scheda **Modifica**. Per avviare l'interfaccia utente di Data Factory, fare clic su **Crea e monitora** nella home page della data factory.
2. Nel riquadro sinistro fare clic su **Connessioni**.
3. Nel riquadro destro passare a **Integration Runtime**. 
4. È possibile usare i pulsanti nella colonna Azioni per **arrestare**, **modificare** o **eliminare** il runtime di integrazione. Il pulsante **Codice** nella colonna **Azioni** consente di visualizzare la definizione JSON associata al runtime di integrazione.  
    
    ![Azioni per il runtime di integrazione Azure-SSIS](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Per riconfigurare un runtime di integrazione Azure-SSIS
1. Arrestare il runtime di integrazione facendo clic su **Arresto** nella colonna **Azioni**. Per aggiornare la visualizzazione elenco, fare clic su **Aggiorna** sulla barra degli strumenti. Dopo l'arresto del runtime di integrazione, la prima azione consente di avviarlo. 

    ![Azioni per il runtime di integrazione Azure-SSIS dopo l'arresto](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Per modificare o riconfigurare il runtime di integrazione, fare clic sul pulsante **Modifica** nella colonna **Azioni**. Nella finestra **Impostazione runtime di integrazione** modificare le impostazioni, ad esempio le dimensioni del nodo, il numero di nodi o il numero massimo di esecuzioni parallele per nodo. 
3. Per riavviare il runtime di integrazione, fare clic sul pulsante **Avvia** nella colonna **Azioni**.     

## <a name="azure-powershell"></a>Azure PowerShell
Dopo il provisioning e l’avvio di un'istanza di runtime di integrazione SSIS di Azure, è possibile riconfigurarlo eseguendo una sequenza di cmdlet PowerShell `Stop` - `Set` - `Start` consecutivamente. Ad esempio, lo script di PowerShell seguente modifica il numero di nodi allocato per l'istanza di runtime di integrazione SSIS di Azure a 5.

### <a name="reconfigure-an-azure-ssis-ir"></a>Riconfigurare un runtime di integrazione SSIS di Azure

1. In primo luogo arrestare il runtime di integrazione SSIS di Azure tramite il cmdlet [Stop-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/stop-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1). Questo comando rilascia tutti i nodi e arresta la fatturazione.

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Riconfigurare quindi il runtime di integrazione SSIS di Azure tramite il cmdlet [Set-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1). Il comando di esempio seguente consente applicare la scalabilità orizzontale a un runtime di integrazione SSIS di Azure per ottenere cinque nodi.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Avviare quindi il runtime di integrazione SSIS di Azure tramite il cmdlet [Start-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1). Questo comando alloca tutti i nodi per i pacchetti SSIS in esecuzione.   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Eliminare un runtime di integrazione SSIS di Azure
1. In primo luogo elencare tutti i runtime di integrazione SSIS di Azure esistenti nella data factory.

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Arrestare quindi tutti i runtime di integrazione SSIS di Azure esistenti nella data factory.

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Rimuovere tutti i runtime di integrazione SSIS di Azure esistenti nella data factory singolarmente.

    ```powershell
    Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Rimuovere infine la data factory.

    ```powershell
    Remove-AzureRmDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Se è stato creato un nuovo gruppo di risorse, rimuovere il gruppo di risorse.

    ```powershell
    Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui runtime SSIS di Azure, vedere gli argomenti seguenti: 

- [Runtime di integrazione SSIS di Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo vengono fornite le informazioni sui runtime di integrazione in generale incluso il runtime di integrazione SSIS di Azure. 
- [Esercitazione: distribuire i pacchetti SSIS in Azure](tutorial-deploy-ssis-packages-azure.md). In questo articolo vengono fornite le istruzioni passo per passo per creare un runtime di integrazione SSIS di Azure e utilizzare un database Azure SQL per ospitare il catalogo SSIS. 
- [Procedura: come creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md). Questo articolo amplia l'esercitazione e fornisce istruzioni sull'utilizzo dell’istanza gestita di database SQL di Azure (anteprima privata) e sull’aggiunta del runtime di integrazione a un VNet. 
- [Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md). Questo articolo fornisce informazioni sull'aggiunta di un runtime di integrazione SSIS di Azure a una rete virtuale di Azure (VNet). Indica inoltre i passaggi per usare il portale di Azure per configurare VNet in modo che il runtime di integrazione SSIS di Azure possa essere aggiunto alla rete virtuale. 
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo viene illustrato come recuperare informazioni su un runtime di integrazione SSIS di Azure e le descrizioni degli stati nelle informazioni restituite. 
 
