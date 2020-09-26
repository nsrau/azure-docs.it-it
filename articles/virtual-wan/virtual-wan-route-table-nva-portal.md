---
title: "Rete WAN virtuale: Creare una tabella di route dell'hub virtuale a un'appliance virtuale di rete: Portale di Azure"
description: Tabella di route dell'hub virtuale della rete WAN virtuale per indirizzare il traffico a un'appliance virtuale di rete tramite il portale.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 8de7ad2808e5319819410b3125472e28496647b0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267143"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Creare una tabella di route dell'hub della rete WAN virtuale per appliance virtuali di rete: Portale di Azure

Questo articolo illustra come indirizzare il traffico da un ramo (sito locale) connesso all'hub della rete WAN virtuale a una rete virtuale Spoke tramite un'appliance virtuale di rete (Network Virtual Appliance, NVA).

![Diagramma della rete WAN virtuale](./media/virtual-wan-route-table-nva/vwanroute.png)

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di aver soddisfatto i criteri seguenti:

*  Si dispone di un'appliance virtuale di rete (Network Virtual Appliance, NVA). Un'appliance virtuale di rete è un software di terze parti di propria scelta, in genere con provisioning da Azure Marketplace in una rete virtuale.

    * All'interfaccia di rete dell'NVA deve essere assegnato un indirizzo IP privato.

    * L'NVA non può essere distribuita nell'hub virtuale, ma deve essere distribuita in una rete virtuale separata.

    *  L'NVA può avere una o più reti virtuali connesse ad essa. In questo articolo si fa riferimento alla rete virtuale dell'NVA come "rete virtuale Spoke indiretta". Queste reti virtuali possono essere connesse alla rete virtuale dell'NVA tramite il peering della rete virtuale. Nella figura precedente, i collegamenti per il peering della rete virtuale sono rappresentati da frecce nere tra le reti VNet 1, VNet 2 e NVA VNet.
*  Sono state create due reti virtuali, che verranno usate come reti virtuali Spoke.

    * Gli spazi indirizzi della rete virtuale Spoke sono: VNet1: 10.0.2.0/24 e VNet2: 10.0.3.0/24. Per informazioni su come creare una rete virtuale, vedere [Creare una rete virtuale](../virtual-network/quick-create-portal.md).

    * Assicurarsi che non ci siano gateway di rete virtuale in nessuna rete virtuale.

    * Le reti virtuali non richiedono alcuna subnet del gateway.

## <a name="1-sign-in"></a><a name="signin"></a>1. Accesso

In un browser passare al [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. Creare una rete WAN virtuale

Creare una rete WAN virtuale. Usare i valori di esempio seguenti:

* **Nome della rete WAN virtuale:** myVirtualWAN
* **Gruppo di risorse:** testRG
* **Località:** Stati Uniti occidentali

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. Creare un hub

Creare l'hub. Usare i valori di esempio seguenti:

* **Località:** Stati Uniti occidentali
* **Nome:** westushub
* **Spazio di indirizzi privato dell'hub:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. Creare e applicare una tabella di route dell'hub

Aggiornare l'hub con una tabella di route dell'hub. Usare i valori di esempio seguenti:

* **Spazi indirizzi della rete virtuale Spoke:** (VNet1 e VNet2) 10.0.2.0/24 e 10.0.3.0/24
* **Indirizzo IP privato dell'interfaccia di rete dell'NVA della rete perimetrale:** 10.0.4.5

1. Passare alla rete WAN virtuale.
2. Fare clic sull'hub per il quale si desidera creare una tabella di route.
3. Fare clic su **...** e quindi fare clic su **Modifica hub virtuale**.
4. Nella pagina **Modifica hub virtuale**, scorrere verso il basso e selezionare la casella di controllo **Usa tabella per il routing**.
5. Nella colonna **Se il prefisso di destinazione è**, aggiungere gli spazi indirizzi. Nella colonna **Invia all'indirizzo hop successivo**, aggiungere l'indirizzo IP privato dell'interfaccia di rete dell'NVA della rete perimetrale.

   > [!NOTE]
   > La rete dell'NVA della rete perimetrale è applicabile all'hub locale.
   
6. Fare clic su **Conferma** per aggiornare la risorsa hub con le impostazioni della tabella di route.

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. Creare connessioni di rete virtuale

Creare una connessione di rete virtuale da ogni rete virtuale Spoke indiretta (VNet1 e VNet2) all'hub. Nella figura precedente, queste connessioni di rete virtuale sono rappresentate da frecce blu. Creare quindi una connessione di rete virtuale dalla rete virtuale dell'appliance di rete virtuale all'hub (freccia nera nella figura).

 Per questo passaggio, è possibile usare i seguenti valori:

| Nome della rete virtuale| Nome connessione|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Ripetere la procedura seguente per ogni rete virtuale che si desidera connettere.

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
