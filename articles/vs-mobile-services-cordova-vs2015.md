<properties 
	pageTitle="" 
	description="Using mobile services in Cordova projects" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/11/2014" 
	ms.author="patshea"/>

### Uso di Servizi mobili con progetti Cordova in Visual Studio 2015 Preview

Per poter usare Servizi mobili di Azure in progetti Cordova in Visual Studio 2015 Preview, è necessario applicare la seguente soluzione alternativa.

1. Nel progetto Cordova di Visual Studio 2015 Preview aprire Config.xml e nella scheda **Plug-in** abilitare il plug-in **Servizi mobili di Microsoft Azure**.<br/>
![][1]

2. In index.html eliminare le righe che fanno riferimento a **MobileServices.Web-1.2.2.min.js**.<br/>

<PRE style="prettyprint">
&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
    &lt;meta charset="utf-8" /&gt;
    &lt;title&gt;MyCordovaApp&gt;/title&gt;

    &lt;!--MyCordovaApp references --&gt;
    &lt;link href="css/index.css" rel="stylesheet" /&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;p&gt;Hello world!&lt/p&gt;

    &lt;!--Cordova reference, this is added to your app when it's build. --&gt;
    &lt;script src="cordova.js"&gt;&lt;/script&gt;
    &lt;script src="scripts/platformOverrides.js"&gt;&lt;/script&gt;

    &lt;script src="scripts/index.js"&gt;&lt;/script&gt;

    &lt;!-- yourservicename references --&gt;
    <span style="background-color:yellow">&lt;script src="//ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.2.min.js"&gt;&lt/script&gt;</span>
    &lt;script src="/services/mobileservices/settings/yourservicename.js"&gt;&lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;
</PRE>

3. Aprire {yourservicename}.js nelle cartelle services -> mobileServices -> settings e sostituire il frammento di codice esistente con quanto segue:

		// http://go.microsoft.com/fwlink/?LinkID=290993&clcid=0x409
		var {yourservicename}Client;
		document.addEventListener("deviceready", function () {    
            mhadetestserviceClient = new WindowsAzure.MobileServiceClient(
                    "https://{yourservicename}.azure-mobile.net/",
                    "<your application key>");
		});

[1]: ./media/vs-mobile-services-cordova-vs2015/AzureMobileServicesPlugIn.png 

<!--HONumber=42-->
