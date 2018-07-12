---
title: Come configurare l'identità del servizio gestita in un VMSS di Azure tramite PowerShell
description: Istruzioni dettagliate per configurare identità assegnate dal sistema e dall'utente in un VMSS di Azure mediante PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 61fa6c94c0d717fe1e71bf8929f2e3b4a0982562
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903880"
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-powershell"></a>Configurare un'identità del servizio gestita di un set di scalabilità di macchine virtuali (VMSS) tramite PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo descrive come eseguire le operazioni di identità del servizio gestita in un set di scalabilità di macchine virtuali (VMSS) mediante PowerShell:
- Abilitare e disabilitare l'identità assegnata dal sistema in un VMSS di Azure
- Aggiungere e rimuovere un'identità assegnata dall'utente in un VMSS di Azure

## <a name="prerequisites"></a>prerequisiti

- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Installare [la versione più recente di Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM), se non è già installata. 

## <a name="system-assigned-managed-identity"></a>Identità gestita assegnata dal sistema

Questa sezione descrive come abilitare e rimuovere un'identità assegnata dal sistema mediante Azure PowerShell.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Abilitare l'identità assegnata dal sistema durante la creazione di un VMSS di Azure

Per creare un VMSS di Azure con l'identità assegnata dal sistema abilitata:

1. Vedere l'*esempio 1* nell'articolo di riferimento sul cmdlet [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) per creare un VMSS con un'identità assegnata dal sistema.  Aggiungere il parametro `-IdentityType SystemAssigned` al cmdlet `New-AzureRmVmssConfig`:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Facoltativo) Aggiungere l'estensione del VMSS per l'identità del servizio gestita usando i parametri `-Name` e `-Type` nel cmdlet [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension). È possibile passare "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", in base al tipo di VM, e assegnare un nome mediante il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token:

    > [!NOTE]
    > Questo passaggio è facoltativo in quanto è possibile usare anche l'endpoint dell'identità del servizio metadati dell'istanza di Azure per recuperare i token.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-vmss"></a>Abilitare l'identità assegnata dal sistema in un VMSS di Azure esistente

Se occorre abilitare l'identità assegnata dal sistema in un VMSS di Azure esistente, eseguire questa procedura:

1. Accedere al portale di Azure con `Login-AzureRmAccount`. Usare un account associato alla sottoscrizione di Azure che contiene la VM. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nella VM, ad esempio "Collaboratore macchine virtuali":

   ```powershell
   Login-AzureRmAccount
   ```

2. Recuperare innanzitutto le proprietà del VMSS usando il cmdlet [`Get-AzureRmVmss`](/powershell/module/azurerm.compute/get-azurermvmss). Per abilitare un'identità assegnata dal sistema, usare l'opzione `-IdentityType` nel cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm):

   ```powershell
   $vm = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVM -IdentityType "SystemAssigned"
   ```

3. Aggiungere l'estensione del VMSS per l'identità del servizio gestita usando i parametri `-Name` e `-Type` nel cmdlet [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension). È possibile passare "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", in base al tipo di VM, e assegnare un nome mediante il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vmss"></a>Disabilitare l'identità assegnata dal sistema in un VMSS di Azure

> [!NOTE]
> La disabilitazione dell'identità del servizio gestita da un set di scalabilità di macchine virtuali non è attualmente supportata. Nel frattempo è possibile alternare tra l'uso delle identità assegnate dal sistema e delle identità assegnate dall'utente.

Se si ha un set di scalabilità di macchine virtuali che non ha più bisogno dell'identità assegnata dal sistema ma ha ancora bisogno di identità assegnate dall'utente, usare il cmdlet seguente:

1. Accedere al portale di Azure con `Login-AzureRmAccount`. Usare un account associato alla sottoscrizione di Azure che contiene la VM. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nella VM, ad esempio "Collaboratore macchine virtuali":

2. Eseguire il cmdlet seguente:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
    ```

## <a name="user-assigned-identity"></a>Identità assegnata dall'utente

Questa sezione illustra come aggiungere e rimuovere un'identità assegnata dall'utente da un VMSS tramite Azure PowerShell.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-vmss"></a>Assegnare un'identità assegnata dall'utente durante la creazione di un VMSS di Azure

Attualmente non è possibile creare un nuovo VMSS con un'identità assegnata dall'utente tramite PowerShell. Vedere la sezione successiva per informazioni sull'aggiunta di un'identità assegnata dall'utente a un VMSS esistente. Ricontrollare in seguito per aggiornamenti.

### <a name="assign-a-user-identity-to-an-existing-azure-vmss"></a>Assegnare un'identità assegnata dall'utente a un VMSS di Azure esistente

Per assegnare un'identità assegnata dall'utente a un VMSS di Azure esistente:

1. Accedere al portale di Azure con `Connect-AzureRmAccount`. Usare un account associato alla sottoscrizione di Azure che contiene la VM. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nella VM, ad esempio "Collaboratore macchine virtuali":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Recuperare innanzitutto le proprietà della macchina virtuale usando il cmdlet `Get-AzureRmVM`. Quindi per assegnare un'identità assegnata dall'utente al VMSS di Azure, usare le opzioni `-IdentityType` e `-IdentityID` nel cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm). Sostituire `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` con valori personalizzati.

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


   ```powershell
   $vmss = Get-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME>
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -VM $vmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Rimuovere un'identità assegnata dall'utente da un VMSS di Azure

> [!NOTE]
> La rimozione di tutte le identità assegnate dall'utente da un set di scalabilità di macchine virtuali non è attualmente supportata, a meno che non si disponga di un'identità assegnata dal sistema. Ricontrollare in seguito per aggiornamenti.

Se il VMSS ha più identità assegnate dall'utente, è possibile rimuoverle tutte tranne l'ultima mediante i comandi seguenti. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VMSS NAME>` con valori personalizzati. `<MSI NAME>` è la proprietà del nome dell'identità assegnata dall'utente, che deve rimanere nel VMSS. È possibile trovare queste informazioni nella sezione relativa all'identità del VMSS usando `az vmss show`:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachineScaleSet $vmss
```

Se il VMSS ha identità sia assegnate dal sistema che assegnate dall'utente, è possibile rimuovere tutte le identità assegnate dall'utente iniziando a usare solo identità assegnate dal sistema. Usare il comando seguente:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = $null
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachine $vmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Contenuti correlati

- [Panoramica dell'Identità del servizio gestito](overview.md)
- Per la guida introduttiva completa sulla creazione di VM di Azure, vedere:
  
  - [Creare una macchina virtuale Windows con PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Creare una macchina virtuale Linux con PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















