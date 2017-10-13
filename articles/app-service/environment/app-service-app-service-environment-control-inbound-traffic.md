---
title: Come controllare il traffico in ingresso a un ambiente del servizio app
description: Informazioni su come configurare le regole di sicurezza di rete per controllare il traffico in ingresso a un ambiente del servizio app.
services: app-service
documentationcenter: 
author: ccompy
manager: erikre
editor: 
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.openlocfilehash: ed72bf3202d6cb2d2161bc0df693d3e6a1fc58ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Come controllare il traffico in ingresso a un ambiente del servizio app
## <a name="overview"></a>Panoramica
Un ambiente del servizio app può essere creato **o** in una rete virtuale di Azure Resource Manager **o** in una [rete virtuale][virtualnetwork] del modello di distribuzione classica.  È possibile definire una nuova rete virtuale e una nuova subnet al momento della creazione di un ambiente del servizio app.  In alternativa, è possibile creare un ambiente del servizio app in una rete virtuale e in una subnet preesistenti.  Con una modifica apportata a giugno 2016, gli ambienti del servizio app possono essere distribuiti nelle reti virtuali che usano intervalli di indirizzi pubblici o spazi di indirizzi RFC1918 (ovvero indirizzi privati).  Per altre informazioni su come creare un ambiente del servizio app, vedere [Come creare un ambiente del servizio app][HowToCreateAnAppServiceEnvironment].

È sempre necessario creare un ambiente del servizio app all'interno di una subnet perché la subnet fornisce un limite di rete che può essere usato per bloccare il traffico in ingresso proveniente da dispositivi e servizi upstream, in modo che il traffico HTTP e HTTPS sia accettato solo da indirizzi IP upstream specifici.

Il traffico in ingresso e in uscita diretto verso e proveniente da una subnet è controllato tramite un [gruppo di sicurezza di rete][NetworkSecurityGroups]. Per controllare il traffico in ingresso è necessario creare regole di sicurezza di rete in un gruppo di sicurezza di rete, quindi assegnare al gruppo di sicurezza di rete la subnet contenente l'ambiente del servizio app.

Dopo aver assegnato un gruppo di sicurezza di rete a una subnet, il traffico in ingresso alle app nell'ambiente del servizio app è consentito/bloccato in base alle regole di autorizzazione consentita o negata definite nel gruppo di sicurezza di rete.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Porte di rete in ingresso usate in un ambiente del servizio app
Prima di bloccare il traffico di rete in ingresso tramite un gruppo di sicurezza di rete, è importante conoscere quali sono le porte di rete obbligatorie e facoltative usate da un ambiente del servizio app.  Il blocco accidentale del traffico ad alcune porte può comportare una perdita di funzionalità nell'ambiente del servizio app.

Di seguito è riportato un elenco delle porte usate da un ambiente del servizio app. Tutte le porte sono di tipo **TCP**, se non indicato diversamente in modo chiaro:

* 454: **porta obbligatoria** usata dall'infrastruttura di Azure per la gestione e la manutenzione degli ambienti del servizio app tramite SSL.  Non bloccare il traffico indirizzato a questa porta.  Questa porta è sempre associata all'indirizzo VIP pubblico di un ambiente del servizio app.
* 455: **porta obbligatoria** usata dall'infrastruttura di Azure per la gestione e la manutenzione degli ambienti del servizio app tramite SSL.  Non bloccare il traffico indirizzato a questa porta.  Questa porta è sempre associata all'indirizzo VIP pubblico di un ambiente del servizio app.
* 80: porta predefinita per il traffico HTTP in ingresso alle app in esecuzione nei piani di servizio app in un ambiente del servizio app.  In un ambiente del servizio app abilitato al bilanciamento del carico interno, questa porta è associata all'indirizzo ILB dell'ambiente.
* 443: porta predefinita per il traffico SSL in ingresso alle app in esecuzione nei piani del servizio app in un ambiente del servizio app.  In un ambiente del servizio app abilitato al bilanciamento del carico interno, questa porta è associata all'indirizzo ILB dell'ambiente.
* 21: canale di controllo per il servizio FTP.  Questa porta può essere bloccata, se non si usa un servizio FTP.  In un ambiente del servizio app abilitato al bilanciamento del carico interno, questa porta è associata all'indirizzo ILB per un ambiente.
* 990: canale di controllo per il servizio FTPS.  Questa porta può essere bloccata, se non si usa un servizio FTPS.  In un ambiente del servizio app abilitato al bilanciamento del carico interno, questa porta è associata all'indirizzo ILB per un ambiente.
* 10001-10020: canali di dati per il servizio FTP.  Come per il canale di controllo, queste porte possono essere bloccate se non si usa il servizio FTP.  In un ambiente del servizio app abilitato al bilanciamento del carico interno, questa porta può essere associata all'indirizzo ILB dell'ambiente.
* 4016: porta usata per il debug remoto con Visual Studio 2012.  Questa porta può essere bloccata, se non si usa questa funzionalità.  In un ambiente del servizio app abilitato al bilanciamento del carico interno, questa porta è associata all'indirizzo ILB dell'ambiente.
* 4018: porta usata per il debug remoto con Visual Studio 2013.  Questa porta può essere bloccata, se non si usa questa funzionalità.  In un ambiente del servizio app abilitato al bilanciamento del carico interno, questa porta è associata all'indirizzo ILB dell'ambiente.
* 4020: porta usata per il debug remoto con Visual Studio 2015.  Questa porta può essere bloccata, se non si usa questa funzionalità.  In un ambiente del servizio app abilitato al bilanciamento del carico interno, questa porta è associata all'indirizzo ILB dell'ambiente.

