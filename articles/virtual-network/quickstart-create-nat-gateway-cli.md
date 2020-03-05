---
title: 'Guida introduttiva: Creare un gateway NAT - Interfaccia della riga di comando di Azure'
titlesuffix: Azure Virtual Network NAT
description: Questa guida di avvio rapido mostra come creare un gateway NAT usando l'interfaccia della riga di comando di Azure
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 1317ea6526a28c5d71dce1a3c25b2fd05f5e446b
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271299"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-cli"></a>Guida introduttiva: Creare un gateway NAT usando l'interfaccia della riga di comando di Azure

Questa guida di avvio rapido illustra come usare il servizio NAT di rete virtuale di Azure. Verrà creato un gateway NAT per fornire connettività in uscita per una macchina virtuale in Azure. 

>[!NOTE] 
>Il servizio NAT di rete virtuale di Azure è attualmente disponibile come anteprima pubblica e in un set limitato di [aree](https://azure.microsoft.com/global-infrastructure/regions/). Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per completare questa esercitazione, è possibile usare Azure Cloud Shell o eseguire i rispettivi comandi localmente.  Se è la prima volta che si usa Azure Cloud Shell, [accedere ora](https://shell.azure.com) per eseguire la configurazione iniziale.
Se si sceglie di eseguire questi comandi localmente, è necessario installare l'interfaccia della riga di comando.  Per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.71 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).


## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Come prima cosa creare un gruppo di risorse con [az group create](https://docs.microsoft.com/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato **myResourceGroupNAT** nella località **Stati Uniti orientali 2**:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Creare il gateway NAT

### <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Per accedere alla rete Internet pubblica, sono necessari uno o più indirizzi IP pubblici per il gateway NAT. Usare il comando [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) per creare una risorsa indirizzo IP pubblico denominata **myPublicIP** in **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Creare un prefisso indirizzo IP pubblico

Con il gateway NAT è possibile usare una o più risorse indirizzo IP pubblico, prefissi IP pubblici o entrambi. Per la dimostrazione, verrà aggiunta una risorsa prefisso indirizzo IP pubblico a questo scenario.   Usare il comando [az network public-ip prefix create](https://docs.microsoft.com/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) per creare una risorsa prefisso indirizzo IP pubblico denominata **myPublicIPprefix** in **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Creare una risorsa gateway NAT

Questa sezione descrive in modo dettagliato come creare e configurare i componenti seguenti del servizio NAT tramite la risorsa gateway NAT:
  - Un pool di indirizzi IP pubblici e un prefisso indirizzo IP pubblico da usare per i flussi in uscita convertiti dalla risorsa gateway NAT.
  - Modificare il timeout di inattività dal valore predefinito di 4 minuti a 10 minuti.

Creare un gateway NAT di Azure globale denominato [myNATgateway](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) con **az network nat gateway create**. Il comando usa l'indirizzo IP pubblico **myPublicIP** e il prefisso indirizzo IP pubblico **myPublicIPprefix**. Il comando imposta il timeout di inattività su **10** minuti.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

A questo punto, il gateway NAT è operativo e rimane solo da configurare da quali subnet di rete virtuale dovrà essere usato.

## <a name="configure-virtual-network"></a>Configurare la rete virtuale

Prima di distribuire una macchina virtuale e di poter usare il gateway NAT, è necessario creare la rete virtuale.

Creare una rete virtuale denominata **myVnet** con una subnet denominata **mySubnet** nel gruppo **myResourceGroupNAT** con il comando [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).  Lo spazio indirizzi IP per la rete virtuale è **192.168.0.0/16**. La subnet all'interno della rete virtuale è **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>Configurare il servizio NAT per la subnet di origine

Verrà configurata la subnet di origine **mySubnet** nella rete virtuale **myVnet** per usare una risorsa gateway NAT specifica **myNATgateway** con [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet).  Questo comando attiverà il servizio NAT nella subnet specificata.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

A questo punto, tutto il traffico in uscita verso le destinazioni Internet usa il gateway NAT.  Non è necessario configurare una route definita dall'utente.

## <a name="create-a-vm-to-use-the-nat-service"></a>Creare una macchina virtuale da usare con il servizio NAT

Ora verrà creata una macchina virtuale per l'uso del servizio NAT.  Questa macchina virtuale ha un indirizzo IP pubblico da usare come indirizzo IP pubblico a livello di istanza per consentire l'accesso alla macchina virtuale.  Il servizio NAT è in grado di riconoscere la direzione del flusso e sostituirà la destinazione Internet predefinita nella subnet. L'indirizzo IP pubblico della macchina virtuale non verrà usato per le connessioni in uscita.

### <a name="create-public-ip-for-source-vm"></a>Creare un indirizzo IP pubblico per la macchina virtuale di origine

Verrà creato un indirizzo IP pubblico da usare per accedere alla macchina virtuale.  Usare il comando [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) per creare una risorsa indirizzo IP pubblico denominata **myPublicIPVM** in **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>Creare un gruppo di sicurezza di rete per la macchina virtuale

Poiché gli indirizzi IP pubblici Standard sono 'sicuri per impostazione predefinita', verrà creato un gruppo di sicurezza di rete per consentire l'accesso in ingresso per SSH. Usare il comando [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) per creare una risorsa gruppo di sicurezza di rete denominata **myNSG** in **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Esporre l'endpoint SSH nella macchina virtuale di origine

Nel gruppo di sicurezza di rete verrà creata una regola per l'accesso SSH alla macchina virtuale di origine. Usare il comando [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) per creare una regola del gruppo di sicurezza di rete denominata **ssh** nel gruppo di sicurezza **myNSG** in **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>Creare la scheda di interfaccia di rete per la macchina virtuale

Creare un'interfaccia di rete con il comando [az network nic create](/cli/azure/network/nic#az-network-nic-create) e associarla all'indirizzo IP pubblico e al gruppo di sicurezza di rete. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIPVM \
    --network-security-group myNSG
```

### <a name="create-vm"></a>Creare una macchina virtuale

Creare la macchina virtuale con il comando [az vm create](/cli/azure/vm#az-vm-create).  Verranno generate le chiavi SSH per questa macchina virtuale e la chiave privata verrà archiviata per un uso successivo.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Attendere la distribuzione della macchina virtuale, quindi continuare con i passaggi rimanenti.

## <a name="discover-the-ip-address-of-the-vm"></a>Individuare l'indirizzo IP della macchina virtuale

È prima di tutto necessario individuare l'indirizzo IP della macchina virtuale che è stata creata. Per recuperare l'indirizzo IP pubblico della macchina virtuale, usare il comando [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Copiare l'indirizzo IP pubblico e quindi incollarlo negli Appunti, in modo da poterlo usare per accedere alla macchina virtuale.

### <a name="sign-in-to-vm"></a>Accedere alla macchina virtuale

Le credenziali SSH sono state archiviate in Cloud Shell nel corso dell'operazione precedente.  Aprire un'istanza di [Azure Cloud Shell](https://shell.azure.com) nel browser. Usare l'indirizzo IP recuperato nel passaggio precedente per stabilire una sessione SSH con la macchina virtuale.

```bash
ssh <ip-address-destination>
```

A questo punto si è pronti per usare il servizio NAT.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse al suo interno non sono più necessari, usare il comando [az group delete](/cli/azure/group#az-group-delete) per rimuoverli.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati creati un gateway NAT e una macchina virtuale. 

Esaminare le metriche in Monitoraggio di Azure per visualizzare il servizio NAT in funzione. Diagnosticare i problemi, ad esempio l'esaurimento delle risorse delle porte SNAT disponibili.  L'esaurimento delle risorse delle porte SNAT è risolvibile aggiungendo altre risorse indirizzo IP pubblico, risorse prefisso indirizzo IP pubblico o entrambe.


- Informazioni sul [servizio NAT di rete virtuale di Azure](./nat-overview.md)
- Informazioni sulla [risorsa gateway NAT](./nat-gateway-resource.md).
- Avvio rapido per la distribuzione della [risorsa gateway NAT tramite l'interfaccia della riga di comando di Azure](./quickstart-create-nat-gateway-cli.md).
- Avvio rapido per la distribuzione della [risorsa gateway NAT tramite Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Avvio rapido per la distribuzione della [risorsa gateway NAT tramite il portale di Azure](./quickstart-create-nat-gateway-portal.md).
- [Inviare commenti e suggerimenti sull'anteprima pubblica](https://aka.ms/natfeedback).
> [!div class="nextstepaction"]

