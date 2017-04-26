---
title: Criteri delle risorse di Azure per gli account di archiviazione | Documentazione Microsoft
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
ms.date: 03/30/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 75685a21ce4a212638016be62640badd4870454a
ms.lasthandoff: 03/31/2017


---
# <a name="apply-resource-policies-to-storage-accounts"></a>Applicare i criteri delle risorse agli account di archiviazione
Questo argomento indica diversi [criteri delle risorse](resource-manager-policy.md) che è possibile applicare agli account di archiviazione di Azure. Questi criteri assicurano la coerenza per gli account di archiviazione distribuiti nell'organizzazione. 

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

Una regola di criterio simile con un parametro per accettare gli SKU consentiti è disponibile come definizione di criterio predefinita. Il criterio predefinito ha l'ID risorsa `/providers/Microsoft.Authorization/policyDefinitions/7433c107-6db4-4ad1-b57a-a76dce0154a1`. 

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

Questa regola di criterio è anche disponibile come definizione di criterio predefinita con l'ID risorsa `/providers/Microsoft.Authorization/policyDefinitions/7c5a74bf-ae94-4a74-8fcf-644d1e0e6e6f`.

## <a name="next-steps"></a>Passaggi successivi
* Dopo aver definito una regola di criterio, come mostrato negli esempi precedenti, è necessario creare la definizione di criterio e assegnarla a un ambito. L'ambito può essere una sottoscrizione, un gruppo di risorse o una risorsa. Per assegnare i criteri tramite il portale, vedere [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Usare il portale di Azure per assegnare e gestire i criteri delle risorse). Per assegnare i criteri tramite l'API REST, PowerShell o l'interfaccia della riga di comando di Azure, vedere [Assegnare e gestire i criteri tramite script](resource-manager-policy-create-assign.md). 
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).


