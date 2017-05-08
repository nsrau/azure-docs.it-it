---
title: Reti virtuali di Azure e macchine virtuali Linux | Microsoft Docs
description: 'Esercitazione: gestire reti virtuali di Azure e macchine virtuali Linux con l&quot;interfaccia della riga di comando di Azure'
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: c1494009c126c4715caf7972a0f59cffdaba3a7f
ms.lasthandoff: 04/22/2017

---

# <a name="manage-azure-virtual-networks-and-linux-virtual-machines-with-the-azure-cli"></a>Gestire reti virtuali di Azure e macchine virtuali Linux con l'interfaccia della riga di comando di Azure

Questa esercitazione illustra la creazione di più macchine virtuali (VM) in una rete virtuale e la configurazione della connettività di rete tra di esse. Al termine, una VM "front-end" sarà accessibile da Internet sulla porta 22 per connessioni SSH e sulla porta 80 per connessioni HTTP. Una VM "back-end" con un database MySQL sarà isolata e accessibile solo dalla VM front-end sulla porta 3306.

Le procedure descritte in questa esercitazione possono essere completate usando l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).

## <a name="create-vm-and-vnet"></a>Creare una macchina virtuale e una rete virtuale

Una rete virtuale (VNet) di Azure è una rappresentazione della rete personalizzata nel cloud. È un isolamento logico del cloud di Azure dedicato alla sottoscrizione. All'interno di una rete virtuale si trovano subnet, regole per la connettività a tali subnet e connessioni dalle VM alle subnet. L'interfaccia della riga di comando di Azure semplifica la creazione di tutte le risorse correlate alla rete necessarie per accedere alle macchine virtuali. 

Per poter creare qualsiasi altra risorsa di Azure, è prima di tutto necessario creare un gruppo di risorse con az group create. Nell'esempio seguente viene creato un gruppo di risorse denominato `myRGNetwork` nella località `westus`:

```azurecli
az group create --name myRGNetwork --location westus
```

Quando si crea una macchina virtuale usando l'interfaccia della riga di comando di Azure, le risorse di rete necessarie vengono create automaticamente nello stesso momento. Creare `myFrontendVM` e le rispettive risorse di rete di supporto con [az vm create](https://docs.microsoft.com/cli/azure/vm#create):

```azurecli
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Dopo la creazione della VM, annotare l'indirizzo IP pubblico. Questo indirizzo viene usato nei passaggi successivi dell'esercitazione:

```bash
{
  "fqdns": "",
  "id": "/subscriptions/{id}/resourceGroups/myRGNetwork/providers/Microsoft.Compute/virtualMachines/myFrontendVM",
  "location": "westus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myRGNetwork"
}
```

Sono state create queste risorse di rete:

- **myFrontendVMNSG**: gruppo di sicurezza di rete che protegge il traffico in ingresso in `myFrontendVM`.
- **myVMPublicIP**: indirizzo IP pubblico che abilita l'accesso a Internet per `myFrontendVM`.
- **myVMVMNic**: interfaccia di rete virtuale che fornisce la connettività di rete per `myFrontendVM`.
- **myVMVNET**: rete virtuale a cui è connesso `myFrontendVM`.

## <a name="install-web-server"></a>Installare il server Web

Creare una connessione SSH con `myFrontendVM`. Sostituire l'indirizzo IP di esempio con l'indirizzo IP pubblico della macchina virtuale:

```bash
ssh 40.68.254.142
```

Eseguire questi comandi per installare NGINX:

```bash
sudo apt-get -y update && sudo apt-get -y install nginx
```

Chiudere la sessione SSH:

```bash
exit
```

## <a name="manage-internet-traffic"></a>Gestire il traffico Internet

Un gruppo di sicurezza di rete (NSG) contiene un elenco di regole di sicurezza che consentono o rifiutano il traffico di rete verso le risorse connesse a una rete virtuale. I gruppi di sicurezza di rete possono essere associati a subnet o singole interfacce di rete collegate a VM. Per aprire o chiudere l'accesso alle VM tramite le porte vengono usate le regole dei gruppi di sicurezza di rete. Alla creazione di `myFrontendVM`, la porta in ingresso 22 è stata aperta automaticamente per la connettività SSH.

Aprire la porta 80 su `myFrontendVM` con [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port):

```azurecli
az vm open-port --port 80 --resource-group myRGNetwork --name myFrontendVM
```

È ora possibile passare all'indirizzo IP pubblico della VM e visualizzare il sito NGINX.

![Sito NGINX predefinito](./media/quick-create-cli/nginx.png)

## <a name="manage-internal-traffic"></a>Gestire il traffico interno

La comunicazione interna delle VM può essere configurata anche usando un gruppo di sicurezza di rete. Questa sezione illustra come creare una subnet aggiuntiva nella rete e assegnare un gruppo di sicurezza di rete a tale subnet per consentire una connessione da `myFrontendVM` a `myBackendVM` sulla porta 3306. La subnet viene quindi assegnata alla VM al momento della creazione.

Aggiungere un nuovo gruppo di sicurezza di rete denominato `myBackendNSG` con [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create). 

```azurecli
az network nsg create \
 --resource-group myRGNetwork \
 --name myBackendNSG
