---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: 963f0698b921caa413c61059ad69284c41b4f265
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999441"
---
L'uso dell'integrazione VNet a livello di area consente all'app di accedere a:

* Risorse in un VNet nella stessa area dell'app.
* Risorse in reti virtuali con peering al VNet con cui è integrata l'app.
* Servizi protetti dell'endpoint di servizio.
* Risorse tra le connessioni Azure ExpressRoute.
* Risorse nella VNet con cui si è integrato.
* Risorse tra connessioni con peering, incluse le connessioni di Azure ExpressRoute.
* Endpoint privati 

Quando si usa l'integrazione di VNet con reti virtuali nella stessa area, è possibile usare le funzionalità di rete di Azure seguenti:

* **Gruppi di sicurezza di rete (gruppi)**: è possibile bloccare il traffico in uscita con una NSG posizionata nella subnet di integrazione. Le regole in ingresso non si applicano perché non è possibile usare l'integrazione VNet per fornire l'accesso in ingresso all'app.
* **Tabelle di route (UDR)**: è possibile inserire una tabella di route nella subnet di integrazione per inviare il traffico in uscita laddove si vuole.

Per impostazione predefinita, l'app instrada solo il traffico RFC1918 in VNet. Se si vuole instradare tutto il traffico in uscita nella VNet, applicare l'impostazione dell'app WEBSITE_VNET_ROUTE_ALL all'app. Per configurare l'impostazione dell'app:

1. Passare all'interfaccia utente di **configurazione** nel portale dell'app. Selezionare **Nuova impostazione applicazione**.
1. Immettere **WEBSITE_VNET_ROUTE_ALL** nella casella **nome** e immettere **1** nella casella **valore** .

   ![Specificare l'impostazione dell'applicazione][4]

1. Selezionare **OK**.
1. Selezionare **Salva**.

> [!NOTE]
> Se si instrada tutto il traffico in uscita nel VNet, è soggetto a gruppi e UdR applicati alla subnet di integrazione. Quando si esegue il routing di tutto il traffico in uscita nella VNet, gli indirizzi in uscita sono ancora gli indirizzi in uscita elencati nelle proprietà dell'app, a meno che non si forniscano route per inviare il traffico altrove.

Esistono alcune limitazioni all'uso dell'integrazione di VNet con reti virtuali nella stessa area:

* Non è possibile raggiungere risorse tra connessioni di peering globali.
* La funzionalità è disponibile in tutte le unità di scala del servizio app in Premium v2 e Premium V3. È anche disponibile in standard ma solo da unità di scala del servizio app più recenti. Se si usa un'unità di scala precedente, è possibile usare la funzionalità solo da un piano di servizio App Premium v2. Se si vuole essere certi di poter usare la funzionalità in un piano di servizio app standard, creare l'app in un piano di servizio App Premium V3. Tali piani sono supportati solo nelle unità di scala più recenti. Se lo si desidera, è possibile ridurre le prestazioni.  
* La subnet di integrazione può essere usata da un solo piano di servizio app.
* La funzionalità non può essere usata da app del piano isolato che si trovano in un ambiente del servizio app.
* Per la funzionalità è necessaria una subnet inutilizzata/28 o superiore in un Azure Resource Manager VNet.
* L'app e il VNet devono trovarsi nella stessa area.
* Non è possibile eliminare un VNet con un'app integrata. Rimuovere l'integrazione prima di eliminare il VNet.
* È possibile eseguire l'integrazione solo con reti virtuali nella stessa sottoscrizione dell'app.
* È possibile avere una sola integrazione VNet a livello di area per ogni piano di servizio app. Più app nello stesso piano di servizio app possono usare lo stesso VNet.
* Non è possibile modificare la sottoscrizione di un'app o di un piano mentre è presente un'app che usa l'integrazione VNet a livello di area.
* L'app non può risolvere gli indirizzi in Zone private di DNS di Azure senza modifiche alla configurazione

L'integrazione di VNet dipende dall'uso di una subnet dedicata.  Quando si esegue il provisioning di una subnet, la subnet di Azure perde 5 IP per dall'inizio. Un indirizzo viene usato dalla subnet di integrazione per ogni istanza del piano. Se si ridimensiona l'app a quattro istanze, vengono usati quattro indirizzi. Il debito di 5 indirizzi dalla dimensione della subnet significa che il numero massimo di indirizzi disponibili per ogni blocco CIDR è il seguente:

- /28 ha 11 indirizzi
- /27 ha 27 indirizzi
- /26 ha 59 indirizzi

Se si aumentano o diminuiscono le dimensioni, è necessario il doppio degli indirizzi necessari per un breve periodo di tempo. I limiti di dimensione indicano che le dimensioni effettive supportate per ogni subnet sono, se la subnet è:

- /28, la scala orizzontale massima è 5 istanze
- /27, la scala orizzontale massima è 13 istanze
- /26, la scala orizzontale massima è 29 istanze

I limiti indicati sulla scala orizzontale massima presuppongono che sia necessario aumentare o ridurre le dimensioni o lo SKU in un determinato momento. 

