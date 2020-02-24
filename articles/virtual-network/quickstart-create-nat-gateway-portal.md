---
title: 'Avvio rapido: Creare un gateway NAT - Portale di Azure'
titlesuffix: Azure Virtual Network NAT
description: Questa guida di avvio rapido mostra come creare un gateway NAT usando il portale di Azure
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 8913c956554fa7bf1d0362b44dc6f8031ffd74f9
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429132"
---
# <a name="quickstart-create-a-nat-gateway-using-the-azure-portal"></a>Avvio rapido: Creare un gateway NAT usando il portale di Azure

Questa guida di avvio rapido illustra come usare il servizio NAT di rete virtuale di Azure. Verrà creato un gateway NAT per fornire connettività in uscita per una macchina virtuale in Azure. 

>[!NOTE] 
>Il servizio NAT di rete virtuale di Azure è attualmente disponibile come anteprima pubblica e in un set limitato di [aree](./nat-overview.md#region-availability). Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

> [!IMPORTANT]
> Dopo aver abilitato il servizio NAT di rete virtuale di Azure [anteprima](./nat-overview.md#enable-preview) nella sottoscrizione, usare https://aka.ms/natportal per accedere al portale.

Accedere al [portale di Azure](https://aka.ms/natportal).


### <a name="create-a-virtual-network"></a>Crea rete virtuale

Prima di distribuire una macchina virtuale e di poter usare il gateway NAT, è necessario creare il gruppo di risorse e la rete virtuale.  

1. Nella parte superiore sinistra della schermata, selezionare **Crea una risorsa** > **Rete** > **Rete virtuale** o cercare una **Rete virtuale** nella ricerca del Marketplace.

2. In **Crea rete virtuale** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Nome | Immettere **myVNet**. |
    | Spazio degli indirizzi | Immettere **192.168.0.0/16**. |
    | Subscription | Selezionare la propria sottoscrizione.|
    | Resource group | Selezionare Crea nuovo - **myResourceGroup**. |
    | Location | Selezionare **Stati Uniti orientali 2**.|
    | Subnet - Nome | Immettere **mySubnet**. |
    | Subnet - Intervallo di indirizzi | Immettere **192.168.0.0/24**. |

3. Accettare tutte le impostazioni predefinite e selezionare **Crea**.

### <a name="create-a-vm-to-use-the-nat-gateway"></a>Creare una macchina virtuale da usare con il gateway NAT

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
- [Inviare commenti e suggerimenti sull'anteprima pubblica](https://aka.ms/natfeedback).
> [!div class="nextstepaction"]

