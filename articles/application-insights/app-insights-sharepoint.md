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
	ms.date="03/24/2016" 
	ms.author="awills"/>

# Monitorare un sito di SharePoint con Application Insights


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


## Acquisizione dell'ID utente


Il frammento di codice della pagina Web standard non acquisisce l'ID utente da SharePoint. Per l'acquisizione è necessario eseguire una piccola modifica.


1. Copiare la chiave di strumentazione dell'app dall'elenco a discesa Informazioni di base in Application Insights. 


    ![](./media/app-insights-sharepoint/02-props.png)

2. Sostituire la chiave di strumentazione per "XXXX" nel frammento di codice riportato di seguito.
3. Incorporare lo script nell'app di SharePoint anziché il frammento di codice ottenuto dal portale.



```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 
  
<script type="text/javascript"> 
var personProperties; 
  
// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 
  
function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 
     
    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 
    
    personProperties = peopleManager.getMyProperties(); 
  
    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 
     
// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 
  
// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## Passaggi successivi

* [Test web](app-insights-monitor-web-app-availability.md) per monitorare la disponibilità del sito.

* [Application Insights](app-insights-overview.md) per altri tipi di app.



<!--Link references-->


 

<!---HONumber=AcomDC_0608_2016-->