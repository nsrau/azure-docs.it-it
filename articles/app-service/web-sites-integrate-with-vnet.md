---
title: Integrare un'app con la rete virtuale di Azure
description: Integrare un'app in Servizio app di Azure con le reti virtuali di Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/05/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f2e17e99208d076d05132638b5161a284b73986f
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018628"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrare un'app con una rete virtuale di Azure

Questo articolo descrive la funzionalità di integrazione rete virtuale di Servizio app di Azure e come configurarla con le app di [Servizio app di Azure](https://go.microsoft.com/fwlink/?LinkId=529714). Con [Rete virtuale di Azure][VNETOverview], è possibile inserire molte risorse di Azure in una rete non instradabile su Internet. La funzionalità di integrazione VNet consente alle app di accedere alle risorse in o tramite un VNet. L'integrazione di VNet non consente l'accesso privato alle app.

App Azure servizio presenta due varianti sulla funzionalità di integrazione VNet:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Abilitare l'integrazione rete virtuale

1. Passare all'interfaccia utente di **Rete** nel portale del servizio app. In **Integrazione rete virtuale** selezionare **Fare clic per configurare**.

1. Selezionare **Add VNet** (Aggiungi rete virtuale).

   ![Selezionare Integrazione rete virtuale][1]

1. L'elenco a discesa contiene tutte le reti virtuali di Azure Resource Manager della sottoscrizione nella stessa area. Sotto è riportato un elenco delle reti virtuali di Resource Manager in tutte le altre aree. Selezionare la rete virtuale che si vuole integrare.

   ![Selezionare la rete virtuale][2]

   * Se la rete virtuale si trova nella stessa area, creare una nuova subnet o selezionare una subnet preesistente vuota.
   * Per selezionare una rete virtuale in un'altra area, è necessario aver effettuato il provisioning di un gateway di rete virtuale con la funzionalità da punto a sito abilitata.
   * Per l'integrazione con una rete virtuale classica, invece di selezionare l'elenco a discesa **Rete virtuale**, selezionare **Fare clic qui per connettersi a una rete virtuale classica**. Selezionare la rete virtuale classica desiderata. La rete virtuale di destinazione deve avere già un gateway di rete virtuale di cui è stato effettuato il provisioning con la funzionalità da punto a sito abilitata.

    ![Selezionare la rete virtuale classica][3]

Durante l'integrazione, l'app viene riavviata. Al termine dell'integrazione, verranno visualizzati i dettagli sulla rete virtuale integrata.

## <a name="regional-vnet-integration"></a>Integrazione rete virtuale a livello di area

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Come funziona l'integrazione rete virtuale a livello di area

Le app nel servizio app sono ospitate in ruoli di lavoro. I piani tariffari Basic e superiori sono piani di hosting dedicati in cui non sono presenti carichi di lavoro di altri clienti in esecuzione negli stessi ruoli di lavoro. L'integrazione rete virtuale a livello di area consiste nel montaggio di interfacce virtuali con indirizzi nella subnet delegata. L'indirizzo di origine, trovandosi nella rete virtuale, può accedere alla maggior parte degli elementi in o attraverso la rete virtuale, esattamente come una macchina virtuale. Poiché l'implementazione della rete è diversa rispetto all'esecuzione di una macchina virtuale nella rete virtuale, alcune funzionalità di rete non sono ancora disponibili per questa funzionalità.

![Come funziona l'integrazione rete virtuale a livello di area][5]

Quando l'integrazione rete virtuale è abilitata a livello di area, l'app effettua chiamate in uscita a Internet tramite i normali canali. Gli indirizzi in uscita elencati nel portale delle proprietà dell'app sono gli indirizzi ancora usati dall'app. Per l'app variano solo le chiamate ai servizi protetti degli endpoint di servizio, ovvero gli indirizzi RFC 1918 accedono alla rete virtuale. Se WEBSITE_VNET_ROUTE_ALL è impostato su 1, tutto il traffico in uscita può essere inviato nella rete virtuale.

> [!NOTE]
> `WEBSITE_VNET_ROUTE_ALL` non è attualmente supportato nei contenitori di Windows.
> 

La funzionalità supporta una sola interfaccia virtuale per ogni ruolo di lavoro. Un'interfaccia virtuale per ogni ruolo di lavoro significa un'integrazione rete virtuale a livello di area per ogni piano di servizio app. Tutte le app nello stesso piano di servizio app possono usare la stessa integrazione rete virtuale. Se è necessario che un'app si connetta a una rete virtuale aggiuntiva, è necessario creare un altro piano di servizio app. L'interfaccia virtuale usata non è una risorsa a cui i clienti hanno accesso diretto.

Considerato il modo in cui funziona questa tecnologia, il traffico usato con l'integrazione rete virtuale non viene visualizzato nei log dei flussi di Azure Network Watcher o dei gruppi di sicurezza di rete.

## <a name="gateway-required-vnet-integration"></a>Integrazione rete virtuale richiesta dal gateway

L'integrazione rete virtuale richiesta dal gateway supporta la connessione a una rete virtuale in un'altra area o a una rete virtuale classica. L'integrazione rete virtuale richiesta dal gateway:

* Consente a un'app di connettersi a una sola rete virtuale alla volta.
* Consente di integrare fino a cinque reti virtuali in un piano di servizio app.
* Consente a più app di un piano di servizio app di usare la stessa rete virtuale senza effetti sul numero totale che può essere usato da un piano di servizio app. Se sei app usano la stessa rete virtuale nello stesso piano di servizio app, è come se venisse usata una sola rete virtuale.
* Supporta un contratto di servizio del 99,9% grazie al contratto di servizio sul gateway.
* Consente alle app di usare il DNS con cui è configurata la rete virtuale.
* Richiede un gateway di rete virtuale basato su route configurato con una VPN da punto a sito SSTP per poter eseguire la connessione a un'app.

Non è possibile usare l'integrazione rete virtuale richiesta dal gateway:

* Con una rete virtuale connessa con Azure ExpressRoute.
* Da un'app Linux.
* Da un [contenitore di Windows](quickstart-custom-container.md).
* Per accedere alle risorse protette dall'endpoint di servizio.
* Con un gateway di coesistenza che supporta le VPN sia ExpressRoute che da punto a sito o da sito a sito.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Configurare un gateway nella rete virtuale di Azure ###

Per creare un gateway:

1. [Creare una subnet del gateway][creategatewaysubnet] nella rete virtuale.

1. [Creare il gateway VPN][creategateway]. Selezionare un tipo di VPN basato su route.

1. [Impostare gli indirizzi da punto a sito][setp2saddresses]. Se il gateway non si trova nello SKU di base, IKEV2 deve essere disabilitato nella configurazione da punto a sito e SSTP deve essere selezionato. Lo spazio indirizzi da punto a sito deve essere compreso nei blocchi di indirizzi RFC 1918 10.0.0.0/8, 172.16.0.0/12 e 192.168.0.0/16.

Se si crea il gateway per l'uso con l'integrazione rete virtuale del servizio app, non è necessario caricare un certificato. La creazione del gateway può richiedere 30 minuti. Non sarà possibile integrare l'app con la rete virtuale fino al completamento del provisioning del gateway.

### <a name="how-gateway-required-vnet-integration-works"></a>Come funziona l'integrazione rete virtuale richiesta dal gateway

L'integrazione rete virtuale richiesta dal gateway si basa sulla tecnologia VPN da punto a sito. Le VPN da punto a sito limitano alla macchina virtuale che ospita l'app l'accesso alla rete. Le app possono inviare traffico in Internet esclusivamente tramite connessioni ibride o integrazione rete virtuale. Quando l'app viene configurata con il portale per l'uso dell'integrazione rete virtuale richiesta dal gateway, viene automaticamente gestita una negoziazione complessa per creare e assegnare certificati sul lato gateway e sul lato applicazione. Il risultato è che i ruoli di lavoro usati per ospitare le app possono connettersi direttamente al gateway di rete virtuale nella rete virtuale selezionata.

![Come funziona l'integrazione rete virtuale richiesta dal gateway][6]

### <a name="access-on-premises-resources"></a>Accedere alle risorse locali

Le app possono accedere alle risorse locali grazie all'integrazione con le reti virtuali che dispongono di connessioni da sito a sito. Se si usa l'integrazione rete virtuale richiesta dal gateway, aggiornare le route di gateway VPN locali con i blocchi di indirizzi da punto a sito. Quando la connessione VPN da sito a sito viene configurata per la prima volta, gli script usati per la configurazione devono configurare le route in modo appropriato. Se gli indirizzi da punto a sito vengono aggiunti dopo aver creato la VPN da sito a sito, è necessario aggiornare le route manualmente. Le informazioni dettagliate su come eseguire questa operazione variano in base al gateway e non sono descritte in questo documento. Non è possibile configurare BGP con una connessione VPN da sito a sito.

Non sono necessarie operazioni di configurazione aggiuntive per la funzionalità di integrazione rete virtuale a livello di area per raggiungere le risorse locali tramite la rete virtuale. È sufficiente connettere la rete virtuale alle risorse locali usando una VPN da sito a sito o ExpressRoute.

> [!NOTE]
> La funzionalità di integrazione rete virtuale richiesta dal gateway non consente l'integrazione di un'app con una rete virtuale dotata di un gateway ExpressRoute. Anche se il gateway ExpressRoute è configurato in [modalità di coesistenza][VPNERCoex], l'integrazione rete virtuale non funziona. Se è necessario accedere alle risorse tramite una connessione ExpressRoute, usare la funzionalità di integrazione rete virtuale a livello di area o un [ambiente del servizio app][ASE] eseguito nella rete virtuale.
>
>

### <a name="peering"></a>Peering

Se si usa il peering con l'integrazione rete virtuale a livello di area, non sono necessarie altre operazioni di configurazione.

Se si usa l'integrazione rete virtuale richiesta dal gateway con peering, è necessario configurare alcuni elementi aggiuntivi. Per configurare il peering per interagire con l'app:

1. Aggiungere una connessione di peering nella rete virtuale a cui si connette l'app. Quando si aggiunge la connessione di peering, abilitare **Consenti accesso alla rete virtuale** e selezionare **Consenti traffico inoltrato** e **Consenti transito gateway**.
1. Aggiungere una connessione di peering nella rete virtuale sottoposta a peering alla rete virtuale a cui si è connessi. Quando si aggiunge la connessione di peering nella rete virtuale di destinazione, abilitare **Consenti accesso alla rete virtuale** e selezionare **Consenti traffico inoltrato** e **Usa gateway remoti**.
1. Passare all'interfaccia utente **Piano di servizio app** > **Rete** > **Integrazione rete virtuale** nel portale. Selezionare la rete virtuale a cui si connette l'app. Nella sezione Routing aggiungere l'intervallo di indirizzi della rete virtuale sottoposta a peering alla rete virtuale a cui è connessa l'app.

## <a name="manage-vnet-integration"></a>Gestire l'integrazione rete virtuale

La connessione e la disconnessione con una rete virtuale vengono eseguite a livello di app. Le operazioni che possono influenzare l'integrazione rete virtuale tra più app vengono eseguite a livello di piano di servizio app. Dal portale dell'app > **Rete** > **Integrazione rete virtuale** è possibile ottenere i dettagli della rete virtuale. È possibile visualizzare informazioni simili a livello di piano di servizio app nel portale **Piano del Servizio app** > **Rete** > **Integrazione rete virtuale**.

L'unica operazione possibile nella visualizzazione app dell'istanza di Integrazione rete virtuale è la disconnessione dell'app dalla rete virtuale a cui è attualmente connessa. Per disconnettere l'app da una rete virtuale, selezionare **Disconnetti**. L'app viene riavviata quando ci si disconnette da una rete virtuale. La disconnessione non modifica la rete virtuale. La subnet o il gateway non viene rimosso. Se quindi si vuole eliminare la rete virtuale, disconnettere prima l'app dalla rete virtuale ed eliminarne le risorse, ad esempio i gateway.

L'interfaccia utente di Integrazione rete virtuale del piano di servizio app mostra tutte le integrazioni di reti virtuali usate dalle app nel piano di servizio app. Per visualizzare i dettagli su ogni rete virtuale, selezionare la rete virtuale in questione. È possibile eseguire due azioni per l'integrazione rete virtuale richiesta dal gateway:

* **Sincronizzare la rete**: l'operazione di sincronizzazione della rete viene usata solo per la funzionalità di integrazione rete virtuale dipendente dal gateway. L'esecuzione di un'operazione di sincronizzazione della rete assicura che i certificati e le informazioni di rete siano sincronizzati. Se si aggiunge o modifica il DNS della rete virtuale, eseguire un'operazione di sincronizzazione della rete. Questa operazione riavvia tutte le app che usano questa rete virtuale. Questa operazione non funzionerà se si usano un'app e una rete virtuale appartenenti a sottoscrizioni diverse.
* **Aggiungere route**: l'aggiunta di route indirizzerà il traffico in uscita nella rete virtuale.

### <a name="gateway-required-vnet-integration-routing"></a>Routing dell'integrazione rete virtuale richiesta dal gateway
Le route definite in una rete virtuale vengono usate per indirizzare il traffico dall'app alla propria rete virtuale. Per inviare altro traffico in uscita nella rete virtuale, aggiungere qui i blocchi di indirizzi. Questa funzionalità può essere usata solo con l'integrazione rete virtuale richiesta dal gateway. Le tabelle di route non influiscono sul traffico dell'app quando si usa l'integrazione rete virtuale richiesta dal gateway, come avviene con l'integrazione rete virtuale a livello di area.

### <a name="gateway-required-vnet-integration-certificates"></a>Certificati dell'integrazione rete virtuale richiesta dal gateway
Quando l'integrazione rete virtuale richiesta dal gateway è abilitata, è necessario uno scambio di certificati per garantire la sicurezza della connessione. Con i certificati si ottengono la configurazione DNS, le route e altre informazioni simili che descrivono la rete.

Se vengono modificati i certificati o le informazioni di rete, selezionare **Sincronizza rete**. Quando si seleziona **Sincronizza rete**, si verifica una breve interruzione della connettività tra l'app e la rete virtuale. L'app non viene riavviata, ma la perdita di connettività potrebbe causare il funzionamento non corretto del sito.

## <a name="pricing-details"></a>Dettagli prezzi
La funzionalità di integrazione Sync Network a livello di area non prevede costi aggiuntivi oltre a quelli del piano tariffario del servizio app.

L'uso della funzionalità di integrazione rete virtuale richiesta dal gateway comporta tre tipi di addebiti:

* **Costi del piano tariffario del piano di servizio app**: le app devono essere in un piano di servizio app standard, Premium, PremiumV2 o PremiumV3. Per altre informazioni sui costi, vedere [Prezzi di Servizio app][ASPricing].
* **Costi di trasferimento dei dati**: è previsto un addebito per i dati in uscita, anche se la rete virtuale si trova nello stesso data center. Gli addebiti sono descritti nei [dettagli relativi ai prezzi dei trasferimenti dei dati][DataPricing].
* **Costi del gateway VPN**: è previsto un costo per il gateway di rete virtuale necessario per la connessione VPN da punto a sito. Per altre informazioni, vedere [Prezzi di Gateway VPN][VNETPricing].

## <a name="troubleshooting"></a>Risoluzione dei problemi

> [!NOTE]
> L'integrazione di VNET non è supportata per gli scenari di Docker Compose nel servizio app.
>

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automazione

Il supporto dell'interfaccia della riga di comando è disponibile per l'integrazione rete virtuale a livello di area. Per accedere ai comandi seguenti, [installare l'interfaccia della riga di comando di Azure][installCLI].

```azurecli
az webapp vnet-integration --help

Group
    az webapp vnet-integration : Methods that list, add, and remove virtual network
    integrations from a webapp.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a regional virtual network integration to a webapp.
    list   : List the virtual network integrations on a webapp.
    remove : Remove a regional virtual network integration from webapp.

az appservice vnet-integration --help

Group
    az appservice vnet-integration : A method that lists the virtual network
    integrations used in an appservice plan.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    list : List the virtual network integrations used in an appservice plan.
```

È disponibile anche il supporto di PowerShell per l'integrazione VNet a livello di area, ma è necessario creare una risorsa generica con una matrice di proprietà della subnet resourceID

```azurepowershell
# Parameters
$sitename = 'myWebApp'
$resourcegroupname = 'myRG'
$VNetname = 'myVNet'
$location = 'myRegion'
$integrationsubnetname = 'myIntegrationSubnet'
$subscriptionID = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee'

#Property array with the SubnetID
$properties = @{
  subnetResourceId = "/subscriptions/$subscriptionID/resourceGroups/$resourcegroupname/providers/Microsoft.Network/virtualNetworks/$VNetname/subnets/$integrationsubnetname"
}

#Creation of the VNet integration
$vNetParams = @{
  ResourceName = "$sitename/VirtualNetwork"
  Location = $location
  ResourceGroupName = $resourcegroupname
  ResourceType = 'Microsoft.Web/sites/networkConfig'
  PropertyObject = $properties
}
New-AzResource @vNetParams
```


Per l'integrazione rete virtuale richiesta dal gateway, è possibile integrare il servizio app con una rete virtuale di Azure usando PowerShell. Per uno script pronto per l'esecuzione, vedere [Connettere un'app di Servizio app di Azure a una rete virtuale di Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: ../virtual-network/virtual-networks-overview.md
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: ../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[setp2saddresses]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool
[VNETRouteTables]: ../virtual-network/manage-route-table.md
[installCLI]: /cli/azure/install-azure-cli?view=azure-cli-latest%2f
[privateendpoints]: networking/private-endpoint.md