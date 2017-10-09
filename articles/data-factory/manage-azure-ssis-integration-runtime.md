---
title: Gestire il runtime di integrazione SSIS di Azure | Documentazione Microsoft
description: "Altre informazioni su come riconfigurare il runtime di integrazione SSIS di Azure nella Azure Data Factory dopo che è stato già effettuato il provisioning."
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
ms.date: 09/25/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 780e5673b5ed159a7f170373d54fea7c1713a910
ms.contentlocale: it-it
ms.lasthandoff: 09/28/2017

---

# <a name="manage-an-azure-ssis-integration-runtime"></a>Gestire un runtime di integrazione SSIS di Azure
L’articolo [Creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md) illustra come creare un runtime di integrazione SSIS di Azure usando Azure Data Factory. In questo articolo lo integra, fornendo informazioni su come arrestare, avviare, riconfigurare o rimuovere un runtime di integrazione SSIS di Azure.  


## <a name="stop"></a>Arresto 
Arresta il runtime di integrazione SSIS di Azure. Questo comando rilascia tutti i nodi e arresta la fatturazione.

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
```

## <a name="start"></a>Inizia 
Avvia il runtime di integrazione SSIS di Azure. Questo comando alloca tutti i nodi e avvia la fatturazione.   

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
```

## <a name="reconfigure"></a>Riconfigurare
Dopo il provisioning e l’avvio di un'istanza di runtime di integrazione SSIS di Azure, è possibile riconfigurarlo eseguendo una sequenza di cmdlet PowerShell `Stop` - `Set` - `Start` consecutivamente. Ad esempio, lo script di PowerShell seguente modifica il numero di nodi allocato per l'istanza di runtime di integrazione SSIS di Azure a 5.

1. Prima di tutto, usare il comando seguente per avviare il runtime di integrazione SSIS di Azure:

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Quindi scalare orizzontalmente il runtime di integrazione SSIS di Azure a cinque nodi.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Infine, avviare il runtime di integrazione SSIS di Azure. Questo alloca tutti i nodi per i pacchetti SSIS in esecuzione.   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

## <a name="remove"></a>Rimuovere
Usare il comando seguente per rimuovere un runtime di integrazione SSIS di Azure esistente: 

```powershell
Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui runtime SSIS di Azure, vedere gli argomenti seguenti: 

- [Runtime di integrazione SSIS di Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo vengono fornite le informazioni sui runtime di integrazione in generale incluso il runtime di integrazione SSIS di Azure. 
- [Esercitazione: distribuire i pacchetti SSIS in Azure](tutorial-deploy-ssis-packages-azure.md). In questo articolo vengono fornite le istruzioni passo per passo per creare un runtime di integrazione SSIS di Azure e utilizzare un database Azure SQL per ospitare il catalogo SSIS. 
- [Procedura: come creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md). Questo articolo amplia l'esercitazione e fornisce istruzioni sull'utilizzo dell’istanza gestita di database SQL di Azure (anteprima privata) e sull’aggiunta del runtime di integrazione a un VNet. 
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo viene illustrato come recuperare informazioni su un runtime di integrazione SSIS di Azure e le descrizioni degli stati nelle informazioni restituite. 
- [Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md). Questo articolo fornisce informazioni sull'aggiunta di un runtime di integrazione SSIS di Azure a una rete virtuale di Azure (VNet). Indica inoltre i passaggi per usare il portale di Azure per configurare VNet in modo che il runtime di integrazione SSIS di Azure possa essere aggiunto alla rete virtuale. 

