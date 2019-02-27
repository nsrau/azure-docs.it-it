---
title: Specificare il gruppo di risorse per le macchine virtuali in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come specificare un gruppo di risorse per macchine virtuali in un lab in Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: 94e5f5b29e93409df2373cf6c56e8185dc5373a2
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312975"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Specificare un gruppo di risorse per le macchine virtuali di un lab in Azure DevTest Labs
I proprietari dei lab possono configurare le macchine virtuali del lab da creare in uno specifico gruppo di risorse. Questa funzionalità è utile negli scenari seguenti: 

- Per fare in modo che i lab creino meno gruppi di risorse nella sottoscrizione.
- Per far sì che i lab operino all'interno di un set fisso di gruppi di risorse configurato dall'utente
- Per aggirare le restrizioni e le approvazioni richieste per creare gruppi di risorse nella sottoscrizione di Azure.
- Per consolidare tutte le risorse del lab in un singolo gruppo di risorse per semplificare il rilevamento di tali risorse e l'applicazione di [criteri](../governance/policy/overview.md) per la gestione a livello di gruppo di risorse.

Con questa funzionalità, è possibile usare uno script per specificare un gruppo di risorse nuovo o esistente nella sottoscrizione di Azure per tutte le macchine virtuali del lab. DevTest Labs supporta attualmente questa funzionalità tramite un'API. 

## <a name="api-to-configure-a-resource-group-for-lab-virtual-machines"></a>API per configurare un gruppo di risorse per le macchine virtuali dei lab
Verranno ora illustrate le opzioni disponibili per i proprietari dei lab quando usano questa API: 

- È possibile scegliere il **gruppo di risorse del lab** per tutte le macchine virtuali.
- È possibile scegliere un **gruppo di risorse esistente** diverso da quello del lab per tutte le macchine virtuali.
- È possibile immettere un nome del **nuovo gruppo di risorse** per tutte le macchine virtuali.
- È possibile continuare con il comportamento esistente, che prevede la creazione di un gruppo di risorse per ogni macchina virtuale del lab.
 
Questa impostazione si applica alle nuove macchine virtuali create nel lab. Le macchine virtuali meno recenti create nei propri gruppi di risorse non subiranno modifiche. Gli ambienti creati nel lab rimarranno nei rispettivi gruppi di risorse.

### <a name="how-to-use-this-api"></a>Come usare questa API:
- Quando si usa questa API, usare la versione **2018_10_15_preview**. 
- Se si specifica un nuovo gruppo di risorse, assicurarsi di avere le **autorizzazioni di scrittura per i gruppi di risorse** nella sottoscrizione. Senza autorizzazioni di scrittura, non è possibile creare nuove macchine virtuali nel gruppo di risorse specificato. 
- Mentre si usa l'API, passare l'**ID completo del gruppo di risorse**. Ad esempio: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Assicurarsi che il gruppo di risorse sia nella stessa sottoscrizione del lab. 

## <a name="use-powershell"></a>Usare PowerShell 
L'esempio seguente descrive come creare tutte le macchine virtuali del lab in un nuovo gruppo di risorse usando uno script di PowerShell.

```PowerShell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

Richiamare lo script usando il comando seguente (ResourceGroup.ps1 è il file che contiene lo script precedente): 

```PowerShell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-azure-resource-manager-template"></a>Usare un modello di Azure Resource Manager
Se si usa il modello di Azure Resource Manager per creare un lab, usare la proprietà **vmCreationResourceGroupId** nella sezione delle proprietà del lab del modello di Resource Manager, come illustrato nell'esempio seguente:

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- [Impostare i criteri per un lab](devtest-lab-get-started-with-lab-policies.md)
- [Domande frequenti](devtest-lab-faq.md)
