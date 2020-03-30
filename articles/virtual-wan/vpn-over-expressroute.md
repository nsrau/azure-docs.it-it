---
title: 'Configurare la crittografia ExpressRoute: IPsec su ExpressRoute per la rete WAN virtuale di AzureConfigure ExpressRoute encryption: IPsec over ExpressRoute for Azure Virtual WAN'
description: In questa esercitazione viene illustrato come usare la rete WAN virtuale di Azure per creare una connessione VPN da sito a sito tramite il peering privato ExpressRoute.In this tutorial, learn how to use Azure Virtual WAN to create a site-to-site VPN connection over ExpressRoute private peering.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: b1e6305d142530ab19849f61f12a122d0c6434aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059294"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>Crittografia ExpressRoute: IPsec su ExpressRoute per la rete WAN virtuale

Questo articolo illustra come usare la rete WAN virtuale di Azure per stabilire una connessione VPN IPsec/IKE dalla rete locale ad Azure tramite il peering privato di un circuito ExpressRoute.This article shows you how to use Azure Virtual WAN to establish an IPsec/IKE VPN connection from your on-premises network to Azure over the private peering of an Azure ExpressRoute circuit. Questa tecnica può fornire un transito crittografato tra le reti locali e le reti virtuali di Azure tramite ExpressRoute, senza passare attraverso la rete Internet pubblica o usare indirizzi IP pubblici.

## <a name="topology-and-routing"></a>Topologia e routing

Il diagramma seguente mostra un esempio di connettività VPN tramite peering privato ExpressRoute:The following diagram shows an example of VPN connectivity over ExpressRoute private peering:

![VPN su ExpressRoute](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

Il diagramma mostra una rete all'interno della rete locale connessa al gateway VPN dell'hub di Azure tramite il peering privato ExpressRoute.The diagram shows a network within the on-premises network connected to the Azure hub VPN gateway over ExpressRoute private peering. L'istituzione della connettività è semplice:

1. Stabilire la connettività ExpressRoute con un circuito ExpressRoute e il peering privato.
2. Stabilire la connettività VPN come descritto in questo articolo.

Un aspetto importante di questa configurazione è il routing tra le reti locali e Azure su entrambi i percorsi ExpressRoute e VPN.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Traffico dalle reti locali ad Azure

Per il traffico dalle reti locali ad Azure, i prefissi di Azure (inclusi l'hub virtuale e tutte le reti virtuali spoke connesse all'hub) vengono annunciati tramite il BGP di peering privato ExpressRoute e il BGP VPN. Il risultato è la creazione di due route di rete (percorsi) verso Azure dalle reti locali:This results in two network routes (paths) verso Azure from the on-premises networks:

- Uno sul percorso protetto da IPsec
- Uno direttamente su ExpressRoute *senza* protezione IPsec 

Per applicare la crittografia alla comunicazione, è necessario assicurarsi che per la rete connessa VPN nel diagramma, le route di Azure tramite gateway VPN locale siano preferite rispetto al percorso ExpressRoute diretto.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Traffico da Azure a reti locali

Lo stesso requisito si applica al traffico da Azure alle reti locali. Per assicurarsi che il percorso IPsec sia preferito rispetto al percorso ExpressRoute diretto (senza IPsec), sono disponibili due opzioni:To ensure that IPsec path is preferred over the direct ExpressRoute path (without IPsec), you have two options:

- Pubblicizzare prefissi più specifici nella sessione BGP VPN per la rete connessa VPN. È possibile annunciare un intervallo più ampio che include la rete connessa VPN tramite peering privato ExpressRoute, quindi intervalli più specifici nella sessione BGP VPN. Ad esempio, pubblicizzare 10.0.0.0/16 su ExpressRoute e 10.0.1.0/24 su VPN.

- Pubblicizzare prefissi disgiunti per VPN ed ExpressRoute.Advertise disjoint prefixes for VPN and ExpressRoute. Se gli intervalli di rete connessi a VPN non sono contigui da altre reti connesse ExpressRoute, è possibile annunciare i prefissi rispettivamente nelle sessioni BGP VPN ed ExpressRoute. Ad esempio, pubblicizzare 10.0.0.0/24 su ExpressRoute e 10.0.1.0/24 su VPN.

In entrambi questi esempi, Azure invierà il traffico a 10.0.1.0/24 tramite la connessione VPN anziché direttamente tramite ExpressRoute senza protezione VPN.

> [!WARNING]
> Se si annunciano gli *stessi* prefissi su connessioni ExpressRoute e VPN, Azure utilizzerà direttamente il percorso ExpressRoute senza protezione VPN.
>

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. Creare una rete WAN virtuale e un hub con gateway

Le risorse di Azure seguenti e le configurazioni locali corrispondenti devono essere disponibili prima di procedere:The following Azure resources and the corresponding on-premises configurations must be in place before you proceed:

- Una rete WAN virtuale di AzureAn Azure virtual WAN
- Un hub WAN virtuale con un [gateway ExpressRoute](virtual-wan-expressroute-portal.md) e un [gateway VPN](virtual-wan-site-to-site-portal.md)

Per la procedura di creazione di una rete WAN virtuale di Azure e di un hub con un'associazione ExpressRoute, vedere [Creare un'associazione ExpressRoute usando](virtual-wan-expressroute-portal.md)la rete WAN virtuale di Azure.For the steps to create an Azure virtual WAN and a hub with an ExpressRoute association, see Create an ExpressRoute association using Azure Virtual WAN . Per la procedura di creazione di un gateway VPN nella rete WAN virtuale, vedere [Creare una connessione da sito a sito tramite WAN virtuale](virtual-wan-site-to-site-portal.md)di Azure.

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. Creare un sito per la rete locale

