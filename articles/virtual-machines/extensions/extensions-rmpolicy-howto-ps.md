---
title: Usare Criteri di Azure per limitare l'installazione di estensioni per macchina virtuale | Microsoft Docs
description: Usare Criteri di Azure per limitare le distribuzioni di estensioni.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz;cynthn
ms.openlocfilehash: e0fcc53df27e46c56066fb0eedc87a3970a7c409
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706023"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Usare Criteri di Azure per limitare l'installazione di estensioni in macchine virtuali Windows

Se si vuole impedire l'uso o l'installazione di determinate estensioni nelle macchine virtuali Windows, è possibile creare criteri di Azure tramite PowerShell per limitare le estensioni per le macchine virtuali all'interno di un gruppo di risorse. 

Questa esercitazione usa Azure PowerShell all'interno di Cloud Shell, che viene costantemente aggiornato alla versione più recente. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-rules-file"></a>Creare un file delle regole

Per limitare le estensioni installabili, è necessario definire una [regola](../../governance/policy/concepts/definition-structure.md#policy-rule) per indicare la logica necessaria a identificare l'estensione.

Questo esempio mostra come negare le estensioni pubblicate da "Microsoft.Compute" creando un file di regole in Azure Cloud Shell, ma se si usa PowerShell in locale è anche possibile creare un file locale e sostituire il percorso ($home/clouddrive) con quello del file locale nel computer in uso.

In [Cloud Shell](https://shell.azure.com/powershell) digitare:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Copiare e incollare il codice JSON seguente nel file.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Al termine premere **CTRL + O** e quindi **INVIO** per salvare il file. Premere **CTRL + X** per chiudere il file e uscire.

## <a name="create-a-parameters-file"></a>Creare un file di parametri

È anche necessario un file di [parametri](../../governance/policy/concepts/definition-structure.md#parameters) per la creazione di una struttura da usare per passare un elenco delle estensioni da bloccare. 

Questo esempio mostra come creare un file di parametri per macchine virtuali in Cloud Shell, ma se si usa PowerShell in locale è anche possibile creare un file locale e sostituire il percorso ($home/clouddrive) con il percorso del file locale nel computer in uso.

In [Cloud Shell](https://shell.azure.com/powershell) digitare:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Copiare e incollare il codice JSON seguente nel file.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

Al termine premere **CTRL + O** e quindi **INVIO** per salvare il file. Premere **CTRL + X** per chiudere il file e uscire.

## <a name="create-the-policy"></a>Creare i criteri

Una definizione di criteri è un oggetto usato per archiviare la configurazione che si desidera usare. La definizione di criteri usa i file di regole e parametri per definire i criteri. Creare una definizione di criteri usando il cmdlet [New-AzPolicyDefinition](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicydefinition).

 Le regole e i parametri dei criteri sono file creati e archiviati come file con estensione JSON in Cloud Shell.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Assegnare i criteri

Questo esempio assegna i criteri a un gruppo di risorse usando [New-AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment). Qualsiasi macchina virtuale creata nel gruppo di risorse **myResourceGroup** non potrà installare le estensioni dell'agente di accesso alle macchine virtuali o di script personalizzato. 

Usare il cmdlet [Get-AzSubscription | Format-Table](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) per ottenere l'ID della sottoscrizione da sostituire a quello nell'esempio.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>Testare i criteri

Per testare i criteri, provare a usare l'estensione di accesso alla macchina virtuale. Il comando seguente avrà esito negativo e presenterà il messaggio "Set-AzVMAccessExtension: Resource 'myVMAccess' was disallowed by policy." (Set-AzureRmVMAccessExtension: la risorsa "myVMAccess" non è stata autorizzata dai criteri.)

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

Nel portale la modifica della password avrà esito negativo e presenterà il messaggio "The template deployment failed because of policy violation." (La distribuzione del modello non è riuscita a causa di una violazione dei criteri.).

## <a name="remove-the-assignment"></a>Rimuovere l'assegnazione

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Rimuovere i criteri

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere [Criteri di Azure](../../governance/policy/overview.md).
