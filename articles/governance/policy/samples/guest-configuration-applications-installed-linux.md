---
title: Esempio - Controllare le applicazioni mancanti nelle VM Linux
description: Questa iniziativa e le definizioni di Configurazione guest di Criteri di Azure di esempio controllano se le applicazioni specificate non sono installate nelle macchine virtuali Linux.
ms.date: 05/02/2019
ms.topic: sample
ms.openlocfilehash: 0789b7f408c1f3eea000bfb2fc21ddf5feff790c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076218"
---
# <a name="sample---audit-if-specified-applications-arent-installed-inside-linux-vms"></a>Esempio: controllare se le applicazioni specificate non sono installate nelle macchine virtuali Linux

Questa iniziativa di Configurazione guest di criteri crea un evento di controllo quando le applicazioni specificate non sono installate nelle macchine virtuali Linux. L'ID di questa iniziativa predefinita è `/providers/Microsoft.Authorization/policySetDefinitions/c937dcb4-4398-4b39-8d63-4a6be432252e`.

> [!IMPORTANT]
> Tutte le iniziative di Configurazione guest sono costituite da definizioni di criteri **audit** e **deployIfNotExists**. L'assegnazione di una sola delle definizioni di criteri impedisce il corretto funzionamento di Configurazione guest.

È possibile assegnare questo esempio tramite:

