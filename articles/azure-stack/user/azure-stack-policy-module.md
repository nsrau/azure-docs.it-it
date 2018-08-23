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
ms.date: 08/15/2018
ms.author: sethm
ms.openlocfilehash: 273b1065d51552dd7b92d4a10fc856294a23a4e7
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2018
ms.locfileid: "42139418"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Gestire i criteri di Azure usando il modulo criteri di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Il modulo criteri di Azure Stack consente di configurare una sottoscrizione di Azure con lo stesso controllo delle versioni e disponibilità dei servizi come Azure Stack.  Il modulo Usa la **New-AzureRMPolicyAssignment** cmdlet per creare un criterio di Azure, che limita i tipi di risorse e servizi disponibili in una sottoscrizione.  Dopo aver configurato i criteri, è possibile usare la sottoscrizione di Azure per sviluppare app di destinazione per Azure Stack.

## <a name="install-the-module"></a>Installare il modulo

1. Installare la versione richiesta del modulo AzureRM di PowerShell, come descritto nel passaggio 1 del [installazione di PowerShell per Azure Stack](azure-stack-powershell-install.md).
2. [Scaricare gli strumenti di Azure Stack da GitHub](azure-stack-powershell-download.md)
3. [Configurare PowerShell per l'uso con Azure Stack](azure-stack-powershell-configure-user.md)

4. Importare il modulo AzureStack.Policy.psm1:

   ```PowerShell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Applicare i criteri alla sottoscrizione di Azure

È possibile usare il comando seguente per applicare un criterio di Azure Stack predefinita nella sottoscrizione di Azure. Prima di eseguire questo comando, sostituire *nome della sottoscrizione Azure* nella sottoscrizione di Azure.

```PowerShell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>Applicare i criteri a un gruppo di risorse

È possibile applicare i criteri più granulari. Ad esempio, potrebbe essere altre risorse in esecuzione nella stessa sottoscrizione. È possibile definire l'ambito l'applicazione di criteri a un gruppo di risorse specifico, che consente di testare le App per Azure Stack tramite le risorse di Azure. Prima di eseguire il comando seguente, sostituire *nome della sottoscrizione Azure* con il nome della sottoscrizione di Azure.

```PowerShell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>Criteri in azione

Dopo aver distribuito i criteri di Azure, viene visualizzato un errore quando si prova a distribuire una risorsa che non consentita dai criteri.

![Risultato di errori di distribuzione di risorse a causa di vincoli di criteri](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire modelli con PowerShell](azure-stack-deploy-template-powershell.md)
* [Distribuire modelli con l'interfaccia della riga di comando di Azure](azure-stack-deploy-template-command-line.md)
* [Distribuire modelli con Visual Studio](azure-stack-deploy-template-visual-studio.md)
