---
title: Gestire i gruppi di sicurezza di rete - interfaccia della riga di comando di Azure 2.0 | Documentazione Microsoft
description: Informazioni su come gestire i gruppi di sicurezza di rete usando l&quot;interfaccia della riga di comando di Azure 2.0.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: ed17d314-07e6-4c7f-bcf1-a8a2535d7c14
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: dcb0455fe223e99b4f1b0035b1d1109ecf5ee268
ms.lasthandoff: 02/28/2017


---
# <a name="manage-network-security-groups-using-the-azure-cli-20"></a>Gestire i gruppi di sicurezza di rete usando l'interfaccia della riga di comando di Azure 2.0

[!INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività 

È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando: 

- [Interfaccia della riga di comando di Azure 1.0](virtual-network-manage-nsg-cli-nodejs.md): l'interfaccia della riga di comando per i modelli di distribuzione classici e di gestione delle risorse 
- [Interfaccia della riga di comando di Azure 2.0](#View-existing-NSGs): interfaccia avanzata per il modello di distribuzione di gestione delle risorse (questo articolo)


[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione Resource Manager che Microsoft consiglia di usare invece del modello di distribuzione classica per le distribuzioni più recenti.
> 

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="prerequisite"></a>Prerequisito
Se questa operazione non è stata ancora eseguita, installare e configurare l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e accedere a un account Azure usando il comando [az login](/cli/azure/#login). 


## <a name="view-existing-nsgs"></a>Visualizzare NSG esistenti
Per visualizzare l'elenco di gruppi di sicurezza di rete in un gruppo di risorse specifico, eseguire il comando [az network nsg list](/cli/azure/network/nsg#list) con un formato di output `-o table`:

```azurecli
az network nsg list -g RG-NSG -o table
```

Output previsto:

    Location    Name          ProvisioningState    ResourceGroup    ResourceGuid
    ----------  ------------  -------------------  ---------------  ------------------------------------
    centralus   NSG-BackEnd   Succeeded            RG-NSG           <guid>
    centralus   NSG-FrontEnd  Succeeded            RG-NSG           <guid>

## <a name="list-all-rules-for-an-nsg"></a>Elencare tutte le regole per un NSG
Per visualizzare le regole di un gruppo di sicurezza di rete denominato **NSG-FrontEnd**, eseguire il comando [az network nsg show](/cli/azure/network/nsg#show) usando il [filtro di query JMESPATH](/cli/azure/query-az-cli2) e il formato di output `-o table`:

```azurecli
    az network nsg show \
    --resource-group RG-NSG \
    --name NSG-FrontEnd \
    --query '[defaultSecurityRules[],securityRules[]][].{Name:name,Desc:description,Access:access,Direction:direction,DestPortRange:destinationPortRange,DestAddrPrefix:destinationAddressPrefix,SrcPortRange:sourcePortRange,SrcAddrPrefix:sourceAddressPrefix}' \
    -o table
```

Output previsto:

    Name                           Desc                                                    Access    Direction    DestPortRange    DestAddrPrefix    SrcPortRange    SrcAddrPrefix
    -----------------------------  ------------------------------------------------------  --------  -----------  ---------------  ----------------  --------------  -----------------
    AllowVnetInBound               Allow inbound traffic from all VMs in VNET              Allow     Inbound      *                VirtualNetwork    *               VirtualNetwork
    AllowAzureLoadBalancerInBound  Allow inbound traffic from azure load balancer          Allow     Inbound      *                *                 *               AzureLoadBalancer
    DenyAllInBound                 Deny all inbound traffic                                Deny      Inbound      *                *                 *               *
    AllowVnetOutBound              Allow outbound traffic from all VMs to all VMs in VNET  Allow     Outbound     *                VirtualNetwork    *               VirtualNetwork
    AllowInternetOutBound          Allow outbound traffic from all VMs to Internet         Allow     Outbound     *                Internet          *               *
    DenyAllOutBound                Deny all outbound traffic                               Deny      Outbound     *                *                 *               *
    rdp-rule                                                                               Allow     Inbound      3389             *                 *               Internet
    web-rule                                                                               Allow     Inbound      80               *                 *               Internet
> [!NOTE]
> È possibile anche usare [az network nsg rule list](/cli/azure/network/nsg/rule#list) per elencare solo le regole personalizzate da un gruppo di sicurezza di rete.
>

## <a name="view-nsg-associations"></a>Visualizzare le associazioni di NSG

Per visualizzare le risorse a cui l'NSG **NSG-FrontEnd** è associato, eseguire il comando `az network nsg show` come mostrato di seguito. 

```azurecli
az network nsg show -g RG-NSG -n nsg-frontend --query '[subnets,networkInterfaces]'
```

Cercare le proprietà **networkInterfaces** e **subnets** come illustrato di seguito:

```json
[
  [
    {
      "addressPrefix": null,
      "etag": null,
      "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/FrontEnd",
      "ipConfigurations": null,
      "name": null,
      "networkSecurityGroup": null,
      "provisioningState": null,
      "resourceGroup": "RG-NSG",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  null
]
```

Nell'esempio precedente, l'NSG non è associato ad alcuna interfaccia di rete (NIC) ed è associato a una subnet denominata **FrontEnd**.

## <a name="add-a-rule"></a>Aggiungere una regola
Per aggiungere una regola che consenta il traffico **in ingresso** alla porta **443** da qualsiasi computer all'NSG **NSG-FrontEnd**, immettere il comando seguente:

```azurecli
az network nsg rule create  \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd  \
--name allow-https \
--description "Allow access to port 443 for HTTPS" \
--access Allow \
--protocol Tcp  \
--direction Inbound \
--priority 102 \
--source-address-prefix "*"  \
--source-port-range "*"  \
--destination-address-prefix "*" \
--destination-port-range "443"
```

Output previsto:

```json
{
  "access": "Allow",
  "description": "Allow access to port 443 for HTTPS",
  "destinationAddressPrefix": "*",
  "destinationPortRange": "443",
  "direction": "Inbound",
  "etag": "W/\"<guid>\"",
  "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https",
  "name": "allow-https",
  "priority": 102,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "RG-NSG",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

## <a name="change-a-rule"></a>Modificare una regola
Per modificare la regola creata in precedenza per consentire solo il traffico in ingresso da **Internet**, eseguire il comando [az network nsg rule update](/cli/azure/network/nsg/rule#update) come illustrato di seguito.

```azurecli
az network nsg rule update \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd \
--name allow-https \
--source-address-prefix Internet
```

Output previsto:

```json
{
"access": "Allow",
"description": "Allow access to port 443 for HTTPS",
"destinationAddressPrefix": "*",
"destinationPortRange": "443",
"direction": "Inbound",
"etag": "W/\"<guid>\"",
"id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https",
"name": "allow-https",
"priority": 102,
"protocol": "Tcp",
"provisioningState": "Succeeded",
"resourceGroup": "RG-NSG",
"sourceAddressPrefix": "Internet",
"sourcePortRange": "*"
}
```

## <a name="delete-a-rule"></a>Eliminare una regola
Per eliminare la regola creata in precedenza, eseguire il comando seguente:

```azurecli
az network nsg rule delete \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd \
--name allow-https
```


## <a name="associate-an-nsg-to-a-nic"></a>Associare un NSG a una NIC
Per associare il gruppo di sicurezza di rete **NSG-FrontEnd** alla scheda di interfaccia di rete **TestNICWeb1**, eseguire il comando [az network nic update](/cli/azure/network/nic#update):

```azurecli
az network nic update \
--resource-group RG-NSG \
--name TestNICWeb1 \
--network-security-group NSG-FrontEnd    
```

Output previsto:

```json
{
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": [],
    "internalDnsNameLabel": null,
    "internalDomainNameSuffix": "k0wkaguidnqrh0ud.gx.internal.cloudapp.net",
    "internalFqdn": null
  },
  "enableAcceleratedNetworking": false,
  "enableIpForwarding": false,
  "etag": "W/\"<guid>\"",
  "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1",
  "ipConfigurations": [
    {
      "applicationGatewayBackendAddressPools": null,
      "etag": "W/\"<guid>\"",
      "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1",
      "loadBalancerBackendAddressPools": null,
      "loadBalancerInboundNatRules": null,
      "name": "ipconfig1",
      "primary": true,
      "privateIpAddress": "192.168.1.6",
      "privateIpAddressVersion": "IPv4",
      "privateIpAllocationMethod": "Static",
      "provisioningState": "Succeeded",
      "publicIpAddress": null,
      "resourceGroup": "RG-NSG",
      "subnet": {
        "addressPrefix": null,
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
        "ipConfigurations": null,
        "name": null,
        "networkSecurityGroup": null,
        "provisioningState": null,
        "resourceGroup": "RG-NSG",
        "resourceNavigationLinks": null,
        "routeTable": null
      }
    }
  ],
  "location": "centralus",
  "macAddress": "00-0D-3A-91-A9-60",
  "name": "TestNICWeb1",
  "networkSecurityGroup": {
    "defaultSecurityRules": null,
    "etag": null,
    "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
    "location": null,
    "name": null,
    "networkInterfaces": null,
    "provisioningState": null,
    "resourceGroup": "RG-NSG",
    "resourceGuid": null,
    "securityRules": null,
    "subnets": null,
    "tags": null,
    "type": null
  },
  "primary": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "RG-NSG",
  "resourceGuid": "<guid>",
  "tags": {},
  "type": "Microsoft.Network/networkInterfaces",
  "virtualMachine": null
}
```

## <a name="dissociate-an-nsg-from-a-nic"></a>Annullare l'associazione tra un NSG e una NIC

Per annullare l'associazione del gruppo di sicurezza di rete **NSG-FrontEnd** dalla scheda di interfaccia di rete **TestNICWeb1** eseguire di nuovo il comando [az network nsg rule update](/cli/azure/network/nsg/rule#update) ma sostituire l'argomento `--network-security-group` con una stringa vuota (`""`).

```azurecli
az network nic update --resource-group RG-NSG --name TestNICWeb3 --network-security-group ""
```

Nell'output la chiave `networkSecurityGroup` è impostata su null.

## <a name="dissociate-an-nsg-from-a-subnet"></a>Annullare l'associazione tra un NSG e una subnet
Per annullare l'associazione del gruppo di sicurezza di rete **NSG-FrontEnd** dalla subnet **FrontEnd** eseguire il comando [az network nsg rule update](/cli/azure/network/nsg/rule#update), ma sostituire l'argomento `--network-security-group` con una stringa vuota (`""`).

```azurecli
az network vnet subnet update \
--resource-group RG-NSG \
--vnet-name testvnet \
--name FrontEnd \
--network-security-group ""
```

Nell'output la chiave `networkSecurityGroup` è impostata su null.

## <a name="associate-an-nsg-to-a-subnet"></a>Associare un gruppo di sicurezza di rete a una subnet
Per associare di nuovo il gruppo di sicurezza di rete **NSG-FrontEnd** alla subnet **FrontEnd**, eseguire il comando seguente:

```azurecli
az network vnet subnet update \
--resource-group RG-NSG \
--vnet-name testvnet \
--name FrontEnd \
--network-security-group NSG-FrontEnd
```

Nell'output il valore della chiave `networkSecurityGroup` è simile al seguente:

```json
"networkSecurityGroup": {
    "defaultSecurityRules": null,
    "etag": null,
    "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
    "location": null,
    "name": null,
    "networkInterfaces": null,
    "provisioningState": null,
    "resourceGroup": "RG-NSG",
    "resourceGuid": null,
    "securityRules": null,
    "subnets": null,
    "tags": null,
    "type": null
  }
  ```

## <a name="delete-an-nsg"></a>Eliminare un gruppo di sicurezza di rete
È possibile eliminare un NSG solo se non è associato ad alcuna risorsa. Per eliminare un NSG, seguire questa procedura.

1. Per controllare le risorse associate a un NSG, eseguire `azure network nsg show` come illustrato in [Visualizzare le associazioni di NSG](#View-NSGs-associations).
2. Se l'NSG è associato a una o più NIC, eseguire `azure network nic set` come illustrato in [Annullare l'associazione tra un NSG e una NIC](#Dissociate-an-NSG-from-a-NIC) per ognuna delle NIC. 
3. Se l'NSG è associato a una o più subnet, eseguire `azure network vnet subnet set` come illustrato in [Annullare l'associazione tra un NSG e una subnet](#Dissociate-an-NSG-from-a-subnet) per ognuna delle subnet.
4. Per eliminare l'NSG, eseguire il comando seguente:

    ```azurecli
    az network nsg delete --resource-group RG-NSG --name NSG-FrontEnd
    ```
## <a name="next-steps"></a>Passaggi successivi
* [Abilitare la registrazione](virtual-network-nsg-manage-log.md) per gli NSG.


