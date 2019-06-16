---
title: Filtrare il traffico di rete - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Questo articolo illustra come filtrare il traffico di rete a una subnet, con un gruppo di sicurezza di rete, usando l'interfaccia della riga di comando di Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: a1ade6e823201419c3a742a36c66a50a9dc09976
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64728811"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Filtrare il traffico di rete con un gruppo di sicurezza di rete usando l'interfaccia della riga di comando di Azure

È possibile filtrare il traffico di rete in ingresso e in uscita da una subnet di rete virtuale con un gruppo di sicurezza di rete. I gruppi di sicurezza di rete contengono regole di sicurezza per filtrare il traffico di rete in base a indirizzo IP, porta e protocollo. Le regole di sicurezza vengono applicate alle risorse distribuite in una subnet. In questo articolo viene spiegato come:

* Creare un gruppo di sicurezza di rete e le regole di sicurezza
* Creare una rete virtuale e associare un gruppo di sicurezza di rete a una subnet
* Distribuire le macchine virtuali in una subnet
* Testare i filtri del traffico

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede la versione 2.0.28 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 


## <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Un gruppo di sicurezza di rete contiene regole di sicurezza. Le regole di sicurezza specificano un'origine e una destinazione. Le origini e le destinazioni possono essere gruppi di sicurezza delle applicazioni.

### <a name="create-application-security-groups"></a>Creare gruppi di sicurezza delle applicazioni

Creare innanzitutto un gruppo di risorse con [az group create](/cli/azure/group) per tutte le risorse create in questo articolo. L'esempio seguente crea un gruppo di risorse nella località *eastus*: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Creare un gruppo di sicurezza delle applicazioni con il comando [az network asg create](/cli/azure/network/asg). Un gruppo di sicurezza delle applicazioni consente di raggruppare i server con requisiti di filtro delle porte simili. L'esempio seguente crea due gruppi di sicurezza dell'applicazione.

```azurecli-interactive
az network asg create \
  --resource-group myResourceGroup \
  --name myAsgWebServers \
  --location eastus

az network asg create \
  --resource-group myResourceGroup \
  --name myAsgMgmtServers \
  --location eastus
```

### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete con il comando [az network nsg create](/cli/azure/network/nsg). L'esempio seguente crea un gruppo di sicurezza di rete denominato *myNsg*: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Creare regole di sicurezza

Creare una regola di sicurezza con il comando [az network nsg rule create](/cli/azure/network/nsg/rule). L'esempio seguente crea una regola che consente il traffico in ingresso proveniente da Internet al gruppo di sicurezza delle applicazioni *myWebServers* sulle porte 80 e 443:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-Web-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgWebServers" \
  --destination-port-range 80 443
```

L'esempio seguente crea una regola che consente il traffico in ingresso proveniente da Internet al gruppo di sicurezza delle applicazioni *myMgmtServers* sulla porta 22:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 110 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgMgmtServers" \
  --destination-port-range 22
```

