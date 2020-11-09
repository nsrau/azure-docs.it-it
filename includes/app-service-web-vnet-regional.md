---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 06/08/2020
ms.author: ccompy
ms.openlocfilehash: 14b9d9fe0eb9dfe2f25373c2d87d9b4af15dd0d9
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371712"
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

* **Gruppi di sicurezza di rete (gruppi)** : è possibile bloccare il traffico in uscita con una NSG posizionata nella subnet di integrazione. Le regole in ingresso non si applicano perché non è possibile usare l'integrazione VNet per fornire l'accesso in ingresso all'app.
* **Tabelle di route (UDR)** : è possibile inserire una tabella di route nella subnet di integrazione per inviare il traffico in uscita laddove si vuole.

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
* La funzionalità è disponibile solo dalle più recenti app Azure unità di scala del servizio che supportano i piani di servizio app PremiumV2. Si noti che *questo non significa che l'app deve essere eseguita in un piano tariffario PremiumV2* , ma solo che deve essere eseguita in un piano di servizio app in cui è disponibile l'opzione PremiumV2 (che indica che si tratta di un'unità di scala più recente in cui è disponibile anche questa funzionalità di integrazione VNet).
* La subnet di integrazione può essere usata da un solo piano di servizio app.
* La funzionalità non può essere usata da app del piano isolato che si trovano in un ambiente del servizio app.
* La funzionalità richiede una subnet inutilizzata a/27 con indirizzi 32 o più grandi in un Azure Resource Manager VNet.
* L'app e il VNet devono trovarsi nella stessa area.
* Non è possibile eliminare un VNet con un'app integrata. Rimuovere l'integrazione prima di eliminare il VNet.
* È possibile eseguire l'integrazione solo con reti virtuali nella stessa sottoscrizione dell'app.
* È possibile avere una sola integrazione VNet a livello di area per ogni piano di servizio app. Più app nello stesso piano di servizio app possono usare lo stesso VNet.
* Non è possibile modificare la sottoscrizione di un'app o di un piano mentre è presente un'app che usa l'integrazione VNet a livello di area.
* L'app non può risolvere gli indirizzi in Zone private di DNS di Azure senza modifiche alla configurazione

Per ogni istanza del piano viene usato un indirizzo. Se si ridimensiona l'app a cinque istanze, vengono usati cinque indirizzi. Poiché le dimensioni della subnet non possono essere modificate dopo l'assegnazione, è necessario usare una subnet sufficientemente grande da contenere la scala che l'app può raggiungere. Le dimensioni consigliate sono le dimensioni consigliate per/26 con indirizzi 64. A/26 con 64 indirizzi di un piano Premium con 30 istanze. Quando si ridimensiona un piano verso l'alto o verso il basso, sono necessari due volte il numero di indirizzi per un breve periodo di tempo.

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

Quando l'app si integra con la VNet, USA lo stesso server DNS con cui è configurata la VNet. Per impostazione predefinita, l'app non funzionerà con Zone private di DNS di Azure. Per usare Zone private di DNS di Azure, è necessario aggiungere le impostazioni dell'app seguenti:

1. WEBSITE_DNS_SERVER con valore 168.63.129.16
1. WEBSITE_VNET_ROUTE_ALL con valore 1

Queste impostazioni invieranno tutte le chiamate in uscita dall'app in VNet. Consente inoltre all'app di usare il servizio DNS di Azure eseguendo una query sulla zona DNS privato a livello di lavoro. Questa funzionalità deve essere usata quando un'app in esecuzione accede a una zona DNS privato.

> [!NOTE]
>Il tentativo di aggiungere un dominio personalizzato a un'app Web con DNS privato zona non è possibile con l'integrazione rete virtuale. La convalida del dominio personalizzato viene eseguita a livello di controller, non a livello di ruolo di lavoro, che impedisce la visualizzazione dei record DNS. Per usare un dominio personalizzato da un'area di DNS privato, è necessario ignorare la convalida usando un gateway applicazione o un ambiente del servizio app ILB.



### <a name="private-endpoints"></a>Endpoint privati

Se si desidera effettuare chiamate a [endpoint privati][privateendpoints], è necessario integrare con zone private di DNS di Azure o gestire l'endpoint privato nel server DNS usato dall'app. 

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
