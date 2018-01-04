---
title: Creare una rete virtuale - interfaccia della riga di comando di Azure | Documentazione Microsoft
description: Informazioni su come creare una rete virtuale usando l'interfaccia della riga di comando di Azure.
services: virtual-network
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 75966bcc-0056-4667-8482-6f08ca38e77a
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 659a791124eab002290ac0b7f0898cf1c06c2951
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Creare una rete virtuale usando l'interfaccia della riga di comando di Azure

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure offre due modelli di distribuzione, ovvero Azure Resource Manager e la distribuzione classica. Microsoft consiglia di creare le risorse tramite il modello di distribuzione Resource Manager. Per altre informazioni sulle differenze tra i due modelli, leggere l'articolo [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md) (Informazioni sui modelli di distribuzione di Azure).

È anche possibile creare una rete virtuale tramite Resource Manager usando altri strumenti oppure tramite il modello di distribuzione classica selezionando un'opzione diversa dall'elenco seguente:

> [!div class="op_single_selector"]
> * [di Microsoft Azure](virtual-networks-create-vnet-arm-pportal.md)
> * [PowerShell](virtual-networks-create-vnet-arm-ps.md)
> * [CLI](virtual-networks-create-vnet-arm-cli.md)
> * [Modello](virtual-networks-create-vnet-arm-template-click.md)
> * [Portale (versione classica)](virtual-networks-create-vnet-classic-pportal.md)
> * [PowerShell (versione classica)](virtual-networks-create-vnet-classic-netcfg-ps.md)
> * [Interfaccia della riga di comando (versione classica)](virtual-networks-create-vnet-classic-cli.md)

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Per creare una rete virtuale usando l'interfaccia della riga di comando di Azure, seguire questa procedura:

1. Installare e configurare l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) più aggiornata e accedere a un account di Azure usando il comando [az login](/cli/azure/#login).

2. Creare un gruppo di risorse per la rete virtuale usando il comando [az group create](/cli/azure/group#create) con gli argomenti `--name` e `--location`:

    ```azurecli
    az group create --name TestRG --location centralus
    ```

3. Creare una rete virtuale e subnet:

    ```azurecli
    az network vnet create \
    --name TestVNet \
    --resource-group TestRG \
    --location centralus \
    --address-prefix 192.168.0.0/16 \
    --subnet-name FrontEnd \
    --subnet-prefix 192.168.1.0/24
    ```

    Output previsto:
    
    ```json
    {
        "newVNet": {
            "addressSpace": {
            "addressPrefixes": [
            "192.168.0.0/16"
            ]
            },
            "dhcpOptions": {
            "dnsServers": []
            },
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>",
            "subnets": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                "name": "FrontEnd",
                "properties": {
                "addressPrefix": "192.168.1.0/24",
                "provisioningState": "Succeeded"
                },
                "resourceGroup": "TestRG"
            }
            ]
            }
    }
    ```

    Parametri utilizzati:

    - `--name TestVNet`: nome della rete virtuale da creare.
    - `--resource-group TestRG`: nome del gruppo di risorse che controlla la risorsa. 
    - `--location centralus`: posizione in cui eseguire la distribuzione.
    - `--address-prefix 192.168.0.0/16`: prefisso e blocco dell'indirizzo.  
    - `--subnet-name FrontEnd`: nome della subnet.
    - `--subnet-prefix 192.168.1.0/24`: prefisso e blocco dell'indirizzo.

    Per elencare le informazioni di base da usare nel comando successivo, è possibile eseguire query sulla rete virtuale mediante un [filtro query](/cli/azure/query-az-cli2):

    ```azurecli
    az network vnet list --query '[?name==`TestVNet`].{Where:location,Name:name,Group:resourceGroup}' -o table
    ```

    L'output ottenuto sarà il seguente:

        Where      Name      Group

        centralus  TestVNet  TestRG

4. Creare una subnet:

    ```azurecli
    az network vnet subnet create \
    --address-prefix 192.168.2.0/24 \
    --name BackEnd \
    --resource-group TestRG \
    --vnet-name TestVNet
    ```

    Output previsto:

    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid> \"",
    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
    "ipConfigurations": null,
    "name": "BackEnd",
    "networkSecurityGroup": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "TestRG",
    "resourceNavigationLinks": null,
    "routeTable": null
    }
    ```

    Parametri utilizzati:

    - `--address-prefix 192.168.2.0/24`: blocco CIDR della subnet.
    - `--name BackEnd`: nome della nuova subnet.
    - `--resource-group TestRG`: gruppo di risorse.
    - `--vnet-name TestVNet`: nome della rete virtuale proprietaria.

5. Eseguire query sulle proprietà della nuova rete virtuale:

    ```azurecli
    az network vnet show \
    -g TestRG \
    -n TestVNet \
    --query '{Name:name,Where:location,Group:resourceGroup,Status:provisioningState,SubnetCount:subnets | length(@)}' \
    -o table
    ```

    Output previsto:

        Name      Where      Group    Status       SubnetCount

        TestVNet  centralus  TestRG   Succeeded              2

6. Eseguire query sulle proprietà delle subnet:

    ```azurecli
    az network vnet subnet list \
    -g TestRG \
    --vnet-name testvnet \
    --query '[].{Name:name,CIDR:addressPrefix,Status:provisioningState}' \
    -o table
    ```

    Output previsto:

        Name      CIDR            Status

        FrontEnd  192.168.1.0/24  Succeeded
        BackEnd   192.168.2.0/24  Succeeded

## <a name="next-steps"></a>Passaggi successivi

Leggere le informazioni su come connettere:

- Una macchina virtuale (VM) a una rete virtuale nell'articolo [Creare una VM Linux](../virtual-machines/linux/quick-create-cli.md). Anziché creare una rete virtuale e una subnet, come illustrato nelle procedure degli articoli, è possibile selezionare una rete virtuale e una subnet esistenti a cui connettere una VM.
- La rete virtuale ad altre reti virtuali nell'articolo [Connettere reti virtuali](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
- La rete virtuale a una rete locale tramite una rete privata virtuale (VPN) da sito a sito o il circuito ExpressRoute Per le informazioni su come procedere, leggere [Connect a VNet to an on-premises network using a site-to-site VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) (Connettere una rete virtuale a una rete locale tramite una VPN da sito a sito) e [Collegare una rete virtuale a un circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).