---
title: 'Esercitazione: Creare un gateway NAT - Portale di Azure'
titlesuffix: Azure Virtual Network NAT
description: Questa guida di avvio rapido mostra come creare un gateway NAT usando il portale di Azure
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 5010dc08b695a0376ace5dde935c63caf0c39633
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84711409"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>Esercitazione: Creare un gateway NAT usando il portale di Azure

Questa esercitazione illustra come usare il servizio NAT di rete virtuale di Azure. Verrà creato un gateway NAT per fornire connettività in uscita per una macchina virtuale in Azure. 

Se si preferisce, per eseguire questa procedura è possibile usare l'[interfaccia della riga di comando di Azure](quickstart-create-nat-gateway-cli.md), [Azure PowerShell](quickstart-create-nat-gateway-powershell.md) o distribuire un [Modello di ARM](quickstart-create-nat-gateway-powershell.md) in alternativa al portale.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="virtual-network-and-parameters"></a>Rete virtuale e parametri

Prima di distribuire una macchina virtuale e di poter usare il gateway NAT, è necessario creare il gruppo di risorse e la rete virtuale.

In questa sezione è necessario sostituire i parametri seguenti delle procedure con le informazioni riportate di seguito:

| Parametro                   | valore                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Stati Uniti orientali 2      |
| **\<IPv4-address-space>**   | 192.168.0.0\16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-vm-to-use-the-nat-gateway"></a>Creare una macchina virtuale da usare con il gateway NAT

Ora verrà creata una macchina virtuale per l'uso del servizio NAT. Questa macchina virtuale ha un indirizzo IP pubblico da usare come indirizzo IP pubblico a livello di istanza per consentire l'accesso alla macchina virtuale. Il servizio NAT è in grado di riconoscere la direzione del flusso e sostituirà la destinazione Internet predefinita nella subnet. L'indirizzo IP pubblico della macchina virtuale non verrà usato per le connessioni in uscita.

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Calcolo** > **Ubuntu Server 18.04 LTS** o cercare **Ubuntu Server 18.04 LTS** nella ricerca del Marketplace.

2. In **Crea macchina virtuale** digitare o selezionare i valori seguenti nella scheda **Nozioni di base**:
   - **Sottoscrizione** > **Gruppo di risorse**: selezionare **myResourceGroupNAT**.
   - **Dettagli istanza** > **Nome macchina virtuale**: Digitare **myVM**.
   - **Dettagli istanza** > **Area** > selezionare **Stati Uniti orientali 2**.
   - **Account amministratore** > **Autenticazione digitare**: selezionare **Password**.
   - **Account amministratore**> immettere le informazioni relative a **Nome utente**, **Password** e **Conferma password**.
   - **Regole porta in ingresso** > **Porte in ingresso pubbliche**: Selezionare **Consenti porte selezionate**.
   - **Regole porta in ingresso** > **Selezionare le porte in ingresso**: selezionare **SSH (22)**
   - Selezionare la scheda **Rete**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**.

3. Nella scheda **Rete** verificare che siano selezionate le opzioni seguenti:
   - **Rete virtuale**: **myVnet**
   - **Subnet**: **mySubnet**
   - **Indirizzo IP pubblico** > selezionare **Crea nuovo**.  Nella finestra di **Crea indirizzo IP pubblico** digitare **myPublicIPVM** nel campo **Nome** e scegliere **Standard** per **SKU**.  Fare clic su **OK**.
   - **Gruppo di sicurezza di rete della scheda di interfaccia di rete**: Selezionare **Basic**.
   - **Porte in ingresso pubbliche**: Selezionare **Consenti porte selezionate**.
   - **Selezionare le porte in ingresso**: Verificare che l'opzione **SSH** sia selezionata.

4. Nella scheda **Gestione**, in **Monitoraggio**, impostare **Diagnostica di avvio** su **Off**.

5. Selezionare **Rivedi e crea**. 

6. Rivedere le impostazioni e fare clic su **Crea**.

## <a name="create-the-nat-gateway"></a>Creare il gateway NAT

È possibile usare una o più risorse indirizzo IP pubblico, prefissi IP pubblici o entrambi. Verranno aggiunti una risorsa indirizzo IP pubblico, un prefisso indirizzo IP pubblico e una risorsa gateway NAT.

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
    | Nome | Immettere **myPublicIP**. |
    | Subscription | Selezionare la propria sottoscrizione.|
    | Resource group | selezionare **myResourceGroupNAT**. |
    | Location | Selezionare **Stati Uniti orientali 2**.|

3. Accettare tutte le impostazioni predefinite e selezionare **Crea**.

