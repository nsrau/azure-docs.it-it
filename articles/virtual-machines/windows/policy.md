---
title: Applicare la sicurezza con i criteri per le macchine virtuali Windows in Azure
description: Come applicare criteri a una macchina virtuale Windows di Azure Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 08/02/2017
ms.author: kasing
ms.openlocfilehash: a2ee5f36b7dba03bea3cc219651804c5039115d5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032964"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Applicare criteri alle macchine virtuali Windows con Azure Resource Manager
Tramite i criteri è possibile imporre diverse convenzioni e regole in tutta l'organizzazione. L'imposizione del comportamento desiderato consente di attenuare i rischi, contribuendo nello stesso tempo al successo dell'organizzazione. Questo articolo illustra come usare i criteri di Azure Resource Manager per definire il comportamento desiderato per le macchine virtuali dell'organizzazione.

Per un'introduzione ai criteri, vedere [Informazioni su Criteri di Azure](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Macchine virtuali permesse
Per assicurarsi che le macchine virtuali per l'organizzazione siano compatibili con un'applicazione, è possibile limitare i sistemi operativi consentiti. Nell'esempio di criterio che segue si consente solo la creazione di macchine virtuali Windows Server 2012 R2 Datacenter:

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
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-R2-Datacenter"
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

Per modificare il criterio precedente e consentire qualsiasi immagine Windows Server Datacenter, usare un carattere jolly:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Per modificare il criterio precedente e consentire qualsiasi immagine Windows Server 2012 R2 Datacenter o versione successiva, usare anyOf:

```json
{
  "anyOf": [
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2012-R2-Datacenter*"
    },
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2016-Datacenter*"
    }
  ]
}
```

Per informazioni sui campi dei criteri, vedere [Alias dei criteri](../../governance/policy/concepts/definition-structure.md#aliases).

## <a name="managed-disks"></a>Dischi gestiti

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

## <a name="images-for-virtual-machines"></a>Immagini per macchine virtuali

Per motivi di sicurezza, è possibile richiedere che solo le immagini personalizzate approvate vengano distribuite nell'ambiente in uso. È possibile specificare il gruppo di risorse che contiene le immagini approvate o immagini approvate specifiche.

L'esempio seguente richiede le immagini da un gruppo di risorse approvato:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

L'esempio seguente specifica gli ID immagine approvati:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Estensioni di macchina virtuale

È possibile che si desideri proibire l'utilizzo di tipi specifici di estensioni. Un'estensione potrebbe non essere ad esempio compatibile con determinate immagini di macchina virtuale personalizzata. L'esempio seguente mostra come bloccare un'estensione specifica. Usa server di pubblicazione e tipo per determinare l'estensione da bloccare.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="azure-hybrid-use-benefit"></a>Vantaggio Azure Hybrid Use

Quando si dispone di una licenza locale, è possibile risparmiare il costo della licenza sulle macchine virtuali. Se non si dispone di licenza, è consigliabile impedire questa possibilità. I criteri seguenti impediscono l'uso del vantaggio Azure Hybrid Use (AHUB):

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in":[ "Microsoft.Compute/virtualMachines","Microsoft.Compute/VirtualMachineScaleSets"]
            },
            {
                "field": "Microsoft.Compute/licenseType",
                "exists": true
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
* Dopo aver definito una regola di criterio, come mostrato negli esempi precedenti, è necessario creare la definizione di criterio e assegnarla a un ambito. L'ambito può essere una sottoscrizione, un gruppo di risorse o una risorsa. Per assegnare i criteri, vedere [Usare il portale di Azure per assegnare e gestire i criteri delle risorse](../../governance/policy/assign-policy-portal.md), [Creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure usando PowerShell](../../governance/policy/assign-policy-powershell.md) o [Creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure usando l'interfaccia della riga di comando di Azure](../../governance/policy/assign-policy-azurecli.md).
* Per un'introduzione ai criteri delle risorse, vedere [Informazioni su Criteri di Azure](../../governance/policy/overview.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).