---
title: "Guida introduttiva: Nuova assegnazione di criteri con l'interfaccia della riga di comando di Azure"
description: In questo argomento di avvio rapido viene usata l'interfaccia della riga di comando di Azure per creare un'assegnazione di criteri di Azure per identificare le risorse non conformi.
ms.date: 11/25/2019
ms.topic: quickstart
ms.openlocfilehash: 80dbccdb728da94d9f9fdd0aeb506ade40fd7394
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482634"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>Guida introduttiva: Creare un criterio per identificare le risorse non conformi con l'interfaccia della riga di comando di Azure

Il primo passaggio per comprendere la conformità in Azure consiste nell'identificare lo stato delle risorse.
Questa guida introduttiva illustra il processo di creazione di un'assegnazione di criteri per identificare le macchine virtuali che non usano dischi gestiti.

Alla fine di questo processo, verranno identificate le macchine virtuali che non usano dischi gestiti e che sono quindi _non conformi_ all'assegnazione di criteri.

L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida usa l'interfaccia della riga di comando di Azure per creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

- Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo argomento di avvio rapido richiede l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.76 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

- Registrare il provider di risorse Azure Policy Insights usando l'interfaccia della riga di comando di Azure. La registrazione del provider di risorse consente di assicurare che la sottoscrizione lo usi. Per registrare un provider di risorse, è necessaria l'autorizzazione per eseguire l'operazione del provider di risorse. Questa operazione è inclusa nei ruoli Collaboratore e Proprietario. Eseguire il comando seguente per registrare il provider di risorse:

  ```azurecli-interactive
  az provider register --namespace 'Microsoft.PolicyInsights'
  ```

  Per maggiori dettagli sulla registrazione e la visualizzazione di provider di risorse, vedere [Provider e tipi di risorse](../../azure-resource-manager/resource-manager-supported-services.md)

- Se non è già stato fatto, installare [ARMClient](https://github.com/projectkudu/ARMClient). È uno strumento che invia richieste HTTP alle API basate su Azure Resource Manager.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Creare un'assegnazione di criteri

In questa guida introduttiva si crea un'assegnazione di criteri e si assegna la definizione **Audit VMs that do not use managed disks** (Controllare le macchine virtuali che non usano i dischi gestiti). Questa definizione di criteri identifica le risorse che non rispettano le condizioni in essa impostate.

Eseguire questo comando per creare un'assegnazione di criteri:

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

Il comando precedente usa le informazioni seguenti.

- **Name**: nome effettivo dell'assegnazione. Per questo esempio è stato usato il nome _audit-vm-manageddisks_.
- **DisplayName**: nome visualizzato per l'assegnazione di criteri. In questo caso viene utilizzato _controllare le macchine virtuali senza assegnazione di dischi gestiti_.
- **Policy**: ID della definizione di criteri in base alla quale si crea l'assegnazione. In questo caso si tratta dell'ID della definizione di criteri _Controllare le macchine virtuali che non usano i dischi gestiti_. Per ottenere l'ID della definizione di criteri, eseguire questo comando: `az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`
- **Scope**: ambito che determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di criteri. e può variare da una sottoscrizione a gruppi di risorse. Assicurarsi di sostituire &lt;scope&gt; con il nome del gruppo di risorse.

## <a name="identify-non-compliant-resources"></a>Identificare le risorse non conformi

Per visualizzare le risorse non conformi in base a questa nuova assegnazione, ottenere l'ID dell'assegnazione di criteri eseguendo questi comandi:

```azurepowershell-interactive
$policyAssignment = Get-AzPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs without managed disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

Per altre informazioni sugli ID delle assegnazioni dei criteri, vedere [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment).

Eseguire quindi questo comando per ottenere gli ID risorsa delle risorse non conformi restituite in un file JSON:

```console
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

```azurecli-interactive
az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata assegnata una definizione dei criteri per identificare le risorse non conformi nell'ambiente Azure.

Per altre informazioni sull'assegnazione di criteri per convalidare che le nuove risorse siano conformi, continuare con l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Creazione e gestione dei criteri](./tutorials/create-and-manage.md)