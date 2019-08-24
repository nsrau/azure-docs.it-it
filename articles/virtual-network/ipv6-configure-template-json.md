---
title: Distribuire un'applicazione IPv6 dual stack con Load Balancer di base in rete virtuale di Azure-modello di Resource Manager (anteprima)
titlesuffix: Azure Virtual Network
description: Questo articolo illustra come distribuire un'applicazione IPv6 dual stack in rete virtuale di Azure usando Azure Resource Manager modelli di VM.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/26/2019
ms.author: kumud
ms.openlocfilehash: b397c874045a89f5992aeadacfbbd4434a486977
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012828"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-basic-load-balancer-in-azure---template-preview"></a>Distribuire un'applicazione IPv6 dual stack con Load Balancer di base in Azure-template (anteprima)

Questo articolo fornisce un elenco delle attività di configurazione di IPv6 con la parte del modello di macchina virtuale Azure Resource Manager che si applica a. Usare il modello descritto in questo articolo per distribuire un'applicazione dual stack (IPv4 + IPv6) con Load Balancer di base che include una rete virtuale dual stack con subnet IPv4 e IPv6, una Load Balancer di base con Dual (IPv4 + IPv6) configurazioni front-end, VM con Nic con una doppia configurazione IP, un gruppo di sicurezza di rete e indirizzi IP pubblici.

Per distribuire un'applicazione dual stack (IPV4 + IPv6) con Load Balancer Standard, vedere [distribuire un'applicazione dual stack IPv6 con Load Balancer standard-template](ipv6-configure-standard-load-balancer-template-json.md).

## <a name="required-configurations"></a>Configurazioni obbligatorie

Cercare le sezioni del modello nel modello per vedere dove devono essere eseguite.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>AddressSpace IPv6 per la rete virtuale

Sezione del modello da aggiungere:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>Subnet IPv6 all'interno della rete virtuale IPv6 addressSpace

Sezione del modello da aggiungere:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>Configurazione IPv6 per la scheda di interfaccia di rete

Sezione del modello da aggiungere:
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

### <a name="ipv6-network-security-group-nsg-rules"></a>Regole del gruppo di sicurezza di rete IPv6 (NSG)

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

Se si usa un'appliance virtuale di rete, aggiungere route IPv6 nella tabella di route. In caso contrario, questa configurazione è facoltativa.

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

### <a name="ipv6-internet-access-for-the-virtual-network"></a>Accesso Internet IPv6 per la rete virtuale

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

### <a name="ipv6-front-end-for-load-balancer"></a>Front-end IPv6 per Load Balancer

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>Pool di indirizzi back-end IPv6 per Load Balancer

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

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>Regole del servizio di bilanciamento del carico IPv6 per associare le porte in ingresso e in uscita

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

## <a name="sample-vm-template-json"></a>Esempio di modello di macchina virtuale JSON
Per distribuire un'applicazione IPv6 dual stack con Load Balancer di base in rete virtuale di Azure usando Azure Resource Manager modello, vedere il modello di esempio [qui](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/).

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare informazioni dettagliate sui prezzi per [gli indirizzi IP pubblici](https://azure.microsoft.com/pricing/details/ip-addresses/), la [larghezza di banda di rete](https://azure.microsoft.com/pricing/details/bandwidth/)o [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
