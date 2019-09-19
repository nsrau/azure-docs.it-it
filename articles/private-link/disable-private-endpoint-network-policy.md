---
title: Disabilitare i criteri di rete per gli endpoint privati in Azure
description: Informazioni su come disabilitare i criteri di rete per gli endpoint privati.
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: b510b697fbf8b53e9e55e96f60b27967f90893f1
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104617"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Disabilitare i criteri di rete per gli endpoint privati

I criteri di rete come i gruppi di sicurezza di rete (NSG) non sono supportati per gli endpoint privati. Per distribuire un endpoint privato in una determinata subnet, è necessaria un'impostazione di disabilitazione esplicita in tale subnet. Questa impostazione è applicabile solo per l'endpoint privato. Per altre risorse nella subnet, l'accesso viene controllato in base alla definizione delle regole di sicurezza dei gruppi di sicurezza di rete (NSG). 
 
Quando si usa il portale per creare un endpoint privato, questa impostazione viene disabilitata automaticamente come parte del processo di creazione. Per la distribuzione con altri client è necessario un passaggio aggiuntivo per modificare questa impostazione. È possibile disabilitare l'impostazione usando cloud shell dalla portale di Azure o dalle installazioni locali di Azure PowerShell, dall'interfaccia della riga di comando di Azure o usare modelli di Azure Resource Manager.  
 
Gli esempi seguenti descrivono come disabilitare `PrivateEndpointNetworkPolicies` per una rete virtuale denominata *myVirtualNetwork* con una subnet *predefinita* ospitata in un gruppo di risorse denominato *myResourceGroup*.

## <a name="using-azure-powershell"></a>Uso di Azure PowerShell
Questa sezione descrive come disabilitare i criteri dell'endpoint privato della subnet usando Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork ` 
  | Select -ExpandProperty subnets ` 
  | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
Questa sezione descrive come disabilitare i criteri dell'endpoint privato della subnet usando l'interfaccia della riga di comando di Azure.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
```
## <a name="using-a-template"></a>Uso di un modello
Questa sezione descrive come disabilitare i criteri dell'endpoint privato della subnet usando Azure Resource Manager modello.
```json
{ 
          "name": "myVirtualNetwork", 
          "type": "Microsoft.Network/virtualNetworks", 
          "apiVersion": "2019-04-01", 
          "location": "WestUS", 
          "properties": { 
                "addressSpace": { 
                     "addressPrefixes": [ 
                          "10.0.0.0/16" 
                        ] 
                  }, 
                  "subnets": [ 
                         { 
                                "name": "default", 
                                "properties": { 
                                    "addressPrefix": "10.0.0.0/24", 
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull' [endpoint privato di Azure](private-endpoint-overview.md)
 
