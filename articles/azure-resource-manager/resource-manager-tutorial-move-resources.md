---
title: Spostare le risorse di Azure in un altro gruppo di risorse | Microsoft Docs
description: Verrà illustrato come spostare le risorse di Azure da un gruppo di risorse a un altro o da una sottoscrizione a un'altra.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 12/19/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: cf1894a218af35459e0d0dc432c5813169856cca
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267701"
---
# <a name="tutorial-move-azure-resources-to-another-resource-group-or-subscription"></a>Esercitazione: Spostare le risorse di Azure in un altro gruppo di risorse o sottoscrizione

Verrà illustrato come spostare le risorse di Azure da un gruppo di risorse a un altro. È anche possibile spostare le risorse di Azure da una sottoscrizione di Azure a un'altra. In questa esercitazione si usa un modello di Resource Manager per distribuire due gruppi di risorse e un account di archiviazione. Si sposta quindi l'account di archiviazione da un gruppo di risorse all'altro.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Preparare le risorse.
> * Verificare che la risorsa possa essere spostata.
> * Verificare l'elenco di controllo prima di spostare la risorsa.
> * Convalidare l'operazione di spostamento.
> * Spostare la risorsa.
> * Pulire le risorse.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prepare-the-resources"></a>Preparare le risorse

Un modello è stato creato e inserito in un [account di archiviazione condiviso](https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json). Il modello definisce due gruppi di risorse e un account di archiviazione. Quando si distribuisce il modello, è necessario specificare un nome di progetto. Il nome del progetto viene usato per generare nomi di risorse univoci.  Il codice JSON seguente è estratto dal modello:

```json
"variables": {
  "resourceGroupSource": {
    "name": "[concat(parameters('projectName'), 'rg1')]",
    "location": "eastus"
  },
  "resourceGroupDestination": {
    "name": "[concat(parameters('projectName'), 'rg2')]",
    "location": "westus"      
  },
  "storageAccount": {
    "name": "[concat(parameters('projectName'), 'store')]",
    "location": "eastus"
  }
},
```

Osservare le località definite nel codice JSON: i due gruppi di risorse si trovano negli Stati Uniti orientali e negli Stati Uniti occidentali. L'account di archiviazione si trova negli Stati Uniti orientali. Quando si sposta una risorsa in un altro gruppo di risorse con una località diversa, l'operazione di spostamento non modifica la località della risorsa.

Selezionare **Prova** per aprire Cloud Shell e quindi eseguire lo script di PowerShell in Cloud Shell:

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
New-AzDeployment `
    -Name $projectname `
    -Location "centralus" `
    -TemplateUri "https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json" `
    -projectName $projectName
```

Attendere il completamento dello script, quindi aprire il [portale di Azure](https://portal.azure.com) e verificare che i gruppi di risorse e l'account di archiviazione siano stati distribuiti come previsto.

> [!NOTE]
> Poiché il modello definisce due gruppi di risorse, questa distribuzione è considerata una distribuzione a livello di sottoscrizione. La distribuzione dei modelli del portale non supporta le distribuzioni a livello di sottoscrizione. In questa esercitazione viene quindi usato Azure PowerShell. L'interfaccia della riga di comando di Azure supporta anche le distribuzioni a livello di sottoscrizione. Vedere [Creare gruppi di risorse e risorse per una sottoscrizione di Azure](./deploy-to-subscription.md).

## <a name="verify-the-resource-can-be-moved"></a>Verificare che la risorsa possa essere spostata

Non tutte le risorse possono essere spostate. La risorsa usata in questa esercitazione è un account di archiviazione, che può essere spostato. Per verificare se una risorsa può essere spostata, vedere [Servizi che possono essere spostati](./resource-group-move-resources.md#services-that-can-be-moved).

## <a name="checklist-before-moving-resources"></a>Controllo prima di spostare le risorse

Questo passaggio è facoltativo per questa esercitazione così com'è.

Prima di spostare una risorsa, è necessario eseguire alcuni passi importanti. Vedere [Elenco di controllo da seguire prima di spostare le risorse](./resource-group-move-resources.md#checklist-before-moving-resources).

## <a name="validate-the-move"></a>Convalidare lo spostamento

La convalida dello spostamento è facoltativa per questa esercitazione così com'è.

L'operazione di convalida dello spostamento consente di testare lo scenario di spostamento senza realmente spostare le risorse. Usare questa operazione per determinare se lo spostamento avrà esito positivo. Per altre informazioni, vedere [Convalidare lo spostamento](./resource-group-move-resources.md#validate-move).

## <a name="move-the-resource"></a>Spostare la risorsa

L'account di archiviazione è all'interno del gruppo di risorse di origine (rg1). Eseguire lo script di PowerShell seguente per spostare la risorsa nel gruppo di risorse di destinazione (rg2). Assicurarsi di usare lo stesso nome di progetto usato quando si distribuiscono le risorse.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
$resourceGroupSource = $projectName + "rg1"
$resourceGroupDestination = $projectName + "rg2"
$storageAccountName = $projectName + "store"

$storageAccount = Get-AzResource -ResourceGroupName $resourceGroupSource -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $resourceGroupDestination -ResourceId $storageAccount.ResourceId
```

Aprire il [portale di Azure](https://portal.azure.com), verificare che l'account di archiviazione sia stato spostato nell'altro gruppo di risorse e che la località dell'account di archiviazione sia ancora Stati Uniti orientali.

Durante lo spostamento di risorse, sia il gruppo di origine che il gruppo di destinazione sono bloccati durante l'operazione. Le operazioni di scrittura ed eliminazione sono bloccate nei gruppi di risorse fino al completamento dello spostamento. Questo blocco indica che non è possibile aggiungere, aggiornare o eliminare le risorse dei gruppi di risorse, ma non che le risorse sono bloccate. Se ad esempio si sposta un Server SQL con il relativo database in un nuovo gruppo di risorse, nelle applicazioni che usano il database non si verificano tempi di inattività, poiché rimane possibile leggere e scrivere nel database.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse di origine.  
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.
5. Selezionare il nome del gruppo di risorse di destinazione.  
6. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come spostare un account di archiviazione da un gruppo di risorse a un altro. Finora è stato usato un account di archiviazione o più istanze dell'account di archiviazione. Nell'esercitazione successiva verrà sviluppato un modello con più risorse e più tipi di risorse. alcune con risorse dipendenti.

> [!div class="nextstepaction"]
> [Creare le risorse dipendenti](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
