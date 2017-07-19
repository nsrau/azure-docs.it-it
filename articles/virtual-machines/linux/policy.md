---
title: Applicare la sicurezza con criteri su VM Linux in Azure | Documentazione Microsoft
description: Come applicare criteri a una macchina virtuale Linux di Azure Resource Manager
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 06778ab4-f8ff-4eed-ae10-26a276fc3faa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: singhkay
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: c1ad80a56627695f3594f4d9b60cd623fa9bcce3
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017


---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Applicare criteri alle VM Linux con Azure Resource Manager
Tramite i criteri è possibile imporre diverse convenzioni e regole in tutta l'organizzazione. L'imposizione del comportamento desiderato consente di attenuare i rischi, contribuendo nello stesso tempo al successo dell'organizzazione. Questo articolo illustra come usare i criteri di Azure Resource Manager per definire il comportamento desiderato per le macchine virtuali dell'organizzazione.

Per un'introduzione ai criteri, vedere [Usare i criteri per gestire le risorse e controllare l'accesso](../../azure-resource-manager/resource-manager-policy.md).

## <a name="define-policy-for-permitted-virtual-machines"></a>Definire i criteri per le macchine virtuali consentite
Per assicurarsi che le macchine virtuali dell'organizzazione siano compatibili con un'applicazione, è possibile limitare i sistemi operativi consentiti. Questo criterio di esempio consente di creare solo macchine virtuali Ubuntu 14.04.2-LTS.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
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

Per modificare il criterio precedente e consentire qualsiasi immagine Ubuntu LTS, usare un carattere jolly: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

Per informazioni sui campi dei criteri, vedere [Alias dei criteri](../../azure-resource-manager/resource-manager-policy.md#aliases).

## <a name="define-policy-for-using-managed-disks"></a>Definire i criteri per l'uso dei dischi gestiti

Per richiedere l'uso dei dischi gestiti, usare il criterio seguente:

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>Passaggi successivi
* Dopo aver definito una regola di criterio, come mostrato negli esempi precedenti, è necessario creare la definizione di criterio e assegnarla a un ambito. L'ambito può essere una sottoscrizione, un gruppo di risorse o una risorsa. Per assegnare i criteri tramite il portale, vedere [Use Azure portal to assign and manage resource policies](../../azure-resource-manager/resource-manager-policy-portal.md) (Usare il portale di Azure per assegnare e gestire i criteri delle risorse). Per assegnare i criteri tramite l'API REST, PowerShell o l'interfaccia della riga di comando di Azure, vedere [Assegnare e gestire i criteri tramite script](../../azure-resource-manager/resource-manager-policy-create-assign.md).
* Per un'introduzione ai criteri delle risorse, vedere [Usare i criteri per gestire le risorse e controllare l'accesso](../../azure-resource-manager/resource-manager-policy.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](../../azure-resource-manager/resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).

