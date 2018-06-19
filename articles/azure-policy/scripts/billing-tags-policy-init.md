---
title: 'Esempio JSON di Criteri di Azure: iniziativa relativa ai criteri dei tag di fatturazione | Microsoft Docs'
description: Questo set di criteri di esempio JSON richiede valori di tag specifici per il centro di costo e il nome del prodotto.
services: azure-policy
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-policy
ms.devlang: ''
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 10/30/2017
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: eebf3e4908f9eb484f9dff9e71d3c2919c523574
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601922"
---
# <a name="billing-tags-policy-initiative"></a>Iniziativa relativa ai criteri dei tag di fatturazione

Questo set di criteri richiede valori di tag specifici per il centro di costo e il nome del prodotto. Usa i criteri predefiniti per applicare e imporre i tag obbligatori. Si specificano i valori obbligatori per i tag.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Modello di esempio

[!code-json[main](../../../policy-templates/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.json "Billing Tags Policy Initiative")]

È possibile distribuire questo modello con il [portale di Azure](#deploy-with-the-portal) o con [PowerShell](#deploy-with-powershell).

## <a name="deploy-with-the-portal"></a>Eseguire la distribuzione con il portale

[![Distribuzione in Azure](http://azuredeploy.net/deploybutton.png)](https://aka.ms/getpolicy)

## <a name="deploy-with-powershell"></a>Distribuire con PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$policydefinitions = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.definitions.json"
$policysetparameters = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.parameters.json"

$policyset= New-AzureRmPolicySetDefinition -Name "multiple-billing-tags" -DisplayName "Billing Tags Policy Initiative" -Description "Specify cost Center tag and product name tag" -PolicyDefinition $policydefinitions -Parameter $policysetparameters

New-AzureRmPolicyAssignment -PolicySetDefinition $policyset -Name <assignmentname> -Scope <scope>  -costCenterValue <required value for Cost Center tag> -productNameValue <required value for product Name tag>
```

### <a name="clean-up-powershell-deployment"></a>Eliminare la distribuzione di PowerShell

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="apply-tags-to-existing-resources"></a>Applicare tag alle risorse esistenti

Dopo aver assegnato i criteri, è possibile attivare l'aggiornamento di tutte le risorse esistenti per applicare i criteri dei tag aggiunti. Lo script seguente consente di mantenere gli altri tag presenti nelle risorse:

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup"
$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($r.Tags -eq $NULL) { @{}} else {$r.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere altri esempi in [Esempi di Criteri di Azure](../json-samples.md).