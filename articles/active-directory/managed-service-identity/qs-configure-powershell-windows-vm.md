---
title: Come configurare l'Identità del servizio gestito in una macchina virtuale di Azure tramite PowerShell
description: Istruzioni dettagliate per la configurazione di un'Identità del servizio gestito in una macchina virtuale di Azure, tramite PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 6981c0f917fb7175f444ceca8c55c0df186774db
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "33932320"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Configurare un'Identità del servizio gestito della macchina virtuale tramite PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

<a name="in-this-article-you-learn-how-to-perform-the-following-managed-service-identity-operations-on-an-azure-vm-using-powershell"></a>Questo articolo illustra come eseguire le seguenti operazioni di identità del servizio gestita in una macchina virtuale di Azure mediante PowerShell:
- 

## <a name="prerequisites"></a>prerequisiti

- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Installare [la versione più recente di Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM), se non è già installata.

## <a name="system-assigned-identity"></a>Identità assegnata dal sistema

Questa sezione descriverà come abilitare e disabilitare un'identità assegnata dal sistema mediante Azure PowerShell.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Abilitare l'identità assegnata dal sistema durante la creazione di una macchina virtuale di Azure

Per creare una macchina virtuale di Azure con l'identità assegnata dal sistema abilitata:

1. Fare riferimento a una delle seguenti guide introduttive sulle VM di Azure, completando solo le sezioni necessarie ("Accedere ad Azure", "Creare un gruppo di risorse", "Crea un gruppo di rete", "Creare la VM").
    
    Quando si raggiunge la sezione "Creare la VM", apportare una leggera modifica alla sintassi del cmdlet [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm). Assicurarsi di aggiungere un parametro `-AssignIdentity "SystemAssigned"` per effettuare il provisioning della macchina virtuale con l'identità di sistema assegnata abilitata, ad esempio:
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity "SystemAssigned" ...
    ```

   - [Creare una macchina virtuale Windows mediante PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Creare una macchina virtuale Linux mediante PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Facoltativo) Aggiungere l'estensione della macchina virtuale per l'Identità del servizio gestito usando il parametro `-Type` nel cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). È possibile passare "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", in base al tipo di VM, e assegnare un nome mediante il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Questo passaggio è facoltativo in quanto è possibile usare anche l'endpoint dell'identità del servizio metadati dell'istanza di Azure per recuperare i token.

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Abilitare l'identità assegnata dal sistema in una macchina virtuale di Azure esistente

Se occorre abilitare l'identità assegnata dal sistema in una macchina virtuale esistente:

1. Accedere al portale di Azure con `Login-AzureRmAccount`. Usare un account associato alla sottoscrizione di Azure che contiene la VM. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nella VM, ad esempio "Collaboratore macchine virtuali":

   ```powershell
   Login-AzureRmAccount
   ```

2. Recuperare innanzitutto le proprietà della macchina virtuale usando il cmdlet `Get-AzureRmVM`. Per abilitare un'identità assegnata dal sistema, usare l'opzione `-AssignIdentity` nel cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm):

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity "SystemAssigned"
   ```

3. (Facoltativo) Aggiungere l'estensione della macchina virtuale per l'Identità del servizio gestito usando il parametro `-Type` nel cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). È possibile passare "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", in base al tipo di VM, e assegnare un nome mediante il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token. Assicurarsi di specificare il parametro `-Location` corretto corrispondente alla posizione della macchina virtuale esistente:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Questo passaggio è facoltativo in quanto è possibile usare anche l'endpoint dell'identità del servizio metadati dell'istanza di Azure per recuperare i token.

## <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Disabilitare l'identità assegnata dal sistema in una macchina virtuale di Azure

> [!NOTE]
>  La disabilitazione dell'identità del servizio gestita in una macchina virtuale non è attualmente supportata. Nel frattempo è possibile alternare tra l'uso delle identità assegnate dal sistema e delle identità assegnate dall'utente.

Se si ha una macchina virtuale che non ha più bisogno dell'identità assegnata dal sistema ma ha ancora bisogno di identità assegnate dagli utenti, usare il cmdlet seguente:

1. Accedere al portale di Azure con `Login-AzureRmAccount`. Usare un account associato alla sottoscrizione di Azure che contiene la VM. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nella VM, ad esempio "Collaboratore macchine virtuali":

   ```powershell
   Login-AzureRmAccount
   ```

