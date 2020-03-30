---
title: "Disabilitare i criteri di rete per l'indirizzo IP di origine del servizio Azure Private LinkDisable network policies for Azure Private Link service source IP address "
description: Informazioni su come disabilitare i criteri di rete per Azure private Link
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 4c6bd64d141341e0b7fa5641e04320a95d7951bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453000"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Disabilitare i criteri di rete per l'indirizzo ip di origine del servizio Private LinkDisable network policies for Private Link service source IP

Per scegliere un indirizzo IP di origine per il `privateLinkServiceNetworkPolicies` servizio Private Link, è necessaria un'impostazione di disabilitazione esplicita nella subnet. Questa impostazione è applicabile solo all'indirizzo IP privato specifico scelto come IP di origine del servizio Private Link. Per altre risorse nella subnet, l'accesso viene controllato in base alla definizione delle regole di sicurezza dei gruppi di sicurezza di rete.For other resources in the subnet, access is controlled based on Network Security Groups (NSG) security rules definition. 
 
Quando si usa qualsiasi client di Azure (PowerShell, CLI o modelli), è necessario un passaggio aggiuntivo per modificare questa proprietà. È possibile disabilitare i criteri usando la shell cloud dal portale di Azure o le installazioni locali di Azure PowerShell, l'interfaccia della riga di comando di Azure o i modelli di Azure Resource Manager.You can disable the policy using the cloud shell from the Azure portal, or local installations of Azure PowerShell, Azure CLI, or use Azure Resource Manager templates.  
 
Seguire i passaggi seguenti per disabilitare i criteri di rete del servizio di collegamento privato per una rete virtuale denominata *myVirtualNetwork* con una subnet *predefinita* ospitata in un gruppo di risorse denominato *myResourceGroup*. 

## <a name="using-azure-powershell"></a>Uso di Azure PowerShell
In questa sezione viene descritto come disabilitare i criteri degli endpoint privati della subnet usando Azure PowerShell.This section describes how to disable subnet private endpoint policies using Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
In questa sezione viene descritto come disabilitare i criteri degli endpoint privati della subnet usando l'interfaccia della riga di comando di Azure.This section describes how to disable subnet private endpoint policies using Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>Uso di un modello
Questa sezione descrive come disabilitare i criteri degli endpoint privati della subnet usando il modello di Azure Resource Manager.This section describes how to disable subnet private endpoint policies using Azure Resource Manager Template.
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
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull'endpoint privato di [AzureLearn](private-endpoint-overview.md) more about Azure Private Endpoint
 
