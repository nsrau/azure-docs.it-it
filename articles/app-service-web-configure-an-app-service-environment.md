<properties 
	pageTitle="Come configurare un ambiente del servizio app" 
	description="Configurazione, gestione e monitoraggio degli ambienti del servizio app" 
	services="app-services\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-services-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="ccompy"/>

# Configurazione di un ambiente del servizio app #

## Panoramica ##

Ambienti del servizio app è una nuova funzionalità del piano tariffario Premium offerta in anteprima. Offre nuove funzionalità di scalabilità e accesso alla rete. La nuova funzionalità di scalabilità consente di posizionare un'istanza del servizio app di Azure nella propria rete virtuale. Se non si ha familiarità con la funzionalità Ambiente del servizio app, leggere il documento [Informazioni sull'ambiente del servizio app][WhatisASE] Per informazioni su come creare un ambiente del servizio app, leggere il documento [Come creare un ambiente del servizio app][HowtoCreateASE].

In generale, un ambiente del servizio app è costituito da vari componenti principali:

- Risorse di calcolo in esecuzione nel servizio ospitato Ambiente del servizio app
- Archiviazione
- Database
- Rete virtuale con almeno una subnet
- Subnet con il servizio ospitato Ambiente del servizio app in esecuzione al suo interno

Per gestire e monitorare gli ambienti del servizio app, nel portale di Azure selezionare Sfoglia -\> Ambienti del servizio app per accedere all'interfaccia utente dedicata. La versione iniziale offre tutte le opzioni necessarie per gestire il sistema e continuerà a migliorare tramite l'aggiunta di ulteriori funzionalità nelle prossime settimane.

![][1]

## Monitoraggio ##

Nella versione di anteprima iniziale non sono disponibili numerose funzionalità di metrica, ma ne verranno aggiunte altre a breve. Le funzionalità di metrica consentiranno agli amministratori di sistema di prendere decisioni in merito alle operazioni e alla scalabilità del sistema.

Già da adesso nel portale è possibile visualizzare tutti i piani di servizio app nell'ambiente del servizio app e tutte le app Web incluse nell'ambiente. Per visualizzare questi elenchi, passare a Impostazioni e selezionare gli elementi desiderati.

![][3]

In entrambi gli elenchi è possibile visualizzare l'assegnazione del pool di lavoro con il numero di istanze e le dimensioni della risorsa di calcolo usata. Per visualizzare i dettagli relativi alle prestazioni all'interno di un singolo piano di servizio app, procedere normalmente, ovvero aprendo l'interfaccia utente del piano di servizio app.

![][4]

## Risorse di calcolo ##

Le risorse di calcolo, l'archiviazione e il database sono gestiti dal servizio app di Azure. Le decisioni relative alla quantità e alle dimensioni delle risorse di calcolo spettano invece all'utente.

Indipendentemente dalle dimensioni delle risorse di calcolo, il footprint minimo prevede 2 server front-end e 2 processi di lavoro. Un ambiente del servizio app può essere configurato per usare fino a 55 risorse di calcolo totali. Di queste 55 risorse di calcolo, solo 50 possono essere usate per ospitare i carichi di lavoro. I motivi sono due. Esiste un minimo di 2 risorse di calcolo front-end. Ne rimangono quindi 53 per supportare l'allocazione dei pool di lavoro. Per fornire la tolleranza di errore, è tuttavia necessario allocare una risorsa di calcolo aggiuntiva in base alle regole seguenti:

- per ogni pool di lavoro è necessaria almeno una risorsa di calcolo aggiuntiva a cui non è possibile assegnare un carico di lavoro
- quando la quantità di risorse di calcolo in un pool supera un determinato valore, è necessaria un'altra risorsa di calcolo

All'interno di un singolo pool di lavoro, i requisiti di tolleranza di errore prevedono che per un determinato valore di X risorse assegnate a un pool di lavoro:

- se X è compreso tra 2 e 20, la quantità di risorse di calcolo che si può usare per i carichi di lavoro è pari a X-1
- se X è compreso tra 21 e 40, la quantità di risorse di calcolo che si può usare per i carichi di lavoro è pari a X-2
- se X è compreso tra 41 e 53, la quantità di risorse di calcolo che si può usare per i carichi di lavoro è pari a X-3

Oltre a poter gestire la quantità di risorse di calcolo che si possono assegnare a un determinato pool, è anche possibile gestire le dimensioni. Con gli ambienti del servizio app, è possibile scegliere tra 4 dimensioni diverse, denominate da P1 a P4. Per informazioni dettagliate sulle dimensioni e sui rispettivi prezzi, vedere [Dettagli prezzi del servizio app][AppServicePricing]. Le dimensioni delle risorse di calcolo da P1 a P3 corrispondono a quelle disponibili normalmente. La risorsa di calcolo con dimensioni P4 offre 8 core con 14 GB di RAM ed è disponibile solo in un ambiente del servizio app.