```

Configurare una porta per consentire a `myFrontendVM` e `myBackendVM` di comunicare tra loro nella rete virtuale. Aggiungere una regola del gruppo di sicurezza di rete per consentire il traffico verso `myBackendSubnet` solo da `myVMSubnet` con `az network rule create`:

```azurecli
az network nsg rule create \
 --resource-group myRGNetwork \
 --nsg-name myBackendNSG \
 --name com-rule \
 --access Allow \
 --protocol Tcp \
 --direction Inbound \
 --priority 100 \
 --source-address-prefix 10.0.0.0/24 \
 --source-port-range "*" \
 --destination-address-prefix "*" \
 --destination-port-range 3306
```

## <a name="add-back-end-subnet"></a>Aggiungere la subnet back-end

Una subnet è una risorsa figlio di una rete virtuale e consente di definire i segmenti degli spazi di indirizzi all'interno di un blocco CIDR, usando i prefissi degli indirizzi IP. Le NIC possono essere aggiunte alle subnet e connesse alle macchine virtuali, fornendo connettività per diversi carichi di lavoro.

Aggiungere `myBackEndSubnet` a `myFrontendVMVNet` con [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#create):

```azurecli
az network vnet subnet create \
 --address-prefix 10.0.1.0/24 \
 --name myBackendSubnet \
 --resource-group myRGNetwork \
 --vnet-name myFrontendVMVNET \
 --network-security-group myBackendNSG
```

## <a name="create-back-end-vm"></a>Creare la VM back-end

Creare `myBackendVM` tramite `myBackendSubnet` con `az vm create`:

```azurecli
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --subnet myBackendSubnet \
  --vnet-name myFrontendVMVNET \
  --public-ip-address ""

```

## <a name="install-database"></a>Installare il database

Per questa esercitazione è necessario copiare la chiave privata dalla VM di sviluppo in `myFrontendVM`. In un ambiente di produzione è consigliabile creare chiavi specifiche per l'uso nelle VM, invece di usare chiavi SSH di tipo --generate quando si creano le macchine virtuali. 

La VM back-end non è destinata ad essere accessibile pubblicamente. In questa sezione viene illustrato come usare SSH per accedere a `myFrontendVM` e quindi usare SSH per accedere a `myBackendVM` da `myFrontendVM`.

Sostituire l'indirizzo IP di esempio con l'indirizzo IP pubblico di `myFrontendVM`:

```bash
scp ~/.ssh/id_rsa 40.68.254.142:~/.ssh/id_rsa
```

Creare una connessione SSH con `myFrontendVM`. Sostituire l'indirizzo IP di esempio con l'indirizzo IP pubblico di `myFrontendVM`:

```bash
ssh 40.68.254.142
```

Da `myFrontendVM` connettersi a `myBackendVM`:

```bash
ssh myBackendVM
```

Eseguire il comando seguente per installare MySQL:

```bash
sudo apt-get -y install mysql-server
```

Seguire queste istruzioni per configurare MySQL.

Chiudere le sessioni SSH:

```bash
exit
```

MySQL viene installato per illustrare la modalità di installazione di un'applicazione in `myBackendVM`, ma non viene effettivamente usato in questa esercitazione.
