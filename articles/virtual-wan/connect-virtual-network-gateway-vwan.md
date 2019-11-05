---
title: Connettere un gateway di rete virtuale a una rete WAN virtuale di Azure | Microsoft Docs
description: Questo articolo illustra come connettere un gateway di rete virtuale di Azure a un gateway VPN WAN virtuale di Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 1f8e0db9921c305edd2ee34efad22cdcf568f8df
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514953"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>Connettere un gateway VPN (gateway di rete virtuale) alla rete WAN virtuale

Questo articolo illustra come configurare la connettività da un gateway VPN di Azure (gateway di rete virtuale) a una rete WAN virtuale di Azure (gateway VPN). La creazione di una connessione da un gateway VPN (gateway di rete virtuale) a una rete WAN virtuale (gateway VPN) è simile alla configurazione della connettività a una rete WAN virtuale da siti VPN di Branch.

Per ridurre al minimo la possibile confusione tra due funzionalità, il gateway verrà anteposta al nome della funzionalità a cui si fa riferimento. Ad esempio, gateway di rete virtuale del gateway VPN e gateway VPN WAN virtuale.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare, creare le risorse seguenti:

Rete WAN virtuale di Azure

* [Creare una rete WAN virtuale](virtual-wan-site-to-site-portal.md#openvwan).
* [Creare un hub](virtual-wan-site-to-site-portal.md#hub). L'hub virtuale contiene il gateway VPN WAN virtuale.

Rete virtuale di Azure

* Creare una rete virtuale senza gateway di rete virtuale. Verificare che nessuna delle subnet delle reti locali si sovrappongano con le reti virtuali a cui ci si vuole connettere. Per creare una rete virtuale nella portale di Azure, vedere la [Guida introduttiva](../virtual-network/quick-create-portal.md).

## <a name="vnetgw"></a>1. creare un gateway di rete virtuale di Azure

Creare un gateway di rete virtuale del gateway VPN per la rete virtuale in modalità attivo-attivo per la rete virtuale. Quando si crea il gateway, è possibile usare gli indirizzi IP pubblici esistenti per le due istanze del gateway oppure è possibile creare nuovi indirizzi IP pubblici. Questi indirizzi IP pubblici vengono usati quando si configurano i siti WAN virtuali. Per ulteriori informazioni sulla modalità Active-Active, vedere [configurare connessioni attive-attive](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway).

### <a name="active-active"></a>Impostazione modalità attivo-attivo

![attivo-attivo](./media/connect-virtual-network-gateway-vwan/active.png "attiva-attiva")

### <a name="BGP"></a>Impostazione BGP

L'ASN BGP non può essere 65515. 66515 verrà usato dalla rete WAN virtuale di Azure.

![BGP](./media/connect-virtual-network-gateway-vwan/bgp.png "BGP")

### <a name="pip"></a>Indirizzi IP pubblici

Quando viene creato il gateway, passare alla pagina delle **Proprietà** . Le proprietà e le impostazioni di configurazione saranno simili all'esempio seguente. Si notino i due indirizzi IP pubblici usati per il gateway.

![properties](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="vwansite"></a>2. creare siti VPN WAN virtuali

Per creare siti VPN WAN virtuali, passare alla rete WAN virtuale e in **connettività**selezionare **siti VPN**. In questa sezione si creeranno due siti VPN WAN virtuali che corrispondono ai gateway di rete virtuale creati nella sezione precedente.

1. Selezionare **+ Crea sito**.
2. Nella pagina **Crea siti VPN** Digitare i valori seguenti:

   * **Region** -(la stessa area del gateway della rete virtuale del gateway VPN di Azure)
   * **Fornitore del dispositivo** : immettere il fornitore del dispositivo (qualsiasi nome)
   * **Spazio indirizzi privato** -(immettere un valore o lasciare vuoto quando BGP è abilitato)
   * **Border Gateway Protocol** -(impostare per **abilitare** se il gateway di rete virtuale del gateway VPN di Azure dispone di BGP abilitato)
   * **Connettersi a hub** (selezionare l'hub creato nei prerequisiti dall'elenco a discesa)
3. In **collegamenti**immettere i valori seguenti:

   * **Nome provider** : immettere un nome di collegamento e un nome di provider (qualsiasi nome)
   * **Velocità** -velocità (qualsiasi numero)
   * **Indirizzo IP** : immettere l'indirizzo IP (uguale al primo indirizzo IP pubblico visualizzato sotto le proprietà del gateway di rete virtuale (gateway VPN))
   * **Indirizzo BGP** e indirizzo **ASN-BGP e ASN** . Devono essere uguali a uno degli indirizzi IP del peer BGP e ASN dal gateway di rete virtuale del gateway VPN configurato nel [passaggio 1](#vnetgw).
4. Esaminare e selezionare **conferma** per creare il sito.
5. Ripetere i passaggi precedenti per creare il secondo sito per la corrispondenza con la seconda istanza del gateway di rete virtuale del gateway VPN. Si manterrà le stesse impostazioni, a meno che non si usi il secondo indirizzo IP pubblico e il secondo indirizzo IP del peer BGP dalla configurazione del gateway VPN.
6. È ora possibile eseguire correttamente il provisioning di due siti e passare alla sezione successiva per scaricare i file di configurazione.

## <a name="downloadconfig"></a>3. scaricare i file di configurazione VPN

In questa sezione viene scaricato il file di configurazione VPN per ogni sito creato nella sezione precedente.

1. Nella parte superiore della pagina **siti VPN** WAN virtuale selezionare il **sito**, quindi selezionare **Scarica configurazione VPN da sito a sito**. Azure crea un file di configurazione con le impostazioni.

   ![Scarica file di configurazione](./media/connect-virtual-network-gateway-vwan/download.png "download")
2. Scaricare e aprire il file di configurazione.
3. Ripetere questi passaggi per il secondo sito. Una volta aperti entrambi i file di configurazione, è possibile passare alla sezione successiva.

## <a name="createlocalgateways"></a>4. creare i gateway di rete locale

In questa sezione vengono creati due gateway di rete locale del gateway VPN di Azure. I file di configurazione del passaggio precedente contengono le impostazioni di configurazione del gateway. Usare queste impostazioni per creare e configurare i gateway di rete locale del gateway VPN di Azure.

1. Creare il gateway di rete locale usando queste impostazioni. Per informazioni su come creare un gateway di rete locale del gateway VPN, vedere l'articolo gateway VPN [creare un gateway di rete locale](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway).

   * **Indirizzo IP** : usare l'indirizzo IP Instance0 visualizzato per *gatewayconfiguration* dal file di configurazione.
   * **BGP** : se la connessione è su BGP, selezionare **Configura le impostazioni BGP** e immettere il numero ASN "65515". Immettere l'indirizzo IP del peer BGP. Usare ' Instance0 BgpPeeringAddresses ' per *gatewayconfiguration* dal file di configurazione.
   * La **sottoscrizione, il gruppo di risorse e la località** sono identici a quelli per l'hub WAN virtuale.
2. Esaminare e creare il gateway di rete locale. Il gateway di rete locale dovrebbe essere simile a questo esempio.

   ![Scarica file di configurazione](./media/connect-virtual-network-gateway-vwan/lng1.png "instance0")
3. Ripetere questi passaggi per creare un altro gateway di rete locale, ma questa volta usare i valori ' Instance1' anziché i valori ' Instance0' del file di configurazione.

   ![Scarica file di configurazione](./media/connect-virtual-network-gateway-vwan/lng2.png "Instance1")

## <a name="createlocalgateways"></a>5. creare connessioni

In questa sezione viene creata una connessione tra il gateway di rete locale gateway VPN e il gateway di rete virtuale. Per i passaggi relativi alla creazione di una connessione gateway VPN, vedere [configurare una connessione](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection).

1. Nel portale passare al gateway di rete virtuale e fare clic su **connessioni**. Nella parte superiore della pagina Connessioni fare clic su **+Aggiungi** per aprire la pagina **Aggiungi connessione**.
2. Nella pagina **Aggiungi connessione** configurare i valori seguenti per la connessione:

   * **Nome:** assegnare un nome alla connessione.
   * **Tipo di connessione:** Selezione **da sito a sito (IPSec)**
   * **Gateway di rete virtuale:** questo valore è fisso perché la connessione viene eseguita da questo gateway.
   * **Gateway di rete locale:** Questa connessione collegherà il gateway di rete virtuale al gateway di rete locale. Scegliere uno dei gateway di rete locale creati in precedenza.
   * **Chiave condivisa:** Immettere una chiave condivisa.
   * **Protocollo IKE:** Scegliere il protocollo IKE.
   * **BGP:** Scegliere **Abilita BGP** se la connessione è tramite BGP.
3. Fare clic su **OK** per creare la connessione.
4. È possibile visualizzare la connessione nella pagina **Connessioni** relativa al gateway di rete virtuale.

   ![Connection](./media/connect-virtual-network-gateway-vwan/connect.png "connessione")
5. Ripetere i passaggi precedenti per creare una seconda connessione. Per la seconda connessione selezionare l'altro gateway di rete locale creato.

## <a name="test"></a>6. testare le connessioni

È possibile testare la connettività creando due macchine virtuali, una sul lato del gateway di rete virtuale del gateway VPN e una in una rete virtuale per la rete WAN virtuale, quindi effettuando il ping delle due macchine virtuali.

1. Creare una macchina virtuale nella rete virtuale (test1-VNet) per il gateway VPN di Azure (test1-VNG). Non creare la macchina virtuale in GatewaySubnet.
2. Creare un'altra rete virtuale per connettersi alla rete WAN virtuale. Creare una macchina virtuale in una subnet di questa rete virtuale. Questa rete virtuale non può contenere alcun gateway di rete virtuale. È possibile creare rapidamente una rete virtuale usando la procedura di PowerShell nell'articolo relativo alla [connessione da sito a sito](virtual-wan-site-to-site-portal.md#vnet) . Assicurarsi di modificare i valori prima di eseguire i cmdlet.
3. Connettere il VNet all'hub WAN virtuale. Nella pagina della rete WAN virtuale selezionare connessioni di **rete virtuale**, quindi **+ Aggiungi connessione**. Nella pagina **Aggiungi connessione** compilare i campi seguenti:

    * **Nome connessione** - Specificare un nome per la connessione.
    * **Hub** - Selezionare l'hub che si vuole associare a questa connessione.
    * **Sottoscrizione** - Verificare la sottoscrizione.
    * **Rete virtuale** - Selezionare la rete virtuale che si vuole connettere all'hub. La rete virtuale non può avere un gateway di rete virtuale già esistente.
4. Fare clic su **OK** per creare la connessione di rete virtuale.
5. La connettività è ora impostata tra le macchine virtuali. Si dovrebbe essere in grado di eseguire il ping di una macchina virtuale dall'altra, a meno che non esistano firewall o altri criteri che bloccano la comunicazione.

## <a name="next-steps"></a>Passaggi successivi

Per i passaggi necessari per configurare un criterio IPsec personalizzato, vedere [configurare un criterio IPsec personalizzato per la rete WAN virtuale](virtual-wan-custom-ipsec-portal.md).
Per altre informazioni sulla rete WAN virtuale, vedere [Informazioni sulla rete WAN virtuale di Azure](virtual-wan-about.md) e [Domande frequenti sulla rete WAN virtuale di Azure](virtual-wan-faq.md).