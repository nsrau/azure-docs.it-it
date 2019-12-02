---
title: 'Guida introduttiva: Nuova assegnazione di criteri con PowerShell'
description: In questa guida di avvio rapido si userà Azure PowerShell per creare un'assegnazione di criteri di Azure per identificare le risorse non conformi.
ms.date: 11/25/2019
ms.topic: quickstart
ms.openlocfilehash: 3ce823a7abfe16e4433128dcdfe073dfcfaeba50
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482393"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Guida introduttiva: Creare un'assegnazione di criteri per identificare le risorse non conformi con Azure PowerShell

Il primo passaggio per comprendere la conformità in Azure consiste nell'identificare lo stato delle risorse. In questo avvio rapido si creerà un'assegnazione di criteri per identificare le macchine virtuali che non usano dischi gestiti. Al termine, si identificheranno le macchine virtuali _non conformi_.

Il modulo Azure PowerShell viene usato per gestire le risorse di Azure dalla riga di comando o in script.
Questa guida illustra come usare il modulo Az per creare un'assegnazione di criteri.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

- Prima di iniziare verificare che sia installata la versione più recente di Azure PowerShell. Per informazioni dettagliate, vedere [Installare il modulo di Azure PowerShell](/powershell/azure/install-az-ps).

- Registrare il provider di risorse Azure Policy Insights usando Azure PowerShell. La registrazione del provider di risorse consente di assicurare che la sottoscrizione lo usi. Per registrare un provider di risorse, è necessaria l'autorizzazione per eseguire l'operazione del provider di risorse. Questa operazione è inclusa nei ruoli Collaboratore e Proprietario. Eseguire il comando seguente per registrare il provider di risorse:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Per maggiori dettagli sulla registrazione e la visualizzazione di provider di risorse, vedere [Provider e tipi di risorse](../../azure-resource-manager/resource-manager-supported-services.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Creare un'assegnazione di criteri

In questo argomento di avvio rapido si creerà un'assegnazione di criteri per la definizione _Audit VMs without managed disks_. Questa definizione di criteri identifica le macchine virtuali che non usano dischi gestiti.

Eseguire questi comandi per creare un nuova assegnazione di criteri:

```azurepowershell-interactive
# Get a reference to the resource group that will be the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition that will be assigned
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

I comandi precedenti utilizzano le informazioni seguenti:

- **Name**: nome effettivo dell'assegnazione. Per questo esempio è stato usato il nome _audit-vm-manageddisks_.
- **DisplayName**: nome visualizzato per l'assegnazione di criteri. In questo caso viene usato _controllare le macchine virtuali senza assegnazione di dischi gestiti_.
- **Definition**: definizione di criteri in base alla quale si crea l'assegnazione. In questo caso si tratta dell'ID della definizione di criteri _Controllare le macchine virtuali che non usano i dischi gestiti_.
- **Scope**: ambito che determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di criteri. e può variare da una sottoscrizione a gruppi di risorse. Assicurarsi di sostituire &lt;scope&gt; con il nome del gruppo di risorse.

A questo punto si è pronti per identificare le risorse non conformi e comprendere così lo stato di conformità dell'ambiente.

## <a name="identify-non-compliant-resources"></a>Identificare le risorse non conformi

Usare le informazioni seguenti per identificare le risorse non conformi all'assegnazione di criteri creata. Eseguire i comandi seguenti:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

Per altre informazioni su come ottenere lo stato dei criteri, vedere [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState).

I risultati saranno simili all'esempio seguente:

```output
Timestamp                   : 3/9/19 9:21:29 PM
ResourceId                  : /subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmId}
PolicyAssignmentId          : /subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/audit-vm-manageddisks
PolicyDefinitionId          : /providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d
IsCompliant                 : False
SubscriptionId              : {subscriptionId}
ResourceType                : /Microsoft.Compute/virtualMachines
ResourceTags                : tbd
PolicyAssignmentName        : audit-vm-manageddisks
PolicyAssignmentOwner       : tbd
PolicyAssignmentScope       : /subscriptions/{subscriptionId}
PolicyDefinitionName        : 06a78e20-9358-41c9-923c-fb736d382a4d
PolicyDefinitionAction      : audit
PolicyDefinitionCategory    : Compute
ManagementGroupIds          : {managementGroupId}
```

I risultati corrispondono a quanto visualizzato nella scheda **Conformità risorsa** di un'assegnazione di criteri nella vista del portale di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Eseguire il comando seguente per rimuovere l'assegnazione creata:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata assegnata una definizione dei criteri per identificare le risorse non conformi nell'ambiente Azure.

Per altre informazioni sull'assegnazione di criteri per convalidare che le nuove risorse siano conformi, continuare con l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Creazione e gestione dei criteri](./tutorials/create-and-manage.md)