       <properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="vs-what-happened" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Per iniziare](/documentation/articles/vs-mobile-services-dotnet-getting-started/)
> - [Risultati](/documentation/articles/vs-mobile-services-dotnet-what-happened/)

###<span id="whathappened">Che cosa è successo al progetto?</span>

#####Aggiunta di riferimenti

Il pacchetto NuGet per Servizi mobili di Azure è stato aggiunto al progetto. Sono stati quindi aggiunti al progetto i riferimenti a .NET seguenti:

- `Microsoft.WindowsAzure.Mobile`
- `Microsoft.WindowsAzure.Mobile.Ext`
- `Newtonsoft.Json`
- `System.Net.Http.Extensions`
- `System.Net.Http.Primitives` 

#####Valori della stringa di connessione per Servizi mobili

Nel file App.xaml.cs è stato creato un oggetto **MobileServiceClient** con l'URL applicazione e la chiave applicazione del servizio mobile selezionato. 

[Altre informazioni sui servizi mobili](http://azure.microsoft.com/documentation/services/mobile-services/)
