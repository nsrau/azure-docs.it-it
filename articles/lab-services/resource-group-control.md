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
ms.date: 02/05/2019
ms.author: spelluru
ms.openlocfilehash: ddda9ef2b9bb716f7cdd33aa8fe9233f6c7d8e82
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749001"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Specificare un gruppo di risorse per le macchine virtuali di un lab in Azure DevTest Labs
I proprietari dei lab possono configurare le macchine virtuali del lab da creare in uno specifico gruppo di risorse. Usare questa funzionalità per non raggiungere i limiti del gruppo di risorse della sottoscrizione di Azure. Questa funzionalità consente anche di consolidare tutte le risorse del lab in un singolo gruppo di risorse. Semplifica anche la verifica di tali risorse e l'applicazione dei [criteri](../governance/policy/overview.md) necessari per gestirle a livello di gruppo di risorse.

Con questa funzionalità, è possibile usare uno script per specificare un gruppo di risorse nuovo o esistente nella sottoscrizione di Azure per tutte le macchine virtuali del lab. DevTest Labs supporta attualmente questa funzionalità tramite un'API. 

## <a name="api-to-configure-a-resource-group-for-labs-vms"></a>API per configurare un gruppo di risorse per le macchine virtuali dei lab
Verranno ora illustrate le opzioni disponibili per i proprietari dei lab quando usano questa API: 

- È possibile scegliere il **gruppo di risorse del lab** per tutte le macchine virtuali.
- È possibile scegliere un **gruppo di risorse esistente** diverso da quello del lab per tutte le macchine virtuali.
- È possibile immettere un nome del **nuovo gruppo di risorse** per tutte le macchine virtuali.
- È possibile continuare con il comportamento esistente, che prevede la creazione di un gruppo di risorse per ogni macchina virtuale del lab.
 
Questa impostazione si applica alle nuove macchine virtuali create nel lab. Le macchine virtuali meno recenti create nei propri gruppi di risorse non subiranno modifiche. È tuttavia possibile eseguire la migrazione di queste macchine virtuali dai singoli gruppi di risorse al gruppo di risorse comune in modo che tutte le macchine virtuali del lab siano in un gruppo di risorse comune. Per altre informazioni, vedere [Spostare le risorse in un nuovo gruppo di risorse](../azure-resource-manager/resource-group-move-resources.md). Gli ambienti creati nel lab rimarranno nei rispettivi gruppi di risorse.

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
                "uniqueIdentifier": "6e6f668f-992b-435c-bac3-d328b745cd25"
            },
            "dependsOn": []
        },
```


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- [Impostare i criteri per un lab](devtest-lab-get-started-with-lab-policies.md)
- [Domande frequenti](devtest-lab-faq.md)
