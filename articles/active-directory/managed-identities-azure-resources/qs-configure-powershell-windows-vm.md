---
title: Come configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure tramite PowerShell
description: Istruzioni dettagliate per la configurazione di identità gestite per le risorse di Azure in una macchina virtuale di Azure tramite PowerShell.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57d1ff4b44ff352742ee91b61c0c774cfe7c3f9d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56181355"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure tramite PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come eseguire queste identità gestite per le operazioni delle risorse di Azure su una macchina virtuale di Azure usando PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Installare [la versione più recente di Azure PowerShell](/powershell/azure/install-az-ps), se non è già installata.

## <a name="system-assigned-managed-identity"></a>Identità gestita assegnata dal sistema

Questa sezione descriverà come abilitare e disabilitare un'identità gestita assegnata dal sistema tramite Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Abilitare l'identità gestita assegnata dal sistema durante la creazione di una macchina virtuale di Azure

Per creare una macchina virtuale di Azure con l'identità gestita assegnata dal sistema abilitata, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Fare riferimento a una delle seguenti guide introduttive sulle VM di Azure, completando solo le sezioni necessarie ("Accedere ad Azure", "Creare un gruppo di risorse", "Crea un gruppo di rete", "Creare la VM").
    
    Quando si raggiunge la sezione "Creare la VM", apportare una leggera modifica alla sintassi del cmdlet [New-AzVMConfig](/powershell/module/az.compute/new-azvm). Assicurarsi di aggiungere un parametro `-AssignIdentity:$SystemAssigned` per effettuare il provisioning della macchina virtuale con l'identità assegnata dal sistema abilitata, ad esempio:
      
    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Creare una macchina virtuale Windows mediante PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Creare una macchina virtuale Linux mediante PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Facoltativo) Aggiungere le identità gestite per l'estensione della macchina virtuale delle risorse di Azure (pianificazione deprecata nel gennaio 2019) usando il parametro `-Type` nel cmdlet [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension). È possibile passare "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", in base al tipo di VM, e assegnare un nome mediante il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Questo passaggio è facoltativo in quanto è possibile usare anche l'endpoint dell'identità del servizio metadati dell'istanza di Azure per recuperare i token. La deprecazione dell'estensione della macchina virtuale relativa alle identità gestite per le risorse di Azure è pianificata per il gennaio 2019. 

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Abilitare l'identità gestita assegnata dal sistema in una macchina virtuale di Azure esistente

Per abilitare l'identità gestita assegnata dal sistema in una macchina virtuale di cui originariamente è stato effettuato il provisioning senza tale identità, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Accedere al portale di Azure con `Connect-AzAccount`. Usare un account associato alla sottoscrizione di Azure che contiene la VM.

   ```powershell
   Connect-AzAccount
   ```

2. Recuperare innanzitutto le proprietà della macchina virtuale usando il cmdlet `Get-AzVM`. Per abilitare un'identità gestita assegnata dal sistema, usare l'opzione `-AssignIdentity` nel cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm):

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```

3. (Facoltativo) Aggiungere le identità gestite per l'estensione della macchina virtuale delle risorse di Azure (pianificazione deprecata nel gennaio 2019) usando il parametro `-Type` nel cmdlet [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension). È possibile passare "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", in base al tipo di VM, e assegnare un nome mediante il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token. Assicurarsi di specificare il parametro `-Location` corretto corrispondente alla posizione della macchina virtuale esistente:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Questo passaggio è facoltativo in quanto è possibile usare anche l'endpoint dell'identità del servizio metadati dell'istanza di Azure per recuperare i token.

### <a name="add-vm-system-assigned-identity-to-a-group"></a>Aggiungere l'identità assegnata dal sistema della macchina virtuale a un gruppo

Dopo aver abilitato l'identità assegnata dal sistema in una macchina virtuale, è possibile aggiungerla a un gruppo.  La procedura seguente consente di aggiungere un'identità assegnata dal sistema della macchina virtuale a un gruppo.

1. Accedere al portale di Azure con `Connect-AzAccount`. Usare un account associato alla sottoscrizione di Azure che contiene la VM.

   ```powershell
   Connect-AzAccount
   ```

2. Recuperare e prendere nota dell'`ObjectID` (come specificato nel campo `Id` dei valori restituiti) dell'entità servizio della macchina virtuale:

   ```powerhshell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. Recuperare e prendere nota dell'`ObjectID` (come specificato nel campo `Id` dei valori restituiti) del gruppo:

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. Aggiungere l'entità servizio della macchina virtuale al gruppo:

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Disabilitare un'identità gestita assegnata dal sistema in una macchina virtuale di Azure

Per disabilitare l'identità gestita assegnata dal sistema in una macchina virtuale, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

Se si ha una macchina virtuale che non ha più bisogno dell'identità gestita assegnata dal sistema ma ha ancora bisogno di identità gestite assegnate dagli utenti, usare il cmdlet seguente:

1. Accedere al portale di Azure con `Connect-AzAccount`. Usare un account associato alla sottoscrizione di Azure che contiene la VM.

   ```powershell
   Connect-AzAccount
   ```

2. Recuperare le proprietà della macchina virtuale usando il cmdlet `Get-AzVM` e impostare il parametro `-IdentityType` su `UserAssigned`:

   ```powershell   
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Se si ha una macchina virtuale che non ha più bisogno dell'identità gestita assegnata dal sistema e non dispone di identità gestite assegnate dall'utente, usare i comandi seguenti:

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

Per rimuovere le identità gestite per l'estensione delle macchine virtuali delle risorse di Azure, usare l'opzione relativa al nome con il cmdlet [Remove-AzVMExtension](/powershell/module/az.compute/remove-azvmextension) specificando lo stesso nome usato quando è stata aggiunta l'estensione:

   ```powershell
   Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-managed-identity"></a>Identità gestita assegnata dall'utente

