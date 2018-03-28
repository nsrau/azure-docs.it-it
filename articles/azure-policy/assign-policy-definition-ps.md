---
title: Guida introduttiva - Usare PowerShell per creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure | Microsoft Docs
description: In questa guida introduttiva si userà PowerShell per creare un'assegnazione di criteri di Azure per identificare le risorse non conformi.
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 3/14/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 9f7d32d3d1208b6fe6075f7dacdd6d350aee03e2
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-the-azure-rm-powershell-module"></a>Guida introduttiva: Creare un'assegnazione di criteri per identificare le risorse non conformi con il modulo AzureRM di PowerShell

Il primo passaggio per comprendere la conformità in Azure consiste nell'identificare lo stato delle risorse. In questa guida introduttiva si creerà un'assegnazione di criteri per identificare le macchine virtuali che non usano dischi gestiti. Al termine, si identificheranno le macchina virtuali *non conformi* all'assegnazione di criteri.

Il modulo AzureRM di PowerShell viene usato per creare e gestire le risorse di Azure dalla riga di comando o in script. Questa guida illustra come usare AzureRM per creare un'assegnazione di criteri. I criteri identificano le risorse non conformi nell'ambiente Azure.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

- Prima di iniziare verificare che sia installata la versione più recente di PowerShell. Per informazioni dettagliate, vedere [Panoramica di Azure PowerShell](/powershell/azureps-cmdlets-docs).
- Aggiornare il modulo AzureRM di PowerShell all'ultima versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-policy-assignment"></a>Creare un'assegnazione di criteri

In questa guida introduttiva si crea un'assegnazione di criteri e si assegna la definizione *Audit Virtual Machines without Managed Disks* (Controllare le macchine virtuali senza i dischi gestiti). Questa definizione di criteri identifica le risorse che non rispettano le condizioni in essa impostate.

Eseguire questi comandi per creare un nuova assegnazione di criteri:

```powershell
$rg = Get-AzureRmResourceGroup -Name "<resourceGroupName>"

$definition = Get-AzureRmPolicyDefinition -Name "Audit Virtual Machines without Managed Disks"

New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition -Sku @{Name='A1';Tier='Standard'}

```

I comandi precedenti utilizzano le informazioni seguenti:

- **Name**: nome visualizzato per l'assegnazione di criteri. In questo caso si usa *Audit Virtual Machines without Managed Disks Assignment*.
- **Definition**: definizione di criteri in base alla quale si crea l'assegnazione. In questo caso si tratta della definizione di criteri *Audit Virtual Machines without Managed Disks*.
- **Scope**: ambito che determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di criteri. Può variare da una sottoscrizione a gruppi di risorse. Assicurarsi di sostituire &lt;scope&gt; con il nome del gruppo di risorse.
- **Sku**: questo comando crea un'assegnazione di criteri con il livello Standard. Il livello Standard offre funzionalità di gestione, valutazione della conformità e correzione su vasta scala. Attualmente è gratuito. In futuro comporterà l'addebito di costi. Quando verrà applicata la modifica dei prezzi, il relativo annuncio e altri dettagli saranno disponibili nella pagina [Prezzi di Criteri di Azure](https://azure.microsoft.com/pricing/details/azure-policy).


A questo punto si è pronti per identificare le risorse non conformi per comprendere lo stato di conformità dell'ambiente.

## <a name="identify-non-compliant-resources"></a>Identificare le risorse non conformi

Usare le informazioni seguenti per identificare le risorse non conformi all'assegnazione di criteri creata. Eseguire i comandi seguenti:

```powershell
$policyAssignment = Get-AzureRmPolicyAssignment | where {$_.properties.displayName -eq "Audit Virtual Machines without Managed Disks"}
```

```powershell
$policyAssignment.PolicyAssignmentId
```

Per altre informazioni sugli ID delle assegnazioni di criteri, vedere [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

Eseguire quindi questo comando per ottenere gli ID risorsa delle risorse non conformi restituite in un file JSON:

```powershell
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

Le guide successive di questa raccolta si basano su questa guida introduttiva. Se si prevede di continuare a usare le altre esercitazioni, non eseguire la pulizia delle risorse create in questa guida introduttiva. Se non si intende continuare, è possibile eliminare l'assegnazione creata eseguendo questo comando:

```powershell
Remove-AzureRmPolicyAssignment -Name "Audit Virtual Machines without Managed Disks Assignment" -Scope /subscriptions/<subscriptionID>/<resourceGroupName>
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata assegnata una definizione dei criteri per identificare le risorse non conformi nell'ambiente Azure.

Per altre informazioni sull'assegnazione di criteri e per assicurarsi che le risorse create **in futuro** siano conformi, passare all'esercitazione seguente:

> [!div class="nextstepaction"]
> [Creazione e gestione dei criteri](./create-manage-policy.md)
