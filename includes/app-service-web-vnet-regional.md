---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: c731e2a7d4da1a66aabb50b335d526fbb6a0a302
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671481"
---
L'uso dell'integrazione VNet a livello di area consente all'app di accedere a:

* risorse in VNet nella stessa area integrata con 
* risorse in reti virtuali con peering al VNet che si trovano nella stessa area
* Servizi protetti endpoint di servizio
* risorse tra connessioni ExpressRoute
* risorse nella VNet a cui si è connessi
* risorse tra connessioni con peering, incluse le connessioni ExpressRoute
* Endpoint privati 

Quando si usa l'integrazione di VNet con reti virtuali nella stessa area, è possibile usare le funzionalità di rete di Azure seguenti:

* Gruppi di sicurezza di rete (gruppi): è possibile bloccare il traffico in uscita con un gruppo di sicurezza di rete che si trova nella subnet di integrazione. Le regole in ingresso non si applicano perché non è possibile usare l'integrazione VNet per fornire l'accesso in ingresso all'app.
* Tabelle di route (UDR): è possibile inserire una tabella di route nella subnet di integrazione per inviare il traffico in uscita laddove si vuole. 

Per impostazione predefinita, l'app indirizza il traffico RFC1918 solo nella VNet. Se si vuole instradare tutto il traffico in uscita nella VNet, applicare l'impostazione dell'app WEBSITE_VNET_ROUTE_ALL all'app. Per configurare l'impostazione dell'app:

1. Passare all'interfaccia utente di configurazione nel portale dell'app. Selezionare l' **impostazione nuova applicazione**
1. Digitare **WEBSITE_VNET_ROUTE_ALL** nel campo nome e **1** nel campo valore

   ![Specificare l'impostazione dell'applicazione][4]

1. Selezionare **OK**.
1. Selezionare **Salva**

Se si instrada tutto il traffico in uscita nel VNet, sarà soggetto a gruppi e UdR applicati alla subnet di integrazione. Quando si esegue il routing di tutto il traffico in uscita nella VNet, gli indirizzi in uscita saranno ancora gli indirizzi in uscita elencati nelle proprietà dell'app, a meno che non si forniscano route per inviare il traffico altrove. 

Esistono alcune limitazioni all'uso dell'integrazione di VNet con reti virtuali nella stessa area:

* Non è possibile raggiungere risorse tra connessioni di peering globali
* La funzionalità è disponibile solo dalle più recenti unità di scala del servizio app che supportano i piani di servizio app PremiumV2.
* La subnet di integrazione può essere usata solo da un solo piano di servizio app
* Non è possibile usare la funzionalità da app del piano isolato che si trovano in un ambiente del servizio app
* Per la funzionalità è necessaria una subnet inutilizzata/27 con indirizzi 32 o più grandi in una Gestione risorse VNet
* L'app e la rete virtuale devono essere nella stessa area
* Non è possibile eliminare una rete virtuale con un'app integrata. Rimuovere l'integrazione prima di eliminare il VNet. 
* È possibile eseguire l'integrazione solo con reti virtuali nella stessa sottoscrizione dell'app
* È possibile avere una sola integrazione VNet a livello di area per ogni piano di servizio app. Più app nello stesso piano di servizio app possono usare lo stesso VNet. 
* Non è possibile modificare la sottoscrizione di un'app o di un piano mentre è presente un'app che usa l'integrazione VNet a livello di area

Per ogni istanza del piano viene usato un indirizzo. Se si ridimensiona l'app a cinque istanze, vengono usati cinque indirizzi. Poiché non è possibile modificare le dimensioni della subnet dopo l'assegnazione, è necessario usare una subnet sufficientemente grande da contenere la scala che l'app può raggiungere. Le dimensioni consigliate sono le dimensioni consigliate per/26 con indirizzi 64. Un piano Premium a/26 con 64 includerà 30 istanze. Quando si ridimensiona un piano verso l'alto o verso il basso, sono necessari due volte il numero di indirizzi per un breve periodo di tempo. 

