<properties 
	pageTitle="Gestire un'app per le API" 
	description="Informazioni su come gestire le app per le API nel portale di anteprima di Azure e in Esplora server in Visual Studio." 
	services="app-service\api" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na"
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="tdykstra"/>

# Gestire un'app per le API

Questo articolo illustra come usare il portale di anteprima di Azure per eseguire attività di gestione relative all'app per le API, ad esempio configurare l'autenticazione e impostare la scalabilità automatica. Mostra inoltre come eseguire alcune attività di gestione nella finestra Esplora server in Visual Studio.

## Informazioni sull'architettura dell'app per le API

Nel servizio app di Azure un'app per le API è un'[app Web](app-service-web-overview.md) con funzionalità aggiuntive per l'hosting di servizi Web. Nel portale di anteprima di Azure è disponibile il pannello **App per le API** per gestire le funzionalità specifiche dell'API e il pannello **Host app per le API** per gestire l'app Web sottostante.

Ogni gruppo di risorse che contiene almeno un'app per le API include anche un *gateway*. Il gateway funge da proxy e gestisce l'autenticazione e altre funzioni amministrative per tutte le app per le API in un gruppo di risorse. Come un'app per le API, un gateway è un'app Web con funzionalità aggiuntive, di conseguenza sono disponibili anche due pannelli del portale per gestire il gateway: il pannello **Gateway** per le funzioni specifiche del gateway e il pannello **Host gateway** per gestire l'app Web sottostante.

### Attività del pannello App per le API 

Usare il pannello **App per le API** per eseguire queste attività:

- Configurare il livello di accesso: fare clic su **Impostazioni > Impostazioni applicazione**. Il valore predefinito è Interno: solo alle altre app per le API dello stesso gruppo di risorse è consentito chiamare l'app per le API. Per altre informazioni, vedere [Proteggere un'app per le API](app-service-api-dotnet-add-authentication.md).   
- Configurare i criteri di aggiornamento: fare clic su **Impostazioni > Impostazioni applicazione**. Il valore predefinito è **Attivato**. Questo significa che quando una nuova versione dell'app per le API viene pubblicata nel Marketplace, l'app per le API in uso verrà automaticamente aggiornata alla nuova versione, se si tratta di una modifica non sostanziale.  
- Configurare l'autenticazione per le chiamate in uscita dall'app per le API: fare clic su  **Impostazioni > Autenticazione**. Se l'app per le API effettua chiamate a un servizio esterno che richiede l'autenticazione, è necessario immettere i valori di configurazione necessari in questa sezione. Ad esempio, un connettore Dropbox richiede un ID client e un segreto client per accedere al servizio Dropbox.
- Configurare il [Controllo degli accessi in base al ruolo](role-based-access-control-configure.md): fare clic su **Impostazioni > Utenti**. L'accesso utente configurato in questa sezione determina solo gli utenti che possono accedere alle funzionalità specifiche dell'app per le API. Per configurare il Controllo degli accessi in base al ruolo per le funzionalità dell'app Web, usare il pannello **Host app per le API**. In genere, si tende a mantenere sincronizzate le impostazioni di Controllo degli accessi in base al ruolo per l'app per le API e per l'host dell'app per le API. Se si concede a un utente l'accesso all'app per le API ma non all'host dell'app per le API, l'utente non potrà usare le funzionalità disponibili nel pannello **App per le API** che si riferiscono all'host dell'app per le API. La relazione tra il pannello **App per le API** e il pannello **Host app per le API** è spiegata più avanti.
- Visualizzare la definizione dell'API: fare clic su **Definizione API** nella sezione **Riepilogo** per visualizzare un elenco dei metodi esposti dall'**app per le API**.

### Funzionalità comuni dei pannelli App per le API e Host app per le API 

Il pannello **App per le API** consente di eseguire molte attività che riguardano l'app Web sottostante. Ad esempio fornisce i pulsanti per arrestare, avviare e riavviare l'app Web che ospita l'app per le API. Queste attività possono tuttavia essere eseguite dal pannello **Host app per le API**. Ecco perché i due pannelli condividono gran parte dell'interfaccia utente. I pulsanti **Arresta**, **Avvia** e **Riavvia** nel pannello **App per le API** hanno esattamente lo stesso effetto dei pulsanti **Arresta**, **Avvia** e **Riavvia** disponibili nel pannello **Host app per le API**. Allo stesso modo, le informazioni di monitoraggio fornite nel pannello **App per le API** sono le stesse visualizzate nel pannello **Host app per le API**.

