---
title: Integrare l'app con rete virtuale di Azure
description: Integra l'app nel servizio app Azure con le reti virtuali di Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 04/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 78b49b8b7e17f12d49825390a302e28a61e10d16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770849"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrare l'app con una rete virtuale di Azure

Questo articolo descrive la funzionalità di integrazione VNet del servizio app Azure e come configurarla con le app nel [servizio app Azure](https://go.microsoft.com/fwlink/?LinkId=529714). Con la [rete virtuale di Azure][VNETOverview] (reti virtuali) è possibile inserire molte delle risorse di Azure in una rete non instradabile su Internet.

Il servizio app Azure presenta due varianti:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Abilitare l'integrazione di VNet

> [!NOTE]
> Se il pannello "rete" è disabilitato (in grigio) nel menu per le app Linux, significa che la funzionalità non è attualmente disponibile.
>

1. Passare all'interfaccia utente di **rete** nel portale del servizio app. In **integrazione VNet**selezionare **fare clic qui per configurare**.

1. Selezionare **Add VNet** (Aggiungi rete virtuale).

   ![Seleziona integrazione VNet][1]

1. L'elenco a discesa contiene tutte le reti virtuali Azure Resource Manager nella sottoscrizione nella stessa area. Sotto è riportato un elenco delle reti virtuali Gestione risorse in tutte le altre aree. Selezionare il VNet con cui si vuole eseguire l'integrazione.

   ![Selezionare il VNet][2]

   * Se il VNet si trova nella stessa area, creare una nuova subnet o selezionare una subnet preesistente vuota.
   * Per selezionare un VNet in un'altra area, è necessario disporre di un gateway VNet di cui è stato effettuato il provisioning con la funzionalità da punto a sito.
   * Per eseguire l'integrazione con una VNet classica, anziché selezionare l'elenco a discesa **rete virtuale** , selezionare **fare clic qui per connettersi a una VNet classica**. Selezionare la rete virtuale classica desiderata. Il VNet di destinazione deve avere già un gateway di rete virtuale di cui è stato effettuato il provisioning con la funzionalità da punto a sito abilitata.

    ![Selezione VNet classica][3]

Durante l'integrazione, l'app viene riavviata. Al termine dell'integrazione, verranno visualizzati i dettagli sul VNet con cui si è integrato.

## <a name="regional-vnet-integration"></a>Integrazione VNet a livello di area

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Funzionamento dell'integrazione VNet a livello di area

Le app nel servizio app sono ospitate in ruoli di lavoro. I piani tariffari di base e superiore sono piani di hosting dedicati in cui non sono presenti altri carichi di lavoro dei clienti in esecuzione negli stessi thread di lavoro. L'integrazione VNet a livello di area funziona montando interfacce virtuali con indirizzi nella subnet delegata. Poiché l'indirizzo from si trova nel VNet, può accedere alla maggior parte delle cose in o tramite VNet come una macchina virtuale in VNet. L'implementazione della rete è diversa rispetto all'esecuzione di una macchina virtuale nella VNet. Ecco perché alcune funzionalità di rete non sono ancora disponibili per questa funzionalità.

![Funzionamento dell'integrazione VNet a livello di area][5]

Quando è abilitata l'integrazione VNet a livello di area, l'app effettua chiamate in uscita a Internet tramite gli stessi canali del normale. Gli indirizzi in uscita elencati nel portale delle proprietà dell'app sono gli indirizzi ancora usati dall'app. Quali modifiche sono state apportate all'app per le chiamate ai servizi protetti degli endpoint di servizio oppure gli indirizzi RFC 1918 entrano in VNet. Se WEBSITE_VNET_ROUTE_ALL è impostato su 1, tutto il traffico in uscita può essere inviato nella VNet.

La funzionalità supporta una sola interfaccia virtuale per ogni thread di lavoro. Un'interfaccia virtuale per ogni thread di lavoro indica un'integrazione VNet a livello di area per ogni piano di servizio app. Tutte le app nello stesso piano di servizio app possono usare la stessa integrazione con VNet. Se è necessario che un'app si connetta a un VNet aggiuntivo, è necessario creare un altro piano di servizio app. L'interfaccia virtuale utilizzata non è una risorsa a cui i clienti hanno accesso diretto.

A causa della natura del funzionamento di questa tecnologia, il traffico usato con l'integrazione di VNet non viene visualizzato nei log dei flussi di Azure Network Watcher o NSG.

## <a name="gateway-required-vnet-integration"></a>Gateway: integrazione VNet necessaria

Gateway: l'integrazione VNet necessaria supporta la connessione a una VNet in un'altra area o a una rete virtuale classica. Gateway: integrazione VNet necessaria:

* Consente a un'app di connettersi a un solo VNet alla volta.
* Consente di integrare fino a cinque reti virtuali all'interno di un piano di servizio app.
* Consente l'uso della stessa VNet da parte di più app in un piano di servizio app senza influire sul numero totale che può essere usato da un piano di servizio app. Se sono presenti sei app che usano lo stesso VNet nello stesso piano di servizio app, questo viene conteggiato come un VNet in uso.
* Supporta uno SLA del 99,9% a causa del contratto di contratto sul gateway.
* Consente alle app di usare il DNS con cui è configurata la VNet.
* Richiede un gateway basato su route di rete virtuale configurato con una VPN da punto a sito SSTP prima che possa essere connessa a un'app.

Non è possibile usare l'integrazione VNet necessaria per il gateway:

* Con le app Linux.
* Con una VNet connessa con Azure ExpressRoute.
* Per accedere alle risorse protette dell'endpoint di servizio.
* Con un gateway di coesistenza che supporta le VPN ExpressRoute e da punto a sito o da sito a sito.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Configurare un gateway nella rete virtuale di Azure ###

Per creare un gateway:

1. [Creare una subnet del gateway][creategatewaysubnet] nella rete virtuale.  

1. [Creare il gateway VPN][creategateway]. Selezionare un tipo di VPN basato su route.

1. [Impostare gli indirizzi da punto a sito][setp2saddresses]. Se il gateway non si trova nello SKU di base, IKEV2 deve essere disabilitato nella configurazione da punto a sito e SSTP deve essere selezionato. Lo spazio degli indirizzi da punto a sito deve essere nei blocchi di indirizzi RFC 1918 10.0.0.0/8, 172.16.0.0/12 e 192.168.0.0/16.

Se si crea il gateway per l'uso con l'integrazione del servizio app VNet, non è necessario caricare un certificato. La creazione del gateway può richiedere 30 minuti. Non sarà possibile integrare l'app con la VNet fino a quando non viene effettuato il provisioning del gateway.

### <a name="how-gateway-required-vnet-integration-works"></a>Funzionamento dell'integrazione VNet necessaria per il gateway

Gateway: l'integrazione VNet necessaria è basata sulla tecnologia VPN da punto a sito. Le VPN da punto a sito limitano l'accesso di rete alla macchina virtuale che ospita l'app. Le app sono limitate a inviare il traffico a Internet solo tramite Connessioni ibride o tramite l'integrazione VNet. Quando l'app è configurata con il portale per l'uso dell'integrazione VNet necessaria per il gateway, per conto di creare e assegnare certificati sul gateway e sul lato applicazione viene gestita una negoziazione complessa. Il risultato è che i ruoli di lavoro usati per ospitare le app sono in grado di connettersi direttamente al gateway di rete virtuale nel VNet selezionato.

![Funzionamento dell'integrazione VNet necessaria per il gateway][6]

### <a name="access-on-premises-resources"></a>Accedere alle risorse locali

Le app possono accedere alle risorse locali grazie all'integrazione con le reti virtuali che dispongono di connessioni da sito a sito. Se si usa l'integrazione VNet necessaria per il gateway, aggiornare le route del gateway VPN locale con i blocchi di indirizzi da punto a sito. Quando la connessione VPN da sito a sito viene configurata per la prima volta, gli script usati per la configurazione devono configurare le route in modo appropriato. Se gli indirizzi da punto a sito vengono aggiunti dopo aver creato la VPN da sito a sito, è necessario aggiornare le route manualmente. Informazioni dettagliate su come eseguire questa operazione variano in base al gateway e non sono descritte qui. Non è possibile configurare BGP con una connessione VPN da sito a sito.

Non è necessaria alcuna configurazione aggiuntiva per la funzionalità di integrazione VNet a livello di area tramite il VNet alle risorse locali. È sufficiente connettere la VNet alle risorse locali usando ExpressRoute o una VPN da sito a sito.

> [!NOTE]
> La funzionalità di integrazione VNet necessaria per il gateway non integra un'app con una VNet che dispone di un gateway ExpressRoute. Anche se il gateway ExpressRoute è configurato in [modalità di coesistenza][VPNERCoex], l'integrazione di VNet non funziona. Se è necessario accedere alle risorse tramite una connessione ExpressRoute, usare la funzionalità di integrazione VNet a livello di area o un [ambiente del servizio app][ASE], che viene eseguito nel VNet.
> 
> 

### <a name="peering"></a>Peering

Se si usa il peering con l'integrazione VNet a livello di area, non è necessario eseguire alcuna configurazione aggiuntiva.

Se si usa l'integrazione VNet necessaria per il gateway con il peering, è necessario configurare alcuni elementi aggiuntivi. Per configurare il peering per interagire con l'app:

1. Aggiungere una connessione di peering nella rete virtuale a cui si connette l'app. Quando si aggiunge la connessione di peering, abilitare **Consenti accesso alla rete virtuale** e selezionare **Consenti il traffico con inoltri** e **Consenti transito gateway**.
1. Aggiungere una connessione di peering in VNet di cui viene eseguito il peering al VNet a cui si è connessi. Quando si aggiunge la connessione di peering nella VNet di destinazione, abilitare **Consenti accesso alla rete virtuale** e selezionare **Consenti il traffico trasmesso** e **Consenti gateway remoti**.
1. Passare all'interfaccia utente di**integrazione VNet** di > **rete** > del **piano di servizio app**nel portale. Selezionare la rete virtuale a cui si connette l'app. Nella sezione Routing aggiungere l'intervallo di indirizzi di VNet di cui è stato effettuato il peering con il VNet a cui è connessa l'app.

## <a name="manage-vnet-integration"></a>Gestire l'integrazione con VNet

La connessione e la disconnessione con un VNet sono a livello di app. Le operazioni che possono influenzare l'integrazione di VNet tra più app sono a livello di piano di servizio app. Dall'app > **rete** > **VNet Integration** Portal è possibile ottenere informazioni dettagliate sul VNet. È possibile visualizzare informazioni simili a livello di piano di servizio app nel portale di**integrazione VNet** di > **rete** > del **piano di servizio app**.

L'unica operazione che è possibile eseguire nella visualizzazione App dell'istanza di integrazione VNet è la disconnessione dell'app dalla VNet a cui è attualmente connessa. Per disconnettere l'app da una rete virtuale, selezionare **Disconnetti**. L'app viene riavviata quando si esegue la disconnessione da un VNet. La disconnessione non modifica la rete virtuale. La subnet o il gateway non è stato rimosso. Se quindi si vuole eliminare il VNet, disconnettere l'app da VNet ed eliminarne le risorse, ad esempio i gateway.

L'interfaccia utente dell'integrazione VNet del piano di servizio app Mostra tutte le integrazioni di VNet usate dalle app nel piano di servizio app. Per visualizzare i dettagli su ogni VNet, selezionare il VNet a cui si è interessati. Esistono due azioni che è possibile eseguire qui per l'integrazione VNet necessaria per il gateway:

* **Rete di sincronizzazione**: l'operazione di sincronizzazione rete viene usata solo per la funzionalità di integrazione VNet dipendente dal gateway. L'esecuzione di un'operazione di sincronizzazione della rete garantisce che i certificati e le informazioni di rete siano sincronizzati. Se si aggiunge o si modifica il DNS del VNet, eseguire un'operazione di sincronizzazione della rete. Questa operazione riavvia tutte le app che usano questo VNet.
* **Aggiungere le route**: l'aggiunta di route comporta il traffico in uscita nella VNet.

### <a name="gateway-required-vnet-integration-routing"></a>Gateway: routing di integrazione VNet obbligatorio
Le route definite in VNet vengono usate per indirizzare il traffico nella VNet dall'app. Per inviare il traffico in uscita aggiuntivo in VNet, aggiungere qui i blocchi di indirizzi. Questa funzionalità funziona solo con l'integrazione VNet necessaria per il gateway. Le tabelle di route non influiscono sul traffico dell'app quando si usa l'integrazione VNet necessaria per il gateway come avviene con l'integrazione VNet a livello di area.

### <a name="gateway-required-vnet-integration-certificates"></a>Gateway: certificati di integrazione VNet obbligatori
Quando è abilitata l'integrazione VNet necessaria per il gateway, è necessario lo scambio di certificati per garantire la sicurezza della connessione. Con i certificati si ottengono la configurazione DNS, le route e altre informazioni simili che descrivono la rete.

Se vengono modificati i certificati o le informazioni di rete, selezionare **Sincronizza rete**. Quando si seleziona **Sincronizza rete**, si verifica una breve interruzione della connettività tra l'app e la VNet. L'app non viene riavviata, ma la perdita di connettività potrebbe causare il funzionamento non corretto del sito.

## <a name="pricing-details"></a>Dettagli prezzi
La funzionalità di integrazione VNet a livello di area non prevede costi aggiuntivi per l'uso oltre i costi del piano tariffario del piano di servizio app.

Sono correlati tre addebiti per l'uso della funzionalità di integrazione VNet necessaria per il gateway:

* **Costi del piano tariffario del piano di servizio app**: le app devono essere in un piano di servizio app standard, Premium o PremiumV2. Per altre informazioni su questi costi, vedere [prezzi del servizio app][ASPricing].
* **Costi di trasferimento dei dati**: è previsto un addebito per l'uscita dei dati, anche se il VNet si trova nello stesso data center. Questi addebiti sono descritti in [Dettagli prezzi trasferimento dati][DataPricing].
* **Costi del gateway VPN**: è previsto un costo per il gateway di rete virtuale necessario per la VPN da punto a sito. Per altre informazioni, vedere [prezzi di gateway VPN][VNETPricing].

## <a name="troubleshooting"></a>Risoluzione dei problemi

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automazione

Il supporto dell'interfaccia della riga di comando è disponibile per l'integrazione VNet regionale. Per accedere ai comandi seguenti, [installare l'interfaccia della][installCLI]riga di comando di Azure.

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

Per l'integrazione VNet necessaria per il gateway, è possibile integrare il servizio app con una rete virtuale di Azure usando PowerShell. Per uno script pronto per l'esecuzione, vedere [connettere un'app nel servizio app Azure a una rete virtuale di Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


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
