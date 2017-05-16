---
title: Configurare indirizzi IP privati per le VM - interfaccia della riga di comando di Azure 2.0 | Documentazione Microsoft
description: Informazioni su come configurare indirizzi IP privati per le macchine virtuali usando l&quot;interfaccia della riga di comando di Azure 2.0.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 071156367c1f819a00d31f1d0335e301391fda81
ms.lasthandoff: 04/07/2017


---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli-20"></a>Configurare indirizzi IP privati per una macchina virtuale usando l'interfaccia della riga di comando di Azure 2.0

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]


## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività 

È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando: 

- [Interfaccia della riga di comando di Azure 1.0](virtual-networks-static-private-ip-cli-nodejs.md): l'interfaccia della riga di comando per i modelli di distribuzione classici e di gestione delle risorse 
- [Interfaccia della riga di comando di Azure 2.0](#specify-a-static-private-ip-address-when-creating-a-vm): interfaccia avanzata per il modello di distribuzione di gestione delle risorse (questo articolo)

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Questo articolo illustra il modello di distribuzione Gestione risorse. È anche possibile [gestire un indirizzo IP statico privato nel modello di distribuzione classico](virtual-networks-static-private-ip-classic-cli.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> I comandi di esempio dell'interfaccia della riga di comando di Azure 2.0 seguenti prevedono un ambiente semplice già creato. Se si desidera eseguire i comandi illustrati in questo documento, creare innanzitutto l'ambiente di prova descritto in [creare una rete virtuale](virtual-networks-create-vnet-arm-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Specificare un indirizzo IP statico privato durante la creazione di una macchina virtuale

Per creare una VM denominata *DNS01* nella subnet *FrontEnd* di una rete virtuale denominata *TestVNet* con un indirizzo IP statico privato di *192.168.1.101*, seguire la procedura seguente:

1. Se questa operazione non è stata ancora eseguita, installare e configurare l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e accedere a un account Azure usando il comando [az login](/cli/azure/#login). 

2. Creare un IP pubblico per la VM con il comando [azure network public-ip create](/cli/azure/network/public-ip#create). Nell'elenco riportato dopo l'output sono indicati i parametri usati.

    > [!NOTE]
    > Potrebbe essere possibile o necessario usare valori diversi per gli argomenti in questo passaggio e nei passaggi successivi, a seconda dell'ambiente.
   
    ```azurecli
    az network public-ip create \
    --name TestPIP \
    --resource-group TestRG \
    --location centralus \
    --allocation-method Static
    ```

    Output previsto:
   
   ```json
   {
        "publicIp": {
            "idleTimeoutInMinutes": 4,
            "ipAddress": "52.176.43.167",
            "provisioningState": "Succeeded",
            "publicIPAllocationMethod": "Static",
            "resourceGuid": "79e8baa3-33ce-466a-846c-37af3c721ce1"
        }
    }
    ```

   * `--resource-group`: nome del gruppo di risorse in cui creare l'indirizzo IP pubblico.
   * `--name`: nome dell'indirizzo IP pubblico.
   * `--location`: area di Azure in cui creare l'indirizzo IP pubblico.

3. Eseguire il comando [az network nic create](/cli/azure/network/nic#create) per creare una scheda di interfaccia di rete con indirizzo IP privato statico. Nell'elenco riportato dopo l'output sono indicati i parametri usati. 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    Output previsto:
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    Parametri

    * `--private-ip-address`: indirizzo IP privato statico per la scheda di interfaccia di rete.
    * `--vnet-name`: nome della rete virtuale in cui creare la scheda di interfaccia di rete.
    * `--subnet`: nome della subnet in cui creare la scheda di interfaccia di rete.

4. Eseguire il comando [azure vm create](/cli/azure/vm/nic#create) per creare la VM usando l'indirizzo IP pubblico e il gruppo NIC creati in precedenza. Nell'elenco riportato dopo l'output sono indicati i parametri usati.
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    Output previsto:
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   Parametri diversi dai parametri di base [az vm create](/cli/azure/vm#create).

   * `--nics`: nome della scheda di interfaccia di rete a cui è collegata la VM.
   

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Recuperare le informazioni relative all'indirizzo IP privato statico per una macchina virtuale

Per visualizzare l'indirizzo IP privato statico creato, eseguire il comando seguente dell'interfaccia della riga di comando di Azure e osservare i valori di *Private IP alloc-method* e *Private IP address*:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Output previsto:

```json
"192.168.1.101"
```

Per visualizzare informazioni sull'IP specifico della scheda di interfaccia di rete, eseguire una query sulla scheda di interfaccia di rete specifica:

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

Il risultato sarà simile al seguente:

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Rimuovere un indirizzo IP statico privato da una macchina virtuale

Non è possibile rimuovere un indirizzo IP privato statico da una scheda di interfaccia di rete nell'interfaccia della riga di comando per le distribuzioni di Resource Manager. È necessario:
- Creare una nuova scheda di interfaccia di rete che usa un indirizzo IP dinamico
- Impostare la scheda di rete della VM sulla scheda di rete appena creata. 

Per cambiare la scheda di interfaccia di rete per la VM usata nei comandi precedenti, seguire questa procedura.

1. Eseguire il comando **azure network nic create** per creare una nuova scheda di interfaccia di rete usando l'allocazione di IP dinamica con un nuovo indirizzo IP. Si noti che poiché non è specificato alcun indirizzo IP, il metodo di allocazione è **dinamico**.

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    Output previsto:

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. Eseguire il comando **azure vm set** per cambiare il gruppo NIC usato dalla VM.
   
    ```azurecli
    azure vm set -g TestRG -n DNS01 -N TestNIC2
    ```

    Output previsto:
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > Se le dimensioni della VM consentono di includere più schede di interfacce di rete, eseguire il comando **azure network nic delete** per eliminare la vecchia scheda.
   
## <a name="next-steps"></a>Passaggi successivi
* Informazioni su [indirizzi IP pubblici riservati](virtual-networks-reserved-public-ip.md) .
* Informazioni su [indirizzi IP pubblici a livello di istanza (ILPIP)](virtual-networks-instance-level-public-ip.md) .
* Consultare le [API REST dell'indirizzo IP riservato](https://msdn.microsoft.com/library/azure/dn722420.aspx).