Questa sezione illustra come aggiungere e rimuovere un'identità gestita assegnata dall'utente da una macchina virtuale tramite Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Assegnare a una macchina virtuale un'identità gestita assegnata dall'utente durante la creazione

Per assegnare un'identità assegnata dall'utente a una macchina virtuale, all'account devono essere assegnati i ruoli [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [Operatore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Fare riferimento a una delle seguenti guide introduttive sulle VM di Azure, completando solo le sezioni necessarie ("Accedere ad Azure", "Creare un gruppo di risorse", "Crea un gruppo di rete", "Creare la VM"). 
  
    Quando si raggiunge la sezione "Creare la VM", apportare una leggera modifica alla sintassi del cmdlet [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm). Aggiungere i parametri `-IdentityType UserAssigned` e `-IdentityID ` per eseguire il provisioning della macchina virtuale con un'identità assegnata dall'utente.  Sostituire `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati.  Ad esempio: 
    
    ```powershell 
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Creare una macchina virtuale Windows mediante PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Creare una macchina virtuale Linux mediante PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Facoltativo) Aggiungere l'identità gestita per l'estensione della macchina virtuale delle risorse di Azure usando il parametro `-Type` nel cmdlet [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension). È possibile passare "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", in base al tipo di VM, e assegnare un nome mediante il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token. Assicurarsi di specificare il parametro `-Location` corretto corrispondente alla posizione della macchina virtuale esistente:
      > [!NOTE]
    > Questo passaggio è facoltativo in quanto è possibile usare anche l'endpoint dell'identità del servizio metadati dell'istanza di Azure per recuperare i token. La deprecazione dell'estensione della macchina virtuale relativa alle identità gestite per le risorse di Azure è pianificata per il gennaio 2019.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Assegnare un'identità gestita assegnata dall'utente a una macchina virtuale di Azure esistente

Per assegnare un'identità assegnata dall'utente a una macchina virtuale, all'account devono essere assegnati i ruoli [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [Operatore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Accedere al portale di Azure con `Connect-AzAccount`. Usare un account associato alla sottoscrizione di Azure che contiene la VM.

   ```powershell
   Connect-AzAccount
   ```

2. Creare un'identità gestita assegnata dall'utente tramite il cmdlet [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity).  Si noti `Id` nell'output perché sarà necessaria nel passaggio successivo.

   > [!IMPORTANT]
   > La creazione di identità gestite assegnate dall'utente supporta solo caratteri alfanumerici e trattino (da 0 a 9, da "a" a "z", da "A" a "Z" e "-"). Inoltre, il nome può avere al massimo 24 caratteri perché l'assegnazione alla macchina virtuale/set di scalabilità di macchine virtuali funzioni correttamente. Ricontrollare in seguito per aggiornamenti. Per altre informazioni, vedere [Domande frequenti e problemi noti](known-issues.md)

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Recuperare le proprietà della macchina virtuale usando il cmdlet `Get-AzVM`. Quindi per assegnare un'identità gestita assegnata dall'utente alla macchina virtuale di Azure, usare le opzioni `-IdentityType` e `-IdentityID` nel cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm).  Il valore per il parametro`-IdentityId` è il valore `Id` annotato nel passaggio precedente.  Sostituire `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati.

   > [!WARNING]
   > Per conservare le identità gestite precedentemente assegnate dall'utente alla macchina virtuale, eseguire una query sulla proprietà `Identity` dell'oggetto macchina virtuale (ad esempio, `$vm.Identity`).  Se vengono restituite delle identità gestite assegnate dall'utente, includerle nel comando seguente insieme alla nuova identità gestita assegnata dall'utente che si vuole assegnare alla macchina virtuale.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. Aggiungere l'identità gestita per l'estensione della macchina virtuale delle risorse di Azure (pianificazione deprecata nel gennaio 2019) usando il parametro `-Type` nel cmdlet [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension). È possibile passare "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", in base al tipo di VM, e assegnare un nome mediante il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token. Specificare il parametro `-Location` corretto corrispondente alla posizione della macchina virtuale esistente.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Rimuovere un'identità gestita assegnata dall'utente da una macchina virtuale di Azure

Per rimuovere un'identità assegnata dall'utente da una macchina virtuale, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).

Se la VM ha più identità gestite assegnate dall'utente è possibile rimuoverle tutte tranne l'ultima mediante i comandi seguenti. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VM NAME>` con valori personalizzati. `<USER ASSIGNED IDENTITY NAME>` è la proprietà del nome dell'identità gestita assegnata dall'utente, che deve rimanere nella VM. È possibile trovare queste informazioni eseguendo una query sulla proprietà `Identity` dell'oggetto macchina virtuale.  Ad esempio, `$vm.Identity`:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Se la macchina virtuale non ha un'identità gestita assegnata dal sistema e si vogliono rimuovere tutte le identità gestite assegnate dall'utente al suo interno, usare il comando seguente:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Se la macchina virtuale ha identità gestite sia assegnate dal sistema che assegnate dall'utente, è possibile rimuovere tutte le identità gestite assegnate dall'utente iniziando a usare solo identità gestite assegnate dal sistema.

```powershell 
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica delle identità gestite per le risorse di Azure](overview.md)
- Per la guida introduttiva completa sulla creazione di VM di Azure, vedere:
  
  - [Creare una macchina virtuale Windows con PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Creare una macchina virtuale Linux con PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
