---
title: Usare il modulo criteri di Azure Stack | Documenti Microsoft
description: Imparare a vincolare una sottoscrizione di Azure si comporta come una sottoscrizione di Azure Stack
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: sngun
ms.openlocfilehash: 030cb837dd2082f472275bcc42dbd39b7f8b30c0
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2017
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Gestire i criteri di Azure mediante il modulo criteri di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Il modulo criteri di Stack di Azure consente di configurare una sottoscrizione di Azure con lo stesso controllo delle versioni e la disponibilità del servizio come Stack di Azure.  Il modulo viene utilizzato il **New AzureRMPolicyAssignment** per creare un criterio di Azure, che limita i tipi di risorse e i servizi disponibili in una sottoscrizione.  Al termine dell'operazione, è possibile utilizzare la sottoscrizione di Azure per sviluppare app di destinazione per lo Stack di Azure.  

## <a name="install-the-module"></a>Installare il modulo
1. Installare la versione richiesta del modulo AzureRM PowerShell, come descritto nel passaggio 1 di [installare PowerShell per Azure Stack](azure-stack-powershell-install.md).   
2. [Scaricare gli strumenti di Azure Stack da GitHub](azure-stack-powershell-download.md)  
3. [Configurare PowerShell per l'uso con Azure Stack](azure-stack-powershell-configure-user.md)

4. Importare il modulo AzureStack.Policy.psm1:

   ```PowerShell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-subscription"></a>Applicare i criteri di sottoscrizione
Il comando seguente può essere utilizzato per applicare un criterio di Azure Stack predefinito sulla sottoscrizione Azure. Prima di eseguire, sostituire *nome sottoscrizione di Azure* con la sottoscrizione di Azure.

```PowerShell
Login-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
$rgName = 'AzureStack'
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>Applicare i criteri a un gruppo di risorse
È consigliabile applicare i criteri in un metodo più granulare.  Ad esempio, si potrebbe disporre di altre risorse in esecuzione nella stessa sottoscrizione.  È possibile definire l'ambito di applicazione dei criteri a un gruppo di risorse specifico, che consente di testare le App per lo Stack di Azure utilizzando le risorse di Azure. Prima di eseguire, sostituire *nome sottoscrizione di Azure* con il nome della sottoscrizione di Azure.

```PowerShell
Login-AzureRmAccount
$resourceGroupName = ‘myRG01’
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>Criteri di azione
Dopo aver distribuito i criteri di Azure, viene visualizzato un errore quando si tenta di distribuire una risorsa che non consentita dai criteri.  

![Risultato di errori di distribuzione di risorse a causa di vincoli di criteri](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Passaggi successivi
[Distribuire modelli con PowerShell](azure-stack-deploy-template-powershell.md)

[Distribuire modelli con l'interfaccia della riga di comando di Azure](azure-stack-deploy-template-command-line.md)

[Distribuire i modelli di Visual Studio](azure-stack-deploy-template-visual-studio.md)
