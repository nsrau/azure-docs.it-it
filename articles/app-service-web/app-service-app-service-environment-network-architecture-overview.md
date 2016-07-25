<properties 
	pageTitle="Panoramica dell'architettura di rete degli ambienti del servizio app" 
	description="Panoramica dell'architettura della topologia di rete degli ambienti del servizio app." 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2016" 
	ms.author="stefsch"/>

# Panoramica dell'architettura di rete degli ambienti del servizio app

## Introduzione ##
Gli ambienti del servizio app vengono sempre creati in una subnet di una [rete virtuale][virtualnetwork]. Le app in esecuzione in un ambiente del servizio app possono comunicare con gli endpoint privati che fanno parte della stessa topologia di rete virtuale. Poiché i clienti possono bloccare alcune parti dell'infrastruttura di rete virtuale, è importante conoscere i tipi di flusso delle comunicazioni di rete che avvengono con un ambiente del servizio app.

## Flusso di rete generale ##
 
Quando un ambiente del servizio app usa un indirizzo IP virtuale pubblico (VIP) per le app, tutto il traffico in ingresso viene ricevuto su questo indirizzo VIP pubblico. Sono inclusi il traffico HTTP e HTTPS per le app, altro traffico per l'FTP, la funzionalità di debug remoto e le operazioni di gestione di Azure. Per un elenco completo delle porte specifiche (sia obbligatorie che facoltative) disponibili nell'indirizzo VIP pubblico, vedere l'articolo sul [controllo del traffico in uscita][controllinginboundtraffic] verso un ambiente del servizio app.

Gli ambienti del servizio app supportano anche le applicazioni in esecuzione associate solo a un indirizzo interno di rete virtuale, noto anche come indirizzo ILB (bilanciamento del carico interno). In un ambiente del servizio app abilitato al bilanciamento del carico interno, il traffico HTTP e HTTPS per le app e le chiamate di debug remoto, vengono ricevuti all'indirizzo ILB. Per le configurazioni dell'ambiente di servizio app con bilanciamento del carico interno più comuni, anche il traffico FTP/FTPS verrà ricevuto all'indirizzo ILB. Le operazioni di gestione di Azure useranno tuttavia le porte 454/455 all'indirizzo VIP pubblico di un ambiente del servizio app abilitato per il bilanciamento del carico interno.

Il diagramma seguente mostra una panoramica dei vari flussi di rete in ingresso e in uscita per un ambiente del servizio app in cui le app sono associate a un indirizzo IP virtuale pubblico:

![Flussi di rete generali][GeneralNetworkFlows]

Un ambiente del servizio app può comunicare con svariati endpoint di clienti privati. Ad esempio, le app in esecuzione nell'ambiente del servizio app possono connettersi a uno o più server di database in esecuzione su macchine virtuali IaaS nella stessa topologia di rete virtuale.

>[AZURE.IMPORTANT] Se si esamina il diagramma di rete è possibile osservare che le "altre risorse di calcolo" vengono distribuite in una subnet diversa dall'ambiente del servizio app. La distribuzione delle risorse nella stessa subnet con l'ambiente del servizio app bloccherà la connettività da questo ambiente a tali risorse, ad eccezione di routing specifico all'interno dell'ambiente del servizio app. Distribuire in una subnet diversa, nella stessa rete virtuale. L'ambiente del servizio app sarà quindi in grado di connettersi. Non è necessaria alcuna configurazione aggiuntiva.

Gli ambienti del servizio app comunicano anche con le risorse database SQL e di archiviazione di Azure necessarie per la gestione e il funzionamento di un ambiente del servizio app. Alcune risorse SQL e di archiviazione con cui comunica un ambiente del servizio app si trovano nella stessa area dell'ambiente del servizio app, mentre altre si trovano in aree remote di Azure. Di conseguenza, la connettività in uscita a Internet è sempre necessaria per il corretto funzionamento di un ambiente del servizio app.

Poiché un ambiente del servizio app viene distribuito in una subnet, si possono usare gruppi di sicurezza di rete per controllare il traffico in ingresso alla subnet. Per informazioni dettagliate su come controllare il traffico in ingresso a un ambiente del servizio app, vedere l'[articolo][controllinginboundtraffic] seguente.

Per informazioni dettagliate su come consentire la connettività Internet in uscita da un ambiente del servizio app, vedere il seguente articolo sull'uso di [Express Route][ExpressRoute]. Lo stesso approccio descritto nell'articolo si applica quando si usano la connettività da sito a sito e il tunneling forzato.

## Indirizzi di rete in uscita ##
Quando un ambiente del servizio app esegue chiamate in uscita, un indirizzo IP è sempre associato alle chiamate in uscita. Lo specifico indirizzo IP usato dipende dal fatto che l'endpoint da chiamare si trovi all'interno o all'esterno della topologia di rete virtuale.

