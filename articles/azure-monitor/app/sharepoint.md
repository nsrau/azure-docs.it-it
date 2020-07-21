---
title: Monitorare un sito di SharePoint con Application Insights
description: Avviare il monitoraggio di una nuova applicazione con una nuova chiave di strumentazione
ms.topic: conceptual
ms.date: 07/11/2018
ms.openlocfilehash: a545a24b5a9af883200f25cc83486e3c5621bd78
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86516890"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Monitorare un sito di SharePoint con Application Insights

Azure Application Insights consente di monitorare disponibilità, prestazioni e utilizzo delle app. Di seguito verrà illustrato come impostarlo per un sito di SharePoint.

> [!NOTE]
> A causa di problemi di sicurezza, non è possibile aggiungere direttamente lo script descritto in questo articolo alle pagine Web nell'esperienza utente di SharePoint moderna. In alternativa, è possibile utilizzare [SharePoint Framework (SPFx)](/sharepoint/dev/spfx/extensions/overview-extensions) per creare un'estensione personalizzata che è possibile utilizzare per installare Application Insights nei siti di SharePoint. Per altre informazioni, vedere [come creare una soluzione di estensione SPFx con AppInsights installato da zero](https://github.com/microsoft/ApplicationInsights-JS/tree/master/SPO#how-to-create-a-spfx-extension-solution-with-appinsights-installed-from-scratch) o [visualizzare l'esempio](https://github.com/microsoft/ApplicationInsights-JS/tree/master/SPO/AppInsightsExtensionSolutionSample). 

## <a name="create-an-application-insights-resource"></a>Creare una risorsa di Application Insights
Nel [portale di Azure](https://portal.azure.com) creare una nuova risorsa di Application Insights. Scegliere ASP.NET come tipo di applicazione.

![Fare clic su Proprietà, selezionare il tasto e premere CTRL+C](./media/sharepoint/001.png)

Verrà visualizzata una finestra che mostra i dati di prestazioni e utilizzo relativi all'app. Per visualizzare di nuovo questo pannello al successivo accesso ad Azure, nella schermata Start dovrebbe venire visualizzato un riquadro per l'app. In alternativa, fare clic su Sfoglia per cercarla.

## <a name="add-the-script-to-your-web-pages"></a>Aggiungere lo script alle pagine Web

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

Inserire lo script immediatamente prima del &lt; &gt; tag/Head di ogni pagina di cui si vuole tenere traccia. Se il sito Web dispone di una pagina master, è possibile inserire lo script in tale pagina. Ad esempio, in un progetto ASP.NET MVC inserire lo script in View\Shared\_Layout.cshtml.

Lo script contiene la chiave di strumentazione che indirizza i dati di telemetria alla risorsa di Application Insights.

### <a name="add-the-code-to-your-site-pages"></a>Aggiungere il codice alle pagine del sito
#### <a name="on-the-master-page"></a>Nella pagina master
Se è possibile modificare la pagina master del sito, che fornisce il monitoraggio per ogni pagina del sito.

Consultare la pagina master e modificarla mediante SharePoint Designer o un altro editor.

![Screenshot che illustra come modificare la pagina master utilizzando SharePoint Designer o un altro editor.](./media/sharepoint/03-master.png)

Aggiungere il codice immediatamente prima del </head> Tag. 

![Screenshot che mostra dove aggiungere il codice alla pagina del sito.](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>oppure nelle singole pagine
Per monitorare un set limitato di pagine, aggiungere lo script separatamente a ogni pagina. 

Inserire una Web part e incorporarvi il frammento di codice.

![Screenshot che Mostra come aggiungere lo script per monitorare un set limitato di pagine.](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Visualizza i dati sull'app
Ridistribuire l'app.

Tornare al pannello dell'applicazione nel [portale di Azure](https://portal.azure.com).

I primi eventi verranno visualizzati nella ricerca. 

![Screenshot che mostra i nuovi dati che è possibile visualizzare nell'app.](./media/sharepoint/09-search.png)

Se si prevedono più dati, fare clic su Aggiorna dopo pochi secondi.

## <a name="capturing-user-id"></a>Acquisizione dell'ID utente
Il frammento di codice della pagina Web standard non acquisisce l'ID utente da SharePoint. Per l'acquisizione è necessario eseguire una piccola modifica.

1. Copiare la chiave di strumentazione dell'app dall'elenco a discesa Informazioni di base in Application Insights. 

    ![Screenshot che mostra la copia della strumentazione dell'app dall'elenco a discesa Essentials in Application Insights.](./media/sharepoint/02-props.png)

1. Sostituire la chiave di strumentazione per "XXXX" nel frammento di codice riportato di seguito. 
2. Incorporare lo script nell'app di SharePoint anziché il frammento di codice ottenuto dal portale.

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



## <a name="next-steps"></a>Passaggi successivi
* [Test web](../../azure-monitor/app/monitor-web-app-availability.md) per monitorare la disponibilità del sito.
* [Application Insights](../../azure-monitor/app/app-insights-overview.md) per altri tipi di app.

<!--Link references-->
