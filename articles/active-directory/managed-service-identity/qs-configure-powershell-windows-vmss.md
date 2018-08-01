---
title: Come configurare un'identità del servizio gestita in un set di scalabilità di macchine virtuali di Azure tramite PowerShell
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
ms.openlocfilehash: 5d4539c05d05053ac2ea6cd1c5fadbd161b41173
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257743"
---
# <a name="configure-a-vmss-managed-service-identity-using-powershell"></a>Configurare un'identità del servizio gestita in un set di scalabilità di macchine virtuali tramite PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

L'identità del servizio gestita offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo descrive come eseguire le operazioni dell'identità del servizio gestita in un set di scalabilità di macchine virtuali di Azure tramite PowerShell:
- Abilitare e disabilitare l'identità assegnata dal sistema in un set di scalabilità di macchine virtuali
- Aggiungere e rimuovere un'identità assegnata dall'utente in un set di scalabilità di macchine virtuali

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire le operazioni di gestione illustrate in questo articolo, l'account deve avere le assegnazioni di ruolo seguenti:
    - [Collaboratore macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) per creare un set di scalabilità di macchine virtuali e abilitare e rimuovere da un set di scalabilità di macchine virtuali l'identità utente e/o l'identità gestita assegnata dal sistema.
    - [Collaboratore identità gestita](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) per creare un'identità assegnata dall'utente.
    - [Operatore identità gestita](/azure/role-based-access-control/built-in-roles#managed-identity-operator) per assegnare e rimuovere un'identità assegnata dall'utente da e verso un set di scalabilità di macchine virtuali.
- Installare [la versione più recente di Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM), se non è già installata. 

## <a name="system-assigned-managed-identity"></a>Identità gestita assegnata dal sistema

Questa sezione descrive come abilitare e rimuovere un'identità assegnata dal sistema mediante Azure PowerShell.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Abilitare l'identità assegnata dal sistema durante la creazione di un set di scalabilità di macchine virtuali di Azure

Per creare un VMSS di Azure con l'identità assegnata dal sistema abilitata:

1. Vedere l'*esempio 1* nell'articolo di riferimento sul cmdlet [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) per creare un VMSS con un'identità assegnata dal sistema.  Aggiungere il parametro `-IdentityType SystemAssigned` al cmdlet `New-AzureRmVmssConfig`:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Facoltativo) Aggiungere l'estensione del set di scalabilità di macchine virtuali per l'identità del servizio gestita usando i parametri `-Name` e `-Type` nel cmdlet [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension). È possibile passare "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", in base al tipo di set di scalabilità di macchine virtuali, e assegnare un nome tramite il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token:

    > [!NOTE]
    > Questo passaggio è facoltativo in quanto è possibile usare anche l'endpoint dell'identità del servizio metadati dell'istanza di Azure per recuperare i token.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Abilitare l'identità assegnata dal sistema in un set di scalabilità di macchine virtuali di Azure esistente

Se è necessario abilitare un'identità assegnata dal sistema in un set di scalabilità di macchine virtuali di Azure esistente:

1. Accedere al portale di Azure con `Login-AzureRmAccount`. Usare un account associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nel set di scalabilità di macchine virtuali, ad esempio "Collaboratore macchina virtuale":

   ```powershell
   Login-AzureRmAccount
   ```

2. Recuperare innanzitutto le proprietà del set di scalabilità di macchine virtuali tramite il cmdlet [`Get-AzureRmVmss`](/powershell/module/azurerm.compute/get-azurermvmss). Per abilitare un'identità assegnata dal sistema, usare l'opzione `-IdentityType` nel cmdlet [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss):

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

3. Aggiungere l'estensione del set di scalabilità di macchine virtuali per l'identità del servizio gestita usando i parametri `-Name` e `-Type` nel cmdlet [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension). È possibile passare "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", in base al tipo di set di scalabilità di macchine virtuali, e assegnare un nome tramite il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Disabilitare l'identità assegnata dal sistema da un set di scalabilità di macchine virtuali di Azure

Se si dispone di un set di scalabilità di macchine virtuali che non ha più bisogno dell'identità assegnata dal sistema, ma ha ancora bisogno di identità assegnate dall'utente, usare il cmdlet seguente:

1. Accedere al portale di Azure con `Login-AzureRmAccount`. Usare un account associato alla sottoscrizione di Azure che contiene la VM. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nel set di scalabilità di macchine virtuali, ad esempio "Collaboratore macchina virtuale":

2. Eseguire il cmdlet seguente:

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Se si dispone di un set di scalabilità di macchine virtuali che non ha più bisogno dell'identità assegnata dal sistema e non ha identità assegnate dall'utente, usare i comandi seguenti:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-identity"></a>Identità assegnata dall'utente

Questa sezione illustra come aggiungere e rimuovere un'identità assegnata dall'utente da un set di scalabilità di macchine virtuali tramite Azure PowerShell.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Assegnare un'identità assegnata dall'utente durante la creazione di un set di scalabilità di macchine virtuali di Azure

Attualmente non è possibile creare un nuovo set di scalabilità di macchine virtuali con un'identità assegnata dall'utente tramite PowerShell. Per informazioni sull'aggiunta di un'identità assegnata dall'utente a un set di scalabilità di macchine virtuali esistente, vedere la sezione successiva. Ricontrollare in seguito per aggiornamenti.

### <a name="assign-a-user-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Assegnare un'identità utente a un set di scalabilità di macchine virtuali di Azure esistente

Per assegnare un'identità assegnata dall'utente a un set di scalabilità di macchine virtuali di Azure esistente:

1. Accedere al portale di Azure con `Connect-AzureRmAccount`. Usare un account associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nel set di scalabilità di macchine virtuali, ad esempio "Collaboratore macchina virtuale":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Recuperare innanzitutto le proprietà del set di scalabilità di macchine virtuali tramite il cmdlet `Get-AzureRmVM`. Quindi, per assegnare un'identità assegnata dall'utente al set di scalabilità di macchine virtuali di Azure, usare le opzioni `-IdentityType` e `-IdentityID` nel cmdlet [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). Sostituire `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` con valori personalizzati.

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Rimuovere un'identità gestita assegnata dall'utente da un set di scalabilità di macchine virtuali di Azure

Se il set di scalabilità di macchine virtuali ha più identità assegnate dall'utente, è possibile rimuoverle tutte tranne l'ultima tramite i comandi seguenti. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VMSS NAME>` con valori personalizzati. `<MSI NAME>` è la proprietà del nome dell'identità assegnata dall'utente, che deve rimanere nel set di scalabilità di macchine virtuali. È possibile trovare queste informazioni nella sezione relativa all'identità del set di scalabilità di macchine virtuali usando `az vmss show`:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<MSI NAME>"
```
Se il set di scalabilità di macchine virtuali non ha un'identità assegnata dal sistema e si vogliono rimuovere tutte le identità assegnate dall'utente al suo interno, usare il comando seguente:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Se il set di scalabilità di macchine virtuali ha identità sia assegnate dal sistema che assegnate dall'utente, è possibile rimuovere tutte le identità assegnate dall'utente iniziando a usare solo identità assegnate dal sistema.

```powershell 
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Contenuti correlati

- [Panoramica dell'Identità del servizio gestita](overview.md)
- Per la guida introduttiva completa sulla creazione di VM di Azure, vedere:
  
  - [Creare una macchina virtuale Windows con PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Creare una macchina virtuale Linux con PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















