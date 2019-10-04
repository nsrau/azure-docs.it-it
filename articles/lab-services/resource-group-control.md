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
ms.openlocfilehash: 574cc0c41ce645c71302178afcf6e7deaec69d8e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476091"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Specificare un gruppo di risorse per le macchine virtuali di un lab in Azure DevTest Labs

I proprietari dei lab possono configurare le macchine virtuali del lab da creare in uno specifico gruppo di risorse. Questa funzionalità è utile negli scenari seguenti:

- Per fare in modo che i lab creino meno gruppi di risorse nella sottoscrizione.
- Hanno i propri lab operare all'interno di un set fisso di gruppi di risorse configurati.
- Per aggirare le restrizioni e le approvazioni richieste per creare gruppi di risorse nella sottoscrizione di Azure.
- Consolidare tutte le risorse di lab all'interno di un singolo gruppo di risorse per semplificare il rilevamento di tali risorse e applicando [criteri](../governance/policy/overview.md) per gestire le risorse a livello di gruppo di risorse.

Con questa funzionalità, è possibile usare uno script per specificare un gruppo di risorse nuovo o esistente nella sottoscrizione di Azure per tutte le macchine virtuali nel lab. Azure DevTest Labs supporta attualmente questa funzionalità tramite un'API.

> [!NOTE]
> Tutti i limiti della sottoscrizione si applicano quando si crea lab in DevTest Labs. Pensare a un lab come qualsiasi altra risorsa nella sottoscrizione. In caso di gruppi di risorse, è il limite [980 gruppi di risorse per ogni sottoscrizione](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager). 

## <a name="use-azure-portal"></a>Usare il portale di Azure
Seguire questi passaggi per specificare un gruppo di risorse per tutte le macchine virtuali create nel lab. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **tutti i servizi** dal menu di spostamento a sinistra. 
3. Selezionare **DevTest Labs** dall'elenco.
4. Nell'elenco di Lab, selezionare i **lab**.  
5. Selezionare **configurazione e criteri** nel **impostazioni** sezione nel menu a sinistra. 
6. Selezionare **delle impostazioni di Lab** nel menu a sinistra. 
7. Selezionare **tutte le macchine virtuali in un gruppo di risorse**. 
8. Selezionare un gruppo di risorse esistente nell'elenco a discesa elenco (o) select **Crea nuovo**, immettere una **name** per il gruppo di risorse e selezionare **OK**. 

    ![Selezionare il gruppo di risorse per tutte le macchine virtuali di lab](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Usare PowerShell 
Nell'esempio seguente viene illustrato come usare uno script di PowerShell per creare tutte le macchine virtuali di lab in un nuovo gruppo di risorse.

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

Richiamare lo script usando il comando seguente. ResourceGroup.ps1 è il file che contiene lo script precedente:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Usare un modello di Azure Resource Manager
Se si usa un modello Azure Resource Manager per creare un lab, usare il **vmCreationResourceGroupId** proprietà nella sezione proprietà lab del modello, come illustrato nell'esempio seguente:

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


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API per configurare un gruppo di risorse per macchine virtuali del lab
Sono disponibili le opzioni seguenti come proprietario del lab, quando si utilizza questa API:

- Scegliere il **gruppo di risorse del lab** per tutte le macchine virtuali.
- Scegliere un' **gruppo di risorse esistente** diverso dal gruppo di risorse del lab per tutte le macchine virtuali.
- Immettere un **nuovo gruppo di risorse** nome per tutte le macchine virtuali.
- Continuare a usare il comportamento esistente, in cui viene creato un gruppo di risorse per ogni macchina virtuale nel lab.
 
Questa impostazione si applica alle nuove macchine virtuali create nel lab. Le macchine virtuali meno recenti nell'ambiente lab che sono state create nei propri gruppi di risorse restano invariate. Gli ambienti creati nell'ambiente lab continuano a restare nei propri gruppi di risorse.

Come usare questa API:
- Usare API versione **2018_10_15_preview**.
- Se si specifica un nuovo gruppo di risorse, assicurarsi di aver **le autorizzazioni di scrittura sui gruppi di risorse** nella sottoscrizione. Se non si dispongono delle autorizzazioni di scrittura, creazione di nuove macchine virtuali nel gruppo di risorse specificato non riuscirà.
- Mentre si usa l'API, passare l'**ID completo del gruppo di risorse**. Ad esempio: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Assicurarsi che il gruppo di risorse sia nella stessa sottoscrizione del lab. 


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- [Impostare i criteri per un lab](devtest-lab-get-started-with-lab-policies.md)
- [Domande frequenti](devtest-lab-faq.md)
