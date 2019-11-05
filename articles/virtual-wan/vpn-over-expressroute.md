---
title: Creare una connessione VPN da sito a sito tramite peering privato di ExpressRoute in WAN virtuale di Azure | Microsoft Docs
description: Questa esercitazione illustra come usare la rete WAN virtuale di Azure per creare una connessione VPN da sito a sito tramite il peering privato di ExpressRoute.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 10/11/2019
ms.author: cherylmc
Customer intent: I want to connect my on-premises networks to my VNets using S2S VPN connection over my ExpressRoute private peering using Azure Virtual WAN.
ms.openlocfilehash: 6272d6fe6f8c35c06a8121e10be2dd5a2e5512a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515031"
---
# <a name="create-a-site-to-site-vpn-connection-over-expressroute-private-peering-using-azure-virtual-wan"></a>Creare una connessione VPN da sito a sito tramite il peering privato di ExpressRoute tramite la rete WAN virtuale di Azure

Questo articolo illustra come usare la rete WAN virtuale per stabilire una connessione VPN IPsec/IKE dalla rete locale ad Azure tramite il peering privato di un circuito ExpressRoute. Questo può fornire un transito crittografato tra le reti locali e le reti virtuali di Azure tramite ExpressRoute, senza passare attraverso la rete Internet pubblica o usando indirizzi IP pubblici.

## <a name="topology-and-routing"></a>Topologia e routing

Il diagramma seguente mostra un esempio di VPN sulla connettività del peering privato di ExpressRoute:

![VPN su ExpressRoute](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

Il diagramma mostra una rete all'interno della rete locale connessa al gateway VPN dell'hub di Azure tramite il peering privato di ExpressRoute. Lo stabilimento di connettività è semplice:

1. Stabilire la connettività ExpressRoute con un circuito ExpressRoute e un peering privato
2. Stabilire la connettività VPN come descritto in questo documento

Un aspetto importante di questa configurazione è il routing tra le reti locali e Azure tramite i percorsi ExpressRoute e VPN.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Traffico dalle reti locali ad Azure

Per il traffico da reti locali ad Azure, i prefissi di Azure (inclusi l'hub virtuale e tutte le reti virtuali spoke connesse all'hub) verranno annunciati tramite il protocollo BGP di peering privato ExpressRoute e il protocollo BGP VPN. Questo comporterà due route di rete (percorsi) verso Azure dalle reti locali. uno sul percorso protetto da IPsec e uno direttamente su ExpressRoute **senza** protezione IPSec. Per assicurarsi che la crittografia venga applicata alla comunicazione, è necessario assicurarsi che per la rete connessa alla VPN nel diagramma le route di Azure tramite il gateway VPN locale siano preferibili rispetto al percorso di ExpressRoute diretto.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Traffico da Azure alle reti locali

Lo stesso requisito si applica al traffico da Azure alle reti locali. Per assicurarsi che il percorso IPsec sia preferibile rispetto al percorso ExpressRoute diretto (senza IPsec), sono disponibili due opzioni:

- Annunciare prefissi più specifici nella sessione BGP VPN per la rete connessa alla VPN. È possibile annunciare un intervallo più ampio che include la "rete connessa alla VPN" sul peering privato ExpressRoute, quindi intervalli più specifici nella sessione BGP VPN. Ad esempio, annunciare 10.0.0.0/16 su ExpressRoute e 10.0.1.0/24 su VPN.

- Annunciare i prefissi non contigui per VPN e ExpressRoute. Se gli intervalli di rete connessi alla VPN sono disgiunti da altre reti connesse ExpressRoute, è possibile annunciare rispettivamente i prefissi nelle sessioni BGP VPN e ExpressRoute. Ad esempio, annunciare 10.0.0.0/24 su ExpressRoute e 10.0.1.0/24 su VPN.

In entrambi questi esempi, Azure invierà il traffico a 10.0.1.0/24 attraverso la connessione VPN anziché direttamente tramite ExpressRoute senza protezione VPN.

> [!WARNING]
> Se si annunciano gli **stessi** prefissi tramite connessioni EXPRESSROUTE e VPN, Azure userà **il percorso ExpressRoute direttamente senza la protezione VPN**.
>

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1. creare una rete WAN virtuale e un hub con gateway

Prima di procedere, è necessario che siano presenti le seguenti risorse di Azure e le configurazioni locali corrispondenti:

1. Una rete WAN virtuale di Azure
2. Hub WAN virtuale con [gateway ExpressRoute](virtual-wan-expressroute-portal.md) e [gateway VPN](virtual-wan-site-to-site-portal.md)

Per la procedura per la creazione di una rete WAN virtuale di Azure e di un hub con un'associazione ExpressRoute e per [creare una connessione da sito a sito tramite la rete WAN virtuale](virtual-wan-site-to-site-portal.md) di Azure, vedere [creare un'associazione ExpressRoute](virtual-wan-expressroute-portal.md) Gateway nella rete WAN virtuale.