Se l'endpoint da chiamare è all'**esterno** della topologia di rete virtuale, l'indirizzo in uscita (detto anche indirizzo NAT in uscita) usato è l'indirizzo VIP pubblico dell'ambiente del servizio app. Questo indirizzo si trova nell'interfaccia utente del portale per l'ambiente del servizio app nel pannello Proprietà.
 
![Indirizzo IP in uscita][OutboundIPAddress]

Questo indirizzo può essere determinato per gli ambienti del servizio app che hanno un indirizzo VIP pubblico anche creando un'app nell'ambiente del servizio app ed eseguendo *nslookup* nell'indirizzo dell'app. L'indirizzo IP risultante è sia l'indirizzo VIP pubblico sia l'indirizzo NAT in uscita dell'ambiente del servizio app.

Se l'endpoint da chiamare è all'**interno** della topologia di rete virtuale, l'indirizzo in uscita dell'app chiamante sarà l'indirizzo IP interno della singola risorsa di calcolo che esegue l'app. Tuttavia non esiste un mapping persistente degli indirizzi IP interni di rete virtuale alle app. Le app possono spostarsi in risorse di calcolo diverse e il pool di risorse di calcolo disponibili in un ambiente del servizio app può essere modificato in seguito a operazioni di ridimensionamento.

Tuttavia, poiché un ambiente del servizio app si trova sempre all'interno di una subnet, è certo che l'indirizzo IP interno di una risorsa di calcolo che esegue un'app sarà sempre compreso nell'intervallo CIDR della subnet. Di conseguenza, quando vengono usati gruppi di sicurezza di rete o ACL con granularità fine per proteggere l'accesso ad altri endpoint all'interno della rete virtuale, è necessario concedere l'accesso all'intervallo della subnet contenente l'ambiente del servizio app.

Il diagramma seguente illustra questi concetti in modo più dettagliato:

![Indirizzi di rete in uscita][OutboundNetworkAddresses]

Nel diagramma precedente:

- Poiché l'indirizzo VIP pubblico dell'ambiente del servizio app è 192.23.1.2, questo sarà l'indirizzo IP in uscita usato quando si eseguono chiamate agli endpoint "Internet".
- L'intervallo CIDR della subnet contenitore per l'ambiente del servizio app è 10.0.1.0/26. Gli altri endpoint all'interno della stessa infrastruttura di rete virtuale visualizzeranno le chiamate dalle app come provenienti da un punto compreso in questo intervallo di indirizzi.

## Chiamate tra gli ambienti del servizio app ##
Se si distribuiscono più ambienti del servizio app nella stessa rete virtuale e si eseguono chiamate in uscita da un ambiente del servizio app a un altro, può verificarsi uno scenario più complesso. Anche questi tipi di chiamate tra ambienti del servizio app saranno considerati come chiamate "Internet".

Il diagramma seguente illustra un esempio di un'architettura a più livelli con le app in un ambiente del servizio app (ad esempio, app Web "Porta principale") che eseguono chiamate al secondo ambiente del servizio app (ad esempio, app di API back-end interne non accessibili da Internet).

![Chiamate tra gli ambienti del servizio app][CallsBetweenAppServiceEnvironments]

Nell'esempio precedente l'indirizzo IP in uscita dell'ambiente del servizio app "ASE One" è 192.23.1.2. Se un'app in esecuzione in questo ambiente del servizio app effettua una chiamata in uscita a un'app in esecuzione in un secondo ambiente del servizio app ("ASE Two") che si trova nella stessa rete virtuale, la chiamata in uscita sarà considerata come chiamata "Internet". Di conseguenza il traffico di rete in arrivo nel secondo ambiente del servizio app verrà visualizzato come proveniente da 192.23.1.2 (ossia non l'intervallo di indirizzi della subnet del primo ambiente del servizio app).

Anche se le chiamate tra ambienti del servizio app diversi sono considerate come chiamate "Internet", quando entrambi gli ambienti del servizio app si trovano nella stessa area di Azure, il traffico di rete rimarrà nella rete dell'area di Azure e non transiterà fisicamente attraverso la rete Internet pubblica. Di conseguenza è possibile usare un gruppo di sicurezza di rete nella subnet del secondo ambiente del servizio app per consentire solo chiamate in ingresso dal primo ambiente del servizio app con indirizzo IP in uscita 192.23.1.2, garantendo comunicazioni sicure tra gli ambienti del servizio app.

## Informazioni e collegamenti aggiuntivi ##
Tutti gli articoli e le procedure sugli ambienti del servizio app sono disponibili nel [file LEGGIMI per gli ambienti di servizio dell'applicazione](../app-service/app-service-app-service-environments-readme.md).

Informazioni dettagliate sulle porte in ingresso usate dagli ambienti del servizio app e sull'uso di gruppi di sicurezza di rete per controllare il traffico in ingresso sono disponibili [qui][controllinginboundtraffic].

Informazioni dettagliate sull'uso di route definite dall'utente per concedere l'accesso Internet in uscita agli ambienti del servizio app sono disponibili in questo [articolo][ExpressRoute].


<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

<!---HONumber=AcomDC_0713_2016-->