<properties 
	pageTitle="Come ridimensionare un'app in un ambiente del servizio app" 
	description="Ridimensionamento di un'app in un ambiente del servizio app" 
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
	ms.date="10/26/2015" 
	ms.author="ccompy"/>

# Ridimensionamento di app in un ambiente del servizio app #

In generale, gli ambienti del servizio app sono essenzialmente distribuzioni personali del servizio app di Azure nella rete virtuale e sono gestibili solo dalla propria sottoscrizione. Questi ambienti offrono nuove funzionalità di rete dal momento che sono incluse nella rete virtuale e possono essere ridimensionate oltre quanto normalmente disponibile in ambienti del servizio app di Azure. Per altre informazioni sull'ambiente del servizio app, vedere [Informazioni sull'ambiente del servizio app][WhatisASE]. Per informazioni dettagliate sulla creazione di un ambiente del servizio app o sulla creazione di un'app Web in un ambiente del servizio app, vedere [Come creare un ambiente del servizio app][HowtoCreateASE] ed [Come creare un'app Web in ambiente del servizio app][CreateWebappinASE].

In poche parole, quando si modifica un attributo di ridimensionamento per un'app Web, per dispositivi mobili o per le API, la modifica viene effettuata a livello di piano di servizio app (ASP, App Service Plan). Per informazioni dettagliate sul ridimensionamento di piani di servizio app o sui piani di servizio app all'esterno di ambienti del servizio app, vedere [Ridimensionare un'app Web nel servizio app di Azure][ScaleWebapp] e [Panoramica approfondita dei piani di servizio app di Azure][Appserviceplans].

La procedura di ridimensionamento di un'app in un ambiente del servizio app è simile a quella usata normalmente per ridimensionare le app. Nel servizio app di Azure è possibile ridimensionare tre aspetti:

- piano tariffario
- dimensioni dei processi di lavoro (per le istanze dedicate)
- numero di istanze.

In un ambiente del servizio app non è necessario selezionare o modificare il piano tariffario. In termini di funzionalità il livello è già quello del piano tariffario Premium. In un ambiente del servizio app non esistono inoltre processi di lavoro condivisi, in quanto tutti i processi di lavoro sono dedicati.

L'amministratore dell'ambiente del servizio app può quindi assegnare le dimensioni della risorsa di elaborazione da usare per ogni pool di lavoro, rispetto alle dimensioni del processo di lavoro. Questo significa che, se necessario, è possibile avere il pool di lavoro 1 con risorse di calcolo P4 e il pool di lavoro 2 con risorse di calcolo P1, di conseguenza non è necessario che i pool siano ordinati in base alle dimensioni. Per informazioni dettagliate sulle dimensioni e sui relativi prezzi, leggere il documento [Prezzi del servizio app di Azure][AppServicePricing]. In tal modo le opzioni di ridimensionamento per app Web e piani di servizio app in un ambiente del servizio app sono:

- selezione del pool di lavoro
- numero di istanze

Per modificare uno di questi elementi, usare l'interfaccia utente appropriata visualizzata per i piani di servizio app ospitati nell'ambiente del servizio app. Non è possibile aumentare il numero di risorse del piano di servizio app oltre il numero di risorse di calcolo disponibili nel pool di lavoro in cui si trova il piano di servizio app. Se sono necessarie altre risorse, chiedere all'amministratore dell'ambiente del servizio app di aggiungere altre risorse di calcolo al pool di lavoro in cui servono. Per informazioni sulla riconfigurazione dell'ambiente del servizio app, vedere [Come configurare un ambiente del servizio app][HowtoConfigureASE]. Può anche essere utile sfruttare le funzionalità di scalabilità automatica dell'ambiente del servizio app per aumentare la capacità in base alla pianificazione o alla metrica. Per altri dettagli sulla configurazione della scalabilità automatica per l'ambiente del servizio app, vedere [Come configurare la scalabilità automatica per un ambiente del servizio app][ASEAutoscale].

![][1]

### Ridimensionamento del numero di istanze ###

Quando si crea per la prima volta l'app Web in un ambiente del servizio app, è consigliabile aggiungere almeno altre due istanze per garantire la tolleranza di errore.

Se la capacità dell'ambiente del servizio app è sufficiente, questa operazione è abbastanza semplice. Passare al piano di servizio app che contiene i siti per cui aggiungere le istanze e selezionare Ridimensiona. Viene aperta l'interfaccia utente in cui è possibile impostare manualmente la scalabilità per il piano ASP o configurare le regole di scalabilità automatica per il piano ASP. Per ridimensionare manualmente l'app, è sufficiente impostare ***Ridimensiona di*** su ***numero di istanze immesso manualmente***. Trascinare quindi il dispositivo di scorrimento sulla quantità desiderata oppure immetterla nella casella accanto al dispositivo di scorrimento.

![][2]

Le regole di scalabilità automatica per un piano ASP in un ambiente del servizio app funzionano come sempre. È possibile selezionare ***Percentuale CPU*** in ***Ridimensiona di*** e creare regole di scalabilità automatica per il piano ASP in base alla percentuale di CPU oppure è possibile creare regole più complesse usando l'impostazione ***regole per la pianificazione e le prestazioni***. Per dettagli più completi sulla configurazione della scalabilità automatica, vedere la guida [Scalare un'app in Servizio app di Azure][AppScale].


### Selezione del pool di lavoro ###

Come indicato in precedenza, è possibile accedere alla selezione del pool di lavoro tramite l'interfaccia utente del piano di servizio app. Aprire il pannello del piano di servizio app in cui aumentare il numero di risorse e selezionare Pool di lavoro. Verranno visualizzati tutti i pool di lavoro configurati nell'ambiente del servizio app. Se è presente un solo pool di lavoro, l'elenco conterrà solo tale pool. Per cambiare il pool di lavoro in cui si trova il piano di servizio app, è sufficiente selezionare il pool di lavoro in cui spostare il piano di servizio app.

![][3]

Prima di spostare il piano ASP da un pool di lavoro a un altro, è importante assicurarsi che la capacità disponibile per il piano ASP sia sufficiente. Nell'elenco dei pool di lavoro non è indicato solo il nome del pool di lavoro, ma è anche possibile vedere quanti processi di lavoro sono disponibili nel pool. Assicurarsi che il numero di istanze disponibili sia sufficiente per contenere il piano di servizio app. Se sono necessarie altre risorse di calcolo nel pool di lavoro a cui passare, chiedere all'amministratore dell'ambiente del servizio app di aggiungerle.

> [AZURE.NOTE]Lo spostamento di un piano ASP da un pool di lavoro comporterà un riavvio delle app in tale piano ASP e di conseguenza un possibile periodo di inattività dell'app, la cui durata dipende dal tempo necessario per il riavvio delle app.

## Introduzione

Per iniziare a usare gli ambienti del servizio app, vedere [Come creare un ambiente del servizio app][HowtoCreateASE].

Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Servizio app di Azure][AzureAppService].

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[AppScale]: http://azure.microsoft.com/documentation/articles/web-sites-scale/

<!---HONumber=Nov15_HO1-->