---
title: Usare Criteri di Azure per limitare l'installazione di estensioni per macchina virtuale | Microsoft Docs
description: Usare Criteri di Azure per limitare le distribuzioni di estensioni.
services: virtual-machines-linux
documentationcenter: ''
author: zroiy
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz;cynthn
ms.openlocfilehash: b63bfba4c1d84480724c4b03891f068145109626
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411727"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Usare Criteri di Azure per limitare l'installazione di estensioni in macchine virtuali Windows

Se si vuole impedire l'uso o l'installazione di determinate estensioni nelle macchine virtuali Windows, è possibile creare criteri di Azure tramite PowerShell per limitare le estensioni per le macchine virtuali all'interno di un gruppo di risorse. 

Questa esercitazione usa Azure PowerShell all'interno di Cloud Shell, che viene costantemente aggiornato alla versione più recente. Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 3.6 o successiva. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). 

## <a name="create-a-rules-file"></a>Creare un file delle regole

Per limitare le estensioni installabili, è necessario definire una [regola](/azure/azure-policy/policy-definition#policy-rule) per indicare la logica necessaria a identificare l'estensione.

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

È anche necessario un file di [parametri](/azure/azure-policy/policy-definition#parameters) per la creazione di una struttura da usare per passare un elenco delle estensioni da bloccare. 

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

Una definizione di criteri è un oggetto usato per archiviare la configurazione che si desidera usare. La definizione di criteri usa i file di regole e parametri per definire i criteri. Creare una definizione di criteri usando il cmdlet [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition).

 Le regole e i parametri dei criteri sono file creati e archiviati come file con estensione JSON in Cloud Shell.


```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Assegnare i criteri

Questo esempio assegna i criteri a un gruppo di risorse usando [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment). Qualsiasi macchina virtuale creata nel gruppo di risorse **myResourceGroup** non potrà installare le estensioni dell'agente di accesso alle macchine virtuali o di script personalizzato. 

Usare il cmdlet [Get-AzureRMSubscription | Format-Table](/powershell/module/azurerm.profile/get-azurermsubscription) per ottenere l'ID della sottoscrizione da sostituire a quello nell'esempio.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzureRMPolicyAssignment `
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

Per testare i criteri, provare a usare l'estensione di accesso alla macchina virtuale. Il comando seguente avrà esito negativo e presenterà il messaggio "Set-AzureRmVMAccessExtension : Resource 'myVMAccess' was disallowed by policy." (Set-AzureRmVMAccessExtension : la risorsa "myVMAccess" non è stata autorizzata dai criteri.)

```azurepowershell-interactive
Set-AzureRmVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

Nel portale la modifica della password avrà esito negativo e presenterà il messaggio "The template deployment failed because of policy violation." (La distribuzione del modello non è riuscita a causa di una violazione dei criteri.).

## <a name="remove-the-assignment"></a>Rimuovere l'assegnazione

```azurepowershell-interactive
Remove-AzureRMPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Rimuovere i criteri

```azurepowershell-interactive
Remove-AzureRmPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere [Criteri di Azure](../../azure-policy/azure-policy-introduction.md).
