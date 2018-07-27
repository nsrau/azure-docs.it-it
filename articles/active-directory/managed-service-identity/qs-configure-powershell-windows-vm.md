---
title: Come configurare un'identità del servizio gestita in una macchina virtuale di Azure tramite PowerShell
description: Istruzioni dettagliate per la configurazione di un'identità del servizio gestita in una macchina virtuale di Azure tramite PowerShell.
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
ms.openlocfilehash: 68c9ae7baa6b8fa1ebf672c28bf3c466b4b54860
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258393"
---
# <a name="configure-a-vm-managed-service-identity-using-powershell"></a>Configurare un'identità del servizio gestita della macchina virtuale tramite PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

<a name="in-this-article-you-learn-how-to-perform-the-following-managed-service-identity-operations-on-an-azure-vm-using-powershell"></a>Questo articolo illustra come eseguire le seguenti operazioni di identità del servizio gestita in una macchina virtuale di Azure mediante PowerShell:
- 

## <a name="prerequisites"></a>prerequisiti

- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire le operazioni di gestione in questo articolo, l'account deve avere le assegnazioni di ruolo seguenti:
    - [Collaboratore macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) per creare una macchina virtuale e abilitare e rimuovere da una macchina virtuale di Azure il sistema e/o l'identità gestita assegnata dall'utente.
    - [Collaboratore identità gestita](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) per creare un'identità assegnata dall'utente.
    - [Operatore identità gestita](/azure/role-based-access-control/built-in-roles#managed-identity-operator) per assegnare e rimuovere un'identità assegnata dall'utente da e verso una macchina virtuale.
- Installare [la versione più recente di Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM), se non è già installata.

## <a name="system-assigned-identity"></a>Identità assegnata dal sistema

Questa sezione descriverà come abilitare e disabilitare un'identità assegnata dal sistema tramite Azure PowerShell.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Abilitare l'identità assegnata dal sistema durante la creazione di una macchina virtuale di Azure

Per creare una macchina virtuale di Azure con l'identità assegnata dal sistema abilitata:

1. Fare riferimento a una delle seguenti guide introduttive sulle VM di Azure, completando solo le sezioni necessarie ("Accedere ad Azure", "Creare un gruppo di risorse", "Crea un gruppo di rete", "Creare la VM").
    
    Quando si raggiunge la sezione "Creare la VM", apportare una leggera modifica alla sintassi del cmdlet [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm). Assicurarsi di aggiungere un parametro `-AssignIdentity:$SystemAssigned` per effettuare il provisioning della macchina virtuale con l'identità di sistema assegnata abilitata, ad esempio:
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Creare una macchina virtuale Windows mediante PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Creare una macchina virtuale Linux mediante PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Facoltativo) Aggiungere l'estensione della macchina virtuale per l'identità del servizio gestita usando il parametro `-Type` nel cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). È possibile passare "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", in base al tipo di VM, e assegnare un nome mediante il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Questo passaggio è facoltativo in quanto è possibile usare anche l'endpoint dell'identità del servizio metadati dell'istanza di Azure per recuperare i token.

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Abilitare l'identità assegnata dal sistema in una macchina virtuale di Azure esistente

Se occorre abilitare l'identità assegnata dal sistema in una macchina virtuale esistente:

1. Accedere al portale di Azure con `Login-AzureRmAccount`. Usare un account associato alla sottoscrizione di Azure che contiene la VM.

   ```powershell
   Login-AzureRmAccount
   ```

2. Recuperare innanzitutto le proprietà della macchina virtuale usando il cmdlet `Get-AzureRmVM`. Per abilitare un'identità assegnata dal sistema, usare l'opzione `-AssignIdentity` nel cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm):

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```

3. (Facoltativo) Aggiungere l'estensione della macchina virtuale per l'identità del servizio gestita usando il parametro `-Type` nel cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). È possibile passare "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", in base al tipo di VM, e assegnare un nome mediante il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token. Assicurarsi di specificare il parametro `-Location` corretto corrispondente alla posizione della macchina virtuale esistente:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Questo passaggio è facoltativo in quanto è possibile usare anche l'endpoint dell'identità del servizio metadati dell'istanza di Azure per recuperare i token.

## <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Disabilitare l'identità assegnata dal sistema in una macchina virtuale di Azure

Se si ha una macchina virtuale che non ha più bisogno dell'identità assegnata dal sistema ma ha ancora bisogno di identità assegnate dagli utenti, usare il cmdlet seguente:

1. Accedere al portale di Azure con `Login-AzureRmAccount`. Usare un account associato alla sottoscrizione di Azure che contiene la VM.

   ```powershell
   Login-AzureRmAccount
   ```

2. Recuperare le proprietà della macchina virtuale usando il cmdlet `Get-AzureRmVM` e impostare il parametro `-IdentityType` su `UserAssigned`:

   ```powershell   
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM    
   Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Se una macchina virtuale non ha più bisogno dell'identità assegnata dal sistema ma non ha identità assegnate dall'utente, usare il comando seguente:

