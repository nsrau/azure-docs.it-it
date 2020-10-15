---
title: 'Esercitazione: Creare e testare un gateway NAT - Portale di Azure'
titlesuffix: Azure Virtual Network NAT
description: Questa esercitazione illustra come creare un gateway NAT e testare il servizio NAT tramite il portale di Azure
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: d798725892a9586c17cd7023863fe5cf7df05cb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84417838"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal-and-test-the-nat-service"></a>Esercitazione: Creare un gateway NAT e testare il servizio NAT tramite il portale di Azure

In questa esercitazione verrà creato un gateway NAT per fornire connettività in uscita per le macchine virtuali in Azure. Per testare il gateway NAT, distribuire una macchina virtuale di origine e di destinazione. Il gateway NAT verrà testato tramite connessioni in uscita a un indirizzo IP pubblico dalla macchina virtuale di origine a quella di destinazione.  Questa esercitazione distribuisce la macchina virtuale di origine e quella di destinazione in due reti virtuali diverse nello stesso gruppo di risorse per maggiore semplicità.

Se si preferisce, è possibile eseguire questi passaggi usando l'[interfaccia della riga di comando di Azure](tutorial-create-validate-nat-gateway-cli.md) o [Azure PowerShell](tutorial-create-validate-nat-gateway-powershell.md) in alternativa al portale.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="prepare-the-source-for-outbound-traffic"></a>Preparare l'origine per il traffico in uscita

Nei passaggi successivi verranno fornite istruzioni per la configurazione di un ambiente di test completo e per l'esecuzione dei test stessi. Si inizierà con l'origine, che userà la risorsa gateway NAT che verrà creata nei passaggi successivi.

## <a name="virtual-network-and-parameters"></a>Rete virtuale e parametri

Prima di distribuire una macchina virtuale e di poter usare il gateway NAT, è necessario creare il gruppo di risorse e la rete virtuale.

In questa sezione è necessario sostituire i parametri seguenti delle procedure con le informazioni riportate di seguito:

| Parametro                   | valore                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetsource          |
| **\<region-name>**          | Stati Uniti orientali 2      |
| **\<IPv4-address-space>**   | 192.168.0.0/16          |
| **\<subnet-name>**          | mySubnetsource        |
| **\<subnet-address-range>** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-source-virtual-machine"></a>Creare la macchina virtuale di origine

Ora verrà creata una macchina virtuale per l'uso del servizio NAT. Questa macchina virtuale ha un indirizzo IP pubblico da usare come indirizzo IP pubblico a livello di istanza per consentire l'accesso alla macchina virtuale. Il servizio NAT è in grado di riconoscere la direzione del flusso e sostituirà la destinazione Internet predefinita nella subnet. L'indirizzo IP pubblico della macchina virtuale non verrà usato per le connessioni in uscita.

Per testare il gateway NAT, verrà assegnata una risorsa indirizzo IP pubblico come indirizzo IP pubblico a livello di istanza per accedere a questa macchina virtuale dall'esterno. Questo indirizzo viene usato solo per accedervi ai fini del test.  Verrà illustrato il modo in cui il servizio NAT avrà la precedenza rispetto ad altre opzioni in uscita.

Per esercitarsi, è anche possibile creare questa macchina virtuale senza un indirizzo IP pubblico e creare un'altra macchina virtuale da usare come JumpBox senza un indirizzo IP pubblico.

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Calcolo** > **Ubuntu Server 18.04 LTS** o cercare **Ubuntu Server 18.04 LTS** nella ricerca del Marketplace.

2. In **Crea macchina virtuale** immettere o selezionare i valori seguenti nella scheda **Informazioni di base**:
   - **Sottoscrizione** > **Gruppo di risorse**: selezionare **myResourceGroupNAT**.
   - **Dettagli istanza** > **Nome macchina virtuale**: immettere **myVMsource**.
   - **Dettagli istanza** > **Area** > selezionare **Stati Uniti orientali 2**.
   - **Account amministratore** > **Autenticazione immettere**: selezionare **Password**.
   - **Account amministratore**> immettere le informazioni relative a **Nome utente**, **Password** e **Conferma password**.
   - **Regole porta in ingresso** > **Porte in ingresso pubbliche**: Selezionare **Consenti porte selezionate**.
   - **Regole porta in ingresso** > **Selezionare le porte in ingresso**: selezionare **SSH (22)**
   - Selezionare la scheda **Rete**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**.

