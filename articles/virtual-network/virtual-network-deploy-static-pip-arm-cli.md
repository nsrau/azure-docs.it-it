---
title: Creare una VM con un indirizzo IP pubblico statico - interfaccia della riga di comando di Azure 2.0 | Documentazione Microsoft
description: Informazioni su come creare una VM con un indirizzo IP pubblico statico usando l&quot;interfaccia della riga di comando di Azure 2.0.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: e7874e7d86f75846c452d9863d5604982e9ce50b
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-cli-20"></a>Creare una VM con un IP pubblico statico usando l'interfaccia della riga di comando di Azure 2.0

> [!div class="op_single_selector"]
- [Portale di Azure](virtual-network-deploy-static-pip-arm-portal.md)
- [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
- [Interfaccia della riga di comando di Azure 2.0](virtual-network-deploy-static-pip-arm-cli.md)
- [Interfaccia della riga di comando di Azure 1.0](virtual-network-deploy-static-pip-cli-nodejs.md)
- [Modello](virtual-network-deploy-static-pip-arm-template.md)
- [PowerShell (Classic)](virtual-networks-reserved-public-ip.md) (PowerShell (classico))

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Questo articolo illustra l'uso del modello di distribuzione Resource Manager che Microsoft consiglia di usare invece del modello di distribuzione classica per le distribuzioni più recenti.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="a-name--createacreate-the-vm"></a><a name = "create"></a>Creare la VM

È possibile completare questa attività usando l'interfaccia della riga di comando di Azure 2.0 (questo articolo) o l'[interfaccia della riga di comando di Azure 1.0](virtual-network-deploy-static-pip-cli-nodejs.md). I valori in "" per le variabili nei passaggi seguenti creano le risorse con le impostazioni usate nello scenario. Sostituire i valori in base alle esigenze specifiche dell'ambiente.

1. Installare l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2), se non è già stata installata.
2. Creare una coppia di chiavi SSH pubblica e privata per le VM Linux completando i passaggi descritti in [Creare una coppia di chiavi SSH pubblica e privata per le VM Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Da una shell dei comandi accedere con il comando `az login`.
4. Creare la VM eseguendo lo script seguente in un computer Linux o Mac. Le risorse di indirizzo IP pubblico di Azure, rete virtuale, interfaccia di rete e VM devono essere tutte presenti nella stessa località. Lo script seguente esamina un caso in cui tutte le risorse sono incluse nello stesso gruppo di risorse, anche se questo non è un requisito.

    ```azurecli
    #!/bin/sh

    RgName="IaaSStory"
    Location="westus"
    az group create --name $RgName --location $Location

    # Create a public IP address resource with a static IP address
    PipName="PIPWEB1"
    # Note: The value below must be unique within the azure location it's created in.
    DnsName="iaasstoryws1"

    az network public-ip create \
    --name $PipName \
    --resource-group $RgName \
    --location $Location \

    # The following option allocates a static public IP address to the resource. If you do not specify it, the address is
    # allocated dynamically. The address is assigigned to the resource from a pool of IP adresses unique to each Azure regions.
    # Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653 to see the ranges for each region.
    --allocation-method Static \

    --dns-name $DnsName \

    # Create a virtual network with one subnet

    VnetName="TestVNet"
    VnetPrefix="192.168.0.0/16"
    SubnetName="FrontEnd"
    SubnetPrefix="192.168.1.0/24"

    az network vnet create \
    --name $VnetName \
    --resource-group $RgName \
    --location $Location \
    --address-prefix $VnetPrefix \
    --subnet-name $SubnetName \
    --subnet-prefix $SubnetPrefix

    # Create a network interface connected to the VNet with a static private IP address and associate the public IP address
    # resource to the NIC.
    NicName="NICWEB1"
    PrivateIpAddress="192.168.1.101"

    az network nic create \
    --name $NicName \
    --resource-group $RgName \
    --location $Location \
    --subnet $SubnetName \
    --vnet-name $VnetName \
    --private-ip-address $PrivateIpAddress \
    --public-ip-address $PipName

    # Create a new VM with the NIC
    VmName="WEB1"
    
    # Replace the value for the VmSize variable with a value from the
    # https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article.
    VmSize="Standard_DS1"

    # Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
    # `az vm image list` command. 
    OsImage="credativ:Debian:8:latest"
    
    Username='adminuser'
    
    # Replace the following value with the path to your public key file.
    SshKeyValue="~/.ssh/id_rsa.pub"

    az vm create \
    --name $VmName \
    --resource-group $RgName \
    --image $OsImage \
    --location $Location \
    --size $VmSize \
    --nics $NicName \
    --admin-username $Username \

    # If creating a Windows VM, remove the next line and you'll be prompted for the password you want to configure for the VM.
    --ssh-key-value $SshKeyValue
    ```

    Oltre a creare una VM, lo script crea:
    - Un unico disco gestito Premium per impostazione predefinita, ma sono disponibili altre opzioni per il tipo di disco che è possibile creare. Leggere [Creare una VM Linux usando l'interfaccia della riga di comando di Azure 2.0](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per informazioni dettagliate.
    - Risorse di rete virtuale, subnet, scheda di interfaccia di rete e indirizzo IP pubblico. In alternativa, è possibile usare le risorse *esistenti* di rete virtuale, subnet, scheda di interfaccia di rete o indirizzo IP pubblico. Per informazioni su come usare le risorse di rete esistenti anziché creare risorse aggiuntive, immettere `az vm create -h`.

## <a name="a-name--validateavalidate-vm-creation-and-public-ip-address"></a><a name = "validate"></a>Convalidare la creazione della VM e l'indirizzo IP pubblico

1. Immettere il comando `az resource list --resouce-group IaaSStory --output table` per visualizzare un elenco di risorse create dallo script. L'output restituito dovrebbe contenere cinque risorse: interfaccia di rete, disco, indirizzo IP pubblico, rete virtuale e una macchina virtuale.
2. Immettere il comando `az network public-ip show --name PIPWEB1 --resource-group IaaSStory --output table`. Nell'output restituito prendere nota del valore di **IpAddress** e che il valore di **PublicIpAllocationMethod** è *Static*.
3. Prima di eseguire il comando seguente, rimuovere <>, sostituire *Username* con il nome usato per la variabile **Username** nello script, quindi sostituire *ipAddress* con l'**ipAddress** usato nel passaggio precedente. Eseguire il comando seguente per effettuare la connessione alla VM: `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`. 

## <a name="a-name-clean-uparemove-the-vm-and-associated-resources"></a><a name= "clean-up"></a>Rimuovere la VM e le risorse associate

Se è stato creato un gruppo di risorse esclusivamente per completare i passaggi descritti in questo articolo, è possibile rimuovere tutte le risorse eliminando il gruppo di risorse con il comando `az group delete -n IaaSStory`.

>[!WARNING]
>Prima di eliminare il gruppo di risorse, verificare che in esso non siano presenti altre risorse oltre a quelle create dallo script in questo articolo. Eseguire il comando `az resource list --resouce-group IaaSStory` per visualizzare le risorse nel gruppo di risorse.

È consigliabile eliminare le risorse se la VM non verrà usata in produzione. Le risorse di VM, indirizzo IP pubblico e disco comportano spese finché si esegue il provisioning. 

## <a name="next-steps"></a>Passaggi successivi

Qualsiasi traffico di rete può scorrere da e verso la VM creata in questo articolo. È possibile definire regole in entrata e in uscita all'interno di un gruppo di sicurezza di rete che limitano il traffico in ingresso e in uscita dall'interfaccia di rete, la subnet o entrambe le risorse. Per altre informazioni sui gruppi di sicurezza di rete, vedere l'articolo [Panoramica del gruppi di sicurezza di rete](virtual-networks-nsg.md).
