---
title: Riconfigurare il runtime di integrazione SSIS di Azure | Microsoft Docs
description: Informazioni su come riconfigurare un runtime di integrazione SSIS di Azure nella Azure Data Factory dopo che è stato già effettuato il provisioning.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 3c1178a20debc36fbdbbd374eaf9adb6005a93a7
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454939"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Riconfigurare il runtime di integrazione SSIS di Azure
Questo articolo descrive come configurare un runtime di integrazione Azure-SSIS esistente. Creare un runtime di integrazione Azure-SSIS (IR) in Azure Data Factory, vedere [Creare un runtime di integrazione SSIS di Azure in Azure Data Factory](create-azure-ssis-integration-runtime.md).  

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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dopo il provisioning e l’avvio di un'istanza di runtime di integrazione SSIS di Azure, è possibile riconfigurarlo eseguendo una sequenza di cmdlet PowerShell `Stop` - `Set` - `Start` consecutivamente. Ad esempio, lo script di PowerShell seguente modifica il numero di nodi allocato per l'istanza di runtime di integrazione SSIS di Azure a 5.

### <a name="reconfigure-an-azure-ssis-ir"></a>Riconfigurare un runtime di integrazione SSIS di Azure

1. In primo luogo, arrestare il runtime di integrazione Azure-SSIS usando il [Stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) cmdlet. Questo comando rilascia tutti i nodi e arresta la fatturazione.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Successivamente, riconfigurare il runtime di integrazione Azure-SSIS usando il [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) cmdlet. Il comando di esempio seguente consente applicare la scalabilità orizzontale a un runtime di integrazione SSIS di Azure per ottenere cinque nodi.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Quindi, avviare il runtime di integrazione Azure-SSIS usando il [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) cmdlet. Questo comando alloca tutti i nodi per i pacchetti SSIS in esecuzione.   

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

- [Runtime di integrazione SSIS di Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo vengono fornite le informazioni sui runtime di integrazione in generale incluso il runtime di integrazione SSIS di Azure. 
- [Esercitazione: distribuire i pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md). In questo articolo vengono fornite le istruzioni passo per passo per creare un runtime di integrazione SSIS di Azure e utilizzare un database SQL di Azure per ospitare il catalogo SSIS. 
- [Procedura: Creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md). Questo articolo amplia l'esercitazione e offre istruzioni sull'uso dell'Istanza gestita di database SQL di Azure e sull'aggiunta del runtime di integrazione a una rete virtuale. 
- [Aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md). Questo articolo offre informazioni concettuali sull'aggiunta di un runtime di integrazione Azure-SSIS a una rete virtuale di Azure. Indica inoltre i passaggi per usare il portale di Azure per configurare la rete virtuale in modo che il runtime di integrazione Azure-SSIS possa essere aggiunto alla rete virtuale. 
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo viene illustrato come recuperare informazioni su un runtime di integrazione SSIS di Azure e le descrizioni degli stati nelle informazioni restituite. 
 
