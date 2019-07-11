---
title: Esercitazione - Gestire le macchine virtuali di Azure con Azure PowerShell | Microsoft Docs
description: In questa esercitazione viene descritto come usare Azure PowerShell per gestire macchine virtuali di Azure applicando controllo degli accessi in base al ruolo, criteri, blocchi e tag
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: gwallace
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 12/05/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 74f8ad6935c0d6776dcad6f24e7e92318f7aba4a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708118"
---
# <a name="tutorial-learn-about-windows-virtual-machine-management-with-azure-powershell"></a>Esercitazione: Gestione delle macchine virtuali Windows con Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

## <a name="understand-scope"></a>Informazioni sull'ambito

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

In questa esercitazione tutte le impostazioni di gestione vengono applicate a un gruppo di risorse per semplificare la rimozione delle impostazioni stesse al termine delle operazioni.

Creare il gruppo di risorse.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Il gruppo di risorse è attualmente vuoto.

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Si vuole che gli utenti dell'organizzazione dispongano del giusto livello di accesso a queste risorse. Non si vuole concedere un accesso illimitato agli utenti, ma si vuole comunque avere la certezza che tutti possano svolgere il proprio lavoro. Il [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md) consente di stabilire quali utenti sono autorizzati a completare specifiche azioni in un determinato ambito.

Per creare e rimuovere assegnazioni di ruoli, gli utenti devono disporre di accesso `Microsoft.Authorization/roleAssignments/*`. Questo accesso viene concesso tramite il ruolo Proprietario o Amministratore accessi utente.

Per la gestione di soluzioni di macchine virtuali, sono disponibili tre ruoli specifici delle risorse che forniscono l'accesso in genere necessario:

* [Collaboratore macchine virtuali](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Collaboratore di rete](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Collaboratore account di archiviazione](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Invece di assegnare ruoli ai singoli utenti, è spesso più facile usare un gruppo di Azure Active Directory con utenti che devono eseguire azioni simili. Assegnare quindi tale gruppo al ruolo appropriato. Per questo articolo, usare un gruppo esistente per la gestione della macchina virtuale oppure usare il portale per [creare un gruppo di Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Dopo aver creato un nuovo gruppo o averne trovato uno esistente, usare il comando [New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment) per assegnare il gruppo di Azure Active Directory al ruolo Collaboratore Macchina virtuale per il gruppo di risorse.  

```azurepowershell-interactive
$adgroup = Get-AzADGroup -DisplayName <your-group-name>

New-AzRoleAssignment -ObjectId $adgroup.id `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Se viene visualizzato l'errore **L'entità \<guid> non esiste nella directory**, il nuovo gruppo non è stato ancora propagato in Azure Active Directory. Provare a eseguire di nuovo il comando.

In genere si ripete il processo per *Collaboratore Rete* e *Collaboratore Account di archiviazione* per assicurarsi che vengano assegnati utenti per la gestione delle risorse distribuite. In questo articolo è possibile ignorare questi passaggi.

## <a name="azure-policy"></a>Criteri di Azure

I [Criteri di Azure](../../governance/policy/overview.md) consentono di verificare che tutte le risorse nella sottoscrizione soddisfino gli standard aziendali. La sottoscrizione include già alcune definizioni dei criteri. Per visualizzare le definizioni di criteri disponibili, usare il comando [Get-AzPolicyDefinition](https://docs.microsoft.com/powershell/module/az.resources/Get-AzPolicyDefinition):

```azurepowershell-interactive
(Get-AzPolicyDefinition).Properties | Format-Table displayName, policyType
```

Vengono visualizzate le definizioni dei criteri esistenti. Il tipo di criterio è **BuiltIn** o **Custom**. Esaminare le definizioni per individuare i criteri che descrivono una condizione da assegnare. In questo articolo vengono assegnati criteri che consentono di:

* Limitare le posizioni per tutte le risorse.
* Limitare gli SKU per le macchine virtuali.
* Controllare le macchine virtuali che non usano dischi gestiti.

L'esempio seguente recupera tre definizioni di criteri in base al nome visualizzato. Per assegnare queste definizioni al gruppo di risorse viene usato il comando [New-AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment). Per alcuni criteri occorre fornire i valori dei parametri per specificare i valori consentiti.

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
New-AzPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>Distribuire la macchina virtuale

Sono stati assegnati ruoli e criteri, quindi è ora possibile distribuire la soluzione. Le dimensioni predefinite sono Standard_DS1_v2, che corrispondono a uno degli SKU consentiti. Quando si esegue questo passaggio vengono chieste le credenziali. I valori immessi sono configurati come nome utente e password per la macchina virtuale.

```azurepowershell-interactive
New-AzVm -ResourceGroupName "myResourceGroup" `
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

Per bloccare la macchina virtuale e il gruppo di sicurezza di rete, usare il comando [New-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock):

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Per testare i blocchi, provare a eseguire il comando seguente:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup
```

Viene visualizzato un messaggio di errore che informa che l'operazione di eliminazione non può essere completata a causa di un blocco. Il gruppo di risorse può essere eliminato solo se si rimuovono in modo specifico i blocchi. Questo passaggio è illustrato in [Pulire le risorse](#clean-up-resources).

## <a name="tag-resources"></a>Aggiungere tag alle risorse

I [tag](../../azure-resource-manager/resource-group-using-tags.md) vengono applicati alle risorse di Azure per organizzarle in modo logico in categorie. Ogni tag è costituito da un nome e un valore. Ad esempio, è possibile applicare il nome "Environment" e il valore "Production" a tutte le risorse nell'ambiente di produzione.

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Per applicare tag a una macchina virtuale, usare il comando [Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource):

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Trovare le risorse in base al tag

Per trovare le risorse con un nome e un valore di tag, usare il comando [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource):

```azurepowershell-interactive
(Get-AzResource -Tag @{ Environment="Test"}).Name
```

È possibile usare i valori restituiti per attività di gestione quali l'arresto di tutte le macchine virtuali con un valore di tag.

```azurepowershell-interactive
Get-AzResource -Tag @{ Environment="Test"} | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzVM
```

### <a name="view-costs-by-tag-values"></a>Visualizzare i costi in base ai valori dei tag

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile eliminare il gruppo di sicurezza di rete bloccato solo dopo la rimozione del blocco. Per rimuovere il blocco, usare il comando [Remove-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcelock):

```azurepowershell-interactive
Remove-AzResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
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