La risorsa del sito è uguale ai siti VPN non ExpressRoute per una rete WAN virtuale. L'indirizzo IP del dispositivo VPN locale può ora essere un indirizzo IP privato o un indirizzo IP pubblico nella rete locale raggiungibile tramite il peering privato ExpressRoute creato nel passaggio 1.The IP address of the on-premises VPN device can now be either either a private IP address, or a public IP address in the on-premises network reachable via ExpressRoute private peering created in step 1.

> [!NOTE]
> L'indirizzo IP per il dispositivo VPN locale deve far parte dei prefissi di indirizzo annunciati all'hub WAN virtuale tramite il peering privato di Azure ExpressRoute.The IP address for the on-premises VPN device *must* be part of the address prefixes advertised to the virtual WAN hub via Azure ExpressRoute private peering.
>

1. Passare al portale di Azure nel browser. 
1. Selezionare la rete WAN creata. Nella pagina WAN, in **Connettività**, selezionare **Siti VPN.**
1. Nella pagina **Siti VPN,** selezionare **Crea sito**.
1. Nella pagina **Crea sito** compilare i campi seguenti:
   * **Sottoscrizione**: verificare la sottoscrizione.
   * **Gruppo di risorse:** selezionare o creare il gruppo di risorse che si vuole usare.
   * **Area:** immettere l'area di Azure per la risorsa del sito VPN.
   * **Nome**: immettere il nome con cui si desidera fare riferimento al sito locale.
   * **Fornitore del dispositivo:** immettere il fornitore del dispositivo VPN locale.
   * **Protocollo Border Gateway**: selezionare "Abilita" se la rete locale utilizza BGP.
   * **Spazio di indirizzi privati:** immettere lo spazio di indirizzi IP che si trova nel sito locale. Il traffico destinato a questo spazio di indirizzi viene instradato alla rete locale tramite il gateway VPN.
   * **Hub:** selezionare uno o più hub per connettere questo sito VPN. Gli hub selezionati devono avere gateway VPN già creati.
