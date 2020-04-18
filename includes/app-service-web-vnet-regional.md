---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: f7208307df51ecefb76f9adaedea59b327cdc19e
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604866"
---
L'uso dell'integrazione regionale della rete virtuale consente all'app di accedere a:Using regional VNet Integration enables your app to access:

* Risorse in una rete virtuale nella stessa area dell'app.
* Risorse nelle reti virtuali sottoposte a peered alla rete virtuale con cui è integrata l'app.
* Servizi protetti dall'endpoint del servizio.
* Risorse tra connessioni Azure ExpressRoute.Resources across Azure ExpressRoute connections.
* Risorse nella rete virtuale con cui si è integrati.
* Risorse tra connessioni con peer, che includono connessioni Azure ExpressRoute.Resources across peered connections, which includes Azure ExpressRoute connections.
* Endpoint privati 

Quando si usa l'integrazione della rete virtuale con le reti virtuali nella stessa area, è possibile usare le funzionalità di rete di Azure seguenti:When you use VNet Integration with VNets in the same region, you can use the following Azure networking features:

* Gruppi di sicurezza di rete: è possibile bloccare il traffico in uscita con un gruppo di sicurezza di rete inserito nella subnet di integrazione.Network **security groups (NSGs)**: You can block outbound traffic with an NSG that's placed on your integration subnet. Le regole in ingresso non si applicano perché non è possibile usare l'integrazione della rete virtuale per fornire l'accesso in ingresso all'app.
* **Tabelle di route (UDR):** è possibile inserire una tabella di route nella subnet di integrazione per inviare il traffico in uscita dove si desidera.

Per impostazione predefinita, l'app instrada solo il traffico RFC1918 nella rete virtuale. Se vuoi instradare tutto il traffico in uscita nella rete virtuale, applica l'impostazione dell'app WEBSITE_VNET_ROUTE_ALL all'app. Per configurare l'impostazione dell'app:

1. Passare all'interfaccia utente di **configurazione** nel portale dell'app. Selezionare **Nuova impostazione applicazione**.
1. Immettere **WEBSITE_VNET_ROUTE_ALL** nella casella **Nome** e **1** nella casella **Valore.**

   ![Fornire l'impostazione dell'applicazioneProvide application setting][4]

1. Selezionare **OK**.
1. Selezionare **Salva**.

Se si instrada tutto il traffico in uscita nella rete virtuale, è soggetto ai gruppi di sicurezza di rete e alle dur applicate alla subnet di integrazione. Quando instradare tutto il traffico in uscita nella rete virtuale, gli indirizzi in uscita sono ancora gli indirizzi in uscita elencati nelle proprietà dell'app, a meno che non si forniscano route per inviare il traffico altrove.

Esistono alcune limitazioni con l'utilizzo dell'integrazione della rete virtuale con reti virtuali nella stessa area:There are some limitations with using VNet Integration with VNets in the same region:

* Non è possibile raggiungere le risorse attraverso connessioni peering globali.
* La funzionalità è disponibile solo dalle unità di scala del servizio app di Azure più recenti che supportano i piani di servizio app PremiumV2.The feature is available only from newer Azure App Service scale units that support PremiumV2 App Service plans.
* La subnet di integrazione può essere usata da un solo piano di servizio app.
* La funzionalità non può essere usata dalle app di piano isolato che si trovano in un ambiente del servizio app.
* La funzionalità richiede una subnet inutilizzata che è un /27 con 32 indirizzi o più grandi in una rete virtuale di Azure Resource Manager.The feature requires an unused subnet that's a /27 with 32 addresses or larger in an Azure Resource Manager VNet.
* L'app e la rete virtuale devono trovarsi nella stessa area.
* Non è possibile eliminare una rete virtuale con un'app integrata. Rimuovere l'integrazione prima di eliminare la rete virtuale.
* È possibile integrare solo con le reti virtuali nello stesso abbonamento dell'app.
* È possibile avere una sola integrazione della rete virtuale regionale per ogni piano di servizio app. Più app nello stesso piano di servizio app possono usare la stessa rete virtuale.
* Non è possibile modificare la sottoscrizione di un'app o di un piano mentre è presente un'app che usa l'integrazione regionale della rete virtuale.
* L'app non è in grado di risolvere gli indirizzi nelle zone private DNS di Azure senza modifiche alla configurazione

Viene utilizzato un indirizzo per ogni istanza del piano. Se si ridimensiona l'app a cinque istanze, vengono usati cinque indirizzi. Poiché le dimensioni della subnet non possono essere modificate dopo l'assegnazione, è necessario usare una subnet sufficientemente grande da contenere la scalabilità che l'app potrebbe raggiungere. Un /26 con 64 indirizzi è la dimensione consigliata. Un /26 con 64 indirizzi supporta un piano Premium con 30 istanze. Quando si aumenta o si aumenta la scalabilità di un piano, è necessario il doppio degli indirizzi per un breve periodo di tempo.

Se si vuole che le app in un altro piano raggiungano una rete virtuale già connessa dalle app in un altro piano, selezionare una subnet diversa da quella usata dall'integrazione di rete virtuale preesistente.

La funzionalità è in anteprima per Linux.The feature is in preview for Linux. Il formato Linux della funzionalità supporta solo chiamate a indirizzi RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web-or-function-app-for-containers"></a>App Web o funzione per i contenitori

