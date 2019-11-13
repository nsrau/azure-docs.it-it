---
title: "Rete WAN virtuale: creare una tabella di route dell'hub virtuale in appliance virtuale di rete: portale di Azure"
description: Tabella di route dell'hub virtuale WAN virtuale per indirizzare il traffico a un'appliance virtuale di rete tramite il portale.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 8f24b94226daffb769993c9f6659909fdff039b6
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014974"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Creare una tabella di route dell'hub WAN virtuale per appliance virtuali: portale di Azure

Questo articolo illustra come guidare il traffico da un hub a un'appliance virtuale di rete.

![Diagramma della rete WAN virtuale](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di aver soddisfatto i criteri seguenti:

*  Si dispone di un'appliance virtuale di rete (NVA). Un'appliance virtuale di rete è un software di terze parti che in genere viene sottoposta a provisioning da Azure Marketplace in una rete virtuale.

    * Un indirizzo IP privato deve essere assegnato all'interfaccia di rete dell'appliance virtuale di rete.

    * L'appliance virtuale di dispositivo non è distribuita nell'hub virtuale. Deve essere distribuita in una rete virtuale separata.

    *  Il VNet dell'appliance virtuale di rete può avere una o più reti virtuali connesse. In questo articolo si fa riferimento all'appliance virtuale di VNet come "VNet spoke indiretta". Questi reti virtuali possono essere connessi al VNet dell'appliance virtuale di dispositivo usando il peering VNet.
*  Sono stati creati 2 reti virtuali. Verranno usati come spoke reti virtuali.

    * Per questo esercizio, gli spazi di indirizzi VNet spoke sono: VNet1:10.0.2.0/24 e VNet2:10.0.3.0/24. Se sono necessarie informazioni su come creare un VNet, vedere [creare una rete virtuale](../virtual-network/quick-create-portal.md).

    * Assicurarsi che non siano presenti gateway di rete virtuale in nessuno dei reti virtuali.
    * Per questa configurazione, questi reti virtuali non richiedono una subnet del gateway.

## <a name="signin"></a>1. accedi

In un browser passare al [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.

## <a name="vwan"></a>2. creare una rete WAN virtuale

Creare una rete WAN virtuale. Ai fini di questo esercizio, è possibile usare i valori seguenti:

* **Nome WAN virtuale:** myVirtualWAN
* **Gruppo di risorse:** testRG
* **Percorso:** Stati Uniti occidentali

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. creare un hub

Creare l'hub. Ai fini di questo esercizio, è possibile usare i valori seguenti:

* **Percorso:** Stati Uniti occidentali
* **Nome:** westushub
* **Spazio indirizzi privato Hub:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. creare e applicare una tabella di route Hub

Aggiornare l'hub con una tabella di route Hub. Ai fini di questo esercizio, è possibile usare i valori seguenti:

* **Spazi di indirizzi VNet spoke indiretta:** (VNet1 e VNet2) 10.0.2.0/24 e 10.0.3.0/24
* **Indirizzo IP privato dell'interfaccia di rete DMZ rete virtuale:** 10.0.4.5

1. Passare alla rete WAN virtuale.
2. Fare clic sull'hub per il quale si desidera creare una tabella di route.
3. Fare clic su **...** e quindi su **modifica hub virtuale**.
4. Nella pagina **modifica hub virtuale** scorrere verso il basso e selezionare la casella **di controllo Usa tabella per il routing**.
5. Nella colonna **se il prefisso di destinazione è** , aggiungere gli spazi degli indirizzi. Nella colonna **Invia a hop successivo** aggiungere l'indirizzo IP privato dell'interfaccia di rete DMZ.
6. Fare clic su **conferma** per aggiornare la risorsa Hub con le impostazioni della tabella di route.

## <a name="connections"></a>5. creare le connessioni VNet

Creare una connessione da ogni VNet spoke indiretta (VNet1 e VNet2) all'hub. Quindi, creare una connessione dall'appliance virtuale di VNet all'hub.

 Per questo passaggio, è possibile usare i valori seguenti:

| Nome della rete virtuale| Nome connessione|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Ripetere la procedura seguente per ogni VNet che si desidera connettere.

1. Nella pagina della rete WAN virtuale fare clic su **Connessioni rete virtuale**.
2. Nella pagina di connessione alla rete virtuale fare clic su **+ Aggiungi connessione**.
3. Nella pagina **Aggiungi connessione** compilare i campi seguenti:

    * **Nome connessione** - Specificare un nome per la connessione.
    * **Hub** - Selezionare l'hub che si vuole associare a questa connessione.
    * **Sottoscrizione** - Verificare la sottoscrizione.
    * **Rete virtuale** - Selezionare la rete virtuale che si vuole connettere all'hub. La rete virtuale non può avere un gateway di rete virtuale già esistente.
4. Fare clic su **OK** per creare la connessione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).