## <a name="outbound-connectivity-and-dns-requirements"></a>Requisiti per DNS e connettività in uscita
Per un corretto funzionamento dell'ambiente del servizio app, è necessario l'accesso in uscita ai vari endpoint. Un elenco completo degli endpoint esterni usati da un ambiente del servizio app è disponibile nella sezione "Requisiti della connettività di rete" dell'articolo [Configurazione di rete per ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Gli ambienti del servizio app richiedono un'infrastruttura DNS valida configurata per la rete virtuale.  Se per qualsiasi motivo viene modificata la configurazione DNS dopo aver creato un ambiente di servizio app, gli sviluppatori possono forzare un ambiente di servizio app per selezionare la nuova configurazione del DNS.  L'attivazione di un riavvio di ambiente in sequenza con l'icona "Riavvia" disponibile nella parte superiore del pannello di gestione dell'ambiente del servizio app nel [portale di Azure][NewPortal] farà sì che l'ambiente selezioni la nuova configurazione del DNS.

È anche consigliabile che i server DNS personalizzati nella rete virtuale vengano configurati prima di creare un ambiente del servizio app.  Se la configurazione DNS della rete virtuale viene modificata durante la creazione di un ambiente del servizio app, il processo di creazione dell'ambiente del servizio app avrà esito negativo.  In modo analogo, se esiste un server DNS personalizzato nell’altra estremità di un gateway VPN e il server DNS è irraggiungibile o non disponibile, anche il processo di creazione dell’ambiente del servizio App avrà esito negativo.

## <a name="creating-a-network-security-group"></a>Creazione di un gruppo di sicurezza di rete
Per i dettagli sul funzionamento dei gruppi di sicurezza di rete, vedere le [informazioni][NetworkSecurityGroups] seguenti.  Di seguito sono riportate le informazioni principali relative ai gruppi di sicurezza di rete, con particolare attenzione alla configurazione e all'applicazione di un gruppo di sicurezza di rete a una subnet contenente un ambiente del servizio app.

