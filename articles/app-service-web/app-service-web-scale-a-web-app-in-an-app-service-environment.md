<properties 
	pageTitle="Come ridimensionare un'app Web in un ambiente del servizio app" 
	description="Ridimensionamento di un'app Web in un ambiente del servizio app" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2015" 
	ms.author="ccompy"/>

# Ridimensionamento di app Web in ambiente del servizio app #

In generale, gli ambienti del servizio app sono essenzialmente distribuzioni personali del servizio app di Azure nella rete virtuale e sono gestibili solo dalla propria sottoscrizione. Questi ambienti offrono nuove funzionalità di rete dal momento che sono incluse nella rete virtuale e possono essere ridimensionate oltre quanto normalmente disponibile in ambienti del servizio app di Azure. Per altre informazioni sull'ambiente del servizio app, vedere [Informazioni sull'ambiente del servizio app][WhatisASE]. Per informazioni dettagliate sulla creazione di un ambiente del servizio app o sulla creazione di un'app Web in un ambiente del servizio app, vedere [Come creare un ambiente del servizio app][HowtoCreateASE] ed [Come creare un'app Web in ambiente del servizio app][CreateWebappinASE].

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

In poche parole, quando si modifica un attributo di ridimensionamento per un'app Web, la modifica viene effettuata a livello di piano di servizio app. Per informazioni dettagliate sul ridimensionamento di piani di servizio app o sui piani di servizio app all'esterno di ambienti del servizio app, vedere [Ridimensionare un'app Web nel servizio app di Azure][ScaleWebapp] e [Panoramica approfondita dei piani di servizio app di Azure][Appserviceplans].

La procedura di ridimensionamento di un'app Web in ambiente del servizio app è simile a quella usata normalmente per ridimensionare le app Web. Nel servizio app di Azure è possibile ridimensionare tre aspetti:

- piano tariffario
- dimensioni dei processi di lavoro (per le istanze dedicate)
- numero di istanze.

In un ambiente del servizio app non è necessario selezionare o modificare il piano tariffario. In termini di funzionalità il livello è già quello del piano tariffario Premium. In un ambiente del servizio app non esistono inoltre processi di lavoro condivisi, in quanto tutti i processi di lavoro sono dedicati. L'amministratore dell'ambiente del servizio app può quindi assegnare le dimensioni della risorsa di elaborazione da usare per ogni pool di lavoro, invece di assegnare dimensioni fisse. Questo significa che, se necessario, è possibile avere il pool di lavoro 1 con risorse di calcolo P4 e il pool di lavoro 2 con risorse di calcolo P1, di conseguenza non è necessario che i pool siano ordinati in base alle dimensioni. Per informazioni dettagliate sulle dimensioni e sui relativi prezzi, leggere il documento [Prezzi del servizio app di Azure][AppServicePricing]. In tal modo le opzioni di ridimensionamento per app Web e piani di servizio app in un ambiente del servizio app sono:

- selezione del pool di lavoro
- numero di istanze

Per modificare uno di questi elementi tramite l'interfaccia utente appropriata visualizzata con il piano di servizio app.

![][1]

### Ridimensionamento del numero di istanze ###

Quando si crea per la prima volta l'app Web in un ambiente del servizio app, è consigliabile aggiungere almeno altre due istanze per garantire la tolleranza di errore.

Se la capacità dell'ambiente del servizio app è sufficiente, questa operazione è abbastanza semplice. Passare al piano di servizio app che contiene i siti per cui aggiungere le istanze e selezionare Ridimensiona. Verrà aperta l'interfaccia utente in cui è sufficiente far scorrere l'indicatore di istanze verso l'alto fino alla visualizzazione del valore desiderato e salvare.

![][2]

Non è possibile aumentare il numero di risorse del piano di servizio app oltre il numero di risorse di calcolo disponibili nel pool di lavoro in cui si trova il piano di servizio app. Se sono necessarie altre risorse, chiedere all'amministratore dell'ambiente del servizio app di aggiungere altre risorse di calcolo al pool di lavoro in cui servono. Per informazioni sulla riconfigurazione dell'ambiente del servizio app, vedere [Come configurare un ambiente del servizio app][HowtoConfigureASE].
 

### Selezione del pool di lavoro ###

È possibile accedere alla selezione del pool di lavoro tramite l'interfaccia utente del piano di servizio app. Aprire il piano di servizio app in cui aumentare il numero di risorse e selezionare Pool di lavoro. Verranno visualizzati tutti i pool di lavoro configurati nell'ambiente del servizio app. Se è presente un solo pool di lavoro, l'elenco conterrà solo tale pool. Per cambiare il pool di lavoro in cui si trova il piano di servizio app, è sufficiente selezionare il pool di lavoro in cui spostare il piano di servizio app.

![][3]

Prima di procedere, è importante verificare che la capacità disponibile sia sufficiente per il piano di servizio app. Nell'elenco dei pool di lavoro non è indicato solo il nome del pool di lavoro, ma è anche possibile vedere quanti processi di lavoro sono disponibili nel pool. Assicurarsi che il numero di istanze disponibili sia sufficiente per contenere il piano di servizio app. Se sono necessarie altre risorse di calcolo nel pool di lavoro a cui passare, chiedere all'amministratore dell'ambiente del servizio app di aggiungerle.

Lo spostamento da un'app Web a un pool di lavoro comporterà un riavvio delle app Web e di conseguenza un possibile periodo di inattività dell'app, la cui durata dipende dal tempo necessario per il riavvio.

## Introduzione

Per iniziare a usare gli ambienti del servizio app, vedere [Come creare un ambiente del servizio app][HowtoCreateASE].

Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Servizio app di Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scaleasp.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scaleinstances.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scalepool.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
 

<!---HONumber=Oct15_HO3-->