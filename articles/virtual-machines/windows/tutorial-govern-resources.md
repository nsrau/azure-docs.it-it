---
title: Configurare la governance delle macchine virtuali Windows con Azure PowerShell | Microsoft Docs
description: 'Esercitazione: gestire macchine virtuali di Azure applicando Controllo degli accessi in base al ruolo, criteri, blocchi e tag con Azure PowerShell'
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: 9fbe9318e52f8299c3ef46f73c3be177de6d4a0c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="virtual-machine-governance-with-azure-powershell"></a>Governance delle macchine virtuali con Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure. Per le installazioni locali, occorre anche [scaricare il modulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/) per creare un nuovo gruppo di Azure Active Directory.

## <a name="understand-scope"></a>Informazioni sull'ambito

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

In questa esercitazione tutte le impostazioni di gestione vengono applicate a un gruppo di risorse per semplificare la rimozione delle impostazioni stesse al termine delle operazioni.

Creare il gruppo di risorse.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Il gruppo di risorse è attualmente vuoto.

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Si vuole che gli utenti dell'organizzazione dispongano del giusto livello di accesso a queste risorse. Non si vuole concedere un accesso illimitato agli utenti, ma si vuole comunque avere la certezza che tutti possano svolgere il proprio lavoro. Il [controllo degli accessi in base al ruolo](../../active-directory/role-based-access-control-what-is.md) consente di stabilire quali utenti sono autorizzati a completare specifiche azioni in un determinato ambito.

Per creare e rimuovere assegnazioni di ruoli, gli utenti devono disporre di accesso `Microsoft.Authorization/roleAssignments/*`. Questo accesso viene concesso tramite il ruolo Proprietario o Amministratore accessi utente.

Per la gestione di soluzioni di macchine virtuali, sono disponibili tre ruoli specifici delle risorse che forniscono l'accesso in genere necessario:

* [Collaboratore macchine virtuali](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)
* [Collaboratore di rete](../../active-directory/role-based-access-built-in-roles.md#network-contributor)
* [Collaboratore account di archiviazione](../../active-directory/role-based-access-built-in-roles.md#storage-account-contributor)

Invece di assegnare ruoli ai singoli utenti, è spesso più facile [creare un gruppo di Azure Active Directory](../../active-directory/active-directory-groups-create-azure-portal.md) per gli utenti che devono eseguire azioni simili. Assegnare quindi tale gruppo al ruolo appropriato. Per semplificare questo articolo, creare un gruppo di Azure Active Directory senza membri. È comunque possibile assegnare questo gruppo a un ruolo per un ambito. 

L'esempio seguente crea un gruppo di Azure Active Directory denominato *VMDemoContributors* con il nome alternativo di posta elettronica *vmDemoGroup*. Il nome alternativo di posta elettronica funge da alias del gruppo.

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
```

Dopo la restituzione del risultato del prompt dei comandi, la propagazione del gruppo in Azure Active Directory richiede pochi istanti. Dopo aver atteso 20 o 30 secondi, usare il comando [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) per assegnare il nuovo gruppo di Azure Active Directory al ruolo Collaboratore Macchina virtuale per il gruppo di risorse.  Se si esegue il comando seguente prima della propagazione, si riceve un messaggio di errore che informa che **l'entità <guid> non esiste nella directory**. Provare a eseguire di nuovo il comando.

```azurepowershell-interactive
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

In genere si ripete il processo per *Collaboratore Rete* e *Collaboratore Account di archiviazione* per assicurarsi che vengano assegnati utenti per la gestione delle risorse distribuite. In questo articolo è possibile ignorare questi passaggi.

## <a name="azure-policies"></a>Criteri di Azure

[!INCLUDE [Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Applicare i criteri

La sottoscrizione include già alcune definizioni dei criteri. Per visualizzare le definizioni dei criteri disponibili, usare il comando [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition):

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Vengono visualizzate le definizioni dei criteri esistenti. Il tipo di criterio è **BuiltIn** o **Custom**. Esaminare le definizioni per individuare i criteri che descrivono una condizione da assegnare. In questo articolo vengono assegnati criteri che consentono di:

* Limitare le posizioni per tutte le risorse.
* Limitare gli SKU per le macchine virtuali.
* Controllare le macchine virtuali che non usano dischi gestiti.

L'esempio seguente recupera tre definizioni di criteri in base al nome visualizzato. Viene usato il comando [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) per assegnare queste definizioni al gruppo di risorse. Per alcuni criteri occorre fornire i valori dei parametri per specificare i valori consentiti.

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzureRmResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
New-AzureRMPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>Distribuire la macchina virtuale

Sono stati assegnati ruoli e criteri, quindi è ora possibile distribuire la soluzione. Le dimensioni predefinite sono Standard_DS1_v2, che corrispondono a uno degli SKU consentiti. Quando si esegue questo passaggio vengono chieste le credenziali. I valori immessi sono configurati come nome utente e password per la macchina virtuale.

```azurepowershell-interactive
New-AzureRmVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

Al termine della distribuzione è possibile applicare altre impostazioni di gestione alla soluzione.

## <a name="lock-resources"></a>Bloccare le risorse

I [blocchi delle risorse](../../azure-resource-manager/resource-group-lock-resources.md) impediscono agli utenti dell'organizzazione di modificare o eliminare accidentalmente risorse di importanza fondamentale. Diversamente dal controllo degli accessi in base al ruolo, i blocchi delle risorse consentono di applicare una restrizione a tutti gli utenti e i ruoli. È possibile impostare il livello di blocco *CanNotDelete* o *ReadOnly*.

Per bloccare la macchina virtuale e il gruppo di sicurezza di rete, usare il comando [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock):

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Per testare i blocchi, provare a eseguire il comando seguente:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Viene visualizzato un messaggio di errore che informa che l'operazione di eliminazione non può essere eseguita a causa di un blocco. Il gruppo di risorse può essere eliminato solo se si rimuovono in modo specifico i blocchi. Questo passaggio è illustrato in [Pulire le risorse](#clean-up-resources).

## <a name="tag-resources"></a>Aggiungere tag alle risorse

I [tag](../../azure-resource-manager/resource-group-using-tags.md) vengono applicati alle risorse di Azure per organizzarle in modo logico in categorie. Ogni tag è costituito da un nome e un valore. Ad esempio, è possibile applicare il nome "Environment" e il valore "Production" a tutte le risorse nell'ambiente di produzione.

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Per applicare tag a una macchina virtuale, usare il comando [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource).

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Trovare le risorse in base al tag

Per trovare le risorse tramite un nome e un valore di tag, usare il comando [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource):

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

È possibile usare i valori restituiti per attività di gestione quali l'arresto di tutte le macchine virtuali con un valore di tag.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Visualizzare i costi in base ai valori dei tag

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile eliminare il gruppo di sicurezza di rete bloccato solo dopo la rimozione del blocco. Per rimuovere il blocco, usare il comando [Remove-AzureRmResourceLock](/powershell/module/azurerm.resources/remove-azurermresourcelock):

```azurepowershell-interactive
Remove-AzureRmResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzureRmResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Quando non servono più, è possibile usare il comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'immagine di macchina virtuale personalizzata. Si è appreso come:

> [!div class="checklist"]
> * Assegnare utenti a un ruolo
> * Applicare criteri che applicano gli standard
> * Proteggere le risorse critiche con blocchi
> * Applicare tag alle risorse per semplificare fatturazione e gestione

Passare all'esercitazione successiva per la descrizione delle macchine virtuali a disponibilità elevata.

> [!div class="nextstepaction"]
> [Monitorare le macchine virtuali](tutorial-monitoring.md)

