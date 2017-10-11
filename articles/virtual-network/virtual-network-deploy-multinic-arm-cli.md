---
title: "Creare una VM con più schede di interfaccia di rete - interfaccia della riga di comando di Azure 2.0| Microsoft Docs"
description: "Informazioni su come creare una VM con più schede di interfaccia di rete usando l'interfaccia della riga di comando di Azure 2.0"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8e906a4b-8583-4a97-9416-ee34cfa09a98
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 19b1757dd694e756cfd2d0d6cd67e64f43ccab7f
ms.contentlocale: it-it
ms.lasthandoff: 04/03/2017

---
# <a name="create-a-vm-with-multiple-nics-using-the-azure-cli-20"></a>Creare una VM con più schede di interfaccia di rete usando l'interfaccia della riga di comando di Azure 2.0

[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md).  Questo articolo illustra il modello di distribuzione Resource Manager che Microsoft consiglia di usare per le distribuzioni più recenti in sostituzione del [modello di distribuzione classica](virtual-network-deploy-multinic-classic-cli.md).
>

## <a name="create"></a>Creare la VM

È possibile completare questa attività usando l'interfaccia della riga di comando di Azure 2.0 (questo articolo) o l'[interfaccia della riga di comando di Azure 1.0](virtual-network-deploy-multinic-cli-nodejs.md). I valori in "" per le variabili nei passaggi seguenti creano le risorse con le impostazioni usate nello scenario. Sostituire i valori in base alle esigenze specifiche dell'ambiente.

