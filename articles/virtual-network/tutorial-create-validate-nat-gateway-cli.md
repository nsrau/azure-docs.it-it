---
title: 'Esercitazione: Creare e testare un gateway NAT - Interfaccia della riga di comando di Azure'
titlesuffix: Azure Virtual Network NAT
description: Questa esercitazione illustra come creare un gateway NAT e testare il servizio NAT tramite l'interfaccia della riga di comando di Azure
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: bc815281a005f750072176015a937547ff6d4670
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429072"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Esercitazione: Creare un gateway NAT e testare il servizio NAT tramite l'interfaccia della riga di comando di Azure

In questa esercitazione verrà creato un gateway NAT per fornire connettività in uscita per le macchine virtuali in Azure. Per testare il gateway NAT, distribuire una macchina virtuale di origine e di destinazione. Il gateway NAT verrà testato tramite connessioni in uscita a un indirizzo IP pubblico. Queste connessioni verranno stabilite dall'origine alla macchina virtuale di destinazione. Questa esercitazione distribuisce la macchina virtuale di origine e quella di destinazione in due reti virtuali diverse nello stesso gruppo di risorse per maggiore semplicità.

>[!NOTE] 
>Il servizio NAT di rete virtuale di Azure è attualmente disponibile come anteprima pubblica e in un set limitato di [aree](./nat-overview.md#region-availability). Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per completare questa esercitazione, è possibile usare Azure Cloud Shell o eseguire i rispettivi comandi localmente.  Se è la prima volta che si usa Azure Cloud Shell, è necessario [accedere ora](https://shell.azure.com).

Se si sceglie di eseguire questi comandi localmente, è necessario installare l'interfaccia della riga di comando.  Per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.71 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

> [!IMPORTANT]
> Dopo aver abilitato il servizio NAT di rete virtuale di Azure [anteprima](./nat-overview.md#enable-preview) nella sottoscrizione, usare https://aka.ms/natportal per accedere al portale.

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

Per accedere alla rete Internet pubblica, sono necessari uno o più indirizzi IP pubblici per il gateway NAT. Usare il comando [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) per creare una risorsa indirizzo IP pubblico denominata **myPublicIPsource** in **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Creare un prefisso indirizzo IP pubblico

Con il gateway NAT è possibile usare una o più risorse indirizzo IP pubblico, prefissi IP pubblici o entrambi. Per la dimostrazione, verrà aggiunta una risorsa prefisso indirizzo IP pubblico a questo scenario.   Usare il comando [az network public-ip prefix create](https://docs.microsoft.com/cli/azure/network/public-ip-prefix) per creare una risorsa prefisso indirizzo IP pubblico denominata **myPublicIPprefixsource** in **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Creare una risorsa gateway NAT

Questa sezione descrive in modo dettagliato come creare e configurare i componenti seguenti del servizio NAT tramite la risorsa gateway NAT:
  - Un pool di indirizzi IP pubblici e un prefisso indirizzo IP pubblico da usare per i flussi in uscita convertiti dalla risorsa gateway NAT.
  - Modificare il timeout di inattività dal valore predefinito di 4 minuti a 10 minuti.

Creare un gateway NAT di Azure globale denominato [myNATgateway](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) con **az network nat gateway create**. Il comando usa l'indirizzo IP pubblico **myPublicIP** e il prefisso indirizzo IP pubblico **myPublicIPprefix**. Il comando imposta anche il timeout di inattività su 10 minuti.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
  ```

A questo punto, il gateway NAT è operativo e rimane solo da configurare da quali subnet di rete virtuale dovrà essere usato.

## <a name="prepare-the-source-for-outbound-traffic"></a>Preparare l'origine per il traffico in uscita

Verrà fornita la procedura dettagliata per la configurazione di un ambiente di test completo. Verrà configurato un test usando strumenti open source per verificare il gateway NAT. Si inizierà con l'origine, che userà la risorsa gateway NAT creata in precedenza.

### <a name="configure-virtual-network-for-source"></a>Configurare la rete virtuale per l'origine

Prima di distribuire una macchina virtuale e di poter testare il gateway NAT, è necessario creare la rete virtuale.

Creare una rete virtuale denominata **myVnetsource** con una subnet denominata **mySubnetsource** in **myResourceGroupNAT** con il comando [az network Microsoft Azure Virtual Network create](https://docs.microsoft.com/cli/azure/network/vnet).  Lo spazio indirizzi IP per la rete virtuale è **192.168.0.0/16**. La subnet all'interno della rete virtuale è **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>Configurare il servizio NAT per la subnet di origine

Configurare la subnet di origine **mySubnetsource** nella rete virtuale **myVnetsource** per usare una risorsa gateway NAT specifica **myNATgateway** con [az network Microsoft Azure Virtual Network subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet). Questo comando attiverà il servizio NAT nella subnet specificata.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
```

A questo punto, tutto il traffico in uscita verso le destinazioni Internet usa il servizio NAT.  Non è necessario configurare una route definita dall'utente.

Prima di poter testare il gateway NAT, è necessario creare una macchina virtuale di origine.  Verrà assegnata una risorsa indirizzo IP pubblico come indirizzo IP pubblico a livello di istanza per accedere a questa macchina virtuale dall'esterno. Questo indirizzo viene usato solo per accedervi ai fini del test.  Verrà illustrato il modo in cui il servizio NAT avrà la precedenza rispetto ad altre opzioni in uscita.

Per esercitarsi, è anche possibile creare questa macchina virtuale senza un indirizzo IP pubblico e creare un'altra macchina virtuale da usare come JumpBox senza un indirizzo IP pubblico.

### <a name="create-public-ip-for-source-vm"></a>Creare un indirizzo IP pubblico per la macchina virtuale di origine

Verrà creato un indirizzo IP pubblico da usare per accedere alla macchina virtuale di origine. Usare il comando [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) per creare una risorsa indirizzo IP pubblico denominata **myPublicIPsourceVM** in **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
```

### <a name="create-an-nsg-for-source-vm"></a>Creare un gruppo di sicurezza di rete per la macchina virtuale di origine

Poiché gli indirizzi IP pubblici Standard sono 'sicuri per impostazione predefinita', verrà creato un gruppo di sicurezza di rete per consentire l'accesso in ingresso per SSH.  Il servizio NAT di Azure è in grado di riconoscere la direzione del flusso. Questo gruppo di sicurezza di rete non verrà usato per il traffico in uscita se il gateway NAT è configurato nella stessa subnet. Usare il comando [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) per creare una risorsa gruppo di sicurezza di rete denominata **myNSGsource** in **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Esporre l'endpoint SSH nella macchina virtuale di origine

Nel gruppo di sicurezza di rete verrà creata una regola per l'accesso SSH alla macchina virtuale di origine. Usare il comando [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) per creare una regola del gruppo di sicurezza di rete denominata **ssh**. Questa regola verrà creata nel gruppo di sicurezza di rete denominato **myNSGsource** nel gruppo di risorse **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-source-vm"></a>Creare la scheda di interfaccia di rete per la macchina virtuale di origine

Creare un'interfaccia di rete con il comando [az network nic create](/cli/azure/network/nic#az-network-nic-create) e associarla all'indirizzo IP pubblico e al gruppo di sicurezza di rete. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
```

### <a name="create-a-source-vm"></a>Creare una macchina virtuale di origine

Creare la macchina virtuale con il comando [az vm create](/cli/azure/vm#az-vm-create).  Verranno generate le chiavi SSH per questa macchina virtuale e la chiave privata verrà archiviata per un uso successivo.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
```

Anche se il comando restituisce immediatamente un risultato, potrebbero essere necessari alcuni minuti per la distribuzione della macchina virtuale.

## <a name="prepare-destination-for-outbound-traffic"></a>Preparare la destinazione per il traffico in uscita

Ora verrà creata una destinazione per il traffico in uscita convertito dal servizio NAT per consentirne il test.

### <a name="configure-virtual-network-for-destination"></a>Configurare la rete virtuale per la destinazione

 È necessario creare la rete virtuale in cui risiederà la macchina virtuale di destinazione.  Questi comandi seguono gli stessi passaggi eseguiti per la macchina virtuale di origine con alcune piccole modifiche per esporre l'endpoint di destinazione.

Creare una rete virtuale denominata **myVnetdestination** con una subnet denominata **mySubnetdestination** in **myResourceGroupNAT** con il comando [az network Microsoft Azure Virtual Network create](https://docs.microsoft.com/cli/azure/network/vnet).  Lo spazio indirizzi IP per la rete virtuale è **192.168.0.0/16**. La subnet all'interno della rete virtuale è **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location westus \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
```

### <a name="create-public-ip-for-destination-vm"></a>Creare l'indirizzo IP pubblico per la macchina virtuale di destinazione

Verrà creato un indirizzo IP pubblico da usare per accedere alla macchina virtuale di origine. Usare il comando [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) per creare una risorsa indirizzo IP pubblico denominata **myPublicIPdestinationVM** in **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard \
  --location westus
```

### <a name="create-an-nsg-for-destination-vm"></a>Creare un gruppo di sicurezza di rete per la macchina virtuale di destinazione

Poiché gli indirizzi IP pubblici Standard sono 'sicuri per impostazione predefinita', sarà necessario creare un gruppo di sicurezza di rete per consentire l'accesso in ingresso per SSH. Il servizio NAT di Azure è in grado di riconoscere la direzione del flusso. Questo gruppo di sicurezza di rete non verrà usato per il traffico in uscita se il gateway NAT è configurato nella stessa subnet. Usare il comando [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) per creare una risorsa gruppo di sicurezza di rete denominata **myNSGdestination** in **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination \
    --location westus
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>Esporre l'endpoint SSH nella macchina virtuale di destinazione

Nel gruppo di sicurezza di rete verrà creata una regola per l'accesso SSH alla macchina virtuale di destinazione. Usare il comando [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) per creare una regola del gruppo di sicurezza di rete denominata **ssh**. Questa regola verrà creata nel gruppo di sicurezza di rete denominato **myNSGdestination** nel gruppo di risorse **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="expose-http-endpoint-on-destination-vm"></a>Esporre l'endpoint HTTP nella macchina virtuale di destinazione

Nel gruppo di sicurezza di rete verrà creata una regola per l'accesso HTTP alla macchina virtuale di destinazione. Usare il comando [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) per creare una regola del gruppo di sicurezza di rete denominata **http** nel gruppo di sicurezza **myNSGdestination** in **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
```

### <a name="create-nic-for-destination-vm"></a>Creare la scheda di interfaccia di rete per la macchina virtuale di destinazione

Creare un'interfaccia di rete con il comando [az network nic create](/cli/azure/network/nic#az-network-nic-create) e associarla all'indirizzo IP pubblico **myPublicIPdestinationVM** e al gruppo di sicurezza di rete **myNSGdestination**. 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination \
    --location westus
```

### <a name="create-a-destination-vm"></a>Creare una macchina virtuale di destinazione

Creare la macchina virtuale con il comando [az vm create](/cli/azure/vm#az-vm-create).  Verranno generate le chiavi SSH per questa macchina virtuale e la chiave privata verrà archiviata per un uso successivo.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait \
    --location westus
```
Anche se il comando restituisce immediatamente un risultato, potrebbero essere necessari alcuni minuti per la distribuzione della macchina virtuale.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Preparare un server Web e testare il payload nella macchina virtuale di destinazione

È prima di tutto necessario individuare l'indirizzo IP della macchina virtuale di destinazione.  Per ottenere l'indirizzo IP pubblico della macchina virtuale di destinazione, usare il comando [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Copiare l'indirizzo IP pubblico e quindi incollarlo negli Appunti, in modo da poterlo usare nei passaggi successivi. Indicare che si tratta della macchina virtuale di destinazione.

### <a name="sign-in-to-destination-vm"></a>Accedere alla macchina virtuale di destinazione

Le credenziali SSH sono state archiviate in Cloud Shell nel corso dell'operazione precedente.  Aprire un'istanza di [Azure Cloud Shell](https://shell.azure.com) nel browser. Usare l'indirizzo IP recuperato nel passaggio precedente per stabilire una sessione SSH con la macchina virtuale. 

```bash
ssh <ip-address-destination>
```

Dopo aver eseguito l'accesso, copiare e incollare i comandi seguenti.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Questi comandi aggiorneranno la macchina virtuale, installeranno nginx e creeranno un file da 100 KB. Questo file verrà recuperato dalla macchina virtuale di origine usando il servizio NAT.

Chiudere la sessione SSH con la macchina virtuale di destinazione.

## <a name="prepare-test-on-source-vm"></a>Preparare il test nella macchina virtuale di origine

È prima di tutto necessario individuare l'indirizzo IP della macchina virtuale di origine.  Per ottenere l'indirizzo IP pubblico della macchina virtuale di origine, usare il comando [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Copiare l'indirizzo IP pubblico e quindi incollarlo negli Appunti, in modo da poterlo usare nei passaggi successivi. Indicare che si tratta della macchina virtuale di origine.

### <a name="sign-in-to-source-vm"></a>Accedere alla macchina virtuale di origine

Di nuovo, le credenziali SSH sono archiviate in Cloud Shell. Aprire una nuova scheda per [Azure Cloud Shell](https://shell.azure.com) nel browser.  Usare l'indirizzo IP recuperato nel passaggio precedente per stabilire una sessione SSH con la macchina virtuale. 

```bash
ssh <ip-address-source>
```

Copiare e incollare i comandi seguenti per preparare il test del servizio NAT.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Questo comando aggiornerà la macchina virtuale, installerà go, installerà [hey](https://github.com/rakyll/hey) da GitHub e aggiornerà l'ambiente della shell.

A questo punto si è pronti per testare il servizio NAT.

## <a name="validate-nat-service"></a>Convalidare il servizio NAT

Mentre si è connessi alla macchina virtuale di origine, è possibile usare **curl** e **hey** per generare richieste all'indirizzo IP di destinazione.

Usare curl per recuperare il file da 100 KB.  Sostituire **\<ip-address-destination>** nell'esempio seguente con l'indirizzo IP di destinazione copiato in precedenza.  Il parametro **--output** indica che il file recuperato verrà rimosso.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

È anche possibile generare una serie di richieste usando **hey**. Di nuovo, sostituire **\<ip-address-destination>** con l'indirizzo IP di destinazione copiato in precedenza.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Questo comando genererà 100 richieste, di cui 10 simultanee, con un timeout di 30 secondi. La connessione TCP non verrà riutilizzata.  Ogni richiesta recupererà 100 KB.  Al termine dell'esecuzione, **hey** segnalerà alcune statistiche relative alle prestazioni del servizio NAT.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse al suo interno non sono più necessari, usare il comando [az group delete](/cli/azure/group#az-group-delete) per rimuoverli.

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
```

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato creato un gateway NAT, sono state create una macchina virtuale di origine e una di destinazione e quindi è stato testato il gateway NAT.

Esaminare le metriche in Monitoraggio di Azure per visualizzare il servizio NAT in funzione. Diagnosticare i problemi, ad esempio l'esaurimento delle risorse delle porte SNAT disponibili.  L'esaurimento delle risorse delle porte SNAT è facilmente risolvibile aggiungendo altre risorse indirizzo IP pubblico, risorse prefisso indirizzo IP pubblico o entrambe.

- Informazioni sul [servizio NAT di rete virtuale](./nat-overview.md)
- Informazioni sulla [risorsa gateway NAT](./nat-gateway-resource.md).
- Avvio rapido per la distribuzione della [risorsa gateway NAT tramite l'interfaccia della riga di comando di Azure](./quickstart-create-nat-gateway-cli.md).
- Avvio rapido per la distribuzione della [risorsa gateway NAT tramite Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Avvio rapido per la distribuzione della [risorsa gateway NAT tramite il portale di Azure](./quickstart-create-nat-gateway-portal.md).
- [Inviare commenti e suggerimenti sull'anteprima pubblica](https://aka.ms/natfeedback).

> [!div class="nextstepaction"]

