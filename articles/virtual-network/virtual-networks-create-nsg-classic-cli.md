---
title: Creare un gruppo di sicurezza di rete (versione classica) con l'interfaccia della riga di comando classica di Azure | Microsoft Docs
description: Informazioni su come creare e distribuire un gruppo di sicurezza di rete (versione classica) con l'interfaccia della riga di comando classica di Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 17d98950-5fbb-4653-bef6-d822ab37541e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: 454d17ac4f4c3723d7b7ee1ac2c639b885f3fff9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309981"
---
# <a name="create-a-network-security-group-classic-using-the-azure-classic-cli"></a>Creare un gruppo di sicurezza di rete (versione classica) con l'interfaccia della riga di comando classica di Azure
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

In questo articolo viene illustrato il modello di distribuzione classica. È inoltre possibile [creare gruppi di sicurezza di rete nel modello di distribuzione di Gestione risorse](tutorial-filter-network-traffic-cli.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

I comandi di esempio dell'interfaccia della riga di comando di Azure riportati di seguito prevedono l'uso di un ambiente semplice già creato in base allo scenario. Se si desidera eseguire i comandi illustrati in questo documento, creare prima di tutto l'ambiente di testing [creando una rete virtuale](virtual-networks-create-vnet-classic-cli.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Creare un gruppo di sicurezza di rete per la subnet front-end

1. Se non si è mai usata l'interfaccia della riga di comando di Azure, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](/cli/azure/install-cli-version-1.0).
2. Passare alla modalità classica:

    ```azurecli
    azure config mode asm
    ```   

3. Creare un gruppo di sicurezza di rete:
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. Creare una regola di sicurezza che consenta l'accesso alla porta 3389 (RDP) da Internet:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. Creare una regola di sicurezza che consenta l'accesso alla porta 80 (HTTP) da Internet:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. Associare il gruppo di sicurezza di rete alla subnet front-end:
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>Creare il gruppo di sicurezza di rete per la subnet back-end

1. Creare il gruppo di sicurezza di rete:
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. Creare una regola che consenta l'accesso alla porta 1433 (SQL) dalla subnet front-end:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. Creare una regola che neghi l'accesso a Internet:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. Associare il gruppo di sicurezza di rete alla subnet back-end:
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```