1. Installare l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2), se non è già stata installata.
2. Creare una coppia di chiavi SSH pubblica e privata per le VM Linux completando i passaggi descritti in [Creare una coppia di chiavi SSH pubblica e privata per le VM Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Da una shell dei comandi accedere con il comando `az login`.
4. Creare la VM eseguendo lo script seguente in un computer Linux o Mac. Lo script crea un gruppo di risorse, una rete virtuale con due subnet, due schede di interfaccia di rete e una VM con due schede di interfaccia di rete collegate. Una delle schede di interfaccia di rete è connessa a una subnet a cui sono stati assegnati un indirizzo IP pubblico e un indirizzo IP privato. L'altra scheda di interfaccia di rete è connessa all'altra subnet a cui è stato assegnato un indirizzo IP privato statico e nessun indirizzo IP pubblico. Le risorse di schede di interfaccia di rete, indirizzo IP pubblico, rete virtuale e VM devono essere tutte presenti nella stessa località e nella stessa sottoscrizione. Lo script seguente esamina un caso in cui tutte le risorse sono incluse nello stesso gruppo di risorse, anche se questo non è un requisito.

```bash
#!/bin/sh

RgName="Multi-NIC-VM"
Location="westus"

# Create a resource group.
az group create \
--name $RgName \
--location $Location

# The address is assigned to the resource from a pool of IP adresses unique to each Azure region. 
# Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists
# the ranges for each region.

PipName="PIP-WEB"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static

# Create a virtual network with one subnet

VnetName="VNet1"
VnetPrefix="10.0.0.0/16"
VnetSubnet1Name="Front-End"
VnetSubnet1Prefix="10.0.0.0/24"
az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnet1Name \
--subnet-prefix $VnetSubnet1Prefix

# Create a second subnet within the VNet

VnetSubnet2Name="Back-end"
VnetSubnet2Prefix="10.0.1.0/24"
az network vnet subnet create \
--vnet-name $VnetName \
--resource-group $RgName \
--name $VnetSubnet2Name \
--address-prefix $VnetSubnet2Prefix

# Create a network interface connected to one of the subnets. The NIC is assigned a single dynamic private and
# public IP address by default, but you can instead, assign static addresses, or no public IP address at all.
# You can also assign multiple private or public IP addresses to each NIC. To learn more about IP addressing
# options for NICs, enter the az network nic create -h command.

Nic1Name="NIC-FE"
PrivateIpAddress1="10.0.0.5"

az network nic create \
--name $Nic1Name \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress1 \
--public-ip-address $PipName

# Create a second network interface and connect it to the other subnet. Though multiple NICs attached to the same
# VM can be connected to different subnets, the subnets must all be within the same VNet. Add additional NICs as necessary.

Nic2Name="NIC-BE"
PrivateIpAddress2="10.0.1.5"

az network nic create \
--name $Nic2Name \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet2Name \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress2

# Create a VM and attach the two NICs.

VmName="WEB"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. Not all VM sizes support
# more than one NIC, so be sure to select a VM size that supports the number of NICs you want to attach to the VM.
# You must create the VM with at least two NICs if you want to add more after VM creation. If you create a VM with
# only one NIC, you can't add additional NICs to the VM after VM creation, regardless of how many NICs the VM supports.
# The VM size specified in the following variable supports two NICs.

VmSize="Standard_DS2"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# az vm image list command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file.

SshKeyValue="~/.ssh/id_rsa.pub"

# Before executing the following command, add variable names of additional NICs you may have added to the script that
# you want to attach to the VM. If creating a Windows VM, remove the **ssh-key-value** line and you'll be prompted for
# the password you want to configure for the VM.

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $Nic1Name $Nic2Name \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

Oltre a creare una VM con due schede di interfaccia di rete, lo script crea:
- Un unico disco gestito Premium per impostazione predefinita, ma sono disponibili altre opzioni per il tipo di disco che è possibile creare. Leggere [Creare una VM Linux usando l'interfaccia della riga di comando di Azure 2.0](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per informazioni dettagliate.
- Una rete virtuale con due subnet e un singolo indirizzo IP pubblico. In alternativa, è possibile usare le risorse *esistenti* di rete virtuale, subnet, scheda di interfaccia di rete o indirizzo IP pubblico. Per informazioni su come usare le risorse di rete esistenti anziché creare risorse aggiuntive, immettere `az vm create -h`.

## <a name = "validate"></a>Convalidare la creazione di VM e le schede di interfacce di rete

1. Immettere il comando `az resource list --resouce-group Multi-NIC-VM --output table` per visualizzare un elenco di risorse create dallo script. L'output restituito dovrebbe contenere sei risorse: due schede di interfaccia di rete, un disco, un indirizzo IP pubblico, una rete virtuale e una macchina virtuale.
2. Immettere il comando `az network public-ip show --name PIP-WEB --resource-group Multi-NIC-VM --output table`. Nell'output restituito prendere nota del valore di **IpAddress** e che il valore di **PublicIpAllocationMethod** è *Static*.
3. Prima di eseguire il comando seguente, rimuovere <>, sostituire *Username* con il nome usato per la variabile **Username** nello script, quindi sostituire *ipAddress* con l'**ipAddress** nel passaggio precedente. Eseguire il comando seguente per effettuare la connessione alla VM: `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`. 
4. Una volta completata la connessione alla VM, eseguire il comando `sudo ifconfig` per esaminare le interfacce *eth0* ed *eth1*. A ogni scheda di interfaccia di rete sono assegnati gli indirizzi IP privati statici specificati nello script dai server DHCP di Azure. Non modificare gli indirizzi IP e MAC assegnati alle schede di interfacce di rete finché non viene eliminata la VM. È consigliabile non modificare gli indirizzi IP all'interno di un sistema operativo perché si potrebbe disabilitare la connettività al computer. Gli indirizzi IP pubblici non appaiono all'interno del sistema operativo poiché sono indirizzi di rete che l'infrastruttura di Azure converte nell'indirizzo IP privato o dall'indirizzo IP privato.

## <a name= "clean-up"></a>Rimuovere la VM e le risorse associate

È consigliabile eliminare le risorse create in questo esercizio se non si intende usarle in produzione. Le risorse di VM, indirizzo IP pubblico e disco comportano spese finché si esegue il provisioning. Per rimuovere le risorse create in questo esercizio, completare i passaggi seguenti:
1. Eseguire il comando `az resource list --resource-group Multi-NIC-VM` per visualizzare le risorse nel gruppo di risorse.
2. Verificare che nel gruppo di risorse non siano presenti altre risorse oltre a quelle create dallo script in questo articolo. 
3. Per eliminare tutte le risorse create in questo esercizio, eseguire il comando `az group delete --name Multi-NIC-VM`. Il comando elimina il gruppo di risorse e tutte le risorse in esso contenute.

## <a name="next-steps"></a>Passaggi successivi

Qualsiasi traffico di rete può scorrere da e verso la VM creata in questo articolo. È possibile definire regole in entrata e in uscita all'interno di un gruppo di sicurezza di rete che limitano il traffico in ingresso da e in uscita da ogni interfaccia di rete, ogni subnet o da entrambe le risorse. Per altre informazioni sui gruppi di sicurezza di rete, vedere l'articolo [Panoramica del gruppi di sicurezza di rete](virtual-networks-nsg.md).
