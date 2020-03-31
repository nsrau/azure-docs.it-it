---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: c731e2a7d4da1a66aabb50b335d526fbb6a0a302
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671481"
---
L'uso dell'integrazione regionale della rete virtuale consente all'app di accedere a:Using regional VNet Integration enables your app to access:

* risorse nella rete virtuale nella stessa area in cui ci si integra 
* risorse nelle reti virtuali sottoposte a peered alla rete virtuale che si trovano nella stessa area
* servizi protetti dall'endpoint di servizioService endpoint secured services
* risorse tra connessioni ExpressRoute
* risorse nella rete virtuale a cui si è connessi
* risorse tra connessioni con peered, incluse le connessioni ExpressRoute
* endpoint privati 

Quando si usa l'integrazione della rete virtuale con reti virtuali nella stessa area, è possibile usare le funzionalità di rete di Azure seguenti:When using VNet Integration with VNets in the same region, you can use the following Azure Networking features:

* Gruppi di sicurezza di rete: è possibile bloccare il traffico in uscita con un gruppo di sicurezza di rete che viene inserito nella subnet di integrazione.Network Security Groups (NSGs) - You can block outbound traffic with a Network Security Group that is placed on your integration subnet. Le regole in ingresso non si applicano in quanto non è possibile usare l'integrazione della rete virtuale per fornire l'accesso in ingresso all'app.
* Tabelle di route (UDR): è possibile inserire una tabella di route nella subnet di integrazione per inviare il traffico in uscita dove si desidera. 

Per impostazione predefinita, l'app indirizzerà solo il traffico RFC1918 nella rete virtuale. Se vuoi instradare tutto il traffico in uscita nella rete virtuale, applica l'impostazione dell'app WEBSITE_VNET_ROUTE_ALL all'app. Per configurare l'impostazione dell'app:

1. Passare all'interfaccia utente di configurazione nel portale dell'app. Selezionare **Nuova impostazione applicazione**
1. Digitare **WEBSITE_VNET_ROUTE_ALL** nel campo Nome e **1** nel campo Valore

   ![Fornire l'impostazione dell'applicazioneProvide application setting][4]

1. Seleziona **OK**.
1. Selezionare **Salva**

Se si instrada tutto il traffico in uscita nella rete virtuale, sarà soggetto ai gruppi di sicurezza di rete e alle duR applicate alla subnet di integrazione. Quando instradare tutto il traffico in uscita nella rete virtuale, gli indirizzi in uscita saranno ancora gli indirizzi in uscita elencati nelle proprietà dell'app, a meno che non si forniscano route per inviare il traffico altrove. 

Esistono alcune limitazioni con l'utilizzo dell'integrazione della rete virtuale con reti virtuali nella stessa area:There are some limitations with using VNet Integration with VNets in the same region:

* Non è possibile raggiungere le risorse tra le connessioni peering globali
* La funzionalità è disponibile solo dalle unità di scala del servizio app più recenti che supportano i piani di servizio app PremiumV2.The feature is only available from newer App Service scale units that support PremiumV2 App Service plans.
* La subnet di integrazione può essere usata solo da un solo piano di servizio app
* La funzionalità non può essere utilizzata dalle app del piano isolato che si trovano in un ambiente del servizio app
* La funzionalità richiede una subnet inutilizzata che è un /27 con 32 indirizzi o più grandi in una rete virtuale di Resource Manager
* L'app e la rete virtuale devono essere nella stessa area
* Non è possibile eliminare una rete virtuale con un'app integrata. Rimuovere l'integrazione prima di eliminare la rete virtuale. 
* È possibile integrare solo con le reti virtuali nello stesso abbonamento dell'app
* È possibile avere una sola integrazione della rete virtuale regionale per ogni piano di servizio app. Più app nello stesso piano di servizio app possono usare la stessa rete virtuale. 
* Non è possibile modificare la sottoscrizione di un'app o di un piano mentre è presente un'app che utilizza l'integrazione della rete virtuale regionaleYou cannot change the subscription of an app or a plan while there is an app that is using Regional VNet Integration

Viene utilizzato un indirizzo per ogni istanza del piano. Se si ridimensiona l'app a cinque istanze, vengono usati cinque indirizzi. Poiché le dimensioni della subnet non possono essere modificate dopo l'assegnazione, è necessario usare una subnet sufficientemente grande da contenere la scalabilità che l'app può raggiungere. Un /26 con 64 indirizzi è la dimensione consigliata. Un /26 con 64 indirizzi ospiterà un piano Premium con 30 istanze. Quando si aumenta o si aumenta la scalabilità di un piano, è necessario il doppio degli indirizzi per un breve periodo di tempo. 

