<properties 
	pageTitle="Panoramica dell'architettura di rete degli ambienti del servizio app" 
	description="Panoramica dell'architettura della topologia di rete degli ambienti del servizio app." 
	services="app-service\web" 
	documentationCenter="" 
	authors="stefsch" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/06/2015" 
	ms.author="stefsch"/>

# Panoramica dell'architettura di rete degli ambienti del servizio app

## Introduzione ##
Gli ambienti del servizio app vengono sempre creati in una subnet di una [rete virtuale][virtualnetwork]. Le app in esecuzione in un ambiente del servizio app possono comunicare con gli endpoint privati che fanno parte della stessa topologia di rete virtuale. Poiché i clienti possono bloccare alcune parti dell'infrastruttura di rete virtuale, è importante conoscere i tipi di flusso delle comunicazioni di rete che avvengono con un ambiente del servizio app.

## Flusso di rete generale ##
 
Un ambiente del servizio app ha sempre un indirizzo IP virtuale (VIP) pubblico. Tutto il traffico in ingresso arriva su tale indirizzo VIP pubblico, incluso il traffico HTTP e HTTPS per le app, oltre al rimanente traffico per l'FTP, la funzionalità di debug remoto e le operazioni di gestione di Azure. Per un elenco completo delle porte specifiche (sia obbligatorie che facoltative) disponibili nell'indirizzo VIP pubblico, vedere l'articolo sul [controllo del traffico in uscita][controllinginboundtraffic] verso un ambiente del servizio app.

Il diagramma seguente mostra una panoramica dei vari flussi di rete in ingresso e in uscita:

![Flussi di rete generali][GeneralNetworkFlows]

Un ambiente del servizio app può comunicare con svariati endpoint di clienti privati. Ad esempio, le app in esecuzione nell'ambiente del servizio app possono connettersi a uno o più server di database in esecuzione su macchine virtuali IaaS nella stessa topologia di rete virtuale.

Gli ambienti del servizio app comunicano anche con le risorse database SQL e di archiviazione di Azure necessarie per la gestione e il funzionamento di un ambiente del servizio app. Alcune risorse SQL e di archiviazione con cui comunica un ambiente del servizio app si trovano nella stessa area dell'ambiente del servizio app, mentre altre si trovano in aree remote di Azure. Di conseguenza, la connettività in uscita a Internet è sempre necessaria per il corretto funzionamento di un ambiente del servizio app.

Poiché un ambiente del servizio app viene distribuito in una subnet, si possono usare gruppi di sicurezza di rete per controllare il traffico in ingresso alla subnet. Per informazioni dettagliate su come controllare il traffico in ingresso a un ambiente del servizio app, vedere l'[articolo][controllinginboundtraffic] seguente.

Per informazioni dettagliate su come consentire la connettività Internet in uscita da un ambiente del servizio app, vedere il seguente articolo sull'uso di [Express Route][ExpressRoute]. Lo stesso approccio descritto nell'articolo si applica quando si usano la connettività da sito a sito e il tunneling forzato.

## Indirizzi di rete in uscita ##
Quando un ambiente del servizio app effettua chiamate in uscita, un indirizzo IP è sempre associato alle chiamate in uscita. Lo specifico indirizzo IP usato dipende dal fatto che l'endpoint da chiamare si trovi all'interno o all'esterno della topologia di rete virtuale.

Se l'endpoint da chiamare è all'**esterno** della topologia di rete virtuale, l'indirizzo in uscita (detto anche indirizzo NAT in uscita) usato è l'indirizzo VIP pubblico dell'ambiente del servizio app. Questo indirizzo si trova nell'interfaccia utente del portale per l'ambiente del servizio app (nota: esperienza utente in sospeso).

Questo indirizzo può essere determinato anche creando un'app nell'ambiente del servizio app e quindi eseguendo *nslookup* nell'indirizzo dell'app. L'indirizzo IP risultante è sia l'indirizzo VIP pubblico che l'indirizzo NAT in uscita dell'ambiente del servizio app.

Se l'endpoint da chiamare è all'**interno** della topologia di rete virtuale, l'indirizzo in uscita dell'app chiamante sarà l'indirizzo IP interno della singola risorsa di calcolo che esegue l'app. Tuttavia non esiste un mapping persistente degli indirizzi IP interni di rete virtuale alle app. Le app possono spostarsi in risorse di calcolo diverse e il pool di risorse di calcolo disponibili in un ambiente del servizio app può essere modificato in seguito a operazioni di ridimensionamento.

Tuttavia, poiché un ambiente del servizio app si trova sempre all'interno di una subnet, è certo che l'indirizzo IP interno di una risorsa di calcolo che esegue un'app sarà sempre compreso nell'intervallo CIDR della subnet. Di conseguenza, quando vengono usati gruppi di sicurezza di rete o ACL con granularità fine per proteggere l'accesso ad altri endpoint all'interno della rete virtuale, è necessario concedere l'accesso all'intervallo della subnet contenente l'ambiente del servizio app.

Il diagramma seguente illustra questi concetti in modo più dettagliato:

![Indirizzi di rete in uscita][OutboundNetworkAddresses]

Nel diagramma precedente:

- Poiché l'indirizzo VIP pubblico dell'ambiente del servizio app è 192.23.1.2, questo sarà l'indirizzo IP in uscita usato quando si effettuano chiamate agli endpoint "Internet".
- L'intervallo CIDR della subnet contenitore per l'ambiente del servizio app è 10.0.1.0/26. Gli altri endpoint all'interno della stessa infrastruttura di rete virtuale visualizzeranno le chiamate dalle app come provenienti da un punto compreso in questo intervallo di indirizzi.

## Chiamate tra gli ambienti del servizio app ##
Se si distribuiscono più ambienti del servizio app nella stessa rete virtuale e si effettuano chiamate in uscita da un ambiente del servizio app a un altro ambiente del servizio app, può verificarsi uno scenario più complesso. Anche questi tipi di chiamate tra ambienti del servizio app saranno considerati come chiamate "Internet".

Esempio di uso dell'ambiente del servizio app precedente con l'indirizzo IP in uscita 192.23.1.2: se un'applicazione in esecuzione nell'ambiente del servizio app effettua una chiamata in uscita a un'app in esecuzione in un secondo ambiente del servizio app che si trova nella stessa rete virtuale, le chiamate in uscita che arrivano al secondo ambiente del servizio app sembreranno provenienti dall'indirizzo 192.23.1.2 (vale a dire non dall'intervallo di indirizzi della subnet del primo ambiente del servizio app).

Anche se le chiamate tra ambienti del servizio app diversi sono considerate come chiamate "Internet", quando entrambi gli ambienti del servizio app si trovano nella stessa area Azure, il traffico di rete rimarrà nella rete dell'area di Azure e non transiterà fisicamente attraverso la rete Internet pubblica. Di conseguenza è possibile usare un gruppo di sicurezza di rete nella subnet del secondo ambiente del servizio app per consentire solo chiamate in ingresso da 192.23.1.2, garantendo comunicazioni sicure tra gli ambienti del servizio app.

## Informazioni e collegamenti aggiuntivi ##
Informazioni dettagliate sulle porte in ingresso usate dagli ambienti del servizio app e sull'uso di gruppi di sicurezza di rete per controllare il traffico in ingresso sono disponibili [qui][controllinginboundtraffic].

Informazioni dettagliate sull'uso di route definite dall'utente per concedere l'accesso Internet in uscita agli ambienti del servizio app sono disponibili in questo [articolo][ExpressRoute].


<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png

<!---HONumber=July15_HO5-->