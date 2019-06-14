---
title: Dettagli della configurazione di rete per Azure ExpressRoute - Servizio app
description: Dettagli della configurazione di rete per l'ambiente del servizio app per PowerApps in reti virtuali connesse a un circuito Azure ExpressRoute.
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: e0fa87facec73efdfff1a9908dcba92838215425
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130671"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Dettagli della configurazione di rete per l'ambiente del servizio app per PowerApps con Azure ExpressRoute

I clienti possono connettere un circuito [Azure ExpressRoute][ExpressRoute] alla propria infrastruttura di rete virtuale per estendere la rete locale ad Azure. L'ambiente del servizio app viene creato in una subnet dell'infrastruttura di [rete virtuale][virtualnetwork]. Le app in esecuzione nell'ambiente del servizio app stabiliscono connessioni sicure alle risorse back-end accessibili solo tramite la connessione ExpressRoute.  

È possibile creare un ambiente del servizio app negli scenari seguenti:
- Reti virtuali di Azure Resource Manager.
- Reti virtuali con il modello di distribuzione classica.
- Reti virtuali che usano gli intervalli di indirizzi pubblici o gli spazi di indirizzi RFC1918, ovvero gli indirizzi privati. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Requisiti della connettività di rete

L'ambiente del servizio app ha requisiti di connettività di rete che potrebbero non essere inizialmente soddisfatti in una rete virtuale connessa a ExpressRoute.

Per funzionare correttamente, l'ambiente del servizio app richiede le impostazioni di connettività di rete seguenti:

* Connettività di rete in uscita verso endpoint di Archiviazione di Azure in tutto il mondo su entrambe le porte, 80 e 443. Questi endpoint si trovano nella stessa area dell'ambiente del servizio app e anche in altre aree di Azure. Gli endpoint di Archiviazione di Azure vengono risolti nei domini DNS seguenti: table.core.windows.net, blob.core.windows.net, queue.core.windows.net e file.core.windows.net.  

* Connettività di rete in uscita verso il servizio File di Azure sulla porta 445.

* Connettività di rete in uscita verso gli endpoint del database SQL di Azure che si trovano nella stessa area dell'ambiente del servizio app. Gli endpoint del database SQL vengono risolti nel dominio database.windows.net, che richiede l'accesso completo alle porte 1433, 11000-11999 e 14000-14999. Per informazioni dettagliate sull'uso delle porte della versione 12 del database SQL, vedere [Porte successive alla 1433 per ADO.NET 4.5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

* Connettività di rete in uscita verso gli endpoint del piano di gestione di Azure (endpoint del modello di distribuzione classica di Azure e di Azure Resource Manager). La connettività verso questi endpoint include i domini management.core.windows.net e management.azure.com. 

* Connettività di rete in uscita verso i domini ocsp.msocsp.com, mscrl.microsoft.com e crl.microsoft.com. La connettività a questi domini è necessaria per il supporto della funzionalità SSL.

* La configurazione del DNS per la rete virtuale deve essere in grado di risolvere tutti gli endpoint e i domini indicati in questo articolo. Se gli endpoint non possono essere risolti, l'ambiente del servizio app non viene creato. Gli eventuali ambienti del servizio app esistenti vengono contrassegnati come non integri.

* L'accesso in uscita sulla porta 53 è necessario per la comunicazione con i server DNS.

* Se è presente un server DNS personalizzato all'altra estremità di un gateway VPN, il server DNS deve essere raggiungibile dalla subnet che contiene l'ambiente del servizio app. 

* Il percorso di rete in uscita non può attraversare proxy aziendali interni né può essere sottoposto a tunneling forzato in locale. Queste azioni modificano l'indirizzo NAT effettivo del traffico di rete in uscita dall'ambiente del servizio app. Le modifiche all'indirizzo NAT del traffico di rete in uscita dell'ambiente del servizio app provocano errori di connettività per molti degli endpoint. L'ambiente del servizio app non viene creato. Gli eventuali ambienti del servizio app esistenti vengono contrassegnati come non integri.