```powershell
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

Per rimuovere l'estensione della macchina virtuale dell'identità del servizio gestita, usare l'opzione relativa al nome con il cmdlet [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) specificando lo stesso nome usato quando è stata aggiunta l'estensione:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-identity"></a>Identità assegnata dall'utente

Questa sezione illustra come aggiungere e rimuovere un'identità assegnata dall'utente da una macchina virtuale tramite Azure PowerShell.

### <a name="assign-a-user-assigned-identity-to-a-vm-during-creation"></a>Assegnare a una macchina virtuale un'identità assegnata all'utente durante la creazione

Per assegnare a una macchina virtuale di Azure un'identità assegnata dall'utente durante la creazione di una macchina virtuale:

1. Fare riferimento a una delle seguenti guide introduttive sulle VM di Azure, completando solo le sezioni necessarie ("Accedere ad Azure", "Creare un gruppo di risorse", "Crea un gruppo di rete", "Creare la VM"). 
  
    Quando si raggiunge la sezione "Creare la VM", apportare una leggera modifica alla sintassi del cmdlet [`New-AzureRmVMConfig`](/powershell/module/azurerm.compute/new-azurermvm). Aggiungere i parametri `-IdentityType UserAssigned` e `-IdentityID ` per eseguire il provisioning della macchina virtuale con un'identità assegnata dall'utente.  Sostituire `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` e `<MSI NAME>` con valori personalizzati.  Ad esempio: 
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>..."
    ```
    
    - [Creare una macchina virtuale Windows mediante PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Creare una macchina virtuale Linux mediante PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Facoltativo) Aggiungere l'estensione della macchina virtuale per l'identità del servizio gestita usando il parametro `-Type` nel cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). È possibile passare "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", in base al tipo di VM, e assegnare un nome mediante il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token. Assicurarsi di specificare il parametro `-Location` corretto corrispondente alla posizione della macchina virtuale esistente:
      > [!NOTE]
    > Questo passaggio è facoltativo in quanto è possibile usare anche l'endpoint dell'identità del servizio metadati dell'istanza di Azure per recuperare i token.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-identity-to-an-existing-azure-vm"></a>Assegnare un'identità assegnata dall'utente a una VM di Azure esistente

Per assegnare un'identità assegnata dall'utente a una VM di Azure esistente:

1. Accedere al portale di Azure con `Connect-AzureRmAccount`. Usare un account associato alla sottoscrizione di Azure che contiene la VM.

   ```powershell
   Connect-AzureRmAccount
   ```

2. Creare un'identità assegnata dall'utente tramite il cmdlet [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity).  Si noti `Id` nell'output perché sarà necessaria nel passaggio successivo.

   > [!IMPORTANT]
   > La creazione delle identità assegnate dall'utente supporta solo caratteri alfanumerici e trattino (numeri 0-9, lettere a-z e A-Z, -). Inoltre, il nome può avere al massimo 24 caratteri perché l'assegnazione alla macchina virtuale/set di scalabilità di macchine virtuali funzioni correttamente. Ricontrollare in seguito per aggiornamenti. Per altre informazioni, vedere [Domande frequenti e problemi noti](known-issues.md)

   ```powershell
   New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Recuperare le proprietà della macchina virtuale usando il cmdlet `Get-AzureRmVM`. Quindi per assegnare un'identità assegnata dall'utente alla VM di Azure, usare le opzioni `-IdentityType` e `-IdentityID` nel cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm).  Il valore per il parametro`-IdentityId` è il valore `Id` annotato nel passaggio precedente.  Sostituire `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. Aggiungere l'estensione della macchina virtuale per l'identità del servizio gestita usando il parametro `-Type` nel cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). È possibile passare "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", in base al tipo di VM, e assegnare un nome mediante il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token. Specificare il parametro `-Location` corretto corrispondente alla posizione della macchina virtuale esistente.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Rimuovere un'identità gestita assegnata dall'utente da una macchina virtuale di Azure

Se la VM ha più identità assegnate dall'utente, è possibile rimuoverle tutte tranne l'ultima mediante i comandi seguenti. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VM NAME>` con valori personalizzati. `<MSI NAME>` è la proprietà del nome dell'identità assegnata dall'utente, che deve rimanere nella VM. È possibile trovare queste informazioni nella sezione relativa all'identità della macchina virtuale usando `az vm show`:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID "<MSI NAME>"
```
Se la macchina virtuale non ha un'identità assegnata dal sistema e si desiderano rimuovere tutte le identità assegnate dall'utente al suo interno, utilizzare il comando seguente:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Se la macchina virtuale ha identità sia assegnate dal sistema che assegnate dall'utente, è possibile rimuovere tutte le identità assegnate dall'utente iniziando a usare solo identità assegnate dal sistema.

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Contenuti correlati

- [Panoramica dell'Identità del servizio gestito](overview.md)
- Per la guida introduttiva completa sulla creazione di VM di Azure, vedere:
  
  - [Creare una macchina virtuale Windows con PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Creare una macchina virtuale Linux con PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
