---
title: Usare il modulo criteri di Azure Stack | Microsoft Docs
description: Informazioni su come limitare una sottoscrizione di Azure si comporti come una sottoscrizione di Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2019
ms.author: sethm
ms.lastreviewed: 03/26/2019
ms.openlocfilehash: 9fade97bbe783cf156f5b73523bc0834a34df926
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487448"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Gestire i criteri di Azure usando il modulo criteri di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Il modulo criteri di Azure Stack consente di configurare una sottoscrizione di Azure con lo stesso controllo delle versioni e disponibilità dei servizi come Azure Stack. Il modulo Usa la [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) cmdlet di PowerShell per creare criteri di Azure, che limita i tipi di risorse e servizi disponibili in una sottoscrizione. È quindi creare un'assegnazione di criteri all'interno dell'ambito appropriato usando il [New-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) cmdlet. Dopo aver configurato i criteri, è possibile usare la sottoscrizione di Azure per sviluppare app di destinazione per Azure Stack.

## <a name="install-the-module"></a>Installare il modulo

1. Installare la versione richiesta del modulo AzureRM di PowerShell, come descritto nel passaggio 1 del [installazione di PowerShell per Azure Stack](azure-stack-powershell-install.md).
2. [Scaricare gli strumenti di Azure Stack da GitHub](azure-stack-powershell-download.md).
3. [Configurare PowerShell per l'uso con Azure Stack](azure-stack-powershell-configure-user.md).
4. Importare il modulo AzureStack.Policy.psm1:


   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Applicare i criteri alla sottoscrizione di Azure

È possibile usare il comando seguente per applicare un criterio di Azure Stack predefinita nella sottoscrizione di Azure. Prima di eseguire questo comando, sostituire `Azure subscription name` con il nome della sottoscrizione di Azure:

```powershell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

## <a name="apply-policy-to-a-resource-group"></a>Applicare i criteri a un gruppo di risorse

È possibile applicare i criteri più granulari. Ad esempio, potrebbe essere altre risorse in esecuzione nella stessa sottoscrizione. È possibile definire l'ambito l'applicazione di criteri a un gruppo di risorse specifico, in modo da poter testare le App per Azure Stack tramite le risorse di Azure. Prima di eseguire il comando seguente, sostituire `Azure subscription name` con il nome della sottoscrizione di Azure:

```powershell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>Criteri in azione

Dopo aver distribuito i criteri di Azure, viene visualizzato un errore quando si prova a distribuire una risorsa che non è consentita da criteri:

![Risultato di errori di distribuzione di risorse a causa di vincoli di criteri](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire modelli con PowerShell](azure-stack-deploy-template-powershell.md)
* [Distribuire modelli con l'interfaccia della riga di comando di Azure](azure-stack-deploy-template-command-line.md)
* [Distribuire modelli con Visual Studio](azure-stack-deploy-template-visual-studio.md)