* È necessario che sia consentito l'accesso di rete in ingresso alle porte necessarie per l'ambiente del servizio app. Per informazioni, vedere [Come controllare il traffico in ingresso a un ambiente del servizio app][requiredports].

Per soddisfare i requisiti del DNS, verificare che per la rete virtuale sia configurata e gestita un'infrastruttura DNS valida. Se la configurazione del DNS viene modificata dopo la creazione dell'ambiente del servizio app, gli sviluppatori possono forzare la selezione automatica della nuova configurazione. È possibile attivare un riavvio dell'ambiente in sequenza usando l'icona **Riavvia** nell'area di gestione dell'ambiente del servizio app nel [portale di Azure][NewPortal]. In seguito al riavvio, l'ambiente selezionerà automaticamente la nuova configurazione del DNS.

Per soddisfare i requisiti di accesso di rete in ingresso, configurare un [gruppo di sicurezza di rete (NSG)][NetworkSecurityGroups] nella subnet dell'ambiente del servizio app. Il gruppo di sicurezza di rete consente l'accesso necessario [per controllare il traffico in ingresso all'ambiente del servizio app][requiredports].

## <a name="outbound-network-connectivity"></a>Connettività di rete in uscita

Per impostazione predefinita, un circuito ExpressRoute appena creato annuncia una route predefinita che consente la connettività Internet in uscita. L'ambiente del servizio app può usare questa configurazione per connettersi ad altri endpoint di Azure.

In una configurazione diffusa, i clienti definiscono la propria route predefinita (0.0.0.0/0), forzando così il passaggio del flusso del traffico Internet in uscita attraverso la rete locale. Questo flusso di traffico ha invariabilmente l'effetto di interrompere l'ambiente del servizio app. Il traffico in uscita viene bloccato in locale o convertito tramite NAT in un set non riconoscibile di indirizzi che non funzionano più con i diversi endpoint di Azure.

Per risolvere questo problema, impostare una o più route definite dall'utente (UDR, User Defined Route) nella subnet che contiene l'ambiente del servizio app. Una route definita dall'utente specifica le route della subnet che vengono accettate in sostituzione di quella predefinita.

Se possibile, usare la configurazione seguente:

* La configurazione di ExpressRoute annuncia 0.0.0.0/0 e, per impostazione predefinita, forza il tunneling di tutto il traffico in uscita nella rete locale.
* La route definita dall'utente applicata alla subnet contenente l'ambiente del servizio app definisce 0.0.0.0/0 con un tipo di hop successivo di Internet. Un esempio di questa configurazione è descritto più avanti in questo articolo.

Per effetto di questa configurazione, la route definita dall'utente a livello di subnet ha la precedenza sul tunneling forzato di ExpressRoute, garantendo così l'accesso a Internet in uscita dall'ambiente del servizio app.

> [!IMPORTANT]
> Le route specificate in una route definita dall'utente devono essere sufficientemente specifiche da avere la precedenza su qualsiasi route annunciata dalla configurazione di ExpressRoute. L'esempio descritto in questa sezione usa l'intervallo di indirizzi ampio 0.0.0.0/0. Su questo intervallo possono avere accidentalmente la precedenza gli annunci di route che usano intervalli di indirizzi più specifici.
> 
> L'ambiente del servizio app non è supportato nel caso di configurazioni di ExpressRoute con annuncio incrociato di route dal percorso di peering pubblico al percorso di peering privato. Le configurazioni di ExpressRoute per cui è configurato il peering pubblico ricevono gli annunci delle route da Microsoft per un elevato numero di intervalli di indirizzi IP di Microsoft Azure. In caso di annuncio incrociato degli intervalli di indirizzi nel percorso di peering privato, tutti i pacchetti di rete in uscita dalla subnet dell'ambiente del servizio app vengono sottoposti a tunneling forzato verso un'infrastruttura di rete locale del cliente. Questo flusso di rete non è attualmente supportato con l'ambiente del servizio app. Una soluzione consiste nell'interrompere l'annuncio incrociato di route dal percorso di peering pubblico al percorso di peering privato.
> 
> 

Per informazioni generali sulle route definite dall'utente, vedere [Routing del traffico di rete virtuale][UDROverview].  

Per informazioni su come creare e configurare route definite dall'utente, vedere [Indirizzare il traffico di rete con una tabella di route usando PowerShell][UDRHowTo].

## <a name="udr-configuration"></a>Configurazione di route definita dall'utente

Questa sezione mostra un esempio di configurazione di route definita dall'utente per l'ambiente del servizio app.

### <a name="prerequisites"></a>Prerequisiti

* Installare Azure PowerShell dalla [pagina Download di Azure][AzureDownloads]. Scegliere un download con la data del mese di giugno 2015 o una data successiva. In **Strumenti da riga di comando** > **Windows PowerShell**, selezionare **Installa** per installare i cmdlet di PowerShell più recenti.

* Creare una subnet univoca da usare esclusivamente con l'ambiente del servizio app. In questo modo, le route definite dall'utente applicate alla subnet aprono solo il traffico in uscita per l'ambiente del servizio app.

> [!IMPORTANT]
> Distribuire l'ambiente del servizio app solo dopo aver completato la procedura di configurazione. Questi passaggi consentono di assicurarsi che la connettività di rete in uscita sia disponibile prima di tentare di distribuire l'ambiente del servizio app.

### <a name="step-1-create-a-route-table"></a>Passaggio 1: Creare una tabella di route

Creare una tabella di route denominata **DirectInternetRouteTable** nell'area Stati Uniti occidentali di Azure, come mostrato in questo frammento di codice:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>Passaggio 2: Creare route nella tabella

Aggiungere route alla tabella per abilitare l'accesso in uscita a Internet.  

Configurare l'accesso in uscita a Internet. Definire una route per 0.0.0.0/0 come mostrato in questo frammento di codice:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 è un intervallo di indirizzi ampio. L'intervallo viene sostituito da intervalli di indirizzi più specifici annunciati da ExpressRoute. È consigliabile usare una route definita dall'utente con una route 0.0.0.0/0 insieme a una configurazione di ExpressRoute che annuncia solo 0.0.0.0/0. 

In alternativa, scaricare un elenco completo e aggiornato di intervalli CIDR usati da Azure. Il file XML per tutti gli intervalli di indirizzi IP di Azure è disponibile nell'[Area download Microsoft][DownloadCenterAddressRanges].  

> [!NOTE]
>
> Gli intervalli di indirizzi IP di Azure cambiano nel tempo. Le route definite dall'utente richiedono aggiornamenti manuali periodici per rimanere sincronizzate.
>
> Una singola route definita dall'utente ha un limite massimo predefinito di 100 route. È necessario compattare gli intervalli di indirizzi IP di Azure in modo da rientrare nel limite di 100 route. Le route definite dall'utente devono essere più specifiche rispetto a quelle annunciate dalla connessione ExpressRoute.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>Passaggio 3: Associare la tabella alla subnet

Associare la tabella di route alla subnet in cui si intende distribuire l'ambiente del servizio app. Questo comando associa la tabella **DirectInternetRouteTable** alla subnet **ASESubnet** che conterrà l'ambiente del servizio app.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>Passaggio 4: Testare e confermare la route

Dopo aver associato la tabella di route alla subnet, testare e confermare la route.

Distribuire una macchina virtuale nella subnet e verificare queste condizioni:

* Il traffico in uscita verso gli endpoint di Azure e non di Azure descritti in questo articolo **non** è indirizzato verso il circuito ExpressRoute. Se il traffico in uscita dalla subnet è sottoposto a tunneling forzato in locale, la creazione dell'ambiente del servizio app ha sempre esito negativo.
* Le ricerche DNS per gli endpoint descritti in questo articolo hanno tutte esito positivo. 

Dopo aver completato la procedura di configurazione e confermato la route, eliminare la macchina virtuale. Quando viene creato l'ambiente del servizio app, la subnet deve essere "vuota".

A questo punto è tutto pronto per distribuire l'ambiente del servizio app.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare l'ambiente del servizio app per PowerApps, vedere [Introduzione all'ambiente del servizio app][IntroToAppServiceEnvironment].

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[UDROverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/ 
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell 
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->
