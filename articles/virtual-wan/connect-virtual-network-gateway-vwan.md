---
title: Connettere un gateway di rete virtuale a una rete WAN virtuale di AzureConnect a virtual network gateway to an Azure Virtual WAN
description: Questo articolo illustra come connettere un gateway di rete virtuale di Azure a un gateway VPN della rete WAN virtuale di AzureThis article helps you connect an Azure virtual network gateway to an Azure Virtual WAN gateway
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: 688183bc07aa14d5e5df182d7de0897cec93f0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066240"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>Connettere un gateway VPN (gateway di rete virtuale) alla rete WAN virtuale

Questo articolo illustra come configurare la connettività da un gateway VPN di Azure (gateway di rete virtuale) a una rete WAN virtuale di Azure.This article helps you set up connectivity from an Azure VPN Gateway (virtual network gateway) to an Azure Virtual WAN (VPN gateway). La creazione di una connessione da un gateway VPN (gateway di rete virtuale) a una rete WAN virtuale (gateway VPN) è simile alla configurazione della connettività a una rete WAN virtuale dai siti VPN di succursale.

Al fine di ridurre al minimo la possibile confusione tra due funzionalità, antelineeremo al gateway il nome della funzionalità a cui ci riferiamo. Ad esempio, gateway di rete virtuale gateway VPN e gateway VPN VPN della rete WAN virtuale.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare, creare le risorse seguenti:Before you begin, create the following resources:

Rete WAN virtuale di Azure