Le uniche funzioni presenti nel pannello **App per le API** che non costituiscono un duplicato di quelle presenti nel pannello **Host app per le API** sono quelle elencate nella sezione precedente.

### Attività del pannello Host app per le API

Usare il pannello **Host app per le API** per tutte le attività da eseguire per le app Web. Per altre informazioni, vedere [Gestire le app Web nel portale](web-sites-manage.md).

### Attività del pannello Gateway

Usare il pannello **Gateway** per eseguire queste attività:

- Configurazione del provider di autenticazione per le chiamate in ingresso alle app per le API: fare clic su **Impostazioni > Identità**. Se il gateway deve autenticare gli utenti prima di consentire loro di chiamare le app per le API nel gruppo di risorse, immettere qui i valori di configurazione necessari. Per altre informazioni, vedere [Configurare e testare un connettore SaaS nel servizio app di Azure](app-service-api-connnect-your-app-to-saas-connector.md). 
- Configurare il [Controllo degli accessi in base al ruolo](role-based-access-control-configure.md): fare clic su **Impostazioni > Utenti**. I commenti precedenti che illustrano la relazione tra la configurazione del controllo degli accessi in base al ruolo immessa nel pannello App per le API e nel pannello Host app per le API si applica anche ai pannelli Gateway e Host gateway.

### Attività del pannello Host gateway

Usare il pannello **Host gateway** per tutte le attività da eseguire per le app Web. Per altre informazioni, vedere [Gestire le app Web nel portale](web-sites-manage.md).

## Passare al pannello App per le API 

Per passare al pannello **App per le API** usare la funzionalità Sfoglia nel portale di anteprima di Azure. Nella home page del portale fare clic su **Sfoglia > App per le API** per visualizzare tutte le app per le API che si possono gestire.

![](./media/app-service-api-manage-in-portal/browse.png)

![](./media/app-service-api-manage-in-portal/apiappslist.png)

Quando si fa clic su una riga nell'elenco di **App per le API**, nel portale viene visualizzato il pannello **App per le API**.

![](./media/app-service-api-manage-in-portal/apiappblade.png)

## Passare al pannello Host app per le API

Per passare al pannello **Host app per le API**, fare clic su **Host app per le API** nel pannello **App per le API**.

![](./media/app-service-api-manage-in-portal/apiappbladetohost.png)

![](./media/app-service-api-manage-in-portal/apiapphostbladenocallouts.png)

## Passare al pannello Gateway

Per passare al pannello **Gateway**, fare clic sul collegamento **Gateway** nel pannello **App per le API**.
   
![](./media/app-service-api-manage-in-portal/apiappbladegotogateway.png)

![](./media/app-service-api-manage-in-portal/gatewaybladenocallout.png)

## Passare al pannello Host gateway

Per passare al pannello **Host gateway**, fare clic sul collegamento **Host gateway** nel pannello **Gateway**.
   
![](./media/app-service-api-manage-in-portal/gatewaybladetohost.png)

![](./media/app-service-api-manage-in-portal/gatewayhost.png)

## Accedere alle app per le API da Esplora server in Visual Studio

Da Esplora server in Visual Studio è possibile avviare una sessione di debug remoto, visualizzare i log in streaming e fare clic su una voce di menu che apre il pannello App per le API nel portale.

Per passare a un'app per le API in Esplora server, fare clic su **Azure > Servizio app > [nome del gruppo di risorse] > [nome dell'app per le API]**, come mostrato nella figura.

![](./media/app-service-api-manage-in-portal/se.png)

## Riepilogo

In questo articolo è stato illustrato come usare il portale di anteprima di Azure per eseguire le attività di gestione relative alle app per le API. Per altre informazioni, vedere [Cosa sono le app per le API?](app-service-api-apps-why-best-platform.md)


<!--HONumber=52-->