Se si vuole che le app in un altro piano raggiungano una VNet connessa già da app in un altro piano, è necessario selezionare una subnet diversa da quella usata dall'integrazione VNet preesistente.  

La funzionalità è in anteprima per Linux. Il formato Linux della funzionalità supporta solo l'esecuzione di chiamate a indirizzi RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web--function-app-for-containers"></a>Web/app per le funzioni per i contenitori

Se l'app viene ospitata in Linux con le immagini predefinite, l'integrazione VNet a livello di area funziona senza modifiche aggiuntive. Se si usa web/app per le funzioni per i contenitori, è necessario modificare l'immagine Docker per poter usare l'integrazione VNet. Nell'immagine Docker usare la variabile di ambiente PORT come porta di ascolto del server Web principale, anziché usare un numero di porta hardcoded. La variabile di ambiente PORT viene impostata automaticamente dalla piattaforma al momento dell'avvio del contenitore. Se si usa SSH, il daemon SSH deve essere configurato in modo da restare in ascolto sul numero di porta specificato dalla variabile di ambiente SSH_PORT quando si usa l'integrazione VNet a livello di area.  Non è disponibile alcun supporto per l'integrazione VNet necessaria per il gateway in Linux. 

### <a name="service-endpoints"></a>Endpoint servizio

L'integrazione VNet a livello di area consente di usare gli endpoint di servizio.  Per usare gli endpoint di servizio con l'app, usare l'integrazione VNet a livello di area per connettersi a un VNet selezionato e quindi configurare gli endpoint di servizio nella subnet usata per l'integrazione. 

### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

I gruppi di sicurezza di rete consentono di bloccare il traffico in ingresso e in uscita verso le risorse in una VNet. Un'app che usa l'integrazione VNet a livello di area può usare il [gruppo di sicurezza di rete][VNETnsg] per bloccare il traffico in uscita verso le risorse in VNet o Internet. Per bloccare il traffico verso indirizzi pubblici, è necessario che l'impostazione dell'applicazione WEBSITE_VNET_ROUTE_ALL impostata su 1. Le regole in ingresso in una NSG non si applicano all'app perché l'integrazione con VNet influiscono solo sul traffico in uscita dall'app. Per controllare il traffico in ingresso verso l'app, usare la funzionalità restrizioni di accesso. Un NSG applicato alla subnet di integrazione sarà attivo indipendentemente dalle route applicate alla subnet di integrazione. Se WEBSITE_VNET_ROUTE_ALL è stato impostato su 1 e non sono presenti route che influiscono sul traffico degli indirizzi pubblici sulla subnet di integrazione, tutto il traffico in uscita sarà ancora soggetto a gruppi assegnati alla subnet di integrazione. Se WEBSITE_VNET_ROUTE_ALL non è stato impostato, gruppi verrà applicato solo al traffico RFC1918.

### <a name="routes"></a>Route

Le tabelle di route consentono di instradare il traffico in uscita dall'app a qualsiasi posizione. Per impostazione predefinita, le tabelle di route avranno effetto solo sul traffico di destinazione RFC1918.  Se si imposta WEBSITE_VNET_ROUTE_ALL su 1, verranno interessate tutte le chiamate in uscita. Le route impostate nella subnet di integrazione non avranno effetto sulle risposte alle richieste di app in ingresso. Le destinazioni comuni possono includere dispositivi firewall o gateway. Se si vuole instradare tutto il traffico in uscita in locale, è possibile usare una tabella di route per inviare tutto il traffico in uscita al gateway ExpressRoute. Se si esegue il routing del traffico a un gateway, assicurarsi di impostare le route nella rete esterna per restituire le risposte.

Le route Border Gateway Protocol (BGP) influiscono anche sul traffico dell'app. Se si hanno Route BGP da un gateway ExpressRoute, il traffico in uscita dell'app sarà interessato. Per impostazione predefinita, le route BGP avranno effetto solo sul traffico di destinazione RFC1918. Se WEBSITE_VNET_ROUTE_ALL è impostato su 1, tutto il traffico in uscita può essere influenzato dalle route BGP. 


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/