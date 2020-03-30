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
ms.date: 06/03/2019
ms.author: spelluru
ms.openlocfilehash: 29816d158cf1428727b7ff17bcc2c347f402dedf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77134522"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Specificare un gruppo di risorse per le macchine virtuali di un lab in Azure DevTest Labs

I proprietari dei lab possono configurare le macchine virtuali del lab da creare in uno specifico gruppo di risorse. Questa funzionalità è utile negli scenari seguenti:

- Per fare in modo che i lab creino meno gruppi di risorse nella sottoscrizione.
- Fare in modo che i lab operino all'interno di un set fisso di gruppi di risorse configurati.
- Per aggirare le restrizioni e le approvazioni richieste per creare gruppi di risorse nella sottoscrizione di Azure.
- Consolidare tutte le risorse lab all'interno di un singolo gruppo di risorse per semplificare il rilevamento di tali risorse e l'applicazione di [criteri](../governance/policy/overview.md) per gestire le risorse a livello di gruppo di risorse.

Con questa funzionalità è possibile usare uno script per specificare un gruppo di risorse nuovo o esistente all'interno della sottoscrizione di Azure per tutte le macchine virtuali lab. Attualmente, Azure DevTest Labs supporta questa funzionalità tramite un'API.

> [!NOTE]
> Tutti i limiti di sottoscrizione si applicano quando si creano lab in DevTest Labs.All subscription limits apply when you create labs in DevTest Labs. Considerare un lab come qualsiasi altra risorsa nella sottoscrizione. In caso di gruppi di risorse, il limite è [980 gruppi](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)di risorse per sottoscrizione. 

## <a name="use-azure-portal"></a>Usare il portale di Azure
Seguire questi passaggi per specificare un gruppo di risorse per tutte le macchine virtuali create nel lab. 

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Selezionare **Tutti i servizi** nel menu di navigazione a sinistra. 
3. Selezionare **DevTest Labs** dall'elenco.
4. Nell'elenco dei laboratori selezionare il **lab**.  
5. Selezionare **Configurazione e criteri** nella sezione **Impostazioni** del menu a sinistra. 
6. Selezionare **Impostazioni lab** nel menu a sinistra. 
7. Selezionare **Tutte le macchine virtuali in un gruppo di risorse**. 
8. Selezionare un gruppo di risorse esistente nell'elenco a discesa oppure selezionare **Crea nuovo**, immettere un **nome** per il gruppo di risorse e scegliere **OK**. 

    ![Selezionare il gruppo di risorse per tutte le macchine virtuali labSelect the resource group for all lab VMs](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Usare PowerShell 
Nell'esempio seguente viene illustrato come usare uno script di PowerShell per creare tutte le macchine virtuali lab in un nuovo gruppo di risorse.

```powershell
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

Richiamare lo script utilizzando il comando seguente. ResourceGroup.ps1 è il file che contiene lo script precedente:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Usare un modello di Azure Resource ManagerUse an Azure Resource Manager template
Se si usa un modello di Azure Resource Manager per creare un lab, usare la proprietà vmCreationResourceGroupId nella sezione delle proprietà lab del modello, come illustrato nell'esempio seguente:If you're using an Azure Resource Manager template to create a lab, use the **vmCreationResourceGroupId** property in the lab properties section of your template, as shown in the following example:

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


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API to configure a resource group for lab VMs
Quando si usa questa API, sono disponibili le opzioni seguenti come proprietario del lab:You have the following options as a lab owner when using this API:

- Scegliere il gruppo di **risorse del lab** per tutte le macchine virtuali.
- Scegliere un gruppo di **risorse esistente** diverso da quello del lab per tutte le macchine virtuali.
- Immettere un nuovo nome per il **gruppo** di risorse per tutte le macchine virtuali.
- Continuare a usare il comportamento esistente, in cui viene creato un gruppo di risorse per ogni macchina virtuale nel lab.
 
Questa impostazione si applica alle nuove macchine virtuali create nel lab. Le macchine virtuali meno recenti nel lab create nei propri gruppi di risorse rimangono inalterate. Gli ambienti creati nel lab continuano a rimanere nei propri gruppi di risorse.

Come usare questa API:
- Utilizzare la versione API **2018_10_15_preview**.
- Se si specifica un nuovo gruppo di risorse, assicurarsi di disporre delle autorizzazioni di **scrittura per** i gruppi di risorse nella sottoscrizione. Se mancano le autorizzazioni di scrittura, la creazione di nuove macchine virtuali nel gruppo di risorse specificato avrà esito negativo.
- Mentre si usa l'API, passare l'**ID completo del gruppo di risorse**. Ad esempio `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Verificare che il gruppo di risorse si trova nella stessa sottoscrizione del lab. 


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- [Impostare i criteri per un lab](devtest-lab-get-started-with-lab-policies.md)
- [Domande frequenti](devtest-lab-faq.md)