2. Eseguire il cmdlet seguente: 
    ```powershell       
    Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -IdentityType "UserAssigned"
    ```
Per rimuovere l'estensione VM dell'identità del servizio gestita, usare il parametro del nome con il cmdlet [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) specificando lo stesso nome utilizzato quando è stata aggiunta l'estensione:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-identity"></a>Identità assegnata dall'utente

Questa sezione illustra come aggiungere e rimuovere un'identità assegnata dall'utente da un VMSS tramite Azure PowerShell.

### <a name="assign-a-user-assigned-identity-to-a-vm-during-creation"></a>Assegnare a una macchina virtuale un'identità assegnata all'utente durante la creazione

Per assegnare a una macchina virtuale di Azure un'identità assegnata dall'utente durante la creazione di una macchina virtuale:

1. Fare riferimento a una delle seguenti guide introduttive sulle VM di Azure, completando solo le sezioni necessarie ("Accedere ad Azure", "Creare un gruppo di risorse", "Crea un gruppo di rete", "Creare la VM"). 
  
    Quando si raggiunge la sezione "Creare la VM", apportare una leggera modifica alla sintassi del cmdlet [`New-AzureRmVMConfig`](/powershell/module/azurerm.compute/new-azurermvm). Aggiungere i parametri `-IdentityType UserAssigned` e `-IdentityID ` per eseguire il provisioning della macchina virtuale con un'identità assegnata dall'utente.  Sostituire `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` e `<MSI NAME>` con valori personalizzati.  Ad esempio: 
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>..."
    ```
    
    - [Creare una macchina virtuale Windows mediante PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Creare una macchina virtuale Linux mediante PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Facoltativo) Aggiungere l'estensione della macchina virtuale per l'Identità del servizio gestito usando il parametro `-Type` nel cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). È possibile passare "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", in base al tipo di VM, e assegnare un nome mediante il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token. Assicurarsi di specificare il parametro `-Location` corretto corrispondente alla posizione della macchina virtuale esistente:
      > [!NOTE]
    > Questo passaggio è facoltativo in quanto è possibile usare anche l'endpoint dell'identità del servizio metadati dell'istanza di Azure per recuperare i token.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-identity-to-an-existing-azure-vm"></a>Assegnare un'identità assegnata dall'utente a una VM di Azure esistente

Per assegnare un'identità assegnata dall'utente a una VM di Azure esistente:

1. Accedere al portale di Azure con `Connect-AzureRmAccount`. Usare un account associato alla sottoscrizione di Azure che contiene la VM. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nella VM, ad esempio "Collaboratore macchine virtuali":

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

4. Aggiungere l'estensione della macchina virtuale per l'Identità del servizio gestito usando il parametro `-Type` nel cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). È possibile passare "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", in base al tipo di VM, e assegnare un nome mediante il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token. Specificare il parametro `-Location` corretto corrispondente alla posizione della macchina virtuale esistente.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Rimuovere un'identità gestita assegnata dall'utente da una macchina virtuale di Azure

> [!NOTE]
>  La rimozione di tutte le identità assegnate dall'utente da una macchina virtuale non è attualmente supportata, a meno che non si disponga di un'identità assegnata dal sistema. Ricontrollare in seguito per aggiornamenti.

Se la VM ha più identità assegnate dall'utente, è possibile rimuoverle tutte tranne l'ultima mediante i comandi seguenti. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VM NAME>` con valori personalizzati. `<MSI NAME>` è la proprietà del nome dell'identità assegnata dall'utente, che deve rimanere nella VM. È possibile trovare queste informazioni nella sezione relativa all'identità della VM usando `az vm show`:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm
```

Se la macchina virtuale ha identità sia assegnate dal sistema che assegnate dall'utente, è possibile rimuovere tutte le identità assegnate dall'utente iniziando a usare solo identità assegnate dal sistema. Usare il comando seguente:

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = $null
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Contenuti correlati

- [Panoramica dell'Identità del servizio gestito](overview.md)
- Per la guida introduttiva completa sulla creazione di VM di Azure, vedere:
  
  - [Creare una macchina virtuale Windows con PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Creare una macchina virtuale Linux con PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
