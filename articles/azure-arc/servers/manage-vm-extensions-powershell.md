---
title: Abilitare l'estensione della macchina virtuale usando Azure PowerShell
description: Questo articolo descrive come distribuire le estensioni delle macchine virtuali nei server abilitati per Azure Arc in esecuzione in ambienti cloud ibridi usando Azure PowerShell.
ms.date: 10/23/2020
ms.topic: conceptual
ms.openlocfilehash: d2408f75c7b6d81ba297de6dcdb85a712cd8908f
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495445"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Abilitare le estensioni VM di Azure usando Azure PowerShell

Questo articolo illustra come distribuire e disinstallare le estensioni delle macchine virtuali di Azure, supportate da server abilitati per Azure Arc, in un computer ibrido Linux o Windows usando Azure PowerShell.

## <a name="prerequisites"></a>Prerequisiti

- Un computer con Azure PowerShell. Per istruzioni, vedere [Installare e configurare Azure PowerShell](/powershell/azure/).

Prima di usare Azure PowerShell per gestire le estensioni di macchina virtuale nel server ibrido gestito da server abilitati per Arc, è necessario installare il `Az.ConnectedMachine` modulo. Eseguire il comando seguente nel server abilitato per Arc:

`Install-Module -Name Az.ConnectedMachine`.

Al termine dell'installazione, viene restituito il messaggio seguente:

`The installed extension `AZ. ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Abilita estensione

Per abilitare un'estensione della macchina virtuale nel server abilitato per Arc, usare [New-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) con i `-Name` `-ResourceGroupName` parametri,, `-MachineName` , `-Location` , `-Publisher` ,- `ExtensionType` e `-Settings` .

L'esempio seguente abilita l'estensione della macchina virtuale Log Analytics in un server Linux abilitato per Arc:

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType OmsAgentforLinux"
```

Nell'esempio seguente viene abilitata l'estensione di script personalizzato in un server abilitato per Arc:

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

## <a name="list-extensions-installed"></a>Elenca estensioni installate

Per ottenere un elenco delle estensioni della macchina virtuale nel server abilitato per Arc, usare [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) con `-MachineName` i `-ResourceGroupName` parametri e.

Esempio:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>Rimuovere un'estensione installata

Per rimuovere un'estensione VM installata nel server abilitato per Arc, usare [Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) con i `-Name` `-MachineName` parametri, e `-ResourceGroupName` .

Ad esempio, per rimuovere l'estensione Log Analytics VM per Linux, eseguire il comando seguente:

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>Passaggi successivi

- È possibile distribuire, gestire e rimuovere estensioni di VM usando l' [interfaccia](manage-vm-extensions-cli.md)della riga di comando di Azure, dal [portale di Azure](manage-vm-extensions-portal.md)o da [modelli di Azure Resource Manager](manage-vm-extensions-template.md).

- Le informazioni sulla risoluzione dei problemi sono reperibili nella [Guida Risoluzione dei problemi delle estensioni VM](troubleshoot-vm-extensions.md).