3. Nella scheda **Rete** verificare che siano selezionate le opzioni seguenti:
   - **Rete virtuale**: **myVnetsource**
   - **Subnet**: **mySubnetsource**
   - **Indirizzo IP pubblico** > selezionare **Crea nuovo**.  Nella finestra **Crea indirizzo IP pubblico** immettere **myPublicIPsourceVM** nel campo **Nome**. Selezionare **Standard** per lo **SKU**. Accettare tutte le impostazioni predefinite e fare clic su **OK**.
   - **Gruppo di sicurezza di rete della scheda di interfaccia di rete**: Selezionare **Basic**.
   - **Porte in ingresso pubbliche**: Selezionare **Consenti porte selezionate**.
   - **Selezionare le porte in ingresso**: Verificare che l'opzione **SSH** sia selezionata.

4. Nella scheda **Gestione**, in **Monitoraggio**, impostare **Diagnostica di avvio** su **Off**.

5. Selezionare **Rivedi e crea**.

6. Rivedere le impostazioni e fare clic su **Crea**.

## <a name="create-the-nat-gateway"></a>Creare il gateway NAT

Con il gateway NAT è possibile usare una o più risorse indirizzo IP pubblico, prefissi IP pubblici o entrambi. Verranno aggiunti una risorsa indirizzo IP pubblico, un prefisso indirizzo IP pubblico e una risorsa gateway NAT.

Questa sezione descrive in modo dettagliato come creare e configurare i componenti seguenti del servizio NAT tramite la risorsa gateway NAT:
  - Un pool di indirizzi IP pubblici e un prefisso indirizzo IP pubblico da usare per i flussi in uscita convertiti dalla risorsa gateway NAT.
  - Modificare il timeout di inattività dal valore predefinito di 4 minuti a 10 minuti.

### <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Rete** > **Indirizzo IP pubblico** o cercare **Indirizzo IP pubblico** nella ricerca del Marketplace. 

2. In **Crea indirizzo IP pubblico** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Versione indirizzo IP | Selezionare **IPv4**.
    | SKU | Selezionare **Standard**.
    | Nome | Immettere **myPublicIPsource**. |
    | Subscription | Selezionare la propria sottoscrizione.|
    | Resource group | selezionare **myResourceGroupNAT**. |
    | Location | Selezionare **Stati Uniti orientali 2**.|

3. Accettare tutte le impostazioni predefinite e selezionare **Crea**.

### <a name="create-a-public-ip-prefix"></a>Creare un prefisso indirizzo IP pubblico

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Rete** > **Prefisso indirizzo IP pubblico** o cercare **Prefisso indirizzo IP pubblico** nella ricerca del Marketplace.

2. In **Crea prefisso indirizzo IP pubblico** immettere o selezionare i valori seguenti nella scheda **Informazioni di base**:
   - **Sottoscrizione** > **Gruppo di risorse**: Selezionare **myResourceGroupNAT**>
   - **Dettagli istanza** > **Nome**: immettere **myPublicIPprefixsource**.
   - **Dettagli istanza** > **Area**: Selezionare **Stati Uniti orientali 2**.
   - **Dettagli istanza** > **Dimensioni del prefisso**: selezionare **/31 (2 indirizzi)**

3. Lasciare i valori predefiniti di tutte le altre impostazioni e selezionare **Rivedi e crea**.

4. Rivedere le impostazioni e quindi selezionare **Crea**.


### <a name="create-a-nat-gateway-resource"></a>Creare una risorsa gateway NAT

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Rete** > **Gateway NAT** o cercare **Gateway NAT** nella ricerca del Marketplace.

