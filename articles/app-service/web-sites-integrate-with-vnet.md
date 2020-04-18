---
title: Integrare l'app con la rete virtuale di AzureIntegrate app with Azure Virtual Network
description: Integra l'app nel servizio app di Azure con le reti virtuali di Azure.Integrate app in Azure App Service with Azure virtual networks.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 04/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4866397af244ffb3c6aa9c7547b0a9413b10ccfd
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604872"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrare l'app con una rete virtuale di AzureIntegrate your app with an Azure virtual network

Questo articolo descrive la funzionalità di integrazione della rete virtuale del servizio app di Azure e come configurarla con le app nel servizio app di Azure.This article describes the Azure App Service VNet Integration feature and how to set it up with apps in [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Con Rete virtuale di [Azure,][VNETOverview] è possibile inserire molte delle risorse di Azure in una rete non instradabile.With Azure Virtual Network (VNets), you can place many of your Azure resources in a non-internet-routable network.

Il servizio app di Azure presenta due varianti:Azure App Service has two variations:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Abilitare l'integrazione della rete virtualeEnable VNet Integration

1. Passare all'interfaccia utente **Rete** nel portale del servizio app. In **Integrazione rete virtuale**selezionare Fare clic qui per **configurare**.

1. Selezionare **Add VNet** (Aggiungi rete virtuale).

   ![Selezionare integrazione vNet][1]

1. L'elenco a discesa contiene tutte le reti virtuali di Azure Resource Manager nella sottoscrizione nella stessa area. Sotto c'è un elenco delle reti virtuali di Resource Manager in tutte le altre aree. Selezionare la rete virtuale con cui si desidera eseguire l'integrazione.

   ![Selezionare la rete virtuale][2]

   * Se la rete virtuale si trova nella stessa area, creare una nuova subnet o selezionare una subnet preesistente vuota.
   * Per selezionare una rete virtuale in un'altra area, è necessario disporre di un gateway della rete virtuale con l'abilitazione del punto al sito.
   * Per eseguire l'integrazione con una rete virtuale classica, anziché selezionare l'elenco a discesa **Rete virtuale,** selezionare **Fare clic qui per connettersi a una rete virtuale classica.** Selezionare la rete virtuale classica desiderata. La rete virtuale di destinazione deve avere già un gateway di rete virtuale con abilitato da punto a sito.

    ![Selezionare la rete virtuale classica][3]

Durante l'integrazione, l'app viene riavviata. Al termine dell'integrazione, vedrai i dettagli sulla rete virtuale con cui sei integrato.

## <a name="regional-vnet-integration"></a>Integrazione della rete virtuale regionale

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Funzionamento dell'integrazione regionale della rete virtuale

Le app nel servizio app sono ospitate nei ruoli di lavoro. I piani di business Basic e higher pricing sono piani di hosting dedicati in cui non sono in esecuzione carichi di lavoro di altri clienti sugli stessi lavoratori. L'integrazione regionale della rete virtuale funziona montando le interfacce virtuali con gli indirizzi nella subnet delegata. Poiché l'indirizzo da si trova nella rete virtuale, può accedere alla maggior parte degli elementi in o tramite la rete virtuale come una macchina virtuale nella rete virtuale. L'implementazione di rete è diversa dall'esecuzione di una macchina virtuale nella rete virtuale. Ecco perché alcune funzionalità di rete non sono ancora disponibili per questa funzionalità.

![Funzionamento dell'integrazione regionale della rete virtuale][5]

Quando l'integrazione della rete virtuale regionale è abilitata, l'app effettua chiamate in uscita verso Internet tramite gli stessi canali di consueto. Gli indirizzi in uscita elencati nel portale delle proprietà dell'app sono gli indirizzi ancora usati dall'app. Le modifiche apportate all'app sono le chiamate ai servizi protetti dall'endpoint del servizio o gli indirizzi RFC 1918 nella rete virtuale. Se WEBSITE_VNET_ROUTE_ALL è impostato su 1, tutto il traffico in uscita può essere inviato nella rete virtuale.

La funzionalità supporta una sola interfaccia virtuale per worker. Un'interfaccia virtuale per lavoratore indica un'integrazione della rete virtuale regionale per ogni piano di servizio app. Tutte le app nello stesso piano di servizio app possono usare la stessa integrazione della rete virtuale. Se è necessaria un'app per connettersi a una rete virtuale aggiuntiva, è necessario creare un altro piano di servizio app. L'interfaccia virtuale utilizzata non è una risorsa a cui i clienti hanno accesso diretto.

A causa della natura del funzionamento di questa tecnologia, il traffico usato con l'integrazione della rete virtuale non viene visualizzato nei log di flusso di Azure Network Watcher o NSG.

## <a name="gateway-required-vnet-integration"></a>Integrazione della rete virtuale richiesta dal gateway

Integrazione della rete virtuale richiesta dal gateway supporta la connessione a una rete virtuale in un'altra area o a una rete virtuale classica. Integrazione della rete virtuale richiesta dal gateway:

* Consente a un'app di connettersi a una sola rete virtuale alla volta.
* Consente l'installazione di un massimo di cinque reti virtuali all'interno di un piano di servizio app.
* Consente che la stessa rete virtuale possa essere usata da più app in un piano di servizio app senza influire sul numero totale che può essere usato da un piano di servizio app. Se si dispone di sei app che usano la stessa rete virtuale nello stesso piano di servizio app, viene conteggiata come una rete virtuale in uso.
* Supporta un servizio di servizio del 99,9% a causa del servizio di sla sul gateway.
* Consente alle app di utilizzare il DNS con cui è configurata la rete virtuale.
* Richiede un gateway basato su route di rete virtuale configurato con una VPN da punto a sito SSTP prima che possa essere connesso a un'app.

Non è possibile usare l'integrazione della rete virtuale richiesta dal gateway:You can't use gateway-required VNet Integration:

* Con le app Linux.
* With a VNet connected with Azure ExpressRoute.
* Per accedere alle risorse protette dell'endpoint del servizio.
* Con un gateway di coesistenza che supporta sia ExpressRoute che VPN da punto a sito o da sito a sito.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Configurare un gateway nella rete virtuale di AzureSet up a gateway in your Azure virtual network ###

Per creare un gateway:

1. [Creare una subnet del gateway][creategatewaysubnet] nella rete virtuale.  

1. [Creare il gateway VPN][creategateway]. Selezionare un tipo di VPN basato su route.

1. [Impostare gli indirizzi da punto a sito][setp2saddresses]. Se il gateway non si trova nello SKU di base, IKEV2 deve essere disabilitato nella configurazione da punto a sito e SSTP deve essere selezionato. Lo spazio degli indirizzi da punto a sito deve trovarsi nei blocchi di indirizzi RFC 1918 10.0.0.0/8, 172.16.0.0/12 e 192.168.0.0/16.

Se si crea il gateway per l'utilizzo con l'integrazione della rete virtuale del servizio app, non è necessario caricare un certificato. La creazione del gateway può richiedere 30 minuti. Non sarà possibile integrare l'app con la rete virtuale fino a quando non viene eseguito il provisioning del gateway.

### <a name="how-gateway-required-vnet-integration-works"></a>Funzionamento dell'integrazione della rete virtuale richiesta dal gateway

L'integrazione della rete virtuale richiesta dal gateway si basa sulla tecnologia VPN point-to-site. Le VPN da punto a sito limitano l'accesso di rete alla macchina virtuale che ospita l'app. Le app sono limitate per l'invio di traffico a Internet solo tramite connessioni ibride o tramite l'integrazione della rete virtuale. Quando l'app è configurata con il portale per usare l'integrazione della rete virtuale richiesta dal gateway, viene gestita una negoziazione complessa per conto dell'utente per creare e assegnare certificati nel gateway e sul lato dell'applicazione. Il risultato è che i lavoratori utilizzati per ospitare le app sono in grado di connettersi direttamente al gateway di rete virtuale nella rete virtuale selezionata.

![Funzionamento dell'integrazione della rete virtuale richiesta dal gateway][6]

### <a name="access-on-premises-resources"></a>Accedere alle risorse locali

Le app possono accedere alle risorse locali grazie all'integrazione con le reti virtuali che dispongono di connessioni da sito a sito. Se si usa l'integrazione della rete virtuale richiesta dal gateway, aggiornare le route del gateway VPN locali con i blocchi di indirizzi da punto a sito. Quando la connessione VPN da sito a sito viene configurata per la prima volta, gli script usati per la configurazione devono configurare le route in modo appropriato. Se gli indirizzi da punto a sito vengono aggiunti dopo aver creato la VPN da sito a sito, è necessario aggiornare le route manualmente. I dettagli su come eseguire questa operazione variano per gateway e non sono descritti qui. Non è possibile configurare BGP con una connessione VPN da sito a sito.

Non è necessaria alcuna configurazione aggiuntiva per la funzionalità di integrazione della rete virtuale regionale per raggiungere attraverso la rete virtuale alle risorse locali. È sufficiente connettere la rete virtuale alle risorse locali usando ExpressRoute o una VPN da sito a sito.

> [!NOTE]
> La funzionalità di integrazione della rete virtuale richiesta dal gateway non integra un'app con una rete virtuale con un gateway ExpressRoute.The gateway-required VNet Integration feature doesn't integrate an app with a VNet that has an ExpressRoute gateway. Anche se il gateway ExpressRoute è configurato in modalità di [coesistenza,][VPNERCoex]l'integrazione della rete virtuale non funziona. Se è necessario accedere alle risorse tramite una connessione ExpressRoute, usare la funzionalità di integrazione della rete virtuale regionale o un ambiente del [servizio][ASE]app, che viene eseguito nella rete virtuale.
> 
> 

### <a name="peering"></a>Peering

Se si usa il peering con l'integrazione regionale della rete virtuale, non è necessario eseguire alcuna configurazione aggiuntiva.

Se si usa l'integrazione della rete virtuale richiesta dal gateway con il peering, è necessario configurare alcuni elementi aggiuntivi. Per configurare il peering per interagire con l'app:

1. Aggiungere una connessione di peering nella rete virtuale a cui si connette l'app. Quando si aggiunge la connessione di peering, abilitare Consenti accesso alla **rete virtuale** e selezionare Consenti traffico **inoltrato** e Consenti transito **gateway**.
1. Aggiungere una connessione peering nella rete virtuale di cui si sta peerando la rete virtuale a cui si è connessi. Quando si aggiunge la connessione di peering nella rete virtuale di destinazione, abilitare Consenti accesso alla **rete virtuale** e selezionare Consenti traffico **inoltrato** e Consenti gateway **remoti**.
1. Passare all'interfaccia utente di**Networking** > **integrazione della rete di** reti del **piano** > di servizio app nel portale. Selezionare la rete virtuale a cui si connette l'app. Nella sezione routing aggiungi l'intervallo di indirizzi della rete virtuale sottoposta a peering con la rete virtuale a cui è connessa l'app.

## <a name="manage-vnet-integration"></a>Gestire l'integrazione della rete virtuale

La connessione e la disconnessione con una rete virtuale sono a livello di app. Le operazioni che possono influire sull'integrazione della rete virtuale tra più app sono a livello di piano di servizio app. Dal portale di**integrazione** della rete > di **rete,** > è possibile ottenere informazioni dettagliate sulla rete virtuale. È possibile visualizzare informazioni simili a livello di piano di servizio app nel portale di**Networking** > **integrazione della rete di** reti di servizio **app.** > 

L'unica operazione che puoi eseguire nella visualizzazione dell'app dell'istanza di integrazione della rete virtuale consiste nel disconnettere l'app dalla rete virtuale a cui è attualmente connessa. Per disconnettere l'app da una rete virtuale, selezionare **Disconnetti**. L'app viene riavviata quando ci si disconnette da una rete virtuale. La disconnessione non modifica la rete virtuale. La subnet o il gateway non viene rimosso. Se si desidera eliminare la rete virtuale, disconnettere prima l'app dalla rete virtuale ed eliminare le risorse in essa in essa in essa in essa inessa, ad esempio i gateway.

L'interfaccia utente di integrazione della rete virtuale del piano di servizio app mostra tutte le integrazioni della rete virtuale usate dalle app nel piano di servizio app. Per visualizzare i dettagli su ogni rete virtuale, selezionare la rete virtuale a cui si è interessati. Esistono due azioni che è possibile eseguire qui per l'integrazione della rete virtuale richiesta dal gateway:There are two actions you can perform here for gateway-required VNet Integration:

* **Rete di sincronizzazione**: L'operazione di rete di sincronizzazione viene utilizzata solo per la funzionalità di integrazione della rete virtuale dipendente dal gateway. L'esecuzione di un'operazione di sincronizzazione della rete garantisce che i certificati e le informazioni di rete siano sincronizzati. Se si aggiunge o si modifica il DNS della rete virtuale, eseguire un'operazione di sincronizzazione della rete. Questa operazione riavvia tutte le app che utilizzano questa rete virtuale.
* **Aggiungi route:** l'aggiunta di route determina il traffico in uscita nella rete virtuale.

### <a name="gateway-required-vnet-integration-routing"></a>Routing dell'integrazione della rete virtuale richiesta dal gateway
Le route definite nella rete virtuale vengono usate per indirizzare il traffico nella rete virtuale dall'app. Per inviare ulteriore traffico in uscita nella rete virtuale, aggiungere questi blocchi di indirizzi qui. Questa funzionalità funziona solo con l'integrazione della rete virtuale richiesta dal gateway. Le tabelle di route non influiscono sul traffico dell'app quando si usa l'integrazione della rete virtuale richiesta dal gateway come avviene con l'integrazione della rete virtuale regionale.

### <a name="gateway-required-vnet-integration-certificates"></a>Certificati di integrazione della rete virtuale obbligatoria per il gateway
Quando l'integrazione della rete virtuale richiesta dal gateway è abilitata, è necessario uno scambio di certificati per garantire la sicurezza della connessione. Con i certificati si ottengono la configurazione DNS, le route e altre informazioni simili che descrivono la rete.

Se i certificati o le informazioni di rete vengono modificate, selezionare **Sincronizza rete**. Quando si seleziona **Sincronizza rete**, si causa una breve interruzione della connettività tra l'app e la rete virtuale. L'app non viene riavviata, ma la perdita di connettività potrebbe causare il funzionamento non corretto del sito.

## <a name="pricing-details"></a>Dettagli prezzi
La funzionalità di integrazione della rete virtuale regionale non prevede costi aggiuntivi per l'utilizzo oltre gli addebiti del livello tariffario del piano di servizio app.

Tre costi sono correlati all'utilizzo della funzionalità di integrazione della rete virtuale richiesta dal gateway:

* **Tariffe del piano tariffario**del piano app: le app devono essere in un piano di servizio app Standard, Premium o PremiumV2. Per ulteriori informazioni su tali costi, vedere [Prezzi del servizio app][ASPricing].
* **Costi**di trasferimento dei dati : C'è un costo per l'uscita dei dati, anche se la rete virtuale si trova nello stesso datacenter. Tali addebiti sono descritti in [Dettagli sui prezzi][DataPricing]di trasferimento dati .
* **Costi del gateway VPN:** il gateway di rete virtuale è necessario per la VPN da punto a sito. Per ulteriori informazioni, vedere [Prezzi del gateway VPN][VNETPricing].

## <a name="troubleshooting"></a>Risoluzione dei problemi

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automazione

Il supporto CLI è disponibile per l'integrazione regionale della rete virtuale. Per accedere ai comandi seguenti, [installare l'interfaccia della riga di comando][installCLI]di Azure.

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

Per l'integrazione della rete virtuale richiesta dal gateway, è possibile integrare il servizio app con una rete virtuale di Azure tramite PowerShell.For gateway-required VNet Integration, you can integrate App Service with an Azure virtual network by using PowerShell. Per uno script pronto per l'esecuzione, vedere [Connettere un'app nel servizio app](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)di Azure a una rete virtuale di Azure.For a ready-to-run script, see Connect an app in Azure App Service to an Azure virtual network .


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
[privateendpoints]: networking/private-endpoint.md