In questo articolo SSH (porta 22) è esposto a Internet per la VM *myAsgMgmtServers*. Per gli ambienti di produzione, anziché esporre la porta 22 a Internet, è consigliabile connettersi alle risorse di Azure da gestire tramite una connessione di rete [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [privata](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare una rete virtuale con [az network vnet create](/cli/azure/network/vnet). L'esempio seguente crea una rete virtuale denominata *myVirtualNetwork*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Aggiungere una subnet a una rete virtuale con il comando [az network vnet subnet create](/cli/azure/network/vnet/subnet). L'esempio seguente aggiunge una subnet denominata *mySubnet* alla rete virtuale e vi associa il gruppo di sicurezza di rete *myNsg*:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare due VM nella rete virtuale per convalidare il filtro del traffico in un passaggio successivo. 

Creare una VM con il comando [az vm create](/cli/azure/vm). L'esempio seguente crea una VM che fungerà da server Web. L'opzione `--asgs myAsgWebServers` fa sì che Azure renda l'interfaccia di rete creata per la VM un membro del gruppo di sicurezza delle applicazioni *myAsgWebServers*.

L'opzione `--nsg ""` è specificata per impedire ad Azure di creare un gruppo di sicurezza di rete predefinito per l'interfaccia di rete creata da Azure al momento della creazione della VM. Per semplificare questo articolo, viene usata una password. Le chiavi in genere vengono usate per le distribuzioni di produzione. Se si usano chiavi, occorre configurare anche l'inoltro dell'agente SSH per i passaggi rimanenti. Per altre informazioni, vedere la documentazione del client SSH. Sostituire `<replace-with-your-password>` nel comando seguente con una password a scelta.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgWebServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

La creazione della VM richiede alcuni minuti. Dopo la creazione della VM, viene restituito un output simile all'esempio seguente: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmWeb",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Prendere nota di **publicIpAddress**. Questo indirizzo viene usato per accedere alla macchina virtuale da Internet in un passaggio successivo.  Creare una VM che funge da server di gestione:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgMgmtServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

La creazione della VM richiede alcuni minuti. Dopo la creazione della VM, prendere nota dell'indirizzo **publicIpAddress** nell'output restituito. Questo indirizzo verrà usato per l'accesso alla VM nei passaggi successivi. Prima di continuare con i passaggi successivi, attendere che Azure finisca di creare la VM.

## <a name="test-traffic-filters"></a>Testare i filtri del traffico

Usare il comando seguente per creare una sessione SSH con la VM *myVmMgmt*. Sostituire  *\<publicIpAddress >* con l'indirizzo IP pubblico della macchina virtuale. Nell'esempio precedente l'indirizzo IP è *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Quando viene richiesto di specificare una password, digitare la password specificata in [Create VMs](#create-virtual-machines) (Creare macchine virtuali).

La connessione ha esito positivo, perché sulla porta 22 è consentito il traffico in ingresso proveniente da Internet al gruppo di sicurezza delle applicazioni *myAsgMgmtServers* in cui si trova l'interfaccia di rete collegata alla VM *myVmMgmt*.

Usare il comando seguente per stabilire una connessione SSH alla VM *myVmWeb* dalla VM *myVmMgmt*:

```bash 
ssh azureuser@myVmWeb
```

La connessione ha esito positivo perché una regola di sicurezza predefinita in ciascun gruppo di sicurezza di rete consente il traffico su tutte le porte tra tutti gli indirizzi IP all'interno di una rete virtuale. Non è possibile stabilire una connessione SSH alla VM *myVmWeb* da Internet poiché la regola di sicurezza per *myAsgWebServers* non consente il traffico in ingresso da Internet sulla porta 22.

Usare i comandi seguenti per installare il server Web nginx nella VM *myVmWeb*:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

Nella VM *myVmWeb* è consentito il traffico in uscita a Internet per recuperare nginx, poiché una regola di sicurezza predefinita consente tutto il traffico in uscita a Internet. Chiudere la sessione SSH di *myVmWeb*, rimanendo nel prompt `username@myVmMgmt:~$` della VM *myVmMgmt*. Per recuperare la schermata iniziale di nginx dalla VM *myVmWeb*, digitare il comando seguente:

```bash
curl myVmWeb
```

Disconnettersi dalla VM *myVmMgmt*. Per verificare di poter accedere al server Web *myVmWeb* dall'esterno di Azure, digitare `curl <publicIpAddress>` dal proprio computer. La connessione ha esito positivo, perché sulla porta 80 è consentito il traffico in ingresso proveniente da Internet al gruppo di sicurezza delle applicazioni *myAsgWebServers* in cui si trova l'interfaccia di rete collegata alla VM *myVmWeb*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, usare [az group delete](/cli/azure/group) per rimuoverli.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato un gruppo di sicurezza di rete, che è stato associato a una subnet di rete virtuale. Per altre informazioni sui gruppi di sicurezza di rete, vedere [Panoramica dei gruppi di sicurezza di rete](security-overview.md) e [Gestire un gruppo di sicurezza di rete](manage-network-security-group.md).

Per impostazione predefinita, Azure instrada il traffico tra subnet. È anche possibile ad esempio scegliere di instradare il traffico tra subnet tramite una VM che funge da firewall. Per altre informazioni, vedere [Creare una tabella di route](tutorial-create-route-table-cli.md).
