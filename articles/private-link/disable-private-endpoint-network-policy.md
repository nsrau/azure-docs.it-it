---
title: Disabilitare i criteri di rete per gli endpoint privati in AzureDisable network policies for private endpoints in Azure
description: Informazioni su come disabilitare i criteri di rete per gli endpoint privati.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: b5ab62e7ab57d32a11a45713519633034deb6a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453011"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Disabilitare i criteri di rete per gli endpoint privatiDisable network policies for private endpoints

I criteri di rete, ad esempio i gruppi di sicurezza di rete (NSG), non sono supportati per gli endpoint privati.Network policies like network security groups (NSG) are not supported for private endpoints. Per distribuire un endpoint privato in una determinata subnet, è necessaria un'impostazione di disabilitazione esplicita su tale subnet. Questa impostazione è applicabile solo per l'endpoint privato. Per altre risorse nella subnet, l'accesso viene controllato in base alla definizione delle regole di sicurezza dei gruppi di sicurezza di rete.For other resources in the subnet, access is controlled based on Network Security Groups (NSG) security rules definition. 
 
Quando si usa il portale per creare un endpoint privato, questa impostazione viene disabilitata automaticamente come parte del processo di creazione. La distribuzione tramite altri client richiede un passaggio aggiuntivo per modificare questa impostazione. È possibile disabilitare l'impostazione usando la shell cloud dal portale di Azure o le installazioni locali di Azure PowerShell, l'interfaccia della riga di comando di Azure o i modelli di Azure Resource Manager.You can disable the setting using cloud shell from the Azure portal, or local installations of Azure PowerShell, Azure CLI, or use Azure Resource Manager templates.  
 
Negli esempi seguenti viene `PrivateEndpointNetworkPolicies` descritto come disabilitare per una rete virtuale denominata *myVirtualNetwork* con una subnet *predefinita* ospitata in un gruppo di risorse denominato *myResourceGroup*.

## <a name="using-azure-powershell"></a>Uso di Azure PowerShell
In questa sezione viene descritto come disabilitare i criteri degli endpoint privati della subnet usando Azure PowerShell.This section describes how to disable subnet private endpoint policies using Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
In questa sezione viene descritto come disabilitare i criteri degli endpoint privati della subnet usando l'interfaccia della riga di comando di Azure.This section describes how to disable subnet private endpoint policies using Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
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
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull'endpoint privato di [AzureLearn](private-endpoint-overview.md) more about Azure private endpoint
 
