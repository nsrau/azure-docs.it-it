---
title: Specificare il gruppo di risorse per le macchine virtuali in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come specificare un gruppo di risorse per macchine virtuali in un lab in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 7b72048405d3025ca21b324b6ad3168dd0c9ac95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85483364"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Specificare un gruppo di risorse per le macchine virtuali di un lab in Azure DevTest Labs

I proprietari dei lab possono configurare le macchine virtuali del lab da creare in uno specifico gruppo di risorse. Questa funzionalità è utile negli scenari seguenti:

- Per fare in modo che i lab creino meno gruppi di risorse nella sottoscrizione.
- Far funzionare i Lab in un set fisso di gruppi di risorse configurati.
- Per aggirare le restrizioni e le approvazioni richieste per creare gruppi di risorse nella sottoscrizione di Azure.
- Consolidare tutte le risorse Lab all'interno di un singolo gruppo di risorse per semplificare il rilevamento delle risorse e l'applicazione dei [criteri](../governance/policy/overview.md) per gestire le risorse a livello di gruppo di risorse.

Con questa funzionalità è possibile usare uno script per specificare un gruppo di risorse nuovo o esistente nella sottoscrizione di Azure per tutte le macchine virtuali del Lab. Attualmente, Azure DevTest Labs supporta questa funzionalità tramite un'API.

> [!NOTE]
> Tutti i limiti della sottoscrizione si applicano quando si creano Lab in DevTest Labs. Si pensi a un Lab come a qualsiasi altra risorsa nella sottoscrizione. Nel caso di gruppi di risorse, il limite è [980 gruppi di risorse per sottoscrizione](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). 

## <a name="use-azure-portal"></a>Usare il portale di Azure
Seguire questa procedura per specificare un gruppo di risorse per tutte le macchine virtuali create nel Lab. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **tutti i servizi** nel menu di spostamento a sinistra. 
3. Selezionare **DevTest Labs** dall'elenco.
4. Dall'elenco dei Lab selezionare il **Lab**.  
5. Selezionare **configurazione e criteri** nella sezione **Impostazioni** nel menu a sinistra. 
6. Selezionare **impostazioni Lab** nel menu a sinistra. 
7. Selezionare **tutte le macchine virtuali in un gruppo di risorse**. 
8. Selezionare un gruppo di risorse esistente nell'elenco a discesa oppure selezionare **Crea nuovo**, immettere un **nome** per il gruppo di risorse e fare clic su **OK**. 

    ![Selezionare il gruppo di risorse per tutte le macchine virtuali del Lab](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Usare PowerShell 
L'esempio seguente illustra come usare uno script di PowerShell per creare tutte le macchine virtuali del Lab in un nuovo gruppo di risorse.

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
Se si usa un modello di Azure Resource Manager per creare un Lab, usare la proprietà **vmCreationResourceGroupId** nella sezione Lab Properties (proprietà Lab) del modello, come illustrato nell'esempio seguente:

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


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API per configurare un gruppo di risorse per le macchine virtuali del Lab
Quando si usa questa API sono disponibili le opzioni seguenti come proprietario del Lab:

- Scegliere il **gruppo di risorse del Lab** per tutte le macchine virtuali.
- Scegliere un **gruppo di risorse esistente** diverso dal gruppo di risorse del Lab per tutte le macchine virtuali.
- Immettere un **nuovo** nome per il gruppo di risorse per tutte le macchine virtuali.
- Continuare a usare il comportamento esistente, in cui viene creato un gruppo di risorse per ogni macchina virtuale nel Lab.
 
Questa impostazione si applica alle nuove macchine virtuali create nel lab. Le macchine virtuali meno recenti nel Lab create nei propri gruppi di risorse rimangono inalterate. Gli ambienti creati nel Lab continuano a rimanere nei propri gruppi di risorse.

Come usare questa API:
- Usare la versione dell'API **2018_10_15_preview**.
- Se si specifica un nuovo gruppo di risorse, assicurarsi di disporre **delle autorizzazioni di scrittura per i gruppi di risorse** nella sottoscrizione. Se non si dispone delle autorizzazioni di scrittura, la creazione di nuove macchine virtuali nel gruppo di risorse specificato avrà esito negativo.
- Mentre si usa l'API, passare l'**ID completo del gruppo di risorse**. Ad esempio: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Verificare che il gruppo di risorse si trovi nella stessa sottoscrizione del Lab. 


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- [Impostare i criteri per un lab](devtest-lab-set-lab-policy.md)
- [Domande frequenti](devtest-lab-faq.md)