### <a name="create-a-public-ip-prefix"></a>Creare un prefisso indirizzo IP pubblico

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Rete** > **Prefisso indirizzo IP pubblico** o cercare **Prefisso indirizzo IP pubblico** nella ricerca del Marketplace. 

2. In **Crea prefisso indirizzo IP pubblico** digitare o selezionare i valori seguenti nella scheda **Informazioni di base**:
   - **Sottoscrizione** > **Gruppo di risorse**: selezionare **myResourceGroupNAT**>
   - **Dettagli istanza** > **Nome**: digitare **myPublicIPprefix**.
   - **Dettagli istanza** > **Area**: Selezionare **Stati Uniti orientali 2**.
   - **Dettagli istanza** > **Dimensioni del prefisso**: selezionare **/31 (2 indirizzi)**

3. Lasciare i valori predefiniti di tutte le altre impostazioni e selezionare **Rivedi e crea**.

4. Rivedere le impostazioni e quindi selezionare **Crea**.
   

### <a name="create-a-nat-gateway-resource"></a>Creare una risorsa gateway NAT

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Rete** > **Gateway NAT** o cercare **Gateway NAT** nella ricerca del Marketplace.

2. In **Crea un gateway NAT (Network Address Translation)** digitare o selezionare i valori seguenti nella scheda **Informazioni di base**:
   - **Sottoscrizione** > **Gruppo di risorse**: selezionare **myResourceGroupNAT**.
   - **Dettagli istanza** > **Nome del gateway NAT**: immettere **myNATgateway**.
   - **Dettagli istanza** > **Area**: Selezionare **Stati Uniti orientali 2**.
   - **Dettagli istanza** > **Timeout di inattività (minuti)** : digitare **10**.
   - Selezionare la scheda **Indirizzo IP pubblico** oppure **Avanti: Indirizzo IP pubblico**.

3. Nella scheda **Indirizzo IP pubblico** digitare o selezionare i valori seguenti:
   - **Indirizzi IP pubblici**: selezionare **myPublicIP**.
   - **Prefissi indirizzo IP pubblico**: selezionare **myPublicIPprefix**.
   - Selezionare la scheda **Subnet** oppure **Avanti: Subnet**.

4. Nella scheda **Subnet** digitare o selezionare i valori seguenti:
   - **Rete virtuale**: selezionare **myResourceGroupNAT** > **myVnet**.
   - **Nome della subnet**: selezionare la casella accanto a **mySubnet**.

5. Selezionare **Rivedi e crea**.

6. Rivedere le impostazioni e quindi selezionare **Crea**.

## <a name="discover-the-ip-address-of-the-vm"></a>Individuare l'indirizzo IP della macchina virtuale

1. Nella parte sinistra del portale selezionare **Gruppi di risorse**.
2. Selezionare **myResourceGroupNAT**.
3. Selezionare **myVM**.
4. In **Panoramica** copiare il valore di **Indirizzo IP pubblico** e incollarlo negli Appunti, in modo da poterlo usare per accedere alla macchina virtuale.

>[!IMPORTANT]
>Copiare l'indirizzo IP pubblico e quindi incollarlo negli Appunti, in modo da poterlo usare per accedere alla macchina virtuale.

## <a name="sign-in-to-vm"></a>Accedere alla macchina virtuale

Aprire un'istanza di [Azure Cloud Shell](https://shell.azure.com) nel browser. Usare l'indirizzo IP recuperato nel passaggio precedente per stabilire una sessione SSH con la macchina virtuale.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

A questo punto si è pronti per usare il servizio NAT.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il gateway NAT e tutte le risorse correlate. Selezionare il gruppo di risorse **myResourceGroupNAT** che contiene il gateway NAT e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati creati un gateway NAT e una macchina virtuale. 

Esaminare le metriche in Monitoraggio di Azure per visualizzare il servizio NAT in funzione. Diagnosticare i problemi, ad esempio l'esaurimento delle risorse delle porte SNAT disponibili.  L'esaurimento delle risorse delle porte SNAT è risolvibile aggiungendo altre risorse indirizzo IP pubblico, risorse prefisso indirizzo IP pubblico o entrambe.


- Informazioni sul [servizio NAT di rete virtuale di Azure](./nat-overview.md)
- Informazioni sulla [risorsa gateway NAT](./nat-gateway-resource.md).
- Avvio rapido per la distribuzione della [risorsa gateway NAT tramite l'interfaccia della riga di comando di Azure](./quickstart-create-nat-gateway-cli.md).
- Avvio rapido per la distribuzione della [risorsa gateway NAT tramite Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Avvio rapido per la distribuzione della [risorsa gateway NAT tramite il portale di Azure](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]

