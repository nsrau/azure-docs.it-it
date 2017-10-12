---
title: Dettagli di configurazione di rete per l'uso di Express Route
description: Dettagli di configurazione di rete per eseguire ambienti del servizio app in reti virtuali connesse a un circuito ExpressRoute.
services: app-service
documentationcenter: 
author: stefsch
manager: nirma
editor: 
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.openlocfilehash: bb3e283e8a9327a9c66c8d8ded037cee5195ffc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>Dettagli della configurazione di rete per gli ambienti del servizio app con ExpressRoute
## <a name="overview"></a>Panoramica
I clienti possono connettere un circuito [Azure ExpressRoute][ExpressRoute] all'infrastruttura di rete virtuale per estendere la rete locale ad Azure.  Un ambiente del servizio app può essere creato in una subnet di questa infrastruttura di [rete virtuale][virtualnetwork].  Le app in esecuzione nell'ambiente del servizio app possono quindi stabilire connessioni sicure a risorse back-end accessibili solo tramite la connessione ExpressRoute.  

Un ambiente del servizio app può essere creato in una rete virtuale di Azure Resource Manager ******o** in una rete virtuale del modello di distribuzione classica.  Con una modifica recente apportata a giugno 2016, gli ambienti del servizio app possono essere distribuiti nelle reti virtuali che usano intervalli di indirizzi pubblici o spazi di indirizzi RFC1918, ovvero indirizzi privati. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Requisiti della connettività di rete
Esistono requisiti di connettività di rete per gli ambienti del servizio app che potrebbero non essere inizialmente soddisfatti in una rete virtuale connessa a ExpressRoute.  I requisiti per il corretto funzionamento degli ambienti del servizio app sono i seguenti:

* Connettività di rete in uscita per endpoint di archiviazione di Azure su entrambe le porte, 80 e 443.  Sono inclusi gli endpoint che si trovano nella stessa area dell'ambiente del servizio app, nonché gli endpoint di archiviazione che si trovano in **altre** aree di Azure.  Gli endpoint di archiviazione di Azure vengono risolti nei seguenti domini DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* e *file.core.windows.net*.  
* Connettività di rete in uscita verso il servizio File di Azure sulla porta 445.
* Connettività di rete in uscita agli endpoint Sql DB che si trovano nella stessa area dell'ambiente del servizio app.  Gli endpoint del database SQL vengono risolti nel dominio seguente: *database.windows.net*.  Ciò richiede l'apertura dell'accesso alle porte 1433, 11000-11999 e 14000-14999.  Per altri dettagli, vedere [questo articolo sull'uso delle porte per il database SQL versione 12](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
* Connettività di rete in uscita verso gli endpoint del piano di gestione di Azure (sia gli endpoint ASM che quelli ARM).  È inclusa la connettività in uscita verso *management.core.windows.net* e *management.azure.com*. 
* Connettività di rete in uscita verso *ocsp.msocsp.com*, *mscrl.microsoft.com* e *crl.microsoft.com*.  È necessario per supportare la funzionalità SSL.
* La configurazione DNS per la rete virtuale deve essere in grado di risolvere tutti gli endpoint e i domini indicati nei punti precedenti.  Se questi endpoint non possono essere risolti, il tentativo di creazione dell’ambiente del servizio App avrà esito negativo, e gli ambienti del servizio App esistenti verranno contrassegnati come non integri.
* L'accesso in uscita sulla porta 53 è necessario per la comunicazione con i server DNS.
* Se è presente un server DNS personalizzato nell’altra estremità di un gateway VPN, il server DNS deve essere raggiungibile dalla subnet che contiene l’ambiente di servizio app. 
* Il percorso di rete in uscita non può attraversare i proxy aziendali interni, né può essere sottoposto a tunneling forzato all’ambiente locale.  In questo modo viene modificato l'indirizzo NAT effettivo del traffico di rete in uscita dall'ambiente di servizio app.  La modifica dell'indirizzo NAT del traffico di rete in uscita di un ambiente del servizio app provocherà errori di connettività a molti degli endpoint sopra elencati.  Ciò comporta dei tentativi non riusciti nella creazione dell’ambiente di servizio app, così come il fatto che ambienti di servizio app che prima erano integri vengano contrassegnati come non integri.  
* L'accesso di rete in ingresso alle porte necessarie per gli ambienti del servizio app deve essere consentito come spiegato in questo [articolo][requiredports].

I requisiti DNS possono essere soddisfatti garantendo che un'infrastruttura DNS valida venga configurata e mantenuta per la rete virtuale.  Se per qualsiasi motivo viene modificata la configurazione DNS dopo aver creato un ambiente di servizio app, gli sviluppatori possono forzare un ambiente di servizio app per selezionare la nuova configurazione del DNS.  L'attivazione di un riavvio di ambiente in sequenza con l'icona "Riavvia" disponibile nella parte superiore del pannello di gestione dell'ambiente del servizio app nel [portale di Azure][NewPortal] farà sì che l'ambiente selezioni la nuova configurazione del DNS.

Per soddisfare i requisiti di accesso alla rete in ingresso, configurare un [gruppo di sicurezza di rete][NetworkSecurityGroups] nella subnet dell'ambiente del servizio app per consentire l'accesso necessario, come spiegato in questo [articolo][requiredports].

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>Abilitazione della connettività di rete in uscita per un ambiente del servizio app
Per impostazione predefinita, un circuito ExpressRoute appena creato annuncia una route predefinita che consente la connettività Internet in uscita.  Con questa configurazione, un ambiente del servizio app sarà in grado di connettersi ad altri endpoint di Azure.

Tuttavia, secondo una diffusa configurazione, i clienti definiscono la propria route predefinita (0.0.0.0/0) verso la quale viene forzato il traffico Internet in uscita invece di far passare il flusso localmente.  Questo flusso di traffico interrompe sempre gli ambienti del servizio app perché il traffico in uscita è bloccato in locale o convertito tramite NAT in un set non riconoscibile di indirizzi che non usano più i diversi endpoint di Azure.

La soluzione consiste nel definire una o più route definite dall'utente (UDR, User Defined Route) nella subnet contenente l'ambiente del servizio app.  Una route UDR definisce le route specifiche della subnet che verranno accettate invece della route predefinita.

Se possibile, è consigliabile utilizzare la seguente configurazione:

* La configurazione di ExpressRoute annuncia 0.0.0.0/0 e per impostazione predefinita esegue il tunneling forzato di tutto il traffico in uscita in un ambiente locale.
* L’UDR applicato alla subnet contenente l'ambiente di servizio app definisce 0.0.0.0/0 con un tipo di hop successivo di Internet (è possibile trovare un esempio più avanti in questo articolo).

L'effetto combinato di questi passaggi è che il livello di subnet UDR avrà la precedenza sul tunneling forzato di ExpressRoute, garantendo l'accesso a Internet in uscita dall'ambiente di servizio app.

> [!IMPORTANT]
> Le route definite in una route UDR **devono** essere sufficientemente specifiche da avere la precedenza su qualsiasi route annunciata dalla configurazione di ExpressRoute.  Nell'esempio di seguito viene utilizzato l'intervallo di indirizzi ampio 0.0.0.0/0 e pertanto può essere accidentalmente sottoposto a override dagli annunci di route mediante più intervalli di indirizzi specifici.
> 
> Gli ambienti del servizio app non sono supportati con le configurazioni di ExpressRoute con **annuncio incrociato di route dal percorso di peering pubblico al percorso di peering privato**.  Le configurazioni di ExpressRoute che dispongono di peering pubblico configurato, riceveranno gli annunci di route da Microsoft per un elevato numero di intervalli di indirizzi IP di Microsoft Azure.  In caso di annuncio incrociato di questi intervalli di indirizzi nel percorso di peering privato, il risultato finale è che tutti i pacchetti di rete in uscita dalla subnet dell'ambiente di servizio app servizio verranno sottoposti a tunneling forzato verso un'infrastruttura di rete locale del cliente.  Questo flusso di rete non è attualmente supportato con ambienti del servizio app.  Una soluzione a questo problema consiste nell'interrompere l'annuncio incrociato di route dal percorso di peering pubblico al percorso di peering privato.
> 
> 

Le informazioni generali sulle route definite dall'utente sono disponibili in questa [panoramica][UDROverview].  

Le informazioni dettagliate sulla creazione e la configurazione di route definite dall'utente sono disponibili in questa [guida alle procedure][UDRHowTo].

## <a name="example-udr-configuration-for-an-app-service-environment"></a>Esempio di configurazione UDR per un ambiente del servizio app
**Prerequisiti**

1. Installare Azure PowerShell dalla [pagina di download di Azure][AzureDownloads] (con data giugno 2015 o successiva).  In "Strumenti da riga di comando" è presente un collegamento "Installa" in "Windows Powershell" che installerà i cmdlet di Powershell più recenti.
2. È consigliabile creare una subnet univoca da usare esclusivamente in un ambiente del servizio app.  In questo modo le route UDR applicate alla subnet apriranno solo il traffico in uscita per l'ambiente del servizio app.
3. **Importante**: distribuire l'ambiente del servizio app solo **dopo** aver eseguito questi passaggi di configurazione.  Questo assicura che la connettività di rete in uscita sia disponibile prima di tentare di distribuire un ambiente del servizio app.

**Passaggio 1: Creare una tabella di route denominata**

Il frammento seguente crea una tabella di route denominata "DirectInternetRouteTable" nell'area di Azure Stati Uniti occidentali:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Passaggio 2: Creare una o più route nella tabella di route**

Sarà necessario aggiungere una o più route alla tabella di route per consentire l'accesso Internet in uscita.  

L'approccio consigliato per la configurazione dell'accesso in uscita a Internet consiste nel definire una route per 0.0.0.0/0, come illustrato di seguito.

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

Tenere presente che 0.0.0.0/0 è un intervallo di indirizzi ampio e verrà quindi sottoposto a override da intervalli di indirizzi più specifici annunciati da ExpressRoute.  Per reiterare il consiglio precedente, un UDR con una route 0.0.0.0/0 deve essere utilizzata insieme a una configurazione ExressRoute che allo stesso modo annuncia solo 0.0.0.0/0. 

In alternativa, è possibile scaricare un elenco completo e aggiornato di intervalli CIDR utilizzato da Azure.  Il file XML contenente tutti gli intervalli di indirizzi IP di Azure è disponibile nell'[Area download Microsoft][DownloadCenterAddressRanges].  

Si noti tuttavia che questi intervalli cambiano nel tempo ed è quindi è necessario eseguire aggiornamenti manuali periodici delle route definite dall'utente per mantenere la sincronizzazione.  A causa del limite massimo predefinito di 100 route in una singola route UDR, inoltre, sarà necessario "riepilogare" gli intervalli di indirizzi IP di Azure per rientrare nel limite di 100 route, tenendo presente che le route definite come UDR devono essere più specifiche rispetto alle route annunciate da ExpressRoute.  

**Passaggio 3: Associare la tabella di route alla subnet contenente l'ambiente del servizio app**

Nell'ultimo passaggio della configurazione si associa la tabella di route alla subnet in cui verrà distribuito l'ambiente del servizio app.  Il comando seguente associa "DirectInternetRouteTable" ad "ASESubnet" che alla fine conterrà un ambiente del servizio app.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Passaggio 4: Procedura finale**

Una volta associata la tabella di route alla subnet, si consiglia di testare e verificare l'effetto desiderato.  Ad esempio, distribuire una macchina virtuale nella subnet e verificare che:

* Il traffico in uscita per endpoint di Azure e non indicato in precedenza in questo articolo **non** è indirizzato verso il basso nel circuito ExpressRoute.  È molto importante verificare questo comportamento, poiché se il traffico in uscita dalla subnet è comunque sottoposto a tunneling forzato in un ambiente locale, la creazione dell’ambiente di servizio app avrà sempre esito negativo. 
* Le ricerche DNS per gli endpoint indicati in precedenza hanno tutte esito positivo. 

Una volta che i passaggi sopra riportati vengono confermati, è necessario eliminare la macchina virtuale perché la subnet deve essere "vuota" al momento della creazione dell’ambiente di servizio app.

Procedere quindi con la creazione di un ambiente di servizio app.

## <a name="getting-started"></a>introduttiva
Per iniziare a usare gli ambienti del servizio app, vedere [Introduzione all'ambiente del servizio app][IntroToAppServiceEnvironment]

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[AzureDownloads]: http://azure.microsoft.com/en-us/downloads/ 
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[NewPortal]:  https://portal.azure.com


<!-- IMAGES -->
