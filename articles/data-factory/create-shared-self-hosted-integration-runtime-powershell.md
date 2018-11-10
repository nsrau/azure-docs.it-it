---
title: Creare un runtime di integrazione self-hosted condiviso in Azure Data Factory con PowerShell | Microsoft Docs
description: Informazioni su come creare un runtime di integrazione self-hosted condiviso in Azure Data Factory che consente a più data factory di accedere al runtime di integrazione.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: abnarain
ms.openlocfilehash: d7f3fbcb3235c8c620876e68a62f3e491770779d
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50252033"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory-with-powershell"></a>Creare un runtime di integrazione self-hosted condiviso in Azure Data Factory con PowerShell

Questa guida dettagliata illustra come creare un runtime di integrazione self-hosted condiviso in Azure Data Factory usando Azure PowerShell. È quindi possibile usare il runtime di integrazione self-hosted condiviso in un'altra data factory. In questa esercitazione vengono completati i passaggi seguenti: 

1. Creare una data factory. 
1. Creare un runtime di integrazione self-hosted.
1. Condividere il runtime di integrazione self-hosted con altre data factory.
1. Creare un runtime di integrazione collegato.
1. Revocare la condivisione.

## <a name="prerequisites"></a>Prerequisiti 

- **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare. 

- **Azure PowerShell**. Seguire le istruzioni descritte in [Installare Azure PowerShell in Windows](/powershell/azure/install-azurerm-ps). Usare PowerShell per eseguire uno script per creare un runtime di integrazione self-hosted che può essere condiviso con altre data factory. 

> [!NOTE]
> Per un elenco di aree di Azure in cui Data Factory è attualmente disponibile, selezionare le aree di interesse nella pagina seguente: [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

## <a name="create-a-data-factory"></a>Creare una data factory

1. Avviare Windows PowerShell ISE.

1. Creare le variabili.

    Copiare e incollare lo script seguente e sostituire le variabili (SubscriptionName, ResourceGroupName e così via) con i valori effettivi. 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. Eseguire l'accesso e selezionare una sottoscrizione.

    Aggiungere il codice seguente allo script per eseguire l'accesso e selezionare la sottoscrizione di Azure:

    ```powershell
    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

1. Creare un gruppo di risorse e una data factory.

    *Questo passaggio è facoltativo. Se si ha già una data factory, ignorare questo passaggio.* 

    Creare un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) con il comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo. L'esempio seguente crea un gruppo di risorse denominato `myResourceGroup` nell'area WestEurope. 

    ```powershell
    New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Eseguire questo comando per creare una data factory: 

    ```powershell
    Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

## <a name="create-a-self-hosted-integration-runtime"></a>Creare un runtime di integrazione self-hosted

*Questo passaggio è facoltativo. Se si ha già un runtime di integrazione self-hosted che si vuole condividere con altre data factory, ignorare questo passaggio.*

Eseguire questo comando per creare un runtime di integrazione self-hosted:

```powershell
$SharedIR = Set-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Ottenere la chiave di autenticazione per il runtime di integrazione e registrare un nodo

Eseguire questo comando per ottenere la chiave di autenticazione per il runtime di integrazione self-hosted:

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

La risposta contiene la chiave di autenticazione per il runtime di integrazione self-hosted. Questa chiave viene usata al momento della registrazione del nodo del runtime di integrazione.

### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Installare e registrare il runtime di integrazione self-hosted

1. Scaricare il programma di installazione del runtime di integrazione self-hosted da [Azure Data Factory Integration Runtime](https://aka.ms/dmg).

2. Eseguire il programma scaricato per installare l'integrazione self-hosted in un computer locale.

3. Registrare la nuova integrazione self-hosted con la chiave di autenticazione ottenuta in uno dei passaggi precedenti.

## <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Condividere il runtime di integrazione self-hosted con un'altra data factory

### <a name="create-another-data-factory"></a>Creare un'altra data factory

*Questo passaggio è facoltativo. Se si ha già la data factory con cui si vuole condividere il runtime, ignorare questo passaggio.*

```powershell
$factory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
### <a name="grant-permission"></a>Concedere l'autorizzazione

Concedere l'autorizzazione alla data factory che deve accedere al runtime di integrazione self-hosted creato e registrato.

> [!IMPORTANT]
> Non ignorare questo passaggio.

```powershell
New-AzureRMRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' ` #This is the Contributor role
    -Scope $SharedIR.Id
```

## <a name="create-a-linked-self-hosted-integration-runtime"></a>Creare un runtime di integrazione self-hosted collegato

Eseguire questo comando per creare un runtime di integrazione self-hosted collegato:

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

È ora possibile usare questo runtime di integrazione collegato in qualsiasi servizio collegato. Per eseguire le attività, il runtime di integrazione collegato usa il runtime di integrazione condiviso.

## <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Revocare la condivisione del runtime di integrazione da una data factory

Per revocare l'accesso di una data factory al runtime di integrazione condiviso, è possibile eseguire il comando seguente:

```powershell
Remove-AzureRMRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' `
    -Scope $SharedIR.Id
```

Per rimuovere il runtime di integrazione collegato esistente, è possibile eseguire il comando seguente sul runtime di integrazione condiviso:

```powershell
Remove-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

## <a name="next-steps"></a>Passaggi successivi

- Rivedere i concetti relativi al runtime di integrazione in [Runtime di integrazione in Azure Data Factory](concepts-integration-runtime.md).

- Per informazioni su come creare un runtime di integrazione self-hosted nel portale di Azure, vedere [Creare e configurare un runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md).
