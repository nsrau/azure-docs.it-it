---
title: Creare una VM con un indirizzo IP pubblico statico - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come creare una VM con un indirizzo IP pubblico statico usando l'interfaccia della riga di comando di Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c50f685745a645b5fbe383a5fe4726faa0e36345
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-cli"></a>Creare una VM con un indirizzo IP pubblico statico usando l'interfaccia della riga di comando di Azure

> [!div class="op_single_selector"]
> * [Azure portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Interfaccia della riga di comando di Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [Modello](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (Classic)](virtual-networks-reserved-public-ip.md) (PowerShell (classico))

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Questo articolo illustra l'uso del modello di distribuzione Resource Manager che Microsoft consiglia di usare invece del modello di distribuzione classica per le distribuzioni più recenti.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name = "create"></a>Creare la VM

I valori in "" per le variabili nei passaggi seguenti creano le risorse con le impostazioni usate nello scenario. Sostituire i valori in base alle esigenze specifiche dell'ambiente.

1. Installare l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2), se non è già stata installata.
2. Creare una coppia di chiavi SSH pubblica e privata per le VM Linux completando i passaggi descritti in [Creare una coppia di chiavi SSH pubblica e privata per le VM Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Da una shell dei comandi accedere con il comando `az login`.
4. Creare la VM eseguendo lo script seguente in un computer Linux o Mac. Le risorse di indirizzo IP pubblico di Azure, rete virtuale, interfaccia di rete e VM devono essere tutte presenti nella stessa località. Lo script seguente esamina un caso in cui tutte le risorse sono incluse nello stesso gruppo di risorse, anche se questo non è un requisito.

```bash
RgName="IaaSStory"
Location="westus"

# Create a resource group.

az group create \
--name $RgName \
--location $Location

# Create a public IP address resource with a static IP address using the --allocation-method Static option.
# If you do not specify this option, the address is allocated dynamically. The address is assigned to the
# resource from a pool of IP adresses unique to each Azure region. The DnsName must be unique within the
# Azure location it's created in. Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653#
# that lists the ranges for each region.

PipName="PIPWEB1"
DnsName="iaasstoryws1"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static \
--dns-name $DnsName

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

# Replace the value for the OsImage variable with a value for *urn* from the output returned by entering
# the `az vm image list` command. 

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
--ssh-key-value $SshKeyValue
# If creating a Windows VM, remove the previous line and you'll be prompted for the password you want to configure for the VM.
```

Oltre a creare una VM, lo script crea:
- Un unico disco gestito Premium per impostazione predefinita, ma sono disponibili altre opzioni per il tipo di disco che è possibile creare. Leggere [Creare una VM Linux usando l'interfaccia della riga di comando di Azure 2.0](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per informazioni dettagliate.
- Risorse di rete virtuale, subnet, scheda di interfaccia di rete e indirizzo IP pubblico. In alternativa, è possibile usare le risorse *esistenti* di rete virtuale, subnet, scheda di interfaccia di rete o indirizzo IP pubblico. Per informazioni su come usare le risorse di rete esistenti anziché creare risorse aggiuntive, immettere `az vm create -h`.

## <a name = "validate"></a>Convalidare la creazione della VM e l'indirizzo IP pubblico

1. Immettere il comando `az resource list --resouce-group IaaSStory --output table` per visualizzare un elenco di risorse create dallo script. L'output restituito dovrebbe contenere cinque risorse: interfaccia di rete, disco, indirizzo IP pubblico, rete virtuale e una macchina virtuale.
2. Immettere il comando `az network public-ip show --name PIPWEB1 --resource-group IaaSStory --output table`. Nell'output restituito prendere nota del valore di **IpAddress** e che il valore di **PublicIpAllocationMethod** è *Static*.
3. Prima di eseguire il comando seguente, rimuovere <>, sostituire *Username* con il nome usato per la variabile **Username** nello script, quindi sostituire *ipAddress* con l'**ipAddress** usato nel passaggio precedente. Eseguire il comando seguente per effettuare la connessione alla VM: `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`. 

## <a name= "clean-up"></a>Rimuovere la VM e le risorse associate

È consigliabile eliminare le risorse create in questo esercizio se non si intende usarle in produzione. Le risorse di VM, indirizzo IP pubblico e disco comportano spese finché si esegue il provisioning. Per rimuovere le risorse create in questo esercizio, completare i passaggi seguenti:

1. Eseguire il comando `az resource list --resource-group IaaSStory` per visualizzare le risorse nel gruppo di risorse.
2. Verificare che nel gruppo di risorse non siano presenti altre risorse oltre a quelle create dallo script in questo articolo. 
3. Per eliminare tutte le risorse create in questo esercizio, eseguire il comando `az group delete -n IaaSStory`. Il comando elimina il gruppo di risorse e tutte le risorse in esso contenute.

## <a name="next-steps"></a>Passaggi successivi

Qualsiasi traffico di rete può scorrere da e verso la VM creata in questo articolo. È possibile definire regole in entrata e in uscita all'interno di un gruppo di sicurezza di rete che limitano il traffico in ingresso e in uscita dall'interfaccia di rete, la subnet o entrambe le risorse. Per altre informazioni sui gruppi di sicurezza di rete, vedere l'articolo [Panoramica del gruppi di sicurezza di rete](virtual-networks-nsg.md).