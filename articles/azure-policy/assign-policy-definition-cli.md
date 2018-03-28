---
title: Usare l'interfaccia della riga di comando di Azure per creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure | Microsoft Docs
description: Usare PowerShell per creare un'assegnazione di criteri di Azure per identificare le risorse non conformi nell'ambiente.
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: f56f00aabbef2cfa86264d3e962af9a9c0bafa98
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure usando l'interfaccia della riga di comando di Azure

Il primo passaggio per comprendere la conformità in Azure consiste nell'identificare lo stato delle risorse. Questa guida introduttiva illustra il processo di creazione di un'assegnazione di criteri per identificare le macchine virtuali che non usano dischi gestiti.

Alla fine di questo processo, verranno identificate le macchine virtuali che non usano dischi gestiti e che sono quindi *non conformi* all'assegnazione di criteri.

L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida usa l'interfaccia della riga di comando di Azure per creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).



## <a name="create-a-policy-assignment"></a>Creare un'assegnazione di criteri

In questa guida introduttiva si crea un'assegnazione di criteri e si assegna la definizione Audit Virtual Machines without Managed Disks. Questa definizione di criteri identifica le risorse che non rispettano le condizioni in essa impostate.

Eseguire questo comando per creare un'assegnazione di criteri:

```
az policy assignment create --name 'Audit Virtual Machines without Managed Disks Assignment' --scope '<scope>' --policy '<policy definition ID>' --sku 'standard'
```

Il comando precedente usa le informazioni seguenti.

- **Name**: nome visualizzato per l'assegnazione di criteri. In questo caso si usa *Audit Virtual Machines without Managed Disks Assignment*.
- **Policy**: ID della definizione di criteri in base alla quale si crea l'assegnazione. In questo caso si tratta della definizione di criteri *Audit Virtual Machines without Managed Disks*. Per ottenere l'ID della definizione di criteri, eseguire questo comando: `az policy definition show --name 'Audit Virtual Machines without Managed Disks Assignment'`
- **Scope**: ambito che determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di criteri. Può variare da una sottoscrizione a gruppi di risorse. Assicurarsi di sostituire &lt;scope&gt; con il nome del gruppo di risorse.
- **Sku**: questo comando crea un'assegnazione di criteri con il livello Standard. Il livello Standard offre funzionalità di gestione, valutazione della conformità e correzione su vasta scala. Attualmente è gratuito. In futuro comporterà l'addebito di costi. Quando verrà applicata la modifica dei prezzi, il relativo annuncio e altri dettagli saranno disponibili nella pagina [Prezzi di Criteri di Azure](https://azure.microsoft.com/pricing/details/azure-policy).


## <a name="identify-non-compliant-resources"></a>Identificare le risorse non conformi

Per visualizzare le risorse non conformi in base a questa nuova assegnazione, ottenere l'ID dell'assegnazione di criteri eseguendo questi comandi:

```
$policyAssignment = Get-AzureRmPolicyAssignment | where {$_.properties.displayName -eq "Audit Virtual Machines without Managed Disks"}
```

```
$policyAssignment.PolicyAssignmentId
```

Per altre informazioni sugli ID delle assegnazioni di criteri, vedere [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

Eseguire quindi questo comando per ottenere gli ID risorsa delle risorse non conformi restituite in un file JSON:

```
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

I risultati saranno simili all'esempio seguente:

```
{
"@odata.context":"https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
"@odata.count": 3,
"value": [
{
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

Altre guide in questa raccolta si basano su questa guida introduttiva. Se si prevede di continuare a usare le esercitazioni successive, non eseguire la pulizia delle risorse create in questa guida introduttiva. Se non si intende continuare, eliminare l'assegnazione creata eseguendo questo comando:

```azurecli
az policy assignment delete –name Audit Virtual Machines without Managed Disks Assignment --scope /subscriptions/ <subscriptionID> / <resourceGroupName>
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata assegnata una definizione dei criteri per identificare le risorse non conformi nell'ambiente Azure.

Per altre informazioni sull'assegnazione di criteri e per assicurarsi che le risorse create **in futuro** siano conformi, passare all'esercitazione seguente:

> [!div class="nextstepaction"]
> [Creazione e gestione dei criteri](./create-manage-policy.md)
