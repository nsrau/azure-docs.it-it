---
title: Distribuire un'applicazione Dual Stack IPv6 con Basic Load Balancer nella rete virtuale di Azure - modello Resource ManagerDeploy an IPv6 dual stack application with Basic Load Balancer in Azure virtual network - Resource Manger template
titlesuffix: Azure Virtual Network
description: Questo articolo illustra come distribuire un'applicazione dual stack IPv6 nella rete virtuale di Azure usando i modelli di macchina virtuale di Azure Resource Manager.This article shows how to deploy an IPv6 dual stack application in Azure virtual network using Azure Resource Manager VM templates.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 7c2f66d92597801aa7c6b0b32f86f594d3effb05
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420607"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-basic-load-balancer-in-azure---template"></a>Distribuire un'applicazione Dual Stack IPv6 con Basic Load Balancer in Azure - TemplateDeploy an IPv6 dual stack application with Basic Load Balancer in Azure - Template

Questo articolo fornisce un elenco di attività di configurazione IPv6 con la parte del modello di macchina virtuale di Azure Resource Manager a cui si applica. Utilizzare il modello descritto in questo articolo per distribuire un'applicazione dual stack (IPv4 e IPv6) con Basic Load Balancer che include una rete virtuale dual stack con subnet IPv4 e IPv6, un servizio di bilanciamento del carico di base con configurazioni front-end dual (IPv4 e IPv6), macchine virtuali con schede di interfaccia di rete con una configurazione IP duale, un gruppo di sicurezza di rete e indirizzi IP pubblici.

Per distribuire un'applicazione dual stack (IPV4 e IPv6) utilizzando Load Balancer Standard, vedere [Deploy an IPv6 dual stack application with Standard Load Balancer - Template](ipv6-configure-standard-load-balancer-template-json.md).

## <a name="required-configurations"></a>Configurazioni richieste

Cercare le sezioni del modello nel modello per vedere dove dovrebbero verificarsi.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>Indirizzo IPv6Spazio per la rete virtuale

Sezione Modello da aggiungere:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>Subnet IPv6 all'interno dell'indirizzo della rete virtuale IPv6Spazio

Sezione Modello da aggiungere:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>Configurazione IPv6 per la scheda NIC

Sezione Modello da aggiungere:
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

### <a name="ipv6-network-security-group-nsg-rules"></a>Regole del gruppo di sicurezza di rete IPv6

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

Se si usa un'appliance virtuale di rete, aggiungere route IPv6 nella tabella route. In caso contrario, questa configurazione è facoltativa.

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

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>Regole del bilanciamento del carico IPv6 per associare porte in ingresso e in uscita

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

## <a name="sample-vm-template-json"></a>Modello di macchina virtuale di esempio JSONSample VM template JSON
Per distribuire un'applicazione Dual Stack IPv6 con Load Balancer di base nella rete virtuale di Azure usando il modello di Azure Resource Manager, vedere il modello di esempio [qui.](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/)

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare informazioni dettagliate sui prezzi per [gli indirizzi IP pubblici,](https://azure.microsoft.com/pricing/details/ip-addresses/)la [larghezza di banda di rete](https://azure.microsoft.com/pricing/details/bandwidth/)o [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
