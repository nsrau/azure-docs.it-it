---
title: Creare una tabella di route dell'hub virtuale Azure rete WAN virtuale - portale di Azure | Microsoft Docs
description: Tabella di route dell'hub virtuale WAN virtuale per guidare il traffico verso appliance di rete virtuale usando il portale.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 2c8b3b4671fd14f9b10b8491861ae2c652f0188b
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580584"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Creare una rete WAN virtuale tabella di route dell'hub per Appliance virtuali di rete: portale di Azure

Questo articolo illustra come controllare il traffico proveniente da un hub a una rete virtuale Appliance (NVA).

![Diagramma della rete WAN virtuale](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di aver soddisfatto i criteri seguenti:

*  Si dispone di un'Appliance virtuale di rete (NVA). Appliance virtuale di rete è un software di terze parti di propria scelta che viene in genere il provisioning da Azure Marketplace in una rete virtuale.

    * Deve essere assegnato un indirizzo IP privato all'interfaccia di rete delle Appliance virtuali di rete.

    * Appliance virtuale di rete non è distribuito nell'hub virtuale. Deve essere distribuita in una rete virtuale separata.

    *  L'Appliance virtuale di rete della rete virtuale può avere uno o molti le reti virtuali connesse a esso. In questo articolo si fa riferimento alla rete virtuale di Appliance virtuale di rete come un ' indiretta rete virtuale spoke'. Queste reti virtuali possono essere connesse alla rete virtuale di Appliance di rete virtuale con peering reti virtuali.
*  È stato creato 2 reti virtuali. Verranno usati come reti virtuali spoke.

    * Per questa esercitazione, gli spazi di indirizzi della rete virtuale spoke sono: VNet1: 10.0.2.0/24 e VNet2: 10.0.3.0/24. Se sono necessarie informazioni su come creare una rete virtuale, vedere [creare una rete virtuale](../virtual-network/quick-create-portal.md).

    * Assicurarsi che non sono disponibili gateway di rete virtuale in una qualsiasi delle reti virtuali.
    * Per questa configurazione, queste reti virtuali non richiedono una subnet del gateway.

## <a name="signin"></a>1. Accedi

In un browser passare al [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.

## <a name="vwan"></a>2. Creare una rete WAN virtuale

Creare una rete WAN virtuale. Ai fini di questo esercizio, è possibile usare i valori seguenti:

* **Nome di rete WAN virtuale:** myVirtualWAN
* **Gruppo di risorse:** testRG
* **Località:** Stati Uniti occidentali

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Creare un hub

Creare l'hub. Ai fini di questo esercizio, è possibile usare i valori seguenti:

* **Località:** Stati Uniti occidentali
* **Nome:** westushub
* **Spazio di indirizzi privato hub:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. Creare e applicare una tabella di route dell'hub

Aggiornare l'hub con una tabella di route dell'hub. Ai fini di questo esercizio, è possibile usare i valori seguenti:

* **Spazi di indirizzi della rete virtuale spoke indiretta:** 10.0.3.0/24 e 10.0.2.0/24 (VNet1 e VNet2)
* **Appliance virtuale di rete Perimetrale rete interfaccia indirizzo IP privato:** 10.0.4.5

1. Passare a una rete WAN virtuale.
2. Fare clic sull'hub per il quale si desidera creare una tabella di route.
3. Fare clic su di **...** , quindi fare clic su **hub virtuale modifica**.
4. Nel **hub virtuale Edit** pagina, scorrere verso il basso e selezionare la casella di controllo **tabella usare per il routing**.
5. Nel **se il prefisso di destinazione è** colonna, aggiungere gli spazi degli indirizzi. Nel **invia il messaggio all'hop successivo** colonna, aggiungere l'Appliance virtuale di rete Perimetrale rete privata indirizzo IP dell'interfaccia.
6. Fare clic su **Confirm** per aggiornare la risorsa hub con le impostazioni nella tabella di route.

## <a name="connections"></a>5. Creare le connessioni di rete virtuale

Creare una connessione da ogni rete virtuale (VNet1 e VNet2) spoke indiretto all'hub. Quindi, creare una connessione da rete virtuale di Appliance virtuale di rete all'hub.

 Per questo passaggio, è possibile usare i valori seguenti:

| Nome rete virtuale| Nome connessione|
| --- | --- |
| Rete virtuale1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Ripetere la procedura seguente per ogni rete virtuale che si desidera connettersi.

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