Come indicato in precedenza, la funzionalità Ambiente del servizio app è attualmente disponibile in anteprima e, in quanto tale, potrà ancora crescere in futuro. Oltre alle funzionalità di monitoraggio, verranno aggiunte altre funzionalità di gestione non appena gli ambienti del servizio app passeranno alla fase di disponibilità generale. Per ora, l'interfaccia consente di gestire solo alcuni elementi:

- Numero di risorse di calcolo in ogni pool
- Dimensioni delle risorse di calcolo in ogni pool
- Numero di indirizzi IP disponibili

Per controllare questi elementi, selezionare la voce di configurazione Scalabilità nella parte superiore.

![][2]

Da qui è possibile regolare la quantità di risorse di calcolo in ogni pool e le relative dimensioni. Prima di apportare modifiche, tenere presente quanto segue:

- l'applicazione delle modifiche può richiedere anche ore, a seconda delle dimensioni della modifica richiesta
- se è già in corso una modifica di configurazione dell'ambiente del servizio app, non è possibile avviare un'altra modifica
- se si modificano le dimensioni delle risorse di calcolo usate in un pool di lavoro, potrebbero verificarsi interruzioni del servizio per le app Web in esecuzione in quel pool di lavoro

L'aggiunta di altre istanze a un pool di lavoro è un'operazione innocua che non influisce sull'operatività del sistema. Ma la modifica delle dimensioni della risorsa di calcolo usata in un pool di lavoro deve essere gestita con cautela. Per evitare tempi di inattività delle app durante una modifica delle dimensioni per un pool di lavoro, è consigliabile:

- usare un pool di lavoro inutilizzato per aggiungere le istanze necessarie con le dimensioni desiderate
- ridimensionare i piani di servizio app in base al nuovo pool di lavoro.  
 
Rispetto alla modifica delle dimensioni della risorsa di calcolo mentre sono in esecuzione i carichi di lavoro, questa alternativa non genera alcun impatto sulle app in esecuzione. Per informazioni dettagliate sulla scalabilità delle app Web in un ambiente del servizio app, vedere [Scalabilità delle app Web in un ambiente del servizio app][HowtoScale].

## Rete virtuale ##

La [rete virtuale][virtualnetwork] e la subnet sono controllate dall'utente. Gli ambienti del servizio app prevedono alcuni requisiti di rete, ma tutto il resto può essere controllato dall'utente. I requisiti degli ambienti del servizio app sono:

- una rete virtuale con almeno 512 indirizzi
- una subnet con almeno 256 indirizzi 
- la rete virtuale deve essere una rete virtuale regionale  
 
È possibile amministrare la rete virtuale tramite la normale interfaccia utente di Rete virtuale.

Poiché il servizio app di Azure è collocato all'interno della rete virtuale, le app ospitate nell'ambiente del servizio app possono accedere direttamente alle risorse rese disponibili tramite le reti private virtuali ExpressRoute o da sito a sito. Le app all'interno degli ambienti del servizio app non richiedono ulteriori funzionalità di rete per accedere alle risorse disponibili nella rete virtuale che ospita l'ambiente del servizio app.

Se necessario, è anche possibile controllare gli accessi tramite i gruppi di sicurezza di rete. Questa funzionalità consente di bloccare l'ambiente del servizio app e consentire l'accesso solo agli indirizzi IP desiderati. Per altre informazioni su come limitare gli accessi, vedere [Come controllare il traffico in ingresso in un ambiente del servizio app][ControlInbound].

## Eliminazione di un ambiente del servizio app ##

Per eliminare un ambiente del servizio app, usare l'opzione Elimina nella parte superiore del pannello Ambiente del servizio app. Non è possibile eliminare un ambiente del servizio app con contenuto al suo interno. Per poter eliminare l'ambiente del servizio app, assicurarsi di rimuovere tutte le app Web e i piani di servizio app.

## Introduzione

Per iniziare a usare gli ambienti del servizio app, vedere [Come creare un ambiente del servizio app][HowtoCreateASE].

Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Servizio app di Azure][AzureAppService].


<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/configureaseblade.png
[2]: ./media/app-service-web-configure-an-app-service-environment/configurescale.png
[3]: ./media/app-service-web-configure-an-app-service-environment/configureasplist.png
[4]: ./media/app-service-web-configure-an-app-service-environment/configurewebapplist.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment-in-an-ase/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
<!--HONumber=52-->
