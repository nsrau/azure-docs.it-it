---
title: Distribuire un'applicazione dual-stack IPv6 nella rete virtuale di Azure - modello di Resource Manager (anteprima)
titlesuffix: Azure Virtual Network
description: Questo articolo illustra come distribuisce un'applicazione di dual-stack IPv6 nella rete virtuale di Azure usando i modelli di VM di Azure Resource Manager.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: ae90bc4a12763803f38224d917c4644a68ae7d6b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130933"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---template-preview"></a>Distribuire un'applicazione di dual-stack IPv6 in Azure - modello (anteprima)

Questo articolo fornisce un elenco delle attività di configurazione di IPv6 con la parte del modello di VM di Azure Resource Manager che si applica a. Usare il modello descritto in questo articolo per distribuire un'applicazione dual stack (IPv4 + IPv6) in Azure che include una dual-stack rete virtuale con subnet IPv4 e IPv6, un servizio di bilanciamento del carico con configurazioni front-end dual (IPv4 + IPv6), le macchine virtuali con schede di rete che abbia un IP doppia Configurazione gruppo di sicurezza di rete e indirizzi IP pubblici. 

## <a name="required-configurations"></a>Configurazioni necessarie

Cercare le sezioni del modello nel modello per vedere in cui dovranno essere eseguite.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>AddressSpace IPv6 per la rete virtuale

Sezione di modello da aggiungere:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>Subnet IPv6 all'interno di addressSpace della rete virtuale IPv6

Sezione di modello da aggiungere:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>Configurazione di IPv6 per l'interfaccia di rete

Sezione di modello da aggiungere:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>Regole di gruppo (NSG) di sicurezza di rete IPv6

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "ace:cab:deca:deed::/64",
              "destinationAddressPrefix": "cab:ace:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>Configurazione condizionale

Se si usa un'appliance virtuale di rete, aggiungere le route IPv6 nella tabella di Route. In caso contrario, questa configurazione è facoltativa.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "ace:cab:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "deca:cab:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>Configurazione facoltativa

### <a name="ipv6-internet-access-for-the-virtual-network"></a>Accesso a Internet IPv6 per la rete virtuale

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>Indirizzi IP pubblici IPv6

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>IPv6 front-end di Load Balancer

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>Pool di indirizzi Back-end IPv6 per il bilanciamento del carico

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>IPv6 regole di load balancer per associare le porte in ingresso e in uscita

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>Modello di macchina virtuale di esempio JSON
Fare clic su [qui](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/) per distribuire un'applicazione dual-stack IPv6 nella rete virtuale di Azure usando il modello di Azure Resource Manager.

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare i dettagli sui prezzi relativi [gli indirizzi IP pubblici](https://azure.microsoft.com/pricing/details/ip-addresses/), [larghezza di banda di rete](https://azure.microsoft.com/pricing/details/bandwidth/), o [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