1. Selezionare **Avanti: Collegamenti >** per le impostazioni del collegamento VPN:
   * **Nome collegamento**: Il nome con cui si desidera fare riferimento a questa connessione.
   * **Nome provider**: Il nome del provider di servizi Internet per questo sito. Per una rete ExpressRoute locale, è il nome del provider di servizi ExpressRoute.For an ExpressRoute on-premises network, it's the name of the ExpressRoute service provider.
   * **Velocità:** la velocità del collegamento al servizio Internet o del circuito ExpressRoute.
   * **Indirizzo IP:** l'indirizzo IP pubblico del dispositivo VPN che risiede nel sito locale. In alternativa, per ExpressRoute locale, è l'indirizzo IP privato del dispositivo VPN tramite ExpressRoute.Or, for ExpressRoute on-premises, it's the private IP address of the VPN device via ExpressRoute.

   Se BGP è abilitato, verrà applicato a tutte le connessioni create per questo sito in Azure.If BGP is enabled, it will apply to all connections created for this site in Azure. La configurazione di BGP in una rete WAN virtuale equivale alla configurazione di BGP in un gateway VPN di Azure.Configuring BGP on a virtual WAN is equivalent to configuring BGP on an Azure VPN gateway. 
   
   L'indirizzo peer BGP locale *non deve* essere uguale all'indirizzo IP della VPN per il dispositivo o allo spazio di indirizzi della rete virtuale del sito VPN. Usare un indirizzo IP diverso nel dispositivo VPN per il peer BGP. Può trattarsi di un indirizzo assegnato all'interfaccia di loopback nel dispositivo. Tuttavia, *non può* essere un APIPA (169.254.* x*. *x*( ) . Specificare questo indirizzo nel gateway di rete locale corrispondente che rappresenta la posizione. Per i prerequisiti di BGP, vedere [Informazioni su BGP con i gateway VPN di Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).

1. Selezionare **Successivo: Rivedere e creare >** per verificare i valori delle impostazioni e creare il sito VPN. Se è stata selezionata l'opzione **Hub** per la connessione, verrà stabilita la connessione tra la rete locale e il gateway VPN dell'hub.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. Aggiornare l'impostazione di connessione VPN per usare ExpressRoute

Dopo aver creato il sito VPN e connettersi all'hub, usare la procedura seguente per configurare la connessione per usare il peering privato ExpressRoute:After you create the VPN site and connect to the hub, use the following steps to configure the connection to use ExpressRoute private peering:

