---
title: Analizzare la protezione di rete con la visualizzazione del gruppo di sicurezza di rete di Network Watcher di Azure - Interfaccia della riga di comando di Azure | Documentazione Microsoft
description: Questo articolo descrive come usare l'interfaccia della riga di comando di Azure per analizzare la protezione di macchine virtuali con la visualizzazione di un gruppo di sicurezza.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: fc86b2fd7156ff84b7d91fd39c79caccb815312c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60727806"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Analizzare la protezione della macchina virtuale visualizzando un gruppo di sicurezza con l'interfaccia della riga di comando di Azure

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Interfaccia della riga di comando di Azure](network-watcher-security-group-view-cli.md)
> - [API REST](network-watcher-security-group-view-rest.md)

La visualizzazione di un gruppo di sicurezza consente di recuperare le regole di sicurezza di rete configurate ed effettive applicate a una macchina virtuale. Questa funzionalità è utile per controllare e diagnosticare i gruppi di sicurezza di rete e le regole configurate in una macchina virtuale per verificare che il traffico viene consentito o negato in modo corretto. Questo articolo illustra come recuperare le regole di sicurezza configurate ed effettive applicate a una macchina virtuale tramite l'interfaccia della riga di comando di Azure

Per eseguire i passaggi indicati in questo articolo è necessario [installare l'interfaccia della riga di comando (CLI) di Azure per Mac, Linux e Windows](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Prima di iniziare

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un servizio Network Watcher](network-watcher-create.md) per creare un servizio Network Watcher.

## <a name="scenario"></a>Scenario

Lo scenario illustrato in questo articolo recupera le regole di sicurezza configurate ed effettive applicate a una determinata macchina virtuale.

## <a name="get-a-vm"></a>Ottenere una macchina virtuale

È necessario che una macchina virtuale esegua il cmdlet `vm list`. Il comando seguente elenca le macchine virtuali in un gruppo di risorse:

```azurecli
az vm list -resource-group resourceGroupName
```

Se la macchina virtuale è nota, è possibile usare il cmdlet `vm show` per ottenere il relativo ID della risorsa:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Recuperare la visualizzazione del gruppo di sicurezza

Il passaggio successivo prevede il recupero del risultato della visualizzazione del gruppo di sicurezza.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Visualizzazione dei risultati

L'esempio seguente è una risposta abbreviata dei risultati restituiti. I risultati mostrano tutte le regole di sicurezza effettive e applicate alla macchina virtuale, suddivise nei gruppi **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** e **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Consultare [Auditing Network Security Groups (NSG) with Network Watcher](network-watcher-nsg-auditing-powershell.md) (Verifica dei gruppi di sicurezza di rete con Network Watcher) per informazioni su come automatizzare la verifica dei gruppi di sicurezza di rete.

Per altre informazioni sulle regole di sicurezza applicate alle risorse di rete, leggere la [panoramica sulla visualizzazione di gruppo di sicurezza](network-watcher-security-group-view-overview.md).
