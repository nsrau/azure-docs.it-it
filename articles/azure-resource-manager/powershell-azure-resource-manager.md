---
title: Gestire le soluzioni di Azure con PowerShell | Documentazione Microsoft
description: Usare Azure PowerShell e Resource Manager per gestire le risorse.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: tomfitz
ms.openlocfilehash: 96206482195cdcbd06ee2dafdc551f7b1f81d319
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="manage-resources-with-azure-powershell"></a>Gestire le risorse con Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure.

## <a name="understand-scope"></a>Informazioni sull'ambito

[!INCLUDE [Resource Manager governance scope](../../includes/resource-manager-governance-scope.md)]

In questo articolo tutte le impostazioni di gestione vengono applicate a un gruppo di risorse per semplificare la rimozione delle impostazione al termine delle operazioni.

Creare il gruppo di risorse.

```azurepowershell-interactive
Set-AzureRmContext -Subscription <subscription-name>
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Il gruppo di risorse è attualmente vuoto.

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-rbac.md)]

### <a name="assign-a-role"></a>Assegnare un ruolo

In questo articolo viene eseguita la distribuzione di una macchina virtuale e della rispettiva rete virtuale correlata. Per la gestione di soluzioni di macchine virtuali, sono disponibili tre ruoli specifici delle risorse che forniscono l'accesso in genere necessario:

* [Collaboratore macchine virtuali](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)
* [Collaboratore di rete](../active-directory/role-based-access-built-in-roles.md#network-contributor)
* [Collaboratore account di archiviazione](../active-directory/role-based-access-built-in-roles.md#storage-account-contributor)

Invece di assegnare ruoli ai singoli utenti, è spesso più facile [creare un gruppo di Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md) per gli utenti che devono eseguire azioni simili. Assegnare quindi tale gruppo al ruolo appropriato. Per semplificare questo articolo, creare un gruppo di Azure Active Directory senza membri. È comunque possibile assegnare questo gruppo a un ruolo per un ambito. 

L'esempio seguente crea un gruppo e lo assegna al ruolo Collaboratore Macchina virtuale per il gruppo di risorse. Per eseguire il comando `New-AzureAdGroup`, è necessario usare [Azure Cloud Shell](/azure/cloud-shell/overview) o [scaricare il modulo di Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

In genere si ripete il processo per **Collaboratore Rete** e **Collaboratore Account di archiviazione** per assicurarsi che vengano assegnati utenti per la gestione delle risorse distribuite. In questo articolo è possibile ignorare questi passaggi.

## <a name="azure-policies"></a>Criteri di Azure

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Applicare i criteri

La sottoscrizione include già alcune definizioni dei criteri. Per visualizzare le definizioni dei criteri disponibili, usare il comando seguente:

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Vengono visualizzate le definizioni dei criteri esistenti. Il tipo di criterio è **BuiltIn** o **Custom**. Esaminare le definizioni per individuare i criteri che descrivono una condizione da assegnare. In questo articolo vengono assegnati criteri che consentono di:

* Limitare le posizioni per tutte le risorse
* Limitare gli SKU per le macchine virtuali
* Controllare le macchine virtuali che non usano Managed Disks

```azurepowershell-interactive
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

$rg = Get-AzureRmResourceGroup -Name myResourceGroup

$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus
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

[!INCLUDE [Resource Manager governance locks](../../includes/resource-manager-governance-locks.md)]

### <a name="lock-a-resource"></a>Bloccare una risorsa

Per bloccare la macchina virtuale e il gruppo di sicurezza di rete, usare il comando seguente:

```azurepowershell-interactive
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

La macchina virtuale può essere eliminata solo se si rimuove in modo specifico il blocco. Questo passaggio è illustrato in [Pulire le risorse](#clean-up-resources).

## <a name="tag-resources"></a>Aggiungere tag alle risorse

[!INCLUDE [Resource Manager governance tags](../../includes/resource-manager-governance-tags.md)]

### <a name="tag-resources"></a>Aggiungere tag alle risorse

[!INCLUDE [Resource Manager governance tags Powershell](../../includes/resource-manager-governance-tags-powershell.md)]

Per applicare tag a una macchina virtuale, usare il comando seguente:

```azurepowershell-interactive
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Trovare le risorse in base al tag

Per trovare le risorse tramite un nome e un valore di tag, usare il comando seguente:

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

È possibile usare i valori restituiti per attività di gestione quali l'arresto di tutte le macchine virtuali con un valore di tag.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Visualizzare i costi in base ai valori dei tag

Dopo l'applicazione dei tag alle risorse è possibile visualizzare i costi per le risorse con i tag specificati. La visualizzazione dell'utilizzo più recente nell'analisi dei costi richiede tempo, quindi è possibile che i costi non siano ancora visualizzati. Quando i costi sono disponibili, è possibile visualizzare i costi per le risorse nei gruppi di risorse della sottoscrizione. Per visualizzare i costi, gli utenti devono avere l'[accesso a livello di sottoscrizione alle informazioni di fatturazione](../billing/billing-manage-access.md).

Per visualizzare i costi in base al tag nel portale, selezionare la sottoscrizione e quindi **Analisi dei costi**.

![Analisi dei costi](./media/powershell-azure-resource-manager/select-cost-analysis.png)

Filtrare quindi in base al valore del tag e selezionare **Applica**.

![Visualizzare il costo in base al tag](./media/powershell-azure-resource-manager/view-costs-by-tag.png)

È anche possibile usare le [API di fatturazione di Azure](../billing/billing-usage-rate-card-overview.md) per visualizzare i costi a livello di programmazione.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile eliminare il gruppo di sicurezza di rete bloccato solo dopo la rimozione del blocco. Per rimuovere un blocco, usare il comando seguente:

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

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sul monitoraggio delle macchine virtuali, vedere [Monitorare e aggiornare una macchina virtuale Windows con Azure PowerShell](../virtual-machines/windows/tutorial-monitoring.md).
* Per informazioni sull'uso del Centro sicurezza di Azure per l'implementazione delle procedure di sicurezza consigliate, vedere [Monitorare la sicurezza delle macchine virtuali con il Centro sicurezza di Azure](../virtual-machines/windows/tutorial-azure-security.md).
* È possibile spostare le risorse esistenti in un nuovo gruppo di risorse. Per esempi, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).
