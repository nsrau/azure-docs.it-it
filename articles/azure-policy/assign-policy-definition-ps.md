---
title: Guida introduttiva - Usare PowerShell per creare un'assegnazione dei criteri per identificare le risorse non conformi nell'ambiente Azure
description: In questa guida introduttiva si userà PowerShell per creare un'assegnazione di criteri di Azure per identificare le risorse non conformi.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 50c6a0cc268e2b638a03494bed96d294789da9ab
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794667"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-the-azure-rm-powershell-module"></a>Guida introduttiva: Creare un'assegnazione di criteri per identificare le risorse non conformi con il modulo AzureRM di PowerShell

Il primo passaggio per comprendere la conformità in Azure consiste nell'identificare lo stato delle risorse. In questa guida introduttiva si creerà un'assegnazione di criteri per identificare le macchine virtuali che non usano dischi gestiti. Al termine, si identificheranno le macchina virtuali *non conformi* all'assegnazione di criteri.

Il modulo AzureRM di PowerShell viene usato per creare e gestire le risorse di Azure dalla riga di comando o in script. Questa guida illustra come usare AzureRM per creare un'assegnazione di criteri. I criteri identificano le risorse non conformi nell'ambiente Azure.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

- Se non è già stato fatto, installare [ARMClient](https://github.com/projectkudu/ARMClient). È uno strumento che invia richieste HTTP alle API basate su Azure Resource Manager.
- Prima di iniziare verificare che sia installata la versione più recente di PowerShell. Per informazioni dettagliate, vedere [Panoramica di Azure PowerShell](/powershell/azureps-cmdlets-docs).
- Aggiornare il modulo AzureRM di PowerShell all'ultima versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps).
- Registrare il provider di risorse Policy Insights usando Azure PowerShell. La registrazione del provider di risorse consente di assicurare che la sottoscrizione lo usi. Per registrare un provider di risorse, è necessaria l'autorizzazione per eseguire l'operazione /register/action per il provider di risorse. Questa operazione è inclusa nei ruoli Collaboratore e Proprietario. Eseguire il comando seguente per registrare il provider di risorse:

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Per maggiori dettagli sulla registrazione e la visualizzazione di provider di risorse, vedere [Provider e tipi di risorse](../azure-resource-manager/resource-manager-supported-services.md)

## <a name="create-a-policy-assignment"></a>Creare un'assegnazione di criteri

In questa guida introduttiva si crea un'assegnazione di criteri e si assegna la definizione *Audit Virtual Machines without Managed Disks* (Controllare le macchine virtuali senza i dischi gestiti). Questa definizione di criteri identifica le risorse che non rispettano le condizioni in essa impostate.

Eseguire questi comandi per creare un nuova assegnazione di criteri:

```azurepowershell-interactive
$rg = Get-AzureRmResourceGroup -Name '<resourceGroupName>'
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }
New-AzureRmPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit Virtual Machines without Managed Disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

I comandi precedenti utilizzano le informazioni seguenti:

- **Name**: nome effettivo dell'assegnazione.  Per questo esempio è stato usato il nome *audit-vm-manageddisks*.
- **DisplayName**: nome visualizzato per l'assegnazione di criteri. In questo caso si usa *Audit Virtual Machines without Managed Disks Assignment*.
- **Definition**: definizione di criteri in base alla quale si crea l'assegnazione. In questo caso si tratta dell'ID della definizione di criteri *Audit VMs that do not use managed disks* (Controllare le macchine virtuali che non usano i dischi gestiti).
- **Scope**: ambito che determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di criteri. e può variare da una sottoscrizione a gruppi di risorse. Assicurarsi di sostituire &lt;scope&gt; con il nome del gruppo di risorse.

A questo punto si è pronti per identificare le risorse non conformi per comprendere lo stato di conformità dell'ambiente.

## <a name="identify-non-compliant-resources"></a>Identificare le risorse non conformi

Usare le informazioni seguenti per identificare le risorse non conformi all'assegnazione di criteri creata. Eseguire i comandi seguenti:

```azurepowershell-interactive
$policyAssignment = Get-AzureRmPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit Virtual Machines without Managed Disks Assignment' }
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

Le guide successive di questa raccolta si basano su questa guida introduttiva. Se si prevede di continuare a usare le altre esercitazioni, non eseguire la pulizia delle risorse create in questa guida introduttiva. Se non si intende continuare, è possibile eliminare l'assegnazione creata eseguendo questo comando:

```azurepowershell-interactive
Remove-AzureRmPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata assegnata una definizione dei criteri per identificare le risorse non conformi nell'ambiente Azure.

Per altre informazioni sull'assegnazione di criteri e per assicurarsi che le risorse create **in futuro** siano conformi, passare all'esercitazione seguente:

> [!div class="nextstepaction"]
> [Creazione e gestione dei criteri](create-manage-policy.md)