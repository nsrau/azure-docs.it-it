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
ms.date: 02/09/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 72d398c529fc7dd5eef450da0e134dcdab534ac5
ms.openlocfilehash: 375a8df763eb6b4b8f7349e0061ab39c076ebfc6


---
# <a name="apply-resource-policies-for-tags"></a>Applicare criteri delle risorse per i tag

Questo argomento fornisce regole di criterio comuni che è possibile applicare per garantire l'uso coerente dei tag delle risorse.

Applicando un criterio di tag a un gruppo di risorse o a una sottoscrizione con risorse esistenti non si applica retroattivamente il criterio a tali risorse. Per applicare i criteri a queste risorse, avviare l'aggiornamento delle risorse esistenti, come illustrato in [Attivare aggiornamenti delle risorse esistenti](#trigger-updates-to-existing-resources).

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>Assicurarsi che tutte le risorse di un gruppo di risorse abbiano un tag/valore

Un requisito comune è che tutte le risorse di un gruppo di risorse abbiano un determinato tag e valore. Questo requisito è spesso necessario per tenere traccia dei costi per reparto. Le condizioni seguenti devono essere soddisfatte:

* Il tag e il valore richiesti vengono accodati alle risorse nuove e aggiornate che non hanno tag esistenti.
* Il tag e il valore richiesti vengono accodati alle risorse nuove e aggiornate che hanno altri tag, ma non il tag e il valore richiesti.
* Non è possibile rimuovere il tag e il valore richiesti da eventuali risorse esistenti.

Si soddisfa questo requisito applicando a un gruppo di risorse i tre criteri seguenti:

* [Accodare un tag](#append-tag) 
* [Accodare un tag con altri tag](#append-tag-with-other-tags)
* [Richiedere il tag e il valore](#require-tag-and-value)

### <a name="append-tag"></a>Accodare un tag

La regola di criterio seguente accoda il tag costCenter con un valore predefinito se non sono presenti tag:

```json
{
  "if": {
    "field": "tags",
    "exists": "false"
  },
  "then": {
    "effect": "append",
    "details": [
      {
        "field": "tags",
        "value": {"costCenter":"myDepartment" }
      }
    ]
  }
}
```

### <a name="append-tag-with-other-tags"></a>Accodare un tag con altri tag

La regola di criterio seguente accoda il tag costCenter con un valore predefinito quando sono presenti tag ma il tag costCenter non è definito:

```json
{
  "if": {
    "allOf": [
      {
        "field": "tags",
        "exists": "true"
      },
      {
        "field": "tags.costCenter",
        "exists": "false"
      }
    ]
  },
  "then": {
    "effect": "append",
    "details": [
      {
        "field": "tags.costCenter",
        "value": "myDepartment"
      }
    ]
  }
}
```

### <a name="require-tag-and-value"></a>Richiedere il tag e il valore

La regola di criterio seguente nega l'aggiornamento o la creazione di risorse che non hanno il tag costCenter assegnato con il valore predefinito.

```json
{
  "if": {
    "not": {
      "field": "tags.costCenter",
      "equals": "myDepartment"
    }
  },
  "then": {
    "effect": "deny"
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

## <a name="trigger-updates-to-existing-resources"></a>Attivare aggiornamenti delle risorse esistenti

Il seguente script PowerShell avvia l'aggiornamento delle risorse esistenti per applicare i criteri di tag che sono stati aggiunti.

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($_.Tags -eq $NULL) { @{}} else {$_.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
* Dopo aver definito una regola di criterio, come mostrato negli esempi precedenti, è necessario creare la definizione di criterio e assegnarla a un ambito. L'ambito può essere una sottoscrizione, un gruppo di risorse o una risorsa. Per esempi sulla creazione e l'assegnazione dei criteri, vedere [Assign and manage policies](resource-manager-policy-create-assign.md) (Assegnare e gestire criteri). 
* Per un'introduzione ai criteri delle risorse, vedere [Usare i criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).




<!--HONumber=Feb17_HO3-->