2. In **Crea un gateway NAT (Network Address Translation)** immettere o selezionare i valori seguenti nella scheda **Informazioni di base**:
   - **Sottoscrizione** > **Gruppo di risorse**: selezionare **myResourceGroupNAT**.
   - **Dettagli istanza** > **Nome del gateway NAT**: immettere **myNATgateway**.
   - **Dettagli istanza** > **Area**: Selezionare **Stati Uniti orientali 2**.
   - **Dettagli istanza** > **Timeout di inattività (minuti)** : immettere **10**.
   - Selezionare la scheda **Indirizzo IP pubblico** oppure **Avanti: Indirizzo IP pubblico**.

3. Nella scheda **Indirizzo IP pubblico** immettere o selezionare i valori seguenti:
   - **Indirizzi IP pubblici**: Selezionare **myPublicIPsource**.
   - **Prefissi indirizzo IP pubblico**: Selezionare **myPublicIPprefixsource**.
   - Selezionare la scheda **Subnet** oppure **Avanti: Subnet**.

4. Nella scheda **Subnet** immettere o selezionare i valori seguenti:
   - **Rete virtuale**: Selezionare **myResourceGroupNAT** > **myVnetsource**.
   - **Nome della subnet**: Selezionare la casella accanto a **mySubnetsource**.

5. Selezionare **Rivedi e crea**.

6. Rivedere le impostazioni e quindi selezionare **Crea**.

A questo punto, tutto il traffico in uscita verso le destinazioni Internet usa il servizio NAT.  Non è necessario configurare una route definita dall'utente.


## <a name="prepare-destination-for-outbound-traffic"></a>Preparare la destinazione per il traffico in uscita

Ora verrà creata una destinazione per il traffico in uscita convertito dal servizio NAT per consentirne il test.


## <a name="virtual-network-and-parameters-for-destination"></a>Rete virtuale e parametri per la destinazione

Prima di distribuire una macchina virtuale per la destinazione, è necessario creare una rete virtuale in cui può risiedere la macchina virtuale di destinazione. Di seguito sono riportati gli stessi passaggi eseguiti per la macchina virtuale di origine con alcune piccole modifiche per esporre l'endpoint di destinazione.

In questa sezione è necessario sostituire i parametri seguenti delle procedure con le informazioni riportate di seguito:

| Parametro                   | valore                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetdestination          |
| **\<region-name>**          | Stati Uniti orientali 2      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnetdestination        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-destination-virtual-machine"></a>Creare la macchina virtuale di destinazione

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Calcolo** > **Ubuntu Server 18.04 LTS** o cercare **Ubuntu Server 18.04 LTS** nella ricerca del Marketplace.

2. In **Crea macchina virtuale** immettere o selezionare i valori seguenti nella scheda **Informazioni di base**:
   - **Sottoscrizione** > **Gruppo di risorse**: selezionare **myResourceGroupNAT**.
   - **Dettagli istanza** > **Nome macchina virtuale**: immettere **myVMdestination**.
   - **Dettagli istanza** > **Area** > selezionare **Stati Uniti orientali 2**.
   - **Account amministratore** > **Autenticazione immettere**: selezionare **Password**.
   - **Account amministratore**> immettere le informazioni relative a **Nome utente**, **Password** e **Conferma password**.
   - **Regole porta in ingresso** > **Porte in ingresso pubbliche**: Selezionare **Consenti porte selezionate**.
   - **Regole porta in ingresso** > **Selezionare le porte in ingresso**: Selezionare **SSH (22)** e **HTTP (80)** .
   - Selezionare la scheda **Rete**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**.

3. Nella scheda **Rete** verificare che siano selezionate le opzioni seguenti:
   - **Rete virtuale**: **myVnetdestination**
   - **Subnet**: **mySubnetdestination**
   - **Indirizzo IP pubblico** > selezionare **Crea nuovo**.  Nella finestra **Crea indirizzo IP pubblico** immettere **myPublicIPdestinationVM** nel campo **Nome**. Selezionare **Standard** per lo **SKU**. Accettare tutte le impostazioni predefinite e fare clic su **OK**.
   - **Gruppo di sicurezza di rete della scheda di interfaccia di rete**: Selezionare **Basic**.
   - **Porte in ingresso pubbliche**: Selezionare **Consenti porte selezionate**.
   - **Selezionare le porte in ingresso**: Verificare che le opzioni **SSH** e **HTTP** siano selezionate.

