---
title: Usare PowerShell per creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure | Microsoft Docs
description: Usare PowerShell per creare un'assegnazione di criteri di Azure per identificare le risorse non conformi nell'ambiente.
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/02/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 5c00d50817e40de0a43d05eb85662b494247d8fa
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-using-powershell"></a>Creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure usando PowerShell

Il primo passaggio per ottenere informazioni sulla conformità in Azure è sapere qual è lo stato delle risorse correnti. Questa guida introduttiva illustra il processo di creazione di un'assegnazione criteri per identificare le macchine virtuali che non usano dischi gestiti.

Alla fine di questo processo saranno state identificate correttamente le macchine virtuali che non usano dischi gestiti e pertanto *non conformi*.


PowerShell viene usato per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida descrive l'uso di PowerShell per creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure.

Questa guida richiede il modulo Azure PowerShell 4.0 o versioni successive. Eseguire ```Get-Module -ListAvailable AzureRM``` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps).

Prima di iniziare verificare che sia installata la versione più recente di PowerShell. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="opt-in-to-azure-policy"></a>Consenso esplicito per Criteri di Azure

Criteri di Azure è attualmente disponibile in anteprima pubblica ed è necessario registrarsi per richiedere l'accesso.

1. Passare a Criteri di Azure all'indirizzo https://aka.ms/getpolicy e selezionare **Iscrizione** nel riquadro a sinistra.

   ![Ricerca di criteri](media/assign-policy-definition/sign-up.png)

2. Acconsentire esplicitamente all'uso di Criteri di Azure selezionando le sottoscrizioni che si vuole usare nell'elenco **Sottoscrizione**. Selezionare quindi **Register** (Registra).

   ![Consenso esplicito per l'uso di Criteri di Azure](media/assign-policy-definition/preview-opt-in.png)

   La richiesta viene approvata automaticamente per l'anteprima. Attendere fino a 30 minuti che il sistema elabori la registrazione.

## <a name="create-a-policy-assignment"></a>Creare un'assegnazione di criteri

In questa guida introduttiva si crea un'assegnazione di criteri e si assegna la definizione *Audit Virtual Machines without Managed Disks* (Controllare le macchine virtuali senza i dischi gestiti). Questa definizione di criteri identificherà le risorse che non rispettano le condizioni di definizione dei criteri.

Per creare una nuova assegnazione di criteri, attenersi alla procedura seguente.

Eseguire il comando seguente per visualizzare tutte le definizioni dei criteri e individuare quella che si desidera assegnare:

```powershell
$definition = Get-AzureRmPolicyDefinition
```

Criteri di Azure include definizioni di criteri predefinite che è possibile usare. Saranno disponibili definizioni di criteri predefinite come le seguenti:

- Imporre un tag e il relativo valore
- Applicare un tag e il relativo valore
- Require SQL Server Version 12.0 (Richiedere SQL Server versione 12.0)

Successivamente, assegnare la definizione dei criteri nell'ambito desiderato mediante il cmdlet `New-AzureRmPolicyAssignment`.

Per questa esercitazione, forniamo le informazioni seguenti per il formato:
- **Name** (Nome): nome visualizzato per l'assegnazione dei criteri. In questo caso, si usa Audit Virtual Machines without Managed Disks (Controllare le macchine virtuali senza i dischi gestiti).
- **Policy** (Criterio): si tratta della definizione del criterio, in base a quello che si sta usando per creare l'assegnazione. In questo caso, è la definizione dei criteri: *Audit Virtual Machines without Managed Disks* (Controllare le macchine virtuali senza i dischi gestiti)
- Uno **Scope** (Ambito): determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione dei criteri e può variare da una sottoscrizione a gruppi di risorse. In questo esempio si assegna la definizione dei criteri al gruppo di risorse **FabrikamOMS**.
- **$definition**: è necessario fornire l'ID risorsa della definizione dei criteri. In questo caso si usa l'ID per la definizione dei criteri: *Audit Virtual Machines without Managed Disks* (Controllare le macchine virtuali senza i dischi gestiti).

```powershell
$rg = Get-AzureRmResourceGroup -Name "FabrikamOMS"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

A questo punto si è pronti per identificare le risorse non conformi per comprendere lo stato di conformità dell'ambiente.

## <a name="identify-non-compliant-resources"></a>Identificare le risorse non conformi

1. Ritornare alla pagina di destinazione Criteri di Azure.
2. Selezionare **Compliance** (Conformità) nel riquadro sinistro e cercare l'**Assegnazione criteri** creata.

   ![Conformità ai criteri](media/assign-policy-definition/policy-compliance.png)

   Le eventuali risorse esistenti non conformi con questa nuova assegnazione verranno visualizzate nella scheda **Non-compliant resources** (Risorse non conformi).

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