## <a name="site"></a>2. creare un sito per la rete locale

La risorsa del sito è identica a quella dei siti VPN non ExpressRoute per la rete WAN virtuale. L'aspetto fondamentale da tenere presente è che l'indirizzo IP del dispositivo VPN locale può ora essere un indirizzo IP privato o un indirizzo IP pubblico nella rete locale raggiungibile tramite il peering privato ExpressRoute creato nel passaggio 1.

> [!NOTE]
> L'indirizzo IP del dispositivo VPN locale deve far parte dei prefissi di indirizzo annunciati all'hub WAN virtuale tramite il peering privato di Azure ExpressRoute.
>

1. Passare al portale di Azure nel browser. Fare clic sulla rete WAN creata. Nella pagina WAN, in **connettività**, fare clic su **siti VPN** per aprire la pagina siti VPN.

2. Nella pagina **Siti VPN** fare clic su **+Crea sito**.

3. Nella pagina **Crea sito** compilare i campi seguenti:

   * **Sottoscrizione** - Verificare la sottoscrizione.
   * **Gruppo di risorse** : selezionare o creare il gruppo di risorse che si vuole usare.
   * **Region** : area di Azure per la risorsa del sito VPN.
   * **Nome** - Nome che si vuole usare per fare riferimento al sito locale.
   * **Fornitore del dispositivo** : fornitore del dispositivo VPN locale.
   * **Border Gateway Protocol** : selezionare "Abilita" se la rete locale usa BGP.
   * **Spazio indirizzi privato** - Spazio di indirizzi IP nel sito locale. Il traffico destinato a questo spazio di indirizzi viene indirizzato alla rete locale tramite il gateway VPN.
   * **Hub** : selezionare uno o più hub per connettere il sito VPN. Per gli hub selezionati devono essere già stati creati gateway VPN.

4. Fare clic su **Avanti: collegamenti >** per le impostazioni del collegamento VPN:

   * **Nome collegamento** : nome con cui si desidera fare riferimento a questa connessione.
   * **Nome provider** : nome del provider di servizi Internet per il sito. Nel caso della rete locale ExpressRoute, il nome del provider di servizi ExpressRoute.
   * **Velocità** : la velocità del collegamento al servizio Internet o del circuito ExpressRoute.
   * **Indirizzo IP** : indirizzo IP pubblico del dispositivo VPN che risiede nel sito locale. In alternativa, nel caso di ExpressRoute in locale, l'indirizzo IP privato del dispositivo VPN tramite ExpressRoute.

   Se BGP è abilitato, verrà applicato a tutte le connessioni create per il sito in Azure. La configurazione di BGP in una rete WAN virtuale equivale alla configurazione di BGP in un gateway VPN di Azure. L'indirizzo peer BGP locale *non deve* corrispondere all'indirizzo IP della VPN al dispositivo o allo spazio degli indirizzi VNet del sito VPN. Usare un indirizzo IP diverso nel dispositivo VPN per il peer BGP. Può trattarsi di un indirizzo assegnato all'interfaccia di loopback nel dispositivo. Tuttavia, *non può* essere un indirizzo APIPA (169.254.*x*.*x*). Specificare questo indirizzo nel gateway di rete locale corrispondente che rappresenta la posizione. Per i prerequisiti di BGP, vedere [Informazioni su BGP con i gateway VPN di Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).

5. Fare clic su **Avanti: rivedere + crea >** per controllare i valori delle impostazioni e creare il sito VPN. Se è stata selezionata l'opzione **Hub** per la connessione, verrà stabilita la connessione tra la rete locale e il gateway VPN Hub.

## <a name="hub"></a>3. aggiornare l'impostazione della connessione VPN per l'uso di ExpressRoute

Dopo la creazione del sito VPN e la connessione all'hub, attenersi alla procedura seguente per configurare la connessione per l'uso del peering privato di ExpressRoute:

1. Tornare alla pagina delle risorse WAN virtuale e fare clic sulla risorsa Hub. In alternativa, passare dal sito VPN all'hub connesso.

2. In **connettività**fare clic su **VPN (da sito a sito)**

3. Fare clic su "..." sul sito VPN su ExpressRoute e selezionare "**Modifica connessione VPN a questo hub**"

