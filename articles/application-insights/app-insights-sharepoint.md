<properties 
	pageTitle="Monitorare un sito di SharePoint con Application Insights" 
	description="Avviare il monitoraggio di una nuova applicazione con una nuova chiave di strumentazione" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/06/2015" 
	ms.author="awills"/>

# Monitorare un sito di SharePoint con Application Insights


[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Application Insights consente di monitorare disponibilità, prestazioni e utilizzo delle app. Di seguito verrà illustrato come impostarlo per un sito di SharePoint.


## Creare una risorsa Application Insights


Nel [portale di Azure](https://portal.azure.com) creare una nuova risorsa di Application Insights. Scegliere ASP.NET come tipo di applicazione.

![Fare clic su Proprietà, selezionare il tasto e premere CTRL+C](./media/app-insights-sharepoint/01-new.png)


Viene visualizzato un pannello che mostra le prestazioni e i dati di utilizzo relativi all'app. Per visualizzare di nuovo questo pannello al successivo accesso ad Azure, nella schermata Start dovrebbe venire visualizzato un riquadro per l'app. In alternativa, fare clic su Sfoglia per cercarla.
    


## Aggiungere lo script alle pagine Web

In Avvio rapido ottenere lo script per le pagine Web:

![](./media/app-insights-sharepoint/02-monitor-web-page.png)

Inserire lo script immediatamente prima del tag &lt;/head&gt; di ogni pagina di cui si vuole tenere traccia. Se il sito Web presenta una pagina master, è possibile inserire lo script in tale posizione. Ad esempio, in un progetto ASP.NET MVC inserire lo script in View\\Shared\_Layout.cshtml

Lo script contiene la chiave di strumentazione che indirizza i dati di telemetria alla risorsa di Application Insights.

### Aggiungere il codice alle pagine del sito

#### Nella pagina master

Se è possibile modificare la pagina master del sito, che fornisce il monitoraggio per ogni pagina del sito.

Consultare la pagina master e modificarla mediante SharePoint Designer o un altro editor.

![](./media/app-insights-sharepoint/03-master.png)


Aggiungere il codice appena prima del tag </head>.


![](./media/app-insights-sharepoint/04-code.png)

#### oppure nelle singole pagine

Per monitorare un set limitato di pagine, aggiungere lo script separatamente a ogni pagina.

Inserire una Web part e incorporarvi il frammento di codice.


![](./media/app-insights-sharepoint/05-page.png)


## Visualizza i dati sull'app

Ridistribuire l'app.

Tornare al pannello dell'applicazione nel [portale di Azure](https://portal.azure.com).

I primi eventi verranno visualizzati nella ricerca.

![](./media/app-insights-sharepoint/09-search.png)

Se si prevedono più dati, fare clic su Aggiorna dopo pochi secondi.

Dal pannello Panoramica fare clic su **Analisi di utilizzo** per vedere i grafici di utenti, sessioni e visualizzazioni di pagina:

![](./media/app-insights-sharepoint/06-usage.png)

Fare clic su qualsiasi grafico per vedere maggiori dettagli, ad esempio le visualizzazioni di pagina:

![](./media/app-insights-sharepoint/07-pages.png)

Oppure gli utenti:


![](./media/app-insights-sharepoint/08-users.png)



## Passaggi successivi

* [Test web](app-insights-monitor-web-app-availability.md) per monitorare la disponibilità del sito.

* [Application Insights](app-insights-overview.md) per altri tipi di app.



<!--Link references-->

<!---HONumber=AcomDC_0128_2016-->