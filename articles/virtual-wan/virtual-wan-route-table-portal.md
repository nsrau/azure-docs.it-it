---
title: "WAN virtuale: Creare una tabella di route dell'hub virtuale in NVA: Portale di AzureVirtual WAN: Create virtual hub route table to NVA: Azure portal"
description: Tabella di route dell'hub virtuale WAN virtuale per indirizzare il traffico a un'appliance virtuale di rete tramite il portale.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 0807b535adc45093b439dba5ab8a0ea26b2a0721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402923"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Creare una tabella di route dell'hub WAN virtuale per le nVA: portale di AzureCreate a Virtual WAN hub route table for NVAAs: Azure portal

In questo articolo viene illustrato come sterzare il traffico da una succursale (sito locale) connessa all'hub WAN virtuale a una rete virtuale Spoke (VNet) tramite un'appliance virtuale di rete (NVA).

![Diagramma della rete WAN virtuale](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di aver soddisfatto i criteri seguenti:

*  Si dispone di un network Virtual Appliance (NVA). Un'appliance virtuale di rete è un software di terze parti di propria scelta di cui viene in genere eseguito il provisioning da Azure Marketplace in una rete virtuale.

    * Un indirizzo IP privato deve essere assegnato all'interfaccia di rete NVA.

    * L'appliance virtuale di rete non viene distribuita nell'hub virtuale. Deve essere distribuito in una rete virtuale separata.

    *  La rete virtuale NVA può avere una o più reti virtuali connesse. In questo articolo, si fa riferimento alla rete virtuale NVA come una 'rete virtuale a raggi indiretti'. Queste reti virtuali possono essere connesse alla rete virtuale NVA tramite il peering della rete virtuale. I collegamenti di peering VNet sono rappresentati da frecce nere nella figura precedente tra VNet 1, VNet 2 e NVA VNet.
*  Sono state create due reti virtuali. Essi saranno utilizzati come vNet a raggi.

    * Gli spazi degli indirizzi della rete virtuale sono: VNet1: VNet1: 10.0.2.0/24 e VNet2: 10.0.3.0/24. Per informazioni su come creare una rete virtuale, vedere [Creare una rete virtuale.](../virtual-network/quick-create-portal.md)

    * Verificare che non siano presenti gateway di rete virtuale in nessuna delle reti virtuali.

    * Le reti virtuali non richiedono una subnet gateway.

## <a name="1-sign-in"></a><a name="signin"></a>1. Accedi

In un browser passare al [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. Creare una rete WAN virtuale

Creare una rete WAN virtuale. Utilizzare i valori di esempio seguenti:Use the following example values:

* **Nome WAN virtuale:** myVirtualWAN
* **Gruppo di risorse:** testRG
* **Località:** Stati Uniti occidentali

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. Creare un hub

Creare l'hub. Utilizzare i valori di esempio seguenti:Use the following example values:

* **Località:** Stati Uniti occidentali
* **Nome:** westushub
* **Spazio di indirizzi privati dell'hub:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. Creare e applicare una tabella di route hub

Aggiornare l'hub con una tabella di route dell'hub. Utilizzare i valori di esempio seguenti:Use the following example values:

* **Spazi degli indirizzi VNet a spoke:** (VNet1 e VNet2) 10.0.2.0/24 e 10.0.3.0/24
* **Indirizzo IP privato dell'interfaccia di rete NVA DM:** 10.0.4.5

1. Passare alla rete WAN virtuale.
2. Fare clic sull'hub per il quale si desidera creare una tabella di route.
3. Fare clic su **...**, quindi su **Modifica hub virtuale**.
4. Nella pagina **Modifica hub virtuale** scorrere verso il basso e selezionare la casella di controllo Usa tabella per il **routing**.
5. Nella colonna **If destination prefix è** , aggiungere gli spazi di indirizzo. Nella colonna **Invia all'hop successivo** aggiungere l'indirizzo IP privato dell'interfaccia di rete NVA.
6. Fare clic su **Conferma** per aggiornare la risorsa hub con le impostazioni della tabella di route.

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. Creare le connessioni della rete virtuale

Creare una connessione di rete virtuale da ogni rete virtuale a raggi indiretti (VNet1 e VNet2) all'hub. Queste connessioni di rete virtuale sono rappresentate dalle frecce blu nella figura precedente. Quindi, creare una connessione vNet dalla rete virtuale NVA all'hub (freccia nera nella figura).

 Per questo passaggio, è possibile utilizzare i valori seguenti:For this step, you can use the following values:

| Nome della rete virtuale| Nome connessione|
| --- | --- |
| VNet1 | testconnessione1 |
| VNet2 | testconnessione2 |
| Rete virtuale di Licenza con valori NVAVNet | testconnessione3 |

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