4. Selezionare "Sì" in "**Usa indirizzo IP privato di Azure**". L'impostazione configura il gateway VPN dell'hub per l'uso di indirizzi IP privati all'interno dell'intervallo di indirizzi Hub sul gateway per questa connessione, invece degli indirizzi IP pubblici. In questo modo il traffico dalla rete locale attraversa i percorsi di peering privati di ExpressRoute invece di usare la rete Internet pubblica per questa connessione VPN. La schermata seguente mostra la finestra delle impostazioni.

   ![Impostazione della connessione VPN](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
5. Fare clic su **Save**.

Al termine del salvataggio, il gateway VPN dell'hub userà gli indirizzi IP privati nel gateway VPN per stabilire le connessioni IPsec/IKE con il dispositivo VPN locale su ExpressRoute.

## <a name="associate"></a>4. ottenere gli indirizzi IP privati del gateway VPN dell'hub

Scaricare la configurazione del dispositivo VPN per ottenere gli indirizzi IP privati del gateway VPN dell'hub. Questi sono necessari per configurare il dispositivo VPN locale.

1. Nella pagina dell'Hub fare clic su **VPN (da sito a sito)** in **connettività**

2. Nella parte superiore della pagina Overview fare clic su **download VPN config**. Azure crea un account di archiviazione nel gruppo di risorse "Microsoft-Network-[Location]", dove location è il percorso della rete WAN. Dopo aver applicato la configurazione ai dispositivi VPN, è possibile eliminare questo account di archiviazione.

3. Al termine della creazione del file è possibile fare clic sul collegamento per scaricarlo.

4. Applicare la configurazione al dispositivo VPN.

### <a name="understanding-the-vpn-device-configuration-file"></a>Informazioni sui file di configurazione del dispositivo VPN

Il file di configurazione del dispositivo contiene le impostazioni da usare quando si configura il dispositivo VPN locale. Quando si visualizza questo file, notare le informazioni seguenti:

* **vpnSiteConfiguration -** Questa sezione indica i dettagli del dispositivo configurato come un sito che si connette alla rete WAN virtuale. Include il nome e l'indirizzo IP pubblico del dispositivo della succursale.
* **vpnSiteConnections -** Questa sezione include informazioni sulle impostazioni seguenti:

    * **Spazio degli indirizzi** della rete virtuale degli hub virtuali<br/>Esempio:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * **Spazio degli indirizzi** delle reti virtuali connesse all'hub<br>Esempio:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * **Indirizzi IP** di vpngateway dell'hub virtuale. Dato che ogni connessione del gateway VPN comprende due tunnel nella configurazione attiva-attiva, nel file saranno elencati entrambi gli indirizzi IP. In questo esempio vengono visualizzati "Instance0" e "instance1" per ogni sito e sono indirizzi IP privati anziché indirizzi IP pubblici.<br>Esempio:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * **Dettagli di configurazione della connessione vpngateway** , ad esempio BGP, chiave precondivisa e così via. PSK è la chiave precondivisa generata automaticamente. È sempre possibile modificare la connessione nella pagina Panoramica per una chiave precondivisa personalizzata.
  
### <a name="example-device-configuration-file"></a>Esempio di file di configurazione del dispositivo

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>Configurazione del dispositivo VPN

Se sono necessarie istruzioni per configurare il dispositivo, è possibile usare le istruzioni riportate nella [pagina degli script di configurazione del dispositivo VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) con le avvertenze seguenti:

* Le istruzioni nella pagina dei dispositivi VPN non vengono scritte per la rete WAN virtuale, ma è possibile usare i valori della rete WAN virtuale dal file di configurazione per configurare manualmente il dispositivo VPN. 
* Gli script di configurazione del dispositivo scaricabili per Gateway VPN non funzionano per la rete WAN virtuale, perché la configurazione è diversa.
* Una nuova rete WAN virtuale può supportare IKEv1 e IKEv2.
* La rete WAN virtuale può usare solo dispositivi VPN e istruzioni per il dispositivo basati su route.

## <a name="viewwan"></a>5. visualizzare la rete WAN virtuale

1. Passare alla rete WAN virtuale.

2. Nella pagina Panoramica ogni punto sulla mappa rappresenta un hub. Passare il mouse su qualsiasi punto per visualizzare il riepilogo dell'integrità dell'hub.

3. Nella sezione degli hub e delle connessioni è possibile visualizzare lo stato dell'hub, il sito, l'area, lo stato della connessione VPN e i byte in entrata e in uscita.

## <a name="viewhealth"></a>6. visualizzare l'integrità delle risorse

1. Passare alla rete WAN.

2. Nella pagina della rete WAN, nella sezione **Supporto e risoluzione dei problemi** fare clic su **Integrità** e visualizzare la risorsa.

## <a name="connectmon"></a>7. monitorare una connessione

Creare una connessione per monitorare la comunicazione tra una macchina virtuale di Azure e un sito remoto. Per informazioni su come configurare un monitoraggio della connessione, vedere [Monitorare la comunicazione di rete](~/articles/network-watcher/connection-monitor.md). Il campo di origine è l'IP della macchina virtuale in Azure e l'IP di destinazione è l'indirizzo IP del sito.

## <a name="cleanup"></a>8. pulire le risorse

Quando queste risorse non sono più necessarie, è possibile usare [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse in esso contenute. Sostituire "myResourceGroup" con il nome del gruppo di risorse specifico ed eseguire il comando di PowerShell seguente:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo consente di creare una connessione VPN tramite il peering privato di ExpressRoute tramite la rete WAN virtuale. Per ulteriori informazioni sulla rete WAN virtuale e altre funzionalità correlate, vedere la pagina [Panoramica della rete WAN virtuale](virtual-wan-about.md) .
