---
title: Criteri di Azure Resource Manager per l&quot;archiviazione | Documentazione Microsoft
description: Descrive i criteri di Azure Resource Manager per gestire la distribuzione degli account di archiviazione.
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
ms.date: 01/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: ce31bbcb96a6afe19cf6c25dd9a68d6c2d4d080c
ms.openlocfilehash: f2141304f6db6d137065e06558e10c721b11f892


---
# <a name="apply-azure-resource-policies-to-storage-accounts"></a>Applicare i criteri delle risorse di Azure agli account di archiviazione
I criteri di Azure Resource Manager consentono di definire regole coerenti per la modalità di distribuzione delle risorse nell'organizzazione. È possibile creare criteri personalizzati per impedire agli utenti dell'organizzazione di violare le convenzioni necessarie per gestire le risorse dell'organizzazione. Questo argomento mostra vari criteri che definiscono le regole per gli account di archiviazione di Azure. Per altre informazioni sui criteri, vedere [Usare i criteri per gestire le risorse](resource-manager-policy.md).

Gli esempi in questo argomento mostrano valori hardcoded nella regola dei criteri. Tuttavia, è possibile usare i parametri per passare i valori da adottare al momento dell'assegnazione dei criteri. Per altre informazioni, vedere [Parametri dei criteri](resource-manager-policy.md#parameters).

## <a name="define-permitted-storage-account-types"></a>Definire i tipi di account di archiviazione consentiti

I criteri seguenti consentono di limitare i [tipi di account di archiviazione](../storage/storage-redundancy.md) che è possibile distribuire:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/sku.name",
          "in": [
            "Standard_LRS",
            "Standard_GRS"
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="define-permitted-access-tier"></a>Definire il livello di accesso consentito

I criteri seguenti specificano il tipo di [livello di accesso](../storage/storage-blob-storage-tiers.md) che è possibile definire per gli account di archiviazione:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="ensure-encryption-is-enabled"></a>Verificare che la crittografia sia abilitata

I criteri seguenti richiedono l'abilitazione della [crittografia del servizio di archiviazione](../storage/storage-service-encryption.md) per tutti gli account di archiviazione:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/enableBlobEncryption",
          "equals": "true"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="create-and-assign-policies"></a>Creare e assegnare criteri

Dopo aver definito le regole dei criteri, come mostrato negli esempi precedenti, è necessario creare i criteri e assegnarli a un ambito. L'ambito può essere una sottoscrizione, un gruppo di risorse o una risorsa. Per esempi sulla creazione e l'assegnazione di criteri, vedere [Creare e assegnare un criterio](resource-manager-policy.md#create-and-assign-a-policy). 

## <a name="next-steps"></a>Passaggi successivi
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).




<!--HONumber=Jan17_HO3-->