**Nota:** i gruppi di sicurezza di rete possono essere configurati in modalità grafica con il [portale di Azure](https://portal.azure.com) oppure tramite Azure PowerShell.

I gruppi di sicurezza di rete vengono inizialmente creati come un'entità autonoma associata a una sottoscrizione. Poiché i gruppi di sicurezza di rete vengono creati in un'area di Azure, assicurarsi di creare il gruppo di sicurezza di rete nella stessa area dell'ambiente del servizio app.

Di seguito è illustrata la procedura di creazione di un gruppo di sicurezza di rete:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Dopo aver creato il gruppo di sicurezza di rete, vengono aggiunte una o più regole di sicurezza di rete.  Poiché il set di regole può variare nel tempo, è consigliabile lasciare spazi vuoti nello schema di numerazione usato per le priorità delle regole al fine di agevolare l'inserimento di regole aggiuntive in futuro.

L'esempio seguente illustra una regola che concede l'accesso in modo esplicito alle porte necessarie per la gestione e la manutenzione dell'ambiente del servizio app da parte dell'infrastruttura di Azure.  Si noti che tutto il traffico di gestione transita attraverso il protocollo SSL ed è protetto tramite certificati client, quindi anche se le porte sono aperte risultano inaccessibili a entità diverse dall'infrastruttura di gestione di Azure.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Quando si blocca l'accesso alle porte 80 e 443 per "nascondere" un ambiente del servizio app oltre i dispositivi o servizi upstream, è necessario conoscere l'indirizzo IP upstream.  Se, ad esempio, si un firewall per applicazioni Web, il firewall disporrà di uno o più indirizzi IP che userà per l'inoltro del traffico a un ambiente del servizio app downstream.  Sarà necessario usare questo indirizzo IP nel parametro *SourceAddressPrefix* di una regola di sicurezza di rete.

Nell'esempio seguente, il traffico in ingresso da un indirizzo IP upstream specifico è consentito in modo esplicito.  L'indirizzo *1.2.3.4* è usato come segnaposto per l'indirizzo IP di un firewall per applicazioni Web upstream.  Modificare il valore in modo che corrisponda all'indirizzo usato dal dispositivo o dal servizio upstream corrente.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Se si desidera usufruire del supporto per FTP, è possibile usare le regole seguenti come un modello per concedere l'accesso alla porta di controllo FTP e alle porte dei canali di dati.  Poiché FTP è un protocollo con stato, potrebbe non essere possibile instradare il traffico FTP attraverso un dispositivo proxy o un firewall HTTP/HTTPS tradizionale.  In questo caso sarà necessario impostare *SourceAddressPrefix* su un valore diverso, ad esempio l'intervallo di indirizzi IP per i computer dedicati allo sviluppo o alla distribuzione in cui sono in esecuzione i client FTP. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Nota:** l'intervallo delle porte dei canali di dati per FTP potrebbero variare durante il periodo di anteprima del servizio.

Se si usa la funzionalità di debug remoto con Visual Studio, usare le regole seguenti per concedere l'accesso.  Esiste una regola separata per ogni versione supportata di Visual Studio, dal momento che ogni versione usa una porta diversa per il debug remoto.  Come per l'accesso FTP, il traffico di debug remoto potrebbe non transitare correttamente attraverso un dispositivo proxy o un firewall per applicazioni Web tradizionale.  È quindi possibile impostare *SourceAddressPrefix* sull'intervallo di indirizzi IP dei computer di sviluppo che eseguono Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Assegnazione di un gruppo di sicurezza di rete a una subnet
Un gruppo di sicurezza di rete include una regola di sicurezza predefinita che nega l'accesso a tutto il traffico esterno.  La combinazione delle regole di sicurezza di rete descritte in precedenza e della regola di sicurezza predefinita che blocca il traffico in ingresso determina che solo il traffico proveniente da intervalli di indirizzi di origine associati a un'azione *Consenti* potranno inviare il traffico alle app in esecuzione in un ambiente del servizio app.

Quando un gruppo di sicurezza di rete è stato popolato con le regole di sicurezza, deve essere assegnato alla subnet contenente l'ambiente del servizio app.  Il comando di assegnazione fa riferimento sia al nome della rete virtuale in cui risiede l'ambiente del servizio app, sia al nome della subnet in cui è stato creato l'ambiente.  

L'esempio seguente illustra l'assegnazione di un gruppo di sicurezza di rete a una subnet e a una rete virtuale:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Dopo l'assegnazione del gruppo di sicurezza di rete (si tratta di un'operazione con esecuzione prolungata che potrebbe richiedere alcuni minuti), solo il traffico in ingresso che corrisponde alle regole *Consenti* potrà raggiungere le app nell'ambiente del servizio app.

Per completezza, di seguito viene mostrato un esempio di come rimuovere e quindi dissociare il gruppo di sicurezza di rete dalla subnet:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Considerazioni speciali su IP SSL esplicito
Se un'app è configurata con un indirizzo IP SSL esplicito (applicabile *solo* agli ambienti del servizio app con indirizzo VIP pubblico) anziché usare l'indirizzo IP predefinito dell'ambiente del servizio app, il traffico HTTP e HTTPS transita nella subnet tramite un set di porte diverse dalle porte 80 e 443.

La singola coppia di porte usata da ogni indirizzo IP SSL è disponibile nell'interfaccia utente del portale nel pannello relativo all'esperienza utente con i dettagli dell'ambiente del servizio app.  Selezionare "Tutte le impostazioni" --> "Indirizzi IP".  Il pannello "Indirizzi IP" mostra una tabella di tutti gli indirizzi IP SSL configurati in modo esplicito per l'ambiente del servizio app, con la coppia speciale di porte che consente di instradare il traffico HTTP e HTTPS associato a ogni indirizzo IP-SSL.  Questa è la coppia di porte che deve essere utilizzata per i parametri DestinationPortRange quando si configurano le regole in un gruppo di sicurezza di rete.

Quando un'app in un ambiente del servizio app è configurata in modo da usare l'indirizzo IP SSL, ai clienti esterni non verrà visualizzato il mapping della coppia di porte e non dovranno preoccuparsi di questo aspetto.  Il traffico verso le app transiterà normalmente all'indirizzo IP SSL configurato.  La conversione a una coppia di porte specifica avviene internamente in modo automatico durante l'ultima parte del routing del traffico nella subnet contenente l'ambiente del servizio app. 

## <a name="getting-started"></a>Introduzione
Per iniziare a usare gli ambienti del servizio app, vedere [Introduzione all'ambiente del servizio app][IntroToAppServiceEnvironment]

Per informazioni dettagliate sulle app che si connettono in modo sicuro alla risorsa back-end da un ambiente del servizio app, vedere [Connessione sicura alle risorse back-end da un ambiente del servizio app][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

