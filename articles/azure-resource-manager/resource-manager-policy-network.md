---
title: Criteri delle risorse di Azure per le risorse di rete | Microsoft Docs
description: Descrive i criteri di Azure Resource Manager per gestire la distribuzione delle risorse di rete.
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
ms.date: 07/05/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: bca66bbdd9da9b3e4099d0d961f42c9368a17f5e
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
# <a name="apply-resource-policies-to-network-resources"></a>Applicare i criteri delle risorse alle risorse di rete
In questo articolo viene illustrato un esempio di [criteri delle risorse](resource-manager-policy.md) che è possibile applicare ai gateway di rete virtuale di Azure. Questi criteri assicurano la coerenza per i gateway distribuiti nell'organizzazione. 

## <a name="define-permitted-virtual-network-gateway-sku"></a>Definire lo SKU dei gateway di rete virtuale consentiti

I criteri seguenti consentono di limitare gli SKU che è possibile distribuire per i gateway di rete virtuale:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Network/virtualNetworkGateways"
      },
      {
        "not": {
          "field": "Microsoft.Network/virtualNetworkGateways/sku.name",
          "in": [
            "Basic",
            "VpnGw1"
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

## <a name="next-steps"></a>Passaggi successivi
* Dopo aver definito una regola di criterio, come mostrato negli esempi precedenti, è necessario creare la definizione di criterio e assegnarla a un ambito. L'ambito può essere una sottoscrizione, un gruppo di risorse o una risorsa. Per assegnare i criteri tramite il portale, vedere [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Usare il portale di Azure per assegnare e gestire i criteri delle risorse). Per assegnare i criteri tramite l'API REST, PowerShell o l'interfaccia della riga di comando di Azure, vedere [Assegnare e gestire i criteri tramite script](resource-manager-policy-create-assign.md). 
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).


