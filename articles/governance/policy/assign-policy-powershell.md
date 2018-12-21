---
title: Creare un criterio per identificare le risorse non conformi con Azure PowerShell
description: Usare Azure PowerShell per creare un'assegnazione di criteri di Azure per identificare le risorse non conformi nell'ambiente.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 32fe811c80fd34b4ea3390a3f46a1d36aba7534e
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310709"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Creare un'assegnazione di criteri per identificare le risorse non conformi con Azure PowerShell

Il primo passaggio per comprendere la conformità in Azure consiste nell'identificare lo stato delle risorse. In questo avvio rapido si creerà un'assegnazione di criteri per identificare le macchine virtuali che non usano dischi gestiti. Al termine, si identificheranno le macchina virtuali *non conformi* all'assegnazione di criteri.

Il modulo AzureRM di PowerShell viene usato per creare e gestire le risorse di Azure dalla riga di comando o in script. Questa guida illustra come usare AzureRM per creare un'assegnazione di criteri. I criteri identificano le risorse non conformi nell'ambiente Azure.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- Se non è già stato fatto, installare [ARMClient](https://github.com/projectkudu/ARMClient). È uno strumento che invia richieste HTTP alle API basate su Azure Resource Manager.
- Prima di iniziare verificare che sia installata la versione più recente di PowerShell. Per informazioni dettagliate, vedere [Panoramica di Azure PowerShell](/powershell/azureps-cmdlets-docs).
- Aggiornare il modulo AzureRM di PowerShell all'ultima versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps).
- Registrare il provider di risorse Policy Insights usando Azure PowerShell. La registrazione del provider di risorse consente di assicurare che la sottoscrizione lo usi. Per registrare un provider di risorse, è necessaria l'autorizzazione per eseguire l'operazione del provider di risorse. Questa operazione è inclusa nei ruoli Collaboratore e Proprietario. Eseguire il comando seguente per registrare il provider di risorse:

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Per maggiori dettagli sulla registrazione e la visualizzazione di provider di risorse, vedere [Provider e tipi di risorse](../../azure-resource-manager/resource-manager-supported-services.md)

## <a name="create-a-policy-assignment"></a>Creare un'assegnazione di criteri

In questo avvio rapido si crea un'assegnazione di criteri e si assegna la definizione *controllare le macchine virtuali senza assegnazione di dischi gestiti*. Questa definizione di criteri identifica le risorse che non rispettano le condizioni in essa impostate.

Eseguire questi comandi per creare un nuova assegnazione di criteri:

```azurepowershell-interactive
$rg = Get-AzureRmResourceGroup -Name '<resourceGroupName>'
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }
New-AzureRmPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

I comandi precedenti utilizzano le informazioni seguenti:

- **Name**: nome effettivo dell'assegnazione.  Per questo esempio è stato usato il nome *audit-vm-manageddisks*.
- **DisplayName**: nome visualizzato per l'assegnazione di criteri. In questo caso viene usato *controllare le macchine virtuali senza assegnazione di dischi gestiti*.
- **Definition**: definizione di criteri in base alla quale si crea l'assegnazione. In questo caso si tratta dell'ID della definizione di criteri *Controllare le macchine virtuali che non usano i dischi gestiti*.
- **Scope**: ambito che determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di criteri. e può variare da una sottoscrizione a gruppi di risorse. Assicurarsi di sostituire &lt;scope&gt; con il nome del gruppo di risorse.

A questo punto si è pronti per identificare le risorse non conformi per comprendere lo stato di conformità dell'ambiente.

## <a name="identify-non-compliant-resources"></a>Identificare le risorse non conformi

Usare le informazioni seguenti per identificare le risorse non conformi all'assegnazione di criteri creata. Eseguire i comandi seguenti:

```azurepowershell-interactive
$policyAssignment = Get-AzureRmPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs without managed disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

Per altre informazioni sugli ID delle assegnazioni dei criteri, vedere [Get-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

Eseguire quindi questo comando per ottenere gli ID risorsa delle risorse non conformi restituite in un file JSON:

```
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

I risultati saranno simili all'esempio seguente:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

I risultati sono paragonabili alle informazioni in genere riportate sotto **Non-compliant resources** (Risorse non conformi) nella visualizzazione nel portale di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Eseguire il comando seguente per rimuovere l'assegnazione creata:

```azurepowershell-interactive
Remove-AzureRmPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata assegnata una definizione dei criteri per identificare le risorse non conformi nell'ambiente Azure.

Per altre informazioni sull'assegnazione di criteri per convalidare che le nuove risorse siano conformi, continuare con l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Creazione e gestione dei criteri](./tutorials/create-and-manage.md)