Se si ospita l'app in Linux con le immagini incorporate, l'integrazione regionale della rete virtuale funziona senza ulteriori modifiche. Se si utilizza Web o Function App per i contenitori, è necessario modificare l'immagine docker per utilizzare l'integrazione della rete virtuale. Nell'immagine docker, utilizzare la variabile di ambiente PORT come porta di ascolto del server Web principale, anziché un numero di porta hardcoded. La variabile di ambiente PORT viene impostata automaticamente dalla piattaforma al momento dell'avvio del contenitore. Se si utilizza SSH, il daemon SSH deve essere configurato per l'ascolto sul numero di porta specificato dalla variabile di ambiente SSH_PORT quando si utilizza l'integrazione della rete virtuale regionale. Non è disponibile alcun supporto per l'integrazione della rete virtuale richiesta dal gateway in Linux.There's no support for gateway-required VNet Integration on Linux.

### <a name="service-endpoints"></a>Endpoint di servizio

L'integrazione regionale della rete virtuale consente di utilizzare gli endpoint del servizio. Per usare gli endpoint del servizio con l'app, usare l'integrazione della rete virtuale regionale per connettersi a una rete virtuale selezionata e quindi configurare gli endpoint del servizio con il servizio di destinazione nella subnet usata per l'integrazione. Se si desidera accedere a un servizio tramite endpoint di servizio:If you wanted to access a service over service endpoints:

1. configurare l'integrazione regionale della rete virtuale con l'app Web
1. passare al servizio di destinazione e configurare gli endpoint del servizio nella subnet utilizzata per l'integrazione

### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

È possibile usare i gruppi di sicurezza di rete per bloccare il traffico in ingresso e in uscita verso le risorse in una rete virtuale. Un'app che usa l'integrazione regionale della rete virtuale può usare un gruppo di sicurezza di rete per bloccare il traffico in uscita verso le risorse nella rete virtuale o in Internet.An app that uses regional VNet Integration can use a [network security group][VNETnsg] to block outbound traffic to resources in your VNet or the internet. Per bloccare il traffico verso indirizzi pubblici, è necessario che l'impostazione dell'applicazione WEBSITE_VNET_ROUTE_ALL impostata su 1.To block traffic to public addresses, you must have the application setting WEBSITE_VNET_ROUTE_ALL set to 1. Le regole in ingresso in un gruppo di sicurezza di rete non si applicano all'app perché l'integrazione della rete virtuale influisce solo sul traffico in uscita dall'app.

Per controllare il traffico in ingresso verso l'app, usa la funzionalità Restrizioni di accesso. Un gruppo di sicurezza di rete applicato alla subnet di integrazione è attivo indipendentemente dalle route applicate alla subnet di integrazione. Se WEBSITE_VNET_ROUTE_ALL è impostato su 1 e non sono presenti route che influiscono sul traffico degli indirizzi pubblici nella subnet di integrazione, tutto il traffico in uscita è ancora soggetto ai gruppi di sicurezza di rete assegnati alla subnet di integrazione. Se WEBSITE_VNET_ROUTE_ALL non è impostato, i gruppi di sicurezza di rete vengono applicati solo al traffico RFC1918.If a cis't set, NSGs are only applied to RFC1918 traffic.

### <a name="routes"></a>Route

Puoi usare le tabelle di route per instradare il traffico in uscita dall'app a ovunque desideri. Per impostazione predefinita, le tabelle di route influiscono solo sul traffico di destinazione RFC1918. Se si imposta WEBSITE_VNET_ROUTE_ALL su 1, tutte le chiamate in uscita sono interessate. Le route impostate nella subnet di integrazione non influiscono sulle risposte alle richieste di app in ingresso. Le destinazioni comuni possono includere dispositivi firewall o gateway.

Se si vuole instradare tutto il traffico in uscita in locale, è possibile usare una tabella di route per inviare tutto il traffico in uscita al gateway ExpressRoute.If you want to route all outbound traffic on-premises, you can use a route table to send all outbound traffic to your ExpressRoute gateway. Se si instrada il traffico a un gateway, assicurarsi di impostare le route nella rete esterna per inviare le risposte.

Le route BGP (Border Gateway Protocol) influiscono anche sul traffico dell'app. Se si dispone di route BGP da un'operazione simile a un gateway ExpressRoute, il traffico in uscita dell'app ne risentirà. Per impostazione predefinita, le route BGP influiscono solo sul traffico di destinazione RFC1918. Se WEBSITE_VNET_ROUTE_ALL è impostato su 1, tutto il traffico in uscita può essere influenzato dalle route BGP.

### <a name="azure-dns-private-zones"></a>Aree private DNS di AzureAzure DNS Private zones 

Dopo l'integrazione dell'app con la rete virtuale, usa lo stesso server DNS con cui è configurata la rete virtuale. Per impostazione predefinita, l'app non funziona con le zone private DNS di Azure.By default, your app won't work with Azure DNS Private zones. Per usare le zone private DNS di Azure, è necessario aggiungere le impostazioni dell'app seguenti:To work with Azure DNS Private zones you need to add the following app settings:

1. WEBSITE_DNS_SERVER con valore 168.63.129.16 
1. WEBSITE_VNET_ROUTE_ALL con valore 1

Queste impostazioni invieranno tutte le chiamate in uscita dall'app nella rete virtuale oltre a consentire all'app di usare le zone private DNS di Azure.These settings will send all of your outbound calls from your app into your VNet in addition to enabling your app to use Azure DNS private zones.

### <a name="private-endpoints"></a>Endpoint privati

Se si desidera effettuare chiamate agli [endpoint privati][privateendpoints], è necessario eseguire l'integrazione con le zone private DNS di Azure o gestire l'endpoint privato nel server DNS usato dall'app. 

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