Poiché le dimensioni della subnet non possono essere modificate dopo l'assegnazione, usare una subnet sufficientemente grande da contenere la scala che l'app può raggiungere. Per evitare problemi con la capacità della subnet, le dimensioni consigliate sono pari a/26 con 64 indirizzi.  

Se si vuole che le app in un altro piano raggiungano un VNet già connesso da app in un altro piano, selezionare una subnet diversa da quella usata dall'integrazione VNet preesistente.

La funzionalità è completamente supportata per le app Windows e Linux, inclusi i [contenitori personalizzati](../articles/app-service/quickstart-custom-container.md). Tutti i comportamenti funzionano allo stesso modo tra app di Windows e app Linux.

### <a name="service-endpoints"></a>Endpoint di servizio

L'integrazione VNet a livello di area consente di usare gli endpoint di servizio. Per usare gli endpoint di servizio con l'app, usare l'integrazione VNet a livello di area per connettersi a un VNet selezionato e quindi configurare gli endpoint di servizio con il servizio di destinazione nella subnet usata per l'integrazione. Se si desidera accedere a un servizio tramite gli endpoint del servizio:

1. configurare l'integrazione di VNet a livello di area con l'app Web
1. passare al servizio di destinazione e configurare gli endpoint di servizio nella subnet usata per l'integrazione

### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

È possibile usare i gruppi di sicurezza di rete per bloccare il traffico in ingresso e in uscita verso le risorse in una VNet. Un'app che usa l'integrazione VNet a livello di area può usare un [gruppo di sicurezza di rete][VNETnsg] per bloccare il traffico in uscita verso le risorse in VNet o Internet. Per bloccare il traffico verso indirizzi pubblici, è necessario che l'impostazione dell'applicazione WEBSITE_VNET_ROUTE_ALL impostata su 1. Le regole in ingresso in un NSG non si applicano all'app perché l'integrazione con VNet influiscono solo sul traffico in uscita dall'app.

Per controllare il traffico in ingresso verso l'app, usare la funzionalità restrizioni di accesso. Un NSG applicato alla subnet di integrazione è attivo indipendentemente dalle route applicate alla subnet di integrazione. Se WEBSITE_VNET_ROUTE_ALL è impostato su 1 e non sono presenti route che influiscono sul traffico degli indirizzi pubblici sulla subnet di integrazione, tutto il traffico in uscita è ancora soggetto a gruppi assegnati alla subnet di integrazione. Se WEBSITE_VNET_ROUTE_ALL non è impostato, gruppi viene applicato solo al traffico RFC1918.

### <a name="routes"></a>Route

È possibile usare le tabelle di route per instradare il traffico in uscita dall'app a qualsiasi posizione. Per impostazione predefinita, le tabelle di route influiscono solo sul traffico di destinazione RFC1918. Se si imposta WEBSITE_VNET_ROUTE_ALL su 1, verranno interessate tutte le chiamate in uscita. Le route impostate nella subnet di integrazione non influiranno sulle risposte alle richieste di app in ingresso. Le destinazioni comuni possono includere dispositivi firewall o gateway.

Se si vuole instradare tutto il traffico in uscita in locale, è possibile usare una tabella di route per inviare tutto il traffico in uscita al gateway ExpressRoute. Se si esegue il routing del traffico a un gateway, assicurarsi di impostare le route nella rete esterna per restituire le risposte.

Le route Border Gateway Protocol (BGP) influiscono anche sul traffico dell'app. Se si hanno Route BGP da un gateway ExpressRoute, il traffico in uscita dell'app sarà interessato. Per impostazione predefinita, le route BGP hanno effetto solo sul traffico di destinazione RFC1918. Se WEBSITE_VNET_ROUTE_ALL è impostato su 1, tutto il traffico in uscita può essere influenzato dalle route BGP.

### <a name="azure-dns-private-zones"></a>Zone private di DNS di Azure 

Quando l'app si integra con la VNet, USA lo stesso server DNS con cui è configurata la VNet. È possibile eseguire l'override di questo comportamento nell'app configurando l'impostazione dell'app WEBSITE_DNS_SERVER con l'indirizzo del server DNS desiderato. Se è stato configurato un server DNS personalizzato con il VNet ma si vuole che l'app usi zone private di DNS di Azure, è necessario impostare WEBSITE_DNS_SERVER con il valore 168.63.129.16. 

### <a name="private-endpoints"></a>Endpoint privati

Se si desidera effettuare chiamate a [endpoint privati][privateendpoints], è necessario assicurarsi che le ricerche DNS vengano risolte nell'endpoint privato. Per assicurarsi che le ricerche DNS dall'app puntino agli endpoint privati, è possibile:

* integrazione con Zone private di DNS di Azure. Se il VNet non dispone di un server DNS personalizzato, questo sarà automatico
* gestire l'endpoint privato nel server DNS usato dall'app. A tale scopo, è necessario individuare l'indirizzo dell'endpoint privato e quindi puntare l'endpoint che si sta provando a raggiungere tale indirizzo con un record A.
* configurare il server DNS in modo che si inoltri alle zone private di DNS di Azure

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md