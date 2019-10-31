---
title: Distribuire set di scalabilità di macchine virtuali con IPv6 in Azure
titlesuffix: Azure Virtual Network
description: Questo articolo illustra come distribuire set di scalabilità di macchine virtuali con IPv6 in una rete virtuale di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: kumud
ms.openlocfilehash: b90910614bcd86a54198b1a0961a3378427ea87e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164991"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure-preview"></a>Distribuire set di scalabilità di macchine virtuali con IPv6 in Azure (anteprima)

Questo articolo illustra come distribuire un set di scalabilità di macchine virtuali dual stack (IPv4 + IPv6) con un servizio di bilanciamento del carico esterno a doppio stack in una rete virtuale di Azure. Il processo di creazione di un set di scalabilità di macchine virtuali in grado di supportare IPv6 è quasi identico a quello per la creazione di singole VM descritte [qui](ipv6-configure-standard-load-balancer-template-json.md). Si inizierà con i passaggi simili a quelli descritti per le singole macchine virtuali:
1.  Creare indirizzi IP pubblici IPv4 e IPv6.
2.  Creare un servizio di bilanciamento del carico dual stack.  
3.  Creare regole del gruppo di sicurezza di rete (NSG).  

L'unico passaggio diverso dalle singole macchine virtuali consiste nel creare la configurazione dell'interfaccia di rete (NIC) che usa la risorsa del set di scalabilità di macchine virtuali: networkProfile/networkInterfaceConfigurations. La struttura JSON è simile a quella dell'oggetto Microsoft. Network/networkInterfaces usato per le singole macchine virtuali con l'aggiunta dell'impostazione della scheda di interfaccia di rete e del configurazione IP IPv4 come interfaccia primaria usando l'attributo **"Primary": true** , come illustrato nella esempio seguente:

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


## <a name="sample-virtual-machine-scale-set-template-json"></a>Esempio di modello di set di scalabilità di macchine virtuali JSON

Per distribuire un set di scalabilità di macchine virtuali dual stack (IPv4 + IPv6) con doppio stack esterno Load Balancer e il modello di esempio di visualizzazione rete virtuale [qui](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/).
## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul supporto di IPv6 in reti virtuali di Azure, vedere [che cos'è IPv6 per la rete virtuale di Azure?](ipv6-overview.md).
