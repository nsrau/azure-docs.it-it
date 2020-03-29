---
title: Integrare l'app con la rete virtuale di AzureIntegrate app with Azure Virtual Network
description: Integrare le app nel servizio app di Azure con le reti virtuali di Azure.Integrate apps in Azure App Service with Azure Virtual Networks.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673216"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrare un'app in una rete virtuale di Azure
Questo documento descrive la funzionalità di integrazione della rete virtuale del servizio app di Azure e come configurarla con le app nel servizio app di [Azure.](https://go.microsoft.com/fwlink/?LinkId=529714) [Le reti virtuali][VNETOverview] di Azure consentono di inserire molte delle risorse di Azure in una rete instradabile non Internet.Azure Virtual Networks (VNets) allow you to place many of your Azure resources in a non-internet routable network.  

Il servizio app di Azure ha due varianti.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Abilitare l'integrazione della rete virtualeEnable VNet Integration 

1. Passare all'interfaccia utente Rete nel portale del servizio app. In Integrazione rete virtuale selezionare *"Fare clic qui per configurare".* 

1. Selezionare **Add VNet** (Aggiungi rete virtuale).  

   ![Selezionare integrazione vNet][1]

1. L'elenco a discesa conterrà tutte le reti virtuali di Resource Manager nella sottoscrizione nella stessa area e sotto che è un elenco di tutte le reti virtuali di Resource Manager in tutte le altre aree. Selezionare la rete virtuale con cui si desidera eseguire l'integrazione.

   ![Selezionare la rete virtuale][2]

   * Se la rete virtuale si trova nella stessa area, creare una nuova subnet o selezionare una subnet preesistente vuota. 

   * Per selezionare una rete virtuale in un'altra area, è necessario disporre di un gateway di rete virtuale con il punto per il sito abilitato.

   * Per eseguire l'integrazione con una rete virtuale classica, anziché fare clic sull'elenco a discesa Della rete virtuale, selezionare **Fare clic qui per connettersi a una rete virtuale classica**. Selezionare la rete virtuale classica desiderata. La rete virtuale di destinazione deve avere già un gateway di rete virtuale con il punto del sito abilitato.

    ![Selezionare la rete virtuale classica][3]
    
Durante l'integrazione, l'app viene riavviata.  Al termine dell'integrazione, verranno visualizzati i dettagli sulla rete virtuale con cui si è integrato. 

Una volta che l'app è integrata con la rete virtuale, utilizzerà lo stesso server DNS con cui è configurata la rete virtuale, a meno che non si tratti di zone private DNS di Azure.Once your app is integrated with your VNet, it will use the same DNS server that your VNet is configured with, unless it is Azure DNS Private zones. Al momento non è possibile usare l'integrazione della rete virtuale con le zone private DNS di Azure.You currently cannot use VNet Integration with Azure DNS Private zones.

## <a name="regional-vnet-integration"></a>Integrazione della rete virtuale regionale

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Funzionamento dell'integrazione regionale della rete virtuale

Le app nel servizio app sono ospitate nei ruoli di lavoro. I piani di business Basic e higher pricing sono piani di hosting dedicati in cui non sono presenti altri carichi di lavoro dei clienti in esecuzione sugli stessi lavoratori. L'integrazione regionale della rete virtuale funziona montando le interfacce virtuali con gli indirizzi nella subnet delegata. Poiché l'indirizzo da si trova nella rete virtuale, può accedere alla maggior parte degli elementi all'accesso o tramite la rete virtuale come farebbe una macchina virtuale nella rete virtuale. L'implementazione di rete è diversa dall'esecuzione di una macchina virtuale nella rete virtuale ed è per questo che alcune funzionalità di rete non sono ancora disponibili durante l'uso di questa funzionalità.

![Funzionamento dell'integrazione regionale della rete virtuale][5]

Quando l'integrazione della rete virtuale regionale è abilitata, l'app effettuerà comunque chiamate in uscita verso Internet tramite gli stessi canali di consueto. Gli indirizzi in uscita elencati nel portale delle proprietà dell'app sono ancora gli indirizzi usati dall'app. Quali modifiche per l'app sono, le chiamate ai servizi protetti dall'endpoint del servizio o gli indirizzi RFC 1918 vengono inseriti nella rete virtuale. Se WEBSITE_VNET_ROUTE_ALL è impostato su 1, tutto il traffico in uscita può essere inviato nella rete virtuale. 

La funzionalità supporta solo un'interfaccia virtuale per ogni worker.  Un'interfaccia virtuale per lavoratore indica un'integrazione della rete virtuale regionale per ogni piano di servizio app. Tutte le app nello stesso piano di servizio app possono usare la stessa integrazione della rete virtuale, ma se è necessaria un'app per connettersi a una rete virtuale aggiuntiva, sarà necessario creare un altro piano di servizio app. L'interfaccia virtuale utilizzata non è una risorsa a cui i clienti hanno accesso diretto.

A causa della natura del funzionamento di questa tecnologia, il traffico utilizzato con l'integrazione della rete virtuale non viene visualizzato nei log di flusso di Network Watcher o NSG.  

## <a name="gateway-required-vnet-integration"></a>Integrazione della rete virtuale richiesta dal gateway

Gateway necessaria VNet Integration supporta la connessione a una rete virtuale in un'altra area o a una rete virtuale classica. Integrazione della rete virtuale richiesta dal gateway: 

* Consente a un'app di connettersi a una sola rete virtuale alla volta
* Consente l'integrated di un massimo di cinque reti virtuali all'interno di un piano di servizio app 
* Consente che la stessa rete virtuale possa essere usata da più app in un piano di servizio app senza influire sul numero totale che può essere usato da un piano di servizio app.  Se si dispone di sei app che usano la stessa rete virtuale nello stesso piano di servizio app, viene conteggiata la stessa rete virtuale in uso. 
* Supporta un sLA del 99,9% a causa del servizio Disa
* Consente alle app di utilizzare il DNS con cui è configurata la rete virtuale
* Richiede un gateway basato su route di rete virtuale configurato con VPN da punto a sito SSTP prima che possa essere connesso all'app. 

Non è possibile usare l'integrazione della rete virtuale richiesta dal gateway:You can't use gateway required VNet Integration:

* Con le app Linux
* Con una rete virtuale connessa con ExpressRoute 
* Per accedere alle risorse protette degli endpoint del servizioTo access Service Endpoints secured resources
* Con un gateway di coesistenza che supporta sia ExpressRoute che punto a sito/sito a VPN sito

### <a name="set-up-a-gateway-in-your-vnet"></a>Configurare un gateway nella rete virtuale ###

Per creare un gateway:

1. [Creare una subnet del gateway][creategatewaysubnet] nella rete virtuale.  

1. [Creare il gateway VPN][creategateway]. Selezionare un tipo di VPN basato su route.

1. [Impostare gli indirizzi da punto a sito][setp2saddresses]. Se il gateway non è nello SKU di base, IKEV2 deve essere disabilitato nella configurazione del sito e SSTP deve essere selezionato. Lo spazio di indirizzi del sito deve trovarsi nei blocchi di indirizzi RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Se si sta semplicemente creando il gateway per l'utilizzo con l'integrazione della rete virtuale del servizio app, non è necessario caricare un certificato. La creazione del gateway può richiedere 30 minuti. Non sarà possibile integrare l'app con la rete virtuale fino al completamento del provisioning del gateway. 

### <a name="how-gateway-required-vnet-integration-works"></a>Funzionamento dell'integrazione della rete virtuale richiesta per il gateway

Gateway necessaria integrazione vNet costruita sulla tecnologia VPN point to site. Le VPN da punto a sito limitano l'accesso alla rete solo alla macchina virtuale che ospita l'app. Le app possono esclusivamente inviare traffico in Internet tramite Connessioni ibride o Integrazione rete virtuale. Quando l'app è configurata con il portale per usare l'integrazione della rete virtuale necessaria per il gateway, viene gestita una negoziazione complessa per conto dell'utente per creare e assegnare certificati nel gateway e sul lato dell'applicazione. Il risultato finale è che i lavoratori utilizzati per ospitare le app sono in grado di connettersi direttamente al gateway di rete virtuale nella rete virtuale selezionata. 

![Funzionamento dell'integrazione della rete virtuale richiesta per il gateway][6]

### <a name="accessing-on-premises-resources"></a>Accesso alle risorse locali

Le app possono accedere alle risorse locali grazie all'integrazione con le reti virtuali che dispongono di connessioni da sito a sito. Se si utilizza l'integrazione della rete virtuale richiesta dal gateway, è necessario aggiornare le route del gateway VPN locale con i blocchi di indirizzi da punto a sito. Quando la connessione VPN da sito a sito viene configurata per la prima volta, gli script usati per la configurazione devono configurare le route in modo appropriato. Se gli indirizzi da punto a sito vengono aggiunti dopo aver creato la VPN da sito a sito, è necessario aggiornare le route manualmente. Le informazioni dettagliate su come eseguire questa operazione variano in base al gateway e non sono descritte in questo documento. Non è possibile avere BGP configurato con una connessione VPN da sito a sito.

Non è necessaria alcuna configurazione aggiuntiva per la funzionalità di integrazione della rete virtuale regionale da raggiungere tramite la rete virtuale e in locale. È sufficiente connettere la rete virtuale a server locali usando ExpressRoute o una VPN da sito a sito. 

> [!NOTE]
> La funzionalità di integrazione della rete virtuale richiesta dal gateway non integra un'app con una rete virtuale con un gateway ExpressRoute.The gateway required VNet Integration feature doesn't integrate an app with a VNet that has an ExpressRoute Gateway. Anche se il gateway ExpressRoute è configurato in [modalità di coesistenza][VPNERCoex], Integrazione rete virtuale non funziona. Se è necessario accedere alle risorse tramite una connessione ExpressRoute, è possibile usare la funzionalità di integrazione della rete virtuale regionale o un ambiente del [servizio][ASE]app, che viene eseguito nella rete virtuale. 
> 
> 

### <a name="peering"></a>Peering

Se si utilizza il peering con l'integrazione della rete virtuale regionale, non è necessario eseguire alcuna configurazione aggiuntiva. 

Se si usa il gateway necessario per l'integrazione della rete virtuale con il peering, è necessario configurare alcuni elementi aggiuntivi. Per configurare il peering per interagire con l'app:

1. Aggiungere una connessione di peering nella rete virtuale a cui si connette l'app. Quando si aggiunge la connessione di peering, abilitare **Consenti accesso alla rete virtuale** e selezionare **Consenti traffico inoltrato** e **Consenti transito gateway**.
1. Aggiungere una connessione di peering nella rete virtuale sottoposta a peering alla rete virtuale a cui si è connessi. Quando si aggiunge la connessione di peering nella rete virtuale di destinazione, abilitare **Consenti accesso alla rete virtuale** e selezionare **Consenti traffico inoltrato** e **Usa gateway remoti**.
1. Passare a Piano di servizio app > Rete > interfaccia utente di Integrazione rete virtuale nel portale.  Selezionare la rete virtuale a cui si connette l'app. Nella sezione Routing aggiungere l'intervallo di indirizzi della rete virtuale sottoposta a peering alla rete virtuale a cui è connessa l'app.  

## <a name="managing-vnet-integration"></a>Gestione dell'integrazione della rete virtuale 

La connessione e la disconnessione con una rete virtuale sono a livello di app. Le operazioni che possono influenzare Integrazione rete virtuale tra più app sono a livello del piano di Servizio app. Dal portale di integrazione di Networking > rete > rete virtuale è possibile ottenere informazioni dettagliate sulla rete virtuale. È possibile visualizzare informazioni simili a livello ASP nel portale di integrazione di ASP > Networking >.

L'unica operazione possibile nella vista app di Integrazione rete virtuale è la disconnessione dell'app dalla rete virtuale a cui è attualmente connessa. Per disconnettere l'app da una rete virtuale, selezionare **Disconnetti**. L'app verrà riavviata quando ci si disconnette da una rete virtuale. La disconnessione non modifica la rete virtuale. La subnet o il gateway non viene rimosso. Se si desidera eliminare la rete virtuale, è innanzitutto necessario disconnettere l'app dalla rete virtuale ed eliminare le risorse in essa in essa in essa in essa. 

L'interfaccia utente di integrazione della rete virtuale ASP mostrerà tutte le integrazioni della rete virtuale utilizzate dalle app nell'ASP. Per visualizzare i dettagli su ogni rete virtuale, fare clic sulla rete virtuale in questione. Esistono due azioni che è possibile eseguire qui per l'integrazione della rete virtuale necessaria per il gateway.

* **Sincronizza rete**. L'operazione di sincronizzazione della rete è solo per la funzionalità di integrazione della rete virtuale dipendente dal gateway. L'esecuzione di un'operazione di sincronizzazione della rete garantisce che i certificati e le informazioni di rete siano sincronizzati. Se si aggiunge o si modifica il DNS della rete virtuale, è necessario eseguire un'operazione di sincronizzazione della **rete.** Questa operazione riavvierà qualsiasi app con questa rete virtuale.
* **Aggiungi route**. L'aggiunta di route indirizzerà il traffico in uscita nella rete virtuale. 

**Routing dell'integrazione della rete virtuale necessaria per** il gateway Le route definite nella rete virtuale vengono usate per indirizzare il traffico nella rete virtuale dall'app. Se è necessario inviare altro traffico in uscita nella rete virtuale, è possibile aggiungere qui i blocchi di indirizzi. Questa funzionalità funziona solo con l'integrazione della rete virtuale richiesta dal gateway. Le tabelle di route non influiscono sul traffico dell'app quando si usa l'integrazione della rete virtuale richiesta dal gateway come avviene con l'integrazione della rete virtuale regionale.

**Certificati di integrazione della rete virtuale necessari per** il gateway Quando il gateway richiesto VNet Integration abilitato, esiste uno scambio di certificati necessario per garantire la sicurezza della connessione. Con i certificati si ottengono la configurazione DNS, le route e altre informazioni simili che descrivono la rete.
Se vengono modificati i certificati o le informazioni di rete, è necessario fare clic su "Sincronizza rete". Quando si fa clic su "Sincronizza rete", si verifica una breve interruzione della connettività tra l'app e la rete virtuale. L'app non viene riavviata, ma la perdita di connettività potrebbe causare il funzionamento non corretto del sito. 

## <a name="pricing-details"></a>Dettagli prezzi
La funzionalità di integrazione della rete virtuale regionale non prevede costi aggiuntivi per l'utilizzo oltre gli addebiti del piano tariffario ASP.

Sono previsti tre costi correlati per l'utilizzo della funzionalità di integrazione della rete virtuale richiesta dal gateway:

* Costi del livello tariffario ASP: le app devono essere in un piano di servizio app Standard, Premium o PremiumV2.ASP pricing tier charges - Your apps need to be in a Standard, Premium, or PremiumV2 App Service Plan. Per altre informazioni sui costi, vedere i [prezzi del servizio app][ASPricing]. 
* Costi di trasferimento dei dati: è previsto un costo per l'uscita dei dati, anche se la rete virtuale si trova nello stesso data center. Gli addebiti sono descritti nei [dettagli relativi ai prezzi dei trasferimenti dei dati][DataPricing]. 
* Costi del gateway VPN: il gateway della rete virtuale è necessario per la VPN da punto a sito. I dettagli sono disponibili nella pagina dei [prezzi del gateway VPN][VNETPricing].

## <a name="troubleshooting"></a>Risoluzione dei problemi

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automazione

È disponibile il supporto CLI per l'integrazione regionale della rete virtuale. Per accedere ai comandi seguenti, [Installare l'interfaccia della riga di comando][installCLI]di Azure . 

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

Per l'integrazione della rete virtuale necessaria per il gateway, è possibile integrare il servizio app con una rete virtuale di Azure usando PowerShell.For gateway required VNet Integration, you can integrate App Service with an Azure Virtual Network using PowerShell. Per uno script pronto per l'esecuzione, vedere [Connect an app in Azure App Service to an Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3) (Connettere un'app del Servizio app di Azure a una rete virtuale di Azure).


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
