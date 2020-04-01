---
title: Distribuire set di scalabilità di macchine virtuali con IPv6 in AzureDeploy virtual machine scale sets with IPv6 in Azure
titlesuffix: Azure Virtual Network
description: Questo articolo illustra come distribuire set di scalabilità di macchine virtuali con IPv6 in una rete virtuale di Azure.This article shows how to deploy virtual machine scale sets with IPv6 in an Azure virtual network.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 6a751fa193c8dd530707f790af0292d536a6f47d
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420452"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure"></a>Distribuire set di scalabilità di macchine virtuali con IPv6 in AzureDeploy virtual machine scale sets with IPv6 in Azure

In questo articolo viene illustrato come distribuire un set di scalabilità di macchine virtuali dual stack (IPv4 e IPv6) con un servizio di bilanciamento del carico esterno a doppio stack in una rete virtuale di Azure.This article shows you how to deploy a dual stack (IPv4 - IPv6) Virtual Machine Scale Set with a dual stack external load balancer in an Azure virtual network. Il processo di creazione di un set di scalabilità di macchine virtuali con iPv6 è quasi identico al processo di creazione di singole macchine virtuali descritto [di seguito.](ipv6-configure-standard-load-balancer-template-json.md) Si inizierà con i passaggi simili a quelli descritti per le singole macchine virtuali:You'll start with the steps that are similar to ones described for individual VMs:
1.    Creare indirizzi IP pubblici IPv4 e IPv6.
2.    Creare un servizio di bilanciamento del carico dello stack doppio.  
3.    Creare regole del gruppo di sicurezza di rete.Create network security group (NSG) rules.  

L'unico passaggio diverso dalle singole macchine virtuali è la creazione della configurazione dell'interfaccia di rete (NIC) che utilizza la risorsa set di scalabilità della macchina virtuale: networkProfile/networkInterfaceConfigurations.The only step that is different from individual VMs is creating the network interface (NIC) configuration that uses the virtual machine scale set resource: networkProfile/networkInterfaceConfigurations. La struttura JSON è simile a quella dell'oggetto Microsoft.Network/networkInterfaces utilizzato per le singole macchine virtuali con l'aggiunta dell'impostazione della scheda di interfaccia di rete e di IpConfiguration IPv4 come interfaccia primaria utilizzando l'attributo **"primary": true** come illustrato nell'esempio seguente:

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>Modello di set di scalabilità di macchine virtuali di esempio JSONSample virtual machine scale set template JSON

Per distribuire un set di scalabilità di macchine virtuali dual stack (IPv4 e IPv6) con il bilanciamento del carico esterno a doppio stack e il modello di esempio per la visualizzazione della rete [virtuale.](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/)
## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul supporto IPv6 nelle reti virtuali di Azure, vedere [Che cos'è IPv6 per](ipv6-overview.md)la rete virtuale di Azure? .