Se si vuole che le app in un altro piano raggiungano una rete virtuale già connessa da app in un altro piano, è necessario selezionare una subnet diversa da quella usata dall'integrazione di rete virtuale preesistente.  

La funzionalità è in anteprima per Linux.The feature is in preview for Linux. Il formato Linux della funzionalità supporta solo chiamate a indirizzi RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web--function-app-for-containers"></a>Web / App funzione per i contenitori

Se si ospita l'app in Linux con le immagini incorporate, l'integrazione regionale della rete virtuale funziona senza ulteriori modifiche. Se si utilizza Web / App funzione per i contenitori, è necessario modificare l'immagine docker per utilizzare l'integrazione della rete virtuale. Nell'immagine docker, utilizzare la variabile di ambiente PORT come porta di ascolto del server Web principale, anziché un numero di porta hardcoded. La variabile di ambiente PORT viene impostata automaticamente dalla piattaforma al momento dell'avvio del contenitore. Se si utilizza SSH, il daemon SSH deve essere configurato per l'ascolto sul numero di porta specificato dalla variabile di ambiente SSH_PORT quando si utilizza l'integrazione della rete virtuale regionale.  Non è disponibile alcun supporto per l'integrazione della rete virtuale richiesta dal gateway in Linux.There is no support for gateway required VNet Integration on Linux. 

### <a name="service-endpoints"></a>Endpoint servizio

L'integrazione regionale della rete virtuale consente di utilizzare gli endpoint del servizio.  Per usare gli endpoint del servizio con l'app, usare l'integrazione della rete virtuale regionale per connettersi a una rete virtuale selezionata e quindi configurare gli endpoint del servizio nella subnet usata per l'integrazione. 

### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

I gruppi di sicurezza di rete consentono di bloccare il traffico in ingresso e in uscita verso le risorse in una rete virtuale.Network Security Groups enable you to block inbound and outbound traffic to resources in a VNet. Un'app che usa l'integrazione della rete virtuale regionale può usare il gruppo di sicurezza di rete per bloccare il traffico in uscita verso le risorse nella rete virtuale o in Internet.An app using regional VNet Integration can use [Network Security Group][VNETnsg] to block outbound traffic to resources in your VNet or the internet. Per bloccare il traffico verso indirizzi pubblici, è necessario che l'impostazione dell'applicazione WEBSITE_VNET_ROUTE_ALL impostata su 1.To block traffic to public addresses, you must have the application setting WEBSITE_VNET_ROUTE_ALL set to 1. Le regole in ingresso in un gruppo di sicurezza di rete non si applicano all'app poiché l'integrazione della rete virtuale influisce solo sul traffico in uscita dall'app. Per controllare il traffico in ingresso verso l'app, usa la funzionalità Restrizioni di accesso. Un gruppo di sicurezza di rete applicato alla subnet di integrazione sarà attivo indipendentemente dalle route applicate alla subnet di integrazione. Se WEBSITE_VNET_ROUTE_ALL è stato impostato su 1 e non sono presenti route che influiscono sul traffico degli indirizzi pubblici nella subnet di integrazione, tutto il traffico in uscita sarà comunque soggetto ai gruppi di sicurezza di rete assegnati alla subnet di integrazione. Se non WEBSITE_VNET_ROUTE_ALL non è stato impostato, i gruppi di sicurezza di sicurezza di sicurezza di sicurezza verranno applicati solo al traffico RFC1918.

### <a name="routes"></a>Route

Le tabelle di route consentono di instradare il traffico in uscita dall'app a qualsiasi punto. Per impostazione predefinita, le tabelle di route influiranno solo sul traffico di destinazione RFC1918.  Se si imposta WEBSITE_VNET_ROUTE_ALL su 1, tutte le chiamate in uscita saranno interessate. Le route impostate nella subnet di integrazione non influiranno sulle risposte alle richieste di app in ingresso. Le destinazioni comuni possono includere dispositivi firewall o gateway. Se si vuole instradare tutto il traffico in uscita in locale, è possibile usare una tabella di route per inviare tutto il traffico in uscita al gateway ExpressRoute.If you want to route all outbound traffic on-premises, you can use a route table to send all outbound traffic to your ExpressRoute gateway. Se si instrada il traffico a un gateway, assicurarsi di impostare le route nella rete esterna per inviare le risposte.

Anche le route BGP (Border Gateway Protocol) influiranno sul traffico dell'app. Se si dispone di route BGP da un'operazione simile a un gateway ExpressRoute, il traffico in uscita dell'app ne risentirà. Per impostazione predefinita, le route BGP influiscono solo sul traffico di destinazione RFC1918. Se WEBSITE_VNET_ROUTE_ALL è impostato su 1, tutto il traffico in uscita può essere influenzato dalle route BGP. 


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/