4. Nella scheda **Gestione**, in **Monitoraggio**, impostare **Diagnostica di avvio** su **Off**.

5. Selezionare **Rivedi e crea**.

6. Rivedere le impostazioni e quindi selezionare **Crea**.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Preparare un server Web e testare il payload nella macchina virtuale di destinazione

È prima di tutto necessario individuare l'indirizzo IP della macchina virtuale di destinazione. 

1. Nella parte sinistra del portale selezionare **Gruppi di risorse**.
2. Selezionare **myResourceGroupNAT**.
3. Selezionare **myVMdestination**.
4. In **Panoramica** copiare il valore di **Indirizzo IP pubblico** e incollarlo negli Appunti, in modo da poterlo usare per accedere alla macchina virtuale.

>[!IMPORTANT]
>Copiare l'indirizzo IP pubblico e quindi incollarlo negli Appunti, in modo da poterlo usare nei passaggi successivi. Indicare che si tratta della macchina virtuale di destinazione.

### <a name="sign-in-to-destination-vm"></a>Accedere alla macchina virtuale di destinazione

Aprire un'istanza di [Azure Cloud Shell](https://shell.azure.com) nel browser. Usare l'indirizzo IP recuperato nel passaggio precedente per stabilire una sessione SSH con la macchina virtuale.

```azurecli-interactive
ssh <username>@<ip-address-destination>
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

È prima di tutto necessario individuare l'indirizzo IP della macchina virtuale di origine.

1. Nella parte sinistra del portale selezionare **Gruppi di risorse**.
2. Selezionare **myResourceGroupNAT**.
3. Selezionare **myVMsource**.
4. In **Panoramica** copiare il valore di **Indirizzo IP pubblico** e incollarlo negli Appunti, in modo da poterlo usare per accedere alla macchina virtuale.

>[!IMPORTANT]
>Copiare l'indirizzo IP pubblico e quindi incollarlo negli Appunti, in modo da poterlo usare nei passaggi successivi. Indicare che si tratta della macchina virtuale di origine.

### <a name="log-into-source-vm"></a>Accedere alla macchina virtuale di origine

Aprire una nuova scheda per [Azure Cloud Shell](https://shell.azure.com) nel browser.  Usare l'indirizzo IP recuperato nel passaggio precedente per stabilire una sessione SSH con la macchina virtuale. 

```azurecli-interactive
ssh <username>@<ip-address-source>
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

Questo comando genererà 100 richieste, di cui 10 simultanee, con un timeout di 30 secondi e senza riutilizzare la connessione TCP.  Ogni richiesta recupererà 100 KB.  Al termine dell'esecuzione, **hey** segnalerà alcune statistiche relative alle prestazioni del servizio NAT.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il gateway NAT e tutte le risorse correlate. Selezionare il gruppo di risorse **myResourceGroupNAT** che contiene il gateway NAT e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato creato un gateway NAT, sono state create una macchina virtuale di origine e una di destinazione e quindi è stato testato il gateway NAT.

Esaminare le metriche in Monitoraggio di Azure per visualizzare il servizio NAT in funzione. Diagnosticare i problemi, ad esempio l'esaurimento delle risorse delle porte SNAT disponibili.  L'esaurimento delle risorse delle porte SNAT è facilmente risolvibile aggiungendo altre risorse indirizzo IP pubblico, risorse prefisso indirizzo IP pubblico o entrambe.

- Informazioni sul [servizio NAT di rete virtuale](./nat-overview.md)
- Informazioni sulla [risorsa gateway NAT](./nat-gateway-resource.md).
- Avvio rapido per la distribuzione della [risorsa gateway NAT tramite l'interfaccia della riga di comando di Azure](./quickstart-create-nat-gateway-cli.md).
- Avvio rapido per la distribuzione della [risorsa gateway NAT tramite Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Avvio rapido per la distribuzione della [risorsa gateway NAT tramite il portale di Azure](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

