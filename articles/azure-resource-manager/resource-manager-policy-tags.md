---
title: Criteri delle risorse di Azure per i tag| Documentazione Microsoft
description: Fornisce esempi di criteri delle risorse per la gestione dei tag delle risorse
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: tomfitz
ms.openlocfilehash: 469bd8d637337e5900ea84c6bfaf88064695fb7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="apply-resource-policies-for-tags"></a>Applicare criteri delle risorse per i tag

Questo argomento fornisce regole di criterio comuni che è possibile applicare per garantire l'uso coerente dei tag delle risorse.

Applicando un criterio di tag a un gruppo di risorse o a una sottoscrizione con risorse esistenti non si applica retroattivamente il criterio a tali risorse. Per applicare i criteri a queste risorse, avviare l'aggiornamento delle risorse esistenti. Questo articolo include un esempio di PowerShell per l'attivazione di un aggiornamento.

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>Assicurarsi che tutte le risorse di un gruppo di risorse abbiano un tag/valore

Un requisito comune è che tutte le risorse di un gruppo di risorse abbiano un determinato tag e valore. Questo requisito è spesso necessario per tenere traccia dei costi per reparto. Le condizioni seguenti devono essere soddisfatte:

* Il tag richiesto e il relativo valore vengono accodati alle risorse nuove e aggiornate che non hanno il tag.
* Non è possibile rimuovere il tag richiesto e il relativo valore da eventuali risorse esistenti.

È possibile soddisfare questo requisito applicando due criteri predefiniti a un gruppo di risorse.

| ID | Descrizione |
| ---- | ---- |
| 2a0e14a6-b0a6-4fab-991a-187a4f81c498 | Applica un tag obbligatorio e il relativo valore predefinito quando non viene specificato dall'utente. |
| 1e30110a-5ceb-460c-a204-c1c3969c6d62 | Applica un tag obbligatorio e il relativo valore. |

### <a name="powershell"></a>PowerShell

Lo script di PowerShell seguente assegna le due definizioni di criteri predefiniti a un gruppo di risorse. Prima di eseguire lo script, assegnare tutti i tag richiesti al gruppo di risorse. Ogni tag nel gruppo di risorse è richiesto per le risorse nel gruppo. Per eseguire l'assegnazione a tutti i gruppi di risorse nella sottoscrizione, non fornire il parametro `-Name` quando si recuperano i gruppi di risorse.

```powershell
$appendpolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '2a0e14a6-b0a6-4fab-991a-187a4f81c498'}
$denypolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '1e30110a-5ceb-460c-a204-c1c3969c6d62'}

$rgs = Get-AzureRMResourceGroup -Name ExampleGroup

foreach($rg in $rgs)
{
    $tags = $rg.Tags
    foreach($key in $tags.Keys){
        $key 
        $tags[$key]
        New-AzureRmPolicyAssignment -Name ("append"+$key+"tag") -PolicyDefinition $appendpolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
        New-AzureRmPolicyAssignment -Name ("denywithout"+$key+"tag") -PolicyDefinition $denypolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
    }
}
```

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

## <a name="require-tags-for-a-resource-type"></a>Richiedere tag per un tipo di rosorsa
L'esempio seguente illustra come nidificare gli operatori logici per richiedere un tag dell'applicazione solo per un determinato tipo di risorsa (in questo caso gli account di archiviazione).

```json
{
    "if": {
        "allOf": [
          {
            "not": {
              "field": "tags",
              "containsKey": "application"
            }
          },
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          }
        ]
    },
    "then": {
        "effect": "audit"
    }
}
```

## <a name="require-tag"></a>Tag richiesto
Il criterio seguente nega le richieste senza un tag con la chiave "costCenter" (è possibile applicare qualsiasi valore):

```json
{
  "if": {
    "not" : {
      "field" : "tags",
      "containsKey" : "costCenter"
    }
  },
  "then" : {
    "effect" : "deny"
  }
}
```

## <a name="next-steps"></a>Passaggi successivi
* Dopo aver definito una regola di criterio, come mostrato negli esempi precedenti, è necessario creare la definizione di criterio e assegnarla a un ambito. L'ambito può essere una sottoscrizione, un gruppo di risorse o una risorsa. Per assegnare i criteri tramite il portale, vedere [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Usare il portale di Azure per assegnare e gestire i criteri delle risorse). Per assegnare i criteri tramite l'API REST, PowerShell o l'interfaccia della riga di comando di Azure, vedere [Assegnare e gestire i criteri tramite script](resource-manager-policy-create-assign.md).
* Per un'introduzione ai criteri delle risorse, vedere [Usare i criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).