1. Tornare alla pagina della risorsa WAN virtuale e selezionare la risorsa hub. In alternativa, passare dal sito VPN all'hub connesso.
1. In **Connettività**selezionare **VPN (da sito a sito)**.
1. Selezionare i puntiri di sospensione (**...**) nel sito VPN tramite ExpressRoute e selezionare **Modifica connessione VPN a questo hub**.
1. Per **Usa indirizzo IP privato di Azure**selezionare **Sì**. L'impostazione configura il gateway VPN dell'hub per l'utilizzo di indirizzi IP privati all'interno dell'intervallo di indirizzi hub nel gateway per questa connessione, anziché gli indirizzi IP pubblici. In questo modo il traffico proveniente dalla rete locale attraversa i percorsi di peering privati ExpressRoute anziché usare Internet pubblico per questa connessione VPN. La schermata seguente mostra l'impostazione.

   ![Impostazione per l'utilizzo di un indirizzo IP privato per la connessione VPN](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
1. Selezionare **Salva**.

Dopo aver salvato le modifiche, il gateway VPN dell'hub utilizzerà gli indirizzi IP privati nel gateway VPN per stabilire le connessioni IPsec/IKE con il dispositivo VPN locale su ExpressRoute.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. Ottenere gli indirizzi IP privati per il gateway VPN dell'hub

Scaricare la configurazione del dispositivo VPN per ottenere gli indirizzi IP privati del gateway VPN dell'hub. Questi indirizzi sono necessari per configurare il dispositivo VPN locale.

1. Nella pagina dell'hub selezionare **VPN (da sito a sito)** in **Connettività**.
1. Nella parte superiore della pagina **Panoramica** selezionare **Scarica configurazione VPN**. 

   Azure crea un account di archiviazione nel gruppo di risorse "microsoft-network-[location]", dove *posizione* è la posizione della wan. Dopo aver applicato la configurazione ai dispositivi VPN, è possibile eliminare questo account di archiviazione.
1. Dopo aver creato il file, selezionare il collegamento per scaricarlo.
1. Applicare la configurazione al dispositivo VPN.

### <a name="vpn-device-configuration-file"></a>File di configurazione del dispositivo VPN

Il file di configurazione del dispositivo contiene le impostazioni da usare quando si configura il dispositivo VPN locale. Quando si visualizza questo file, notare le informazioni seguenti:

* **vpnSiteConfiguration**: questa sezione indica i dettagli del dispositivo impostati come sito che si connette alla rete WAN virtuale. Include il nome e l'indirizzo IP pubblico del dispositivo di succursale.
* **vpnSiteConnections**: in questa sezione vengono fornite informazioni sulle seguenti impostazioni:

    * Spazio di indirizzi della rete virtuale dell'hub virtuale.<br/>Esempio:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * Spazio di indirizzi delle reti virtuali connesse all'hub.<br>Esempio:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * Indirizzi IP del gateway VPN dell'hub virtuale. Poiché ogni connessione del gateway VPN è composta da due tunnel nella configurazione attivo-attivo, verranno visualizzati entrambi gli indirizzi IP elencati in questo file. In questo esempio `Instance0` vengono `Instance1` visualizzati e per ogni sito gli indirizzi IP privati anziché gli indirizzi IP pubblici.<br>Esempio:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * Dettagli di configurazione per la connessione al gateway VPN, ad esempio BGP e chiave precondivisa. La chiave precondivisa viene generata automaticamente. È sempre possibile modificare la connessione nella pagina **Panoramica** per una chiave precondivisa personalizzata.
  
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

* Le istruzioni nella pagina del dispositivo VPN non sono scritte per una rete WAN virtuale. Tuttavia, è possibile utilizzare i valori della WAN virtuale del file di configurazione per configurare manualmente il dispositivo VPN. 
* Gli script di configurazione dei dispositivi scaricabili per il gateway VPN non funzionano per la rete WAN virtuale, perché la configurazione è diversa.
* Una nuova rete WAN virtuale può supportare sia IKEv1 che IKEv2.
* Una rete WAN virtuale può utilizzare solo dispositivi VPN basati su route e istruzioni per i dispositivi.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. Visualizzare la rete WAN virtuale

1. Passare alla rete WAN virtuale.
1. Nella pagina **Panoramica** ogni punto della mappa rappresenta un hub.
1. Nella sezione **Hub e connessioni** è possibile visualizzare lo stato di hub, sito, area e connessione VPN. È inoltre possibile visualizzare i byte in e out.

## <a name="7-monitor-a-connection"></a><a name="connectmon"></a>7. Monitorare una connessione

Creare una connessione per monitorare la comunicazione tra una macchina virtuale di Azure e un sito remoto. Per informazioni su come configurare un monitoraggio della connessione, vedere [Monitorare la comunicazione di rete](~/articles/network-watcher/connection-monitor.md). Il campo di origine è l'IP della macchina virtuale in Azure e l'IP di destinazione è l'indirizzo IP del sito.

## <a name="8-clean-up-resources"></a><a name="cleanup"></a>8. Pulire le risorse

Quando queste risorse non sono più necessarie, è possibile usare [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse in esso contenute. Eseguire il comando PowerShell `myResourceGroup` seguente e sostituirlo con il nome del gruppo di risorse:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo consente di creare una connessione VPN tramite il peering privato ExpressRoute tramite la rete WAN virtuale. Per ulteriori informazioni sulla rete WAN virtuale e sulle funzionalità correlate, vedere [Panoramica](virtual-wan-about.md)della rete WAN virtuale .
