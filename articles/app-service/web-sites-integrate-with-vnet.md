---
title: Integrare l'app con rete virtuale di Azure
description: Integra le app nel servizio app Azure con le reti virtuali di Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673216"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrare un'app in una rete virtuale di Azure
Questo documento descrive la funzionalità di integrazione della rete virtuale del servizio app Azure e come configurarla con le app nel [servizio app Azure](https://go.microsoft.com/fwlink/?LinkId=529714). Le [reti virtuali di Azure][VNETOverview] (reti virtuali) consentono di inserire molte delle risorse di Azure in una rete instradabile non Internet.  

Il servizio app Azure presenta due varianti.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Abilitare l'integrazione di VNet 

1. Passare all'interfaccia utente di rete nel portale del servizio app. In integrazione VNet selezionare *"fare clic qui per configurare"* . 

1. Selezionare **Add VNet** (Aggiungi rete virtuale).  

   ![Seleziona integrazione VNet][1]

1. L'elenco a discesa conterrà tutti i Gestione risorse reti virtuali nella sottoscrizione nella stessa area e in basso che è un elenco di tutti i Gestione risorse reti virtuali in tutte le altre aree. Selezionare il VNet con cui si vuole eseguire l'integrazione.

   ![Selezionare il VNet][2]

   * Se il VNet si trova nella stessa area, creare una nuova subnet o selezionare una subnet preesistente vuota. 

   * Per selezionare un VNet in un'altra area, è necessario disporre di un gateway di rete virtuale di cui è stato effettuato il provisioning con la funzionalità da punto a sito.

   * Per l'integrazione con una VNet classica, invece di fare clic sull'elenco a discesa VNet, selezionare **fare clic qui per connettersi a un VNet classico**. Selezionare la VNet classica desiderata. Il VNet di destinazione deve avere già un gateway di rete virtuale di cui è stato effettuato il provisioning con da punto a sito abilitato.

    ![Selezione VNet classica][3]
    
Durante l'integrazione, l'app viene riavviata.  Al termine dell'integrazione, verranno visualizzati i dettagli sul VNet con cui si è integrato. 

Quando l'app è integrata con la VNet, userà lo stesso server DNS con cui è configurata la VNet, a meno che non sia Zone private di DNS di Azure. Attualmente non è possibile usare l'integrazione di VNet con Zone private di DNS di Azure.

## <a name="regional-vnet-integration"></a>Integrazione VNet a livello di area

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Funzionamento dell'integrazione VNet a livello di area

Le app nel servizio app sono ospitate in ruoli di lavoro. I piani tariffari di base e superiore sono piani di hosting dedicati in cui non sono presenti altri carichi di lavoro in esecuzione negli stessi thread di lavoro. L'integrazione VNet a livello di area funziona montando interfacce virtuali con indirizzi nella subnet delegata. Poiché l'indirizzo from si trova nel VNet, può accedere alla maggior parte delle operazioni in o tramite il VNet proprio come una VM nel VNet. L'implementazione della rete è diversa rispetto all'esecuzione di una macchina virtuale nella VNet ed è per questo motivo che alcune funzionalità di rete non sono ancora disponibili durante l'uso di questa funzionalità.

![Funzionamento dell'integrazione VNet a livello di area][5]

Quando è abilitata l'integrazione VNet a livello di area, l'app effettuerà comunque chiamate in uscita a Internet tramite gli stessi canali del normale. Gli indirizzi in uscita elencati nel portale delle proprietà dell'app sono ancora gli indirizzi usati dall'app. Quali modifiche sono state apportate all'app, le chiamate a servizi protetti per endpoint di servizio o indirizzi RFC 1918 passano alla VNet. Se WEBSITE_VNET_ROUTE_ALL è impostato su 1, tutto il traffico in uscita può essere inviato a VNet. 

La funzionalità supporta solo un'interfaccia virtuale per ogni thread di lavoro.  Un'interfaccia virtuale per ogni thread di lavoro indica un'integrazione VNet a livello di area per ogni piano di servizio app. Tutte le app nello stesso piano di servizio app possono usare la stessa integrazione di VNet, ma se è necessario che un'app possa connettersi a un VNet aggiuntivo, sarà necessario creare un altro piano di servizio app. L'interfaccia virtuale utilizzata non è una risorsa a cui i clienti hanno accesso diretto.

A causa della natura del funzionamento di questa tecnologia, il traffico usato con l'integrazione di VNet non viene visualizzato nei log di flusso Network Watcher o NSG.  

## <a name="gateway-required-vnet-integration"></a>Integrazione VNet necessaria per il gateway

L'integrazione VNet necessaria per il gateway supporta la connessione a una VNet in un'altra area o a una VNet classica. Integrazione VNet necessaria per il gateway: 

* Consente a un'app di connettersi solo a 1 VNet alla volta
* Consente l'integrazione di un massimo di cinque reti virtuali all'interno di un piano di servizio app 
* Consente l'uso della stessa VNet da parte di più app in un piano di servizio app senza influisca sul numero totale che può essere usato da un piano di servizio app.  Se sono presenti sei app che usano lo stesso VNet nello stesso piano di servizio app, il numero è pari a 1 VNet in uso. 
* Supporta uno SLA del 99,9% a causa del contratto di contratto sul gateway
* Consente alle app di usare il DNS con cui è configurato il VNet
* Richiede un gateway basato su route di rete virtuale configurato con una VPN da punto a sito SSTP prima che possa essere connessa all'app. 

Non è possibile usare l'integrazione VNet necessaria per il gateway:

* Con le app Linux
* Con una VNet connessa a ExpressRoute 
* Per accedere alle risorse protette degli endpoint di servizio
* Con un gateway di coesistenza che supporta le VPN ExpressRoute e da punto a sito/sito a sito

### <a name="set-up-a-gateway-in-your-vnet"></a>Configurare un gateway nella rete virtuale ###

Per creare un gateway:

1. [Creare una subnet del gateway][creategatewaysubnet] nella VNet.  

1. [Creare il gateway VPN][creategateway]. Selezionare un tipo di VPN basato su route.

1. [Impostare gli indirizzi da punto a sito][setp2saddresses]. Se il gateway non si trova nello SKU Basic, IKEV2 deve essere disabilitato nella configurazione da punto a sito ed è necessario selezionare SSTP. Lo spazio di indirizzi da punto a sito deve essere nei blocchi di indirizzi RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Se si sta semplicemente creando il gateway per l'uso con l'integrazione VNet del servizio app, non è necessario caricare un certificato. La creazione del gateway può richiedere 30 minuti. Non sarà possibile integrare l'app con la rete virtuale fino al completamento del provisioning del gateway. 

### <a name="how-gateway-required-vnet-integration-works"></a>Funzionamento dell'integrazione VNet necessaria per il gateway

Il gateway richiede l'integrazione VNet basata sulla tecnologia VPN da punto a sito. Le VPN da punto a sito limitano l'accesso alla rete solo alla macchina virtuale che ospita l'app. Le app possono esclusivamente inviare traffico in Internet tramite Connessioni ibride o Integrazione rete virtuale. Quando l'app è configurata con il portale per l'uso dell'integrazione VNet necessaria per il gateway, per conto di creare e assegnare certificati sul gateway e sul lato applicazione viene gestita una negoziazione complessa. Il risultato finale è che i ruoli di lavoro usati per ospitare le app sono in grado di connettersi direttamente al gateway di rete virtuale nel VNet selezionato. 

![Funzionamento dell'integrazione VNet necessaria per il gateway][6]

### <a name="accessing-on-premises-resources"></a>Accesso alle risorse locali

Le app possono accedere alle risorse locali grazie all'integrazione con le reti virtuali che dispongono di connessioni da sito a sito. Se si usa l'integrazione VNet necessaria per il gateway, è necessario aggiornare le route del gateway VPN locale con i blocchi di indirizzi da punto a sito. Quando la connessione VPN da sito a sito viene configurata per la prima volta, gli script usati per la configurazione devono configurare le route in modo appropriato. Se gli indirizzi da punto a sito vengono aggiunti dopo aver creato la VPN da sito a sito, è necessario aggiornare le route manualmente. Le informazioni dettagliate su come eseguire questa operazione variano in base al gateway e non sono descritte in questo documento. Non è possibile configurare BGP con una connessione VPN da sito a sito.

Non è necessaria alcuna configurazione aggiuntiva per la funzionalità di integrazione VNet a livello di area tramite VNet e in locale. È sufficiente connettere la VNet al sito locale usando ExpressRoute o una VPN da sito a sito. 

> [!NOTE]
> La funzionalità di integrazione VNet necessaria per il gateway non integra un'app con una VNet che dispone di un gateway ExpressRoute. Anche se il gateway ExpressRoute è configurato in [modalità di coesistenza][VPNERCoex] , l'integrazione di VNet non funziona. Se è necessario accedere alle risorse tramite una connessione ExpressRoute, è possibile usare la funzionalità di integrazione VNet a livello di area o un [ambiente del servizio app][ASE], che viene eseguito nel VNet. 
> 
> 

### <a name="peering"></a>Peering

Se si usa il peering con l'integrazione VNet a livello di area, non è necessario eseguire alcuna configurazione aggiuntiva. 

Se si usa il gateway necessario per l'integrazione di VNet con il peering, è necessario configurare alcuni elementi aggiuntivi. Per configurare il peering per interagire con l'app:

1. Aggiungere una connessione di peering nella rete virtuale a cui si connette l'app. Quando si aggiunge la connessione di peering, abilitare **Consenti accesso alla rete virtuale** e selezionare **Consenti traffico inoltrato** e **Consenti transito gateway**.
1. Aggiungere una connessione di peering nella rete virtuale sottoposta a peering alla rete virtuale a cui si è connessi. Quando si aggiunge la connessione di peering nella rete virtuale di destinazione, abilitare **Consenti accesso alla rete virtuale** e selezionare **Consenti traffico inoltrato** e **Usa gateway remoti**.
1. Passare a Piano di servizio app > Rete > interfaccia utente di Integrazione rete virtuale nel portale.  Selezionare la rete virtuale a cui si connette l'app. Nella sezione Routing aggiungere l'intervallo di indirizzi della rete virtuale sottoposta a peering alla rete virtuale a cui è connessa l'app.  

## <a name="managing-vnet-integration"></a>Gestione dell'integrazione di VNet 

La connessione e la disconnessione con un VNet sono a livello di app. Le operazioni che possono influenzare Integrazione rete virtuale tra più app sono a livello del piano di Servizio app. Dall'app > Networking > portale di integrazione VNet, è possibile ottenere informazioni dettagliate sul VNet. È possibile visualizzare informazioni simili a livello di ASP nella rete ASP > > portale di integrazione VNet.

L'unica operazione possibile nella vista app di Integrazione rete virtuale è la disconnessione dell'app dalla rete virtuale a cui è attualmente connessa. Per disconnettere l'app da una rete virtuale, selezionare **Disconnetti**. L'app verrà riavviata quando ci si disconnette da una rete virtuale. La disconnessione non modifica la rete virtuale. La subnet o il gateway non viene rimosso. Se quindi si vuole eliminare la VNet, è necessario prima disconnettere l'app dalla VNet ed eliminare le risorse in esso presenti, ad esempio i gateway. 

L'interfaccia utente di integrazione ASP VNet mostrerà tutte le integrazioni di VNet usate dalle app in ASP. Per visualizzare i dettagli su ogni rete virtuale, fare clic sulla rete virtuale in questione. Esistono due azioni che è possibile eseguire qui per l'integrazione VNet necessaria per il gateway.

* **Sincronizza rete**. L'operazione di sincronizzazione della rete è solo per la funzionalità di integrazione VNet dipendente dal gateway. L'esecuzione di un'operazione di sincronizzazione della rete garantisce che i certificati e le informazioni di rete siano sincronizzati. Se si aggiunge o si modifica il DNS del VNet, è necessario eseguire un'operazione di **sincronizzazione della rete** . Questa operazione riavvierà qualsiasi app con questa rete virtuale.
* **Aggiungi route**. L'aggiunta di route indirizzerà il traffico in uscita nella rete virtuale. 

Il **gateway richiede il routing dell'integrazione VNet** Le route definite in VNet vengono usate per indirizzare il traffico nella VNet dall'app. Se è necessario inviare altro traffico in uscita nella rete virtuale, è possibile aggiungere qui i blocchi di indirizzi. Questa funzionalità funziona solo con l'integrazione VNet obbligatoria del gateway. Le tabelle di route non influiscono sul traffico dell'app quando si usa l'integrazione VNet necessaria del gateway nel modo in cui vengono eseguite con l'integrazione VNet a livello di area.

**Certificati di integrazione VNet necessari** per il gateway Quando il gateway richiede l'integrazione di VNet abilitata, è necessario lo scambio di certificati per garantire la sicurezza della connessione. Con i certificati si ottengono la configurazione DNS, le route e altre informazioni simili che descrivono la rete.
Se vengono modificati i certificati o le informazioni di rete, è necessario fare clic su "Sincronizza rete". Quando si fa clic su "Sincronizza rete", si verifica una breve interruzione della connettività tra l'app e la rete virtuale. L'app non viene riavviata, ma la perdita di connettività potrebbe causare il funzionamento non corretto del sito. 

## <a name="pricing-details"></a>Dettagli prezzi
La funzionalità di integrazione VNet a livello di area non prevede costi aggiuntivi per l'utilizzo oltre l'importo del piano tariffario ASP.

Esistono tre addebiti correlati all'uso della funzionalità di integrazione VNet necessaria per il gateway:

* Costi del piano tariffario ASP: le app devono essere in un piano di servizio app standard, Premium o PremiumV2. Per altri dettagli sui costi, vedere i prezzi del [servizio app][ASPricing]. 
* Costi di trasferimento dei dati: è previsto un addebito per l'uscita dei dati, anche se il VNet è nello stesso data center. Questi addebiti sono descritti in [Dettagli prezzi trasferimento dati][DataPricing]. 
* Costi del gateway VPN: un costo per il gateway VNet richiesto per la VPN da punto a sito. I dettagli sono disponibili nella pagina dei [prezzi del gateway VPN][VNETPricing] .

## <a name="troubleshooting"></a>risoluzione dei problemi

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automazione

È disponibile il supporto dell'interfaccia della riga di comando per l'integrazione VNet regionale. Per accedere ai comandi seguenti, [installare l'interfaccia][installCLI]della riga di comando di Azure. 

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

Per l'integrazione VNet necessaria per il gateway, è possibile integrare il servizio app con una rete virtuale di Azure usando PowerShell. Per uno script pronto per l'esecuzione, vedere [Connect an app in Azure App Service to an Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3) (Connettere un'app del Servizio app di Azure a una rete virtuale di Azure).


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
