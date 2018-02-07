---
title: Usare PowerShell per creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure | Microsoft Docs
description: Usare PowerShell per creare un'assegnazione di criteri di Azure per identificare le risorse non conformi nell'ambiente.
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 1/17/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 67c779b96dab088d810d22ad3053ade106aec56a
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-using-powershell"></a>Creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure usando PowerShell

Il primo passaggio per comprendere la conformità in Azure consiste nell'identificare lo stato delle risorse. Questa guida introduttiva illustra il processo di creazione di un'assegnazione criteri per identificare le macchine virtuali che non usano dischi gestiti.

Alla fine di questo processo, sarà possibile identificare le macchine virtuali che non usano Managed Disks. Tali macchine sono *non conformi* all'assegnazione dei criteri.

PowerShell viene usato per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida descrive l'uso di PowerShell per creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure.

Questa guida richiede il modulo Azure PowerShell 4.0 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps).

Prima di iniziare verificare che sia installata la versione più recente di PowerShell. Per informazioni dettagliate, vedere [Panoramica di Azure PowerShell](/powershell/azureps-cmdlets-docs).

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.


## <a name="create-a-policy-assignment"></a>Creare un'assegnazione di criteri

In questa guida introduttiva si crea un'assegnazione di criteri e si assegna la definizione *Audit Virtual Machines without Managed Disks* (Controllare le macchine virtuali senza i dischi gestiti). Questa definizione di criteri identifica le risorse che non rispettano le condizioni impostate nella definizione dei criteri.

Per creare una nuova assegnazione di criteri, attenersi alla procedura seguente.

1. Per garantire che la sottoscrizione funzioni con il provider di risorse, registrare il provider di risorse PolicyInsights. Per registrare un provider di risorse, è necessaria l'autorizzazione per eseguire l'operazione /register/action per il provider di risorse. Questa operazione è inclusa nei ruoli Collaboratore e Proprietario.

    Eseguire il comando seguente per registrare il provider di risorse:

    ```
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.PolicyInsights
```

    Non è possibile annullare la registrazione di un provider di risorse se nella sottoscrizione sono presenti tipi di risorsa di tale provider di risorse.

    Per maggiori dettagli sulla registrazione e la visualizzazione dei provider di risorse, vedere [Provider e tipi di risorse](../azure-resource-manager/resource-manager-supported-services.md).

2. Dopo aver registrato il provider di risorse, eseguire il comando seguente per visualizzare tutte le definizioni dei criteri e individuare quella che si vuole assegnare:

    ```powershell
$definition = Get-AzureRmPolicyDefinition
```

    Criteri di Azure include definizioni di criteri predefinite che è possibile usare. Saranno disponibili definizioni di criteri predefinite come le seguenti:

    - Imporre un tag e il relativo valore
    - Applicare un tag e il relativo valore
    - Richiedere SQL Server versione 12.0

3. Successivamente, assegnare la definizione dei criteri nell'ambito desiderato mediante il cmdlet `New-AzureRmPolicyAssignment`.

Per questa esercitazione usare le informazioni seguenti per il comando:

- **Name** (Nome): nome visualizzato per l'assegnazione dei criteri. In questo caso, usare Audit Virtual Machines without Managed Disks (Controllare le macchine virtuali senza i dischi gestiti).
- **Policy** (Criterio): definizione del criterio, in base a quello che si sta usando per creare l'assegnazione. In questo caso, è la definizione dei criteri: *Audit Virtual Machines without Managed Disks* (Controllare le macchine virtuali senza i dischi gestiti)
- Uno **Scope** (Ambito): determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione dei criteri e può variare da una sottoscrizione a gruppi di risorse. In questo esempio si sta assegnando la definizione dei criteri al gruppo di risorse **FabrikamOMS**.
- **$definition**: è necessario fornire l'ID risorsa della definizione dei criteri. In questo caso si usa l'ID per la definizione dei criteri, ovvero *Audit Virtual Machines without Managed Disks* (Controllare le macchine virtuali senza i dischi gestiti).

```powershell
$rg = Get-AzureRmResourceGroup -Name "FabrikamOMS"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

A questo punto si è pronti per identificare le risorse non conformi per comprendere lo stato di conformità dell'ambiente.

## <a name="identify-non-compliant-resources"></a>Identificare le risorse non conformi

1. Ritornare alla pagina di destinazione Criteri di Azure.
2. Selezionare **Conformità** nel riquadro sinistro e cercare l'**assegnazione criteri** creata.

   ![Conformità ai criteri](media/assign-policy-definition/policy-compliance.png)

   Le eventuali risorse esistenti non conformi a questa nuova assegnazione verranno visualizzate nella scheda **Risorse non conformi**.

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide in questa raccolta si basano su questa guida introduttiva. Se si prevede di continuare a usare le esercitazioni successive, non eliminare le risorse create in questa guida introduttiva. Se non si prevede di continuare, eliminare l'assegnazione creata eseguendo questo comando:

```powershell
Remove-AzureRmPolicyAssignment -Name “Audit Virtual Machines without Managed Disks Assignment” -Scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata assegnata una definizione dei criteri per identificare le risorse non conformi nell'ambiente Azure.

Per altre informazioni sull'assegnazione di criteri per assicurarsi che le risorse **future** create siano conformi, continuare con l'esercitazione per:

> [!div class="nextstepaction"]
> [Creazione e gestione dei criteri](./create-manage-policy.md)