* [Creare una rete WAN virtuale](virtual-wan-site-to-site-portal.md#openvwan).
* [Creare un hub](virtual-wan-site-to-site-portal.md#hub). L'hub virtuale contiene il gateway VPN della rete WAN virtuale.

Rete virtuale di Azure

* Creare una rete virtuale senza gateway di rete virtuale. Verificare che nessuna delle subnet della rete locale possa sovrapporsi alle reti virtuali a cui ci si vuole connettere. Per creare una rete virtuale nel portale di Azure, vedere l'[Avvio rapido](../virtual-network/quick-create-portal.md).

## <a name="1-create-an-azure-virtual-network-gateway"></a><a name="vnetgw"></a>1. Creare un gateway di rete virtuale di Azure1. Create an Azure virtual network gateway

Creare un gateway di rete virtuale del gateway VPN per la rete virtuale in modalità attivo-attivo per la rete virtuale. Quando si crea il gateway, è possibile usare indirizzi IP pubblici esistenti per le due istanze del gateway oppure creare nuovi indirizzi IP pubblici. Utilizzare questi indirizzi IP pubblici durante la configurazione dei siti WAN virtuale. Per ulteriori informazioni sulla modalità attivo-attivo, vedere [Configurare le connessioni attivo-attivo](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway).

### <a name="active-active-mode-setting"></a><a name="active-active"></a>Impostazione della modalità attivo-attivo

![attivo-attivo](./media/connect-virtual-network-gateway-vwan/active.png "attiva-attiva")

### <a name="bgp-setting"></a><a name="BGP"></a>Impostazione BGP

L'ASN BGP non può essere 65515. 66515 verrà usato dalla rete WAN virtuale di Azure.66515 will be used by Azure Virtual WAN.

![BGP](./media/connect-virtual-network-gateway-vwan/bgp.png "Bgp")

### <a name="public-ip-addresses"></a><a name="pip"></a>Indirizzi IP pubblici

Quando viene creato il gateway, passare alla pagina **Proprietà.When** the gateway is created, navigate to the Properties page. Le proprietà e le impostazioni di configurazione saranno simili all'esempio seguente. Si notino i due indirizzi IP pubblici utilizzati per il gateway.

![Proprietà](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. Creare siti VPN WAN virtuale

Per creare siti VPN WAN virtuale, passare alla rete WAN virtuale e in **Connettività**selezionare **Siti VPN**. In questa sezione verranno creati due siti VPN della rete WAN virtuale che corrispondono ai gateway di rete virtuale creati nella sezione precedente.

1. Selezionare **Crea sito**.
2. Nella pagina **Crea siti VPN** digitare i valori seguenti:

   * **Area:** (stessa area del gateway di rete virtuale del gateway VPN di Azure)Region - (The same region of the Azure VPN Gateway virtual network gateway)
   * **Fornitore del dispositivo:** immettere il fornitore del dispositivo (qualsiasi nome)
   * **Spazio di indirizzi privati** - (Immettere un valore o lasciare vuoto quando BGP è abilitato)
   * **Protocollo Border Gateway** - (impostato per **abilitare** se il gateway di rete virtuale del gateway VPN di Azure ha BGP abilitato)Border Gateway Protocol - (Set to Enable if the Azure VPN Gateway virtual network gateway has BGP enabled)
   * **Connettersi agli hub** (selezionare l'hub creato nei prerequisiti dall'elenco a discesa)Connect to Hubs (Select hub you created in the prerequisites from the dropdown)
3. In **Collegamenti**immettere i valori seguenti:

   * **Nome provider:** immettere un nome di collegamento e un nome provider (qualsiasi nome)
   * **Velocità** - Velocità (qualsiasi numero)
   * **Indirizzo IP:** immettere l'indirizzo IP (uguale al primo indirizzo IP pubblico visualizzato nelle proprietà del gateway virtuale (gateway VPN)
   * **Indirizzo BGP** e **ASN** - Indirizzo BGP e ASN. Devono essere uguali a uno degli indirizzi IP peer BGP e ASN dal gateway di rete virtuale gateway VPN configurato nel [passaggio 1](#vnetgw).
4. Esaminare e selezionare **Conferma** per creare il sito.
5. Ripetere i passaggi precedenti per creare il secondo sito da abbinare alla seconda istanza del gateway di rete virtuale gateway VPN. Manterrete le stesse impostazioni, ad eccezione dell'utilizzo del secondo indirizzo IP pubblico e del secondo indirizzo IP peer BGP dalla configurazione del gateway VPN.
6. È ora possibile eseguire correttamente il provisioning di due siti e passare alla sezione successiva per scaricare i file di configurazione.

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. Scaricare i file di configurazione VPN

In questa sezione viene scaricato il file di configurazione VPN per ognuno dei siti creati nella sezione precedente.

1. Nella parte superiore della pagina **Siti VPN** WAN virtuale selezionare **Sito**, quindi Avvia configurazione VPN da sito **a sito**. Azure crea un file di configurazione con le impostazioni.

   ![scaricare il file di configurazione](./media/connect-virtual-network-gateway-vwan/download.png "download")
2. Scaricare e aprire il file di configurazione.
3. Ripetere questi passaggi per il secondo sito. Una volta aperti entrambi i file di configurazione, è possibile passare alla sezione successiva.

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. Creare i gateway di rete locale

In questa sezione vengono creati due gateway di rete locale del gateway VPN di Azure.In this section, you create two Azure VPN Gateway local network gateways. I file di configurazione del passaggio precedente contengono le impostazioni di configurazione del gateway. Usare queste impostazioni per creare e configurare i gateway di rete locale del gateway VPN di Azure.Use these settings to create and configure the Azure VPN Gateway local network gateways.

1. Creare il gateway di rete locale utilizzando queste impostazioni. Per informazioni su come creare un gateway di rete locale del gateway VPN, vedere l'articolo Gateway VPN Creare un gateway di [rete locale.](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway)

   * **Indirizzo IP:** utilizzare l'indirizzo IP Instance0 visualizzato per *gatewayconfiguration* dal file di configurazione.
   * **BGP** - Se la connessione è su BGP, selezionare **Configura impostazioni BGP** e immettere l'ASN '65515'. Immettere l'indirizzo IP del peer BGP. Utilizzare 'Instance0 BgpPeeringAddresses' per *gatewayconfiguration* dal file di configurazione.
   * **Sottoscrizione, gruppo di risorse e posizione** sono uguali a quelli dell'hub WAN virtuale.
2. Esaminare e creare il gateway di rete locale. Il gateway di rete locale dovrebbe essere simile a questo esempio.

   ![scaricare il file di configurazione](./media/connect-virtual-network-gateway-vwan/lng1.png "instance0")
3. Ripetere questi passaggi per creare un altro gateway di rete locale, ma questa volta utilizzare i valori 'Instance1' anziché i valori 'Instance0' dal file di configurazione.

   ![scaricare il file di configurazione](./media/connect-virtual-network-gateway-vwan/lng2.png "istanza1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. Creare connessioni

In questa sezione viene creata una connessione tra i gateway di rete locale del gateway VPN e il gateway di rete virtuale. Per la procedura di creazione di una connessione al gateway VPN, vedere [Configurare una connessione.](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection)

1. Nel portale passare al gateway di rete virtuale e fare clic su **Connessioni**. Nella parte superiore della pagina Connessioni fare clic su **+Aggiungi** per aprire la pagina **Aggiungi connessione**.
2. Nella pagina **Aggiungi connessione** configurare i valori seguenti per la connessione:

   * **Nome:** assegnare un nome alla connessione.
   * **Tipo di connessione:** Seleziona **da sito a sito (IPSec)**
   * **Gateway di rete virtuale:** questo valore è fisso perché la connessione viene eseguita da questo gateway.
   * **Gateway di rete locale:** Questa connessione connetterà il gateway di rete virtuale al gateway di rete locale. Scegliere uno dei gateway di rete locale creati in precedenza.
   * **Chiave condivisa:** Immettere una chiave condivisa.
   * **Protocollo IKE:** Scegliere il protocollo IKE.
   * **BGP:** Scegliere **Abilita BGP** se la connessione è su BGP.
3. Fare clic su **OK** per creare la connessione.
4. È possibile visualizzare la connessione nella pagina **Connessioni** relativa al gateway di rete virtuale.

   ![Connessione](./media/connect-virtual-network-gateway-vwan/connect.png "connessione")
5. Ripetere i passaggi precedenti per creare una seconda connessione. Per la seconda connessione, selezionare l'altro gateway di rete locale creato.

## <a name="6-test-connections"></a><a name="test"></a>6. Connessioni di prova

È possibile testare la connettività creando due macchine virtuali, una sul lato del gateway di rete virtuale del gateway VPN e una in una rete virtuale per la rete WAN virtuale, quindi eseguire il ping delle due macchine virtuali.

1. Creare una macchina virtuale nella rete virtuale (Test1-VNet) per il gateway VPN di Azure (Test1-VNG). Non creare la macchina virtuale in GatewaySubnet.
2. Creare un'altra rete virtuale per connettersi alla rete WAN virtuale. Creare una macchina virtuale in una subnet di questa rete virtuale. Questa rete virtuale non può contenere gateway di rete virtuale. È possibile creare rapidamente una rete virtuale usando i passaggi di PowerShell nell'articolo [Connessione da sito a sito.](virtual-wan-site-to-site-portal.md#vnet) Assicurarsi di modificare i valori prima di eseguire i cmdlet.
3. Connettere la rete virtuale all'hub WAN virtuale. Nella pagina della rete WAN virtuale selezionare Connessioni di **rete virtuale**, quindi **Aggiungi connessione**. Nella pagina **Aggiungi connessione** compilare i campi seguenti:

    * **Nome connessione** - Specificare un nome per la connessione.
    * **Hub** - Selezionare l'hub che si vuole associare a questa connessione.
    * **Sottoscrizione** - Verificare la sottoscrizione.
    * **Rete virtuale** - Selezionare la rete virtuale che si vuole connettere all'hub. La rete virtuale non può avere un gateway di rete virtuale già esistente.
4. Fare clic su **OK** per creare la connessione di rete virtuale.
5. La connettività è ora impostata tra le macchine virtuali. Dovrebbe essere possibile eseguire il ping di una macchina virtuale dall'altra, a meno che non siano presenti firewall o altri criteri che bloccano la comunicazione.

## <a name="next-steps"></a>Passaggi successivi

Per la procedura di configurazione di un criterio IPsec personalizzato, vedere [Configure a custom IPsec policy for Virtual WAN](virtual-wan-custom-ipsec-portal.md).
Per altre informazioni sulla rete WAN virtuale, vedere [Informazioni sulla rete WAN virtuale di Azure](virtual-wan-about.md) e [Domande frequenti sulla rete WAN virtuale di Azure](virtual-wan-faq.md).