- Il[portale di Azure](#azure-portal)
- [Azure PowerShell](#azure-powershell)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="components-of-the-initiative"></a>Componenti dell'iniziativa

Questa iniziativa di [Configurazione guest](../concepts/guest-configuration.md) è costituita dai criteri seguenti:

- [controllo](#audit-definition): controllare se le applicazioni non sono installate nelle macchine virtuali Linux
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/fee5cb2b-9d9b-410e-afe3-2902d90d0004`
- [deployIfNotExists](#deployIfNotExists-definition): distribuisce l'estensione della macchina virtuale per controllare quando le applicazioni non sono installate nelle macchine virtuali Linux
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/4d1c04de-2172-403f-901b-90608c35c721`

### <a name="initiative-definition"></a>Definizione di iniziativa

Per creare l'iniziativa, si uniscono le definizioni **audit** e **deployIfNotExists** e i [parametri dell'iniziativa](#initiative-parameters). Questo è il codice JSON della definizione.

[!code-json[initiative-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/azurepolicyset.json "Initiative definition (JSON)")]

### <a name="initiative-parameters"></a>Parametri delle iniziative

|NOME |Type |DESCRIZIONE |
|---|---|---|
|applicationName |string |Nomi delle applicazioni. Esempio: 'python', 'powershell' oppure un elenco delimitato da virgole, come 'python,powershell'. Usare \* per la corrispondenza con carattere jolly, ad esempio 'power\*'. |

Quando si crea un'assegnazione tramite PowerShell o l'interfaccia della riga di comando di Azure, i valori dei parametri possono essere passati in formato JSON in una stringa o tramite un file usando `-PolicyParameter` (PowerShell) o `--params` (interfaccia della riga di comando di Azure).
PowerShell supporta anche `-PolicyParameterObject`, che richiede di passare al cmdlet una tabella hash nome/valore il cmdlet in cui il **nome** è il nome del parametro e il **valore** è il valore singolo o la matrice di valori passati durante assegnazione.

In questo parametro di esempio viene controllata l'installazione delle applicazioni _python_ e _powershell_.

```json
{
    "applicationName": {
        "value": "python,powershell"
    }
}
```

I parametri dell'iniziativa vengono usato solo nella definizione di criteri **deployIfNotExists**.

### <a name="audit-definition"></a>Definizione audit

Codice JSON che definisce le regole della definizione di criteri **audit**.

[!code-json[audit-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json "audit policy rules (JSON)")]

### <a name="deployifnotexists-definition"></a>Definizione deployIfNotExists

Codice JSON che definisce le regole della definizione di criteri **deployIfNotExists**.

[!code-json[deployIfNotExists-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json "deployIfNotExists policy rules (JSON)")]

La definizione di criteri **deployIfNotExists** consente di definire le immagini di Azure convalidate dai criteri:

|Editore |Offerta |SKU |
|-|-|-|
|OpenLogic |CentOS\* |Tutti tranne 6\* |
|RedHat |RHEL |Tutti tranne 6\* |
|RedHat |osa | Tutti |
|credativ |Debian | Tutti tranne 7\* |
|SUSE |SLES\* |Tutti tranne 11\* |
|Canonical| UbuntuServer |Tutti tranne 12\* |
|microsoft-dsvm |linux-data-science-vm-ubuntu |Tutti |
|microsoft-dsvm |azureml |Tutti |
|cloudera |cloudera-centos-os |Tutti tranne 6\* |
|cloudera |cloudera-altus-centos-os |Tutti |
|microsoft-ads |linux\* |Tutti |
|microsoft-aks |Tutti |Tutti |
|AzureDatabricks |Tutti |Tutti |
|qubole-inc |Tutti |Tutti |
|datastax |Tutti |Tutti |
|couchbase |Tutti |Tutti |
|scalegrid |Tutti |Tutti |
|checkpoint |Tutti |Tutti |
|paloaltonetworks |Tutti |Tutti |

La parte **deployment** della regola passa il parametro _installedApplication_ all'agente di Configurazione guest nella macchina virtuale. Questa configurazione consente all'agente di eseguire le convalide e di restituire i dati di conformità tramite la definizione di criteri **audit**.

## <a name="azure-portal"></a>Portale di Azure

Dopo la creazione delle definizioni **audit** e **deployIfNotExists** nel portale, è consigliabile raggrupparle in un'[iniziativa](../concepts/definition-structure.md#initiatives) per l'assegnazione.

### <a name="create-copy-of-audit-definition"></a>Creare la copia della definizione audit

[![Distribuire l'esempio di Criteri in Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)
[![Distribuire l'esempio di Criteri in Azure Gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)

Questi pulsanti per la distribuzione tramite il portale consentono di creare una copia della definizione di criteri **audit**.
Se non è presente la definizione di criteri abbinata **deployIfNotExists**, Configurazione guest non funzionerà correttamente.

### <a name="create-copy-of-deployifnotexists-definition"></a>Creare la copia della definizione deployIfNotExists

[![Distribuire l'esempio di Criteri in Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)
[![Distribuire l'esempio di Criteri in Azure Gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)

Questi pulsanti per la distribuzione tramite il portale consentono di creare una copia della definizione di criteri **deployIfNotExists**. Se non è presente la definizione di criteri abbinata **audit**, Configurazione guest non funzionerà correttamente.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Distribuire con Azure PowerShell

#### <a name="copy-and-assign-the-initiative"></a>Copiare e assegnare l'iniziativa

Questa procedura consente di creare una copia dell'iniziativa che include i criteri predefiniti per **audit** e **deployIfNotExists** e assegna l'iniziativa a un gruppo di risorse.

```azurepowershell-interactive
# Create the policy initiative (Subscription scope)
$initDef = New-AzPolicySetDefinition -Name 'guestconfig-installed-application-linux' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This initiative will both deploy the policy requirements and audit that the specified application is installed inside Linux virtual machines.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.definitions.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the initiative parameter (JSON format)
$initParam = '{ "applicationName": { "value": "python,powershell" } }'

# Create the initiative assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicySetDefinition $initDef -PolicyParameter $initParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Eseguire questi comandi per rimuovere l'assegnazione e la definizione precedenti:

```azurepowershell-interactive
# Remove the initiative assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the initiative definition
Remove-AzPolicySetDefinition -Id $initDef
```

#### <a name="copy-and-assign-the-audit-definition"></a>Copiare e assegnare la definizione audit

Questa procedura consente di creare una copia della definizione **audit** e di assegnarla a un gruppo di risorse. Questa definizione non funzionerà correttamente se non viene assegnata anche la definizione abbinata **deployIfNotExists**.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-audit' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This policy audits that the specified application is installed inside Linux virtual machines. This policy should only be used along with its corresponding deploy policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-audit-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicyDefinition $definition
```

Eseguire questi comandi per rimuovere l'assegnazione e la definizione precedenti:

```azurepowershell-interactive
# Remove the policy definition
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

#### <a name="copy-and-assign-the-deployifnotexists-definition"></a>Copiare e assegnare la definizione deployIfNotExists

Questa procedura consente di creare una copia della definizione **deployIfNotExists** e di assegnarla a un gruppo di risorse.
Questa definizione non funzionerà correttamente se non viene assegnata anche la definizione abbinata **audit**.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-deployIfNotExists' -DisplayName 'GuestConfig - Deploy VM extension to audit that an application is installed inside Linux VMs' -description 'Include this rule to deploy the VM extension for Microsoft Guest Configuration, the VM extension for Microsoft Azure Managed Service Identity, and the content required to audit that an application is installed inside Linux virtual machines. This policy should only be used along with its corresponding audit policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the definition parameter (JSON format)
$policyParam  = '{ "applicationName": { "value": "python,powershell" } }'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-deployIfNotExists-assignment' -DisplayName 'GuestConfig - Deploy VM extension to audit that Python and PowerShell are installed inside Linux VMs' -Scope $scope.ResourceID -PolicyDefinition $definition -PolicyParameter $policyParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Eseguire questi comandi per rimuovere l'assegnazione e la definizione precedenti:

```azurepowershell-interactive
# Remove the policy assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

### <a name="azure-powershell-explanation"></a>Spiegazione di Azure PowerShell

Gli script di distribuzione e rimozione usano i comandi seguenti. Ogni comando della tabella seguente include collegamenti alla documentazione specifica del comando:

| Comando | Note |
|---|---|
| [New-AzPolicySetDefinition](/powershell/module/az.resources/New-AzPolicySetDefinition) | Crea un'iniziativa di Criteri di Azure. |
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-AzPolicyDefinition) | Crea una definizione di Criteri di Azure. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup) | Ottiene un singolo gruppo di risorse. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-AzPolicyAssignment) | Crea una nuova assegnazione di Criteri di Azure per un'iniziativa o definizione. |
| [New-AzRoleAssignment](/powershell/module/az.resources/New-AzRoleAssignment) | Concede un'assegnazione di ruolo esistente all'entità di sicurezza specifica. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-AzPolicyAssignment) | Rimuove un'assegnazione di Criteri di Azure esistente. |
| [Remove-AzPolicySetDefinition](/powershell/module/az.resources/Remove-AzPolicySetDefinition) | Rimuove un'iniziativa. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-AzPolicyDefinition) | Rimuove una definizione. |

## <a name="next-steps"></a>Passaggi successivi

- Vedere altri [esempi di Criteri di Azure](index.md).
- Altre informazioni su [Configurazione guest di Criteri di Azure](../concepts/guest-configuration.md).
- Vedere [Struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
