<properties 
	pageTitle="Come usare più client con un singolo back-end di Servizi mobili | Servizi mobili di Azure" 
	description="Informazioni su come usare un singolo back-end del servizio mobile da più app client per diverse piattaforme mobili, ad esempio Windows Store e Windows Phone." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor="mollybos"/> <tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/25/2015" 
	ms.author="glenga"/>

# Supporto di piattaforme per più dispositivi da un singolo servizio mobile
 
Uno dei principali vantaggi offerto da Servizi mobili di Azure nello sviluppo di app per dispositivi mobili è la possibilità di usare un unico servizio back-end che supporta un'app su più piattaforme client. Servizi mobili fornisce librerie client native per le principali piattaforme per dispositivi, semplificando lo sviluppo di app mediante l'uso di un unico servizio back-end e di strumenti multipiattaforma. Questo argomento illustra gli aspetti relativi all'esecuzione di app su più piattaforme client usando al tempo stesso un unico servizio mobile back-end: Per informazioni di carattere generale su Servizi mobili, fare riferimento al [Centro sviluppatori di Servizi mobili](/documentation/services/mobile-services/).

##<a id="push"></a>Notifiche push multipiattaforma

Servizi mobili usa Hub di notifica di Azure per inviare notifiche push alle app client su tutte le principali piattaforme client. Hub di notifica fornisce un'infrastruttura coerente e unificata per la creazione e la gestione delle registrazioni dei dispositivi e per l'invio di notifiche multipiattaforma. Hub di notifica supporta l'invio di notifiche push usando i seguenti servizi di notifica specifici della piattaforma:

+ APNS (Apple Push Notification Service) per app per iOS
+ GCM (Google Cloud Messaging) per app per Android
+ WNS (Windows Notification Service) per app di Windows Store, Windows Phone 8.1 Store e Windows universale 
+ MPNS (Microsoft Push Notification Service) per app di Windows Phone Silverlight

>[AZURE.NOTE]Attualmente, Hub di notifica non include il supporto di WNS per l'invio di notifiche push ad app di Windows Phone Silverlight 8.1. Per inviare notifiche ad app di Silverlight e Windows Phone 8.0 e 7.0 è necessario usare MPNS.

Per ulteriori informazioni, vedere [Hub di notifica di Azure].

Le registrazioni client vengono create usando la funzione di registrazione della libreria client di Servizi mobili specifica della piattaforma oppure mediante le API REST di Servizi mobili. Hub di notifica supporta due tipi di registrazione dei dispositivi:

+ **Registrazione nativa**<br/>Le registrazioni native sono personalizzate in base al servizio di notifica push specifico della piattaforma. Quando si inviano notifiche a dispositivi registrati usando registrazioni native, è necessario chiamare API specifiche della piattaforma nel servizio mobile in uso. Per inviare una notifica a dispositivi su più piattaforme, è necessario eseguire più chiamate specifiche della piattaforma.   
  
+ **Registrazione modello**<br/>Hub di notifica supporta anche registrazioni modello specifiche della piattaforma. Grazie alle registrazioni modello, è possibile inviare una notifica a un'app in esecuzione su qualsiasi piattaforma registrata eseguendo una sola chiamata all'API. Per ulteriori informazioni, vedere [Utilizzo di Hub di notifica per inviare notifiche agli utenti tra piattaforme diverse].

>[AZURE.NOTE]Quando si tenta di inviare un messaggio a una piattaforma nativa per dispositivi per cui non esistono registrazioni di dispositivi, si verifica un errore. Questo non avviene quando si inviano notifiche modello.

Le tabelle riportate nelle sezioni seguenti forniscono collegamenti a esercitazioni specifiche del client che mostrano come implementare notifiche push da servizi mobili back-end sia .NET sia JavaScript.

###Back-end .NET

In un servizio mobile back-end .NET le notifiche vengono inviate chiamando il metodo [SendAsync] sull'oggetto [PushClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.pushclient.aspx) ottenuto dalla proprietà [ApiServices.Push](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.apiservices.push.aspx). La notifica push inviata (nativa o modello) dipende dallo specifico oggetto derivato da [IPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.ipushmessage.aspx) che viene passato al metodo [SendAsync], come mostrato nella tabella seguente:

|Piattaforma |[APNS](mobile-services-dotnet-backend-ios-get-started-push.md)|[GCM](mobile-services-dotnet-backend-android-get-started-push.md) |[WNS](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md) |[MPNS](mobile-services-dotnet-backend-windows-phone-get-started-push.md)|
|-----|-----|----|----|-----|
|Nativa|[ApplePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.applepushmessage.aspx) |[GooglePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.googlepushmessage.aspx) |[WindowsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.windowspushmessage.aspx) | [MpnsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.mpnspushmessage.aspx) |

Il codice seguente invia una notifica push da un servizio back-end .NET a tutte le registrazioni di dispositivi iOS e Windows Store:

	// Define a push notification for APNS.
	ApplePushMessage apnsMessage = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));    

	// Define a push notification for WNS.
	WindowsPushMessage wnsMessage = new WindowsPushMessage();
    wnsMessage.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                         @"<toast><visual><binding template=""ToastText01"">" +
                         @"<text id=""1"">" + item.Text + @"</text>" +
                         @"</binding></visual></toast>";
    
	// Send push notifications to all registered iOS and Windows Store devices. 
    await Services.Push.SendAsync(apnsMessage);
	await Services.Push.SendAsync(wnsMessage);

Per esempi di come inviare notifiche push alle altre piattaforme client native, fare clic sui collegamenti relativi alla piattaforma nell'intestazione della tabella riportata sopra.

Quando si usano registrazioni client modello anziché registrazioni client native, è possibile inviare la stessa notifica con una singola chiamata a [SendAsync] fornendo un oggetto [TemplatePushMessage], come mostrato di seguito:

	// Create a new template message and add the 'message' parameter.    
	var templatePayload = new TemplatePushMessage();
    templatePayload.Add("message", item.Text);

	// Send a push notification to all template registrations.
    await Services.Push.SendAsync(templatePayload); 
 
###Back-end JavaScript

In un servizio mobile back-end JavaScript le notifiche vengono inviate chiamando il metodo **send** sull'oggetto specifico della piattaforma ottenuto dall'[oggetto push] globale, come mostrato nella tabella seguente:

|Piattaforma |[APNS](mobile-services-javascript-backend-ios-get-started-push.md)|[GCM](mobile-services-javascript-backend-android-get-started-push.md) |[WNS](mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) |[MPNS](mobile-services-javascript-backend-windows-phone-get-started-push.md)|
|-----|-----|----|----|-----|
|Nativa|[Oggetto APNS](http://msdn.microsoft.com/library/azure/jj839711.aspx) |[Oggetto GCM](http://msdn.microsoft.com/library/azure/dn126137.aspx) |[Oggetto WNS](http://msdn.microsoft.com/library/azure/jj860484.aspx) | [Oggetto MPNS](http://msdn.microsoft.com/library/azure/jj871025.aspx) |

Il codice seguente invia una notifica push a tutte le registrazioni Android e Windows Phone:

	// Define a push notification for GCM.
	var gcmPayload = 
    '{"data":{"message" : item.text }}';

	// Define the payload for a Windows Phone toast notification.
	var mpnsPayload = '<?xml version="1.0" encoding="utf-8"?>' +
    '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
    '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text + 
    '</wp:Text2></wp:Toast></wp:Notification>';

	// Send push notifications to all registered Android and Windows Phone 8.0 devices. 
	push.mpns.send(null, mpnsPayload, 'toast', 22, {
            success: function(pushResponse) {
                // Push succeeds.
                },              
                error: function (pushResponse) {
                    // Push fails.
                    }
                });
    push.gcm.send(null, gcmPayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },              
                error: function (pushResponse) {
                    // Push fails.
                    }
                });

Per esempi di come inviare notifiche push alle altre piattaforme client native, fare clic sui collegamenti relativi alla piattaforma nell'intestazione della tabella riportata sopra.

Quando si usano registrazioni client modello anziché registrazioni client native, è possibile inviare la stessa notifica con una singola chiamata della funzione **send** nell'[oggetto push] globale fornendo un payload di messaggio modello, come mostrato di seguito:

	// Create a new template message with the 'message' parameter.    
	var templatePayload = { "message": item.text };

	// Send a push notification to all template registrations.
    push.send(null, templatePayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },              
                error: function (pushResponse) {
                    // Push fails.
                    }
                }); 

##<a id="xplat-app-dev"></a>Sviluppo di app multipiattaforma
Lo sviluppo di app native per dispositivi mobili per tutte le principali piattaforme per dispositivi mobili richiede da parte dell'utente (o dell'organizzazione dell'utente) conoscenza ed esperienza dei seguenti linguaggi di programmazione: Objective-C, Java, C# o JavaScript. A causa dei costi di sviluppo su più piattaforme diverse, alcuni sviluppatori scelgono per le app una soluzione completamente basata su Web browser. Queste soluzioni di sviluppo basate sul Web, tuttavia, non consentono l'accesso alla maggior parte delle risorse native in grado di fornire l'esperienza d'uso a cui gli utenti di dispositivi mobili sono ormai abituati.

Sono disponibili strumenti multipiattaforma che consentono di sviluppare app per dispositivi mobili con le caratteristiche desiderate dagli utenti, condividendo al tempo stesso un unico codice base, in genere JavaScript. Servizi mobili facilita la creazione e la gestione di un servizio back-end per piattaforme di sviluppo di app multipiattaforma fornendo esercitazioni con guide rapide per le seguenti piattaforme di sviluppo:

+ [**Appcelerator**](http://go.microsoft.com/fwlink/p/?LinkId=509987)<br/>Appcelerator consente di usare JavaScript per sviluppare una singola app compilata per essere eseguita come nativa su tutte le piattaforme per dispositivi mobili. Questo assicura la creazione di un'interfaccia utente di facile utilizzo, l'accesso a tutte le risorse native del dispositivo e le prestazioni tipiche delle app native. Per altre informazioni, vedere l'argomento sull'[esercitazione con guida rapida relativa ad Appcelerator][Appcelerator].
 
+ [**PhoneGap**](https://go.microsoft.com/fwLink/p/?LinkID=390707)**/**[**Cordova**](http://cordova.apache.org/)<br/>PhoneGap (una distribuzione del progetto Apache Cordova) è un framework gratuito e open source che consente di usare API Web standardizzate, HTML e JavaScript per sviluppare una singola app eseguibile su dispositivi Android, iOS e Windows. PhoneGap fornisce un'interfaccia utente basata su visualizzazione Web, ma con un'esperienza d'uso avanzata grazie alla possibilità di accesso alle risorse native del dispositivo, ad esempio le notifiche push, l'accelerometro, la fotocamera, gli spazi di archiviazione, la georilevazione e il browser integrato nell'app. Per altre informazioni, vedere l'argomento sull'[esercitazione con guida rapida relativa a PhoneGap][PhoneGap].
	
	Visual Studio consente ora di realizzare app Cordova multipiattaforma usando l'estensione Multi-Device Hybrid Apps per Visual Studio, un software in versione preliminare. Per altre informazioni, vedere l'[introduzione all'uso di Multi-Device Hybrid Apps con HTML e JavaScript](http://msdn.microsoft.com/library/dn771545.aspx).

+ [**Sencha Touch**](http://go.microsoft.com/fwlink/p/?LinkId=509988)<br/>Sencha Touch offre un insieme di controlli, ottimizzati per touch-screen, che consentono di offrire un'esperienza utente simile a quella delle app native su un'ampia gamma di dispositivi mobili partendo da un'unica base di codice HTML e JavaScript. È possibile usare Sencha Touch in combinazione con le librerie di PhoneGap o Cordova per consentire agli utenti di accedere alle risorse native dei dispositivi. Per altre informazioni, vedere l'argomento sull'[esercitazione con guida rapida relativa a Sencha Touch][Sencha].

+ [**Xamarin**](https://go.microsoft.com/fwLink/p/?LinkID=330242)<br/>Xamarin consente di creare app completamente native per dispositivi sia iOS sia Android, con un'interfaccia utente completamente nativa e accesso a tutte le risorse del dispositivo. Le app di Xamarin sono sviluppate in C# anziché in Objective-C e Java. Questo consente agli sviluppatori .NET di pubblicare app per iOS e Android e di condividere il codice di progetti Windows. Xamarin offre agli utenti l'esperienza di app completamente native su dispositivi sia iOS sia Android partendo da codice C#. Questo consente di riutilizzare parte del codice di Servizi mobili derivante da app per Windows su dispositivi sia iOS che Android. Per altre informazioni, vedere la sezione [Sviluppo con Xamarin](#xamarin) riportata di seguito.

	È possibile creare app di Xamarin usando Xamarin Studio o Visual Studio 2013. Per altre informazioni, vedere l'argomento relativo allo [sviluppo multipiattaforma in Visual Studio](http://msdn.microsoft.com/library/dn771552.aspx).


##<a id="shared-vs"></a>Condivisione e riutilizzo del codice in progetti di Visual Studio

Servizi mobili include una libreria client .NET, ovvero una PCL (Portable Class Library) di NET Framework che supporta lo sviluppo su tutte le piattaforme Windows. Per altre informazioni, vedere l'argomento relativo all'[uso di un client .NET con Servizi mobili]. Questo facilita il riutilizzo di parte del codice di Servizi mobili, ad esempio per l'accesso ai dati o l'autenticazione, in più progetti C#.

Un approccio generale per la condivisione e il riutilizzo di codice C# tra diversi progetti consiste nell'implementare un modello MVVM (Model-View-ViewModel) e condividere assembly su più piattaforme. È possibile implementare le classi di questo modello in un progetto di libreria di classi portabile in Visual Studio, quindi creare viste personalizzate per piattaforme diverse. Il codice del modello, comune per tutte le piattaforme, può ad esempio recuperare i dati da un'origine, ad esempio il servizio mobile in uso, in modo indipendente dalla piattaforma. MSDN Library include una <a href="http://msdn.microsoft.com/library/gg597391(v=vs.110)">panoramica e un esempio</a>, una descrizione delle <a href="http://msdn.microsoft.com/library/gg597392(v=vs.110)">differenze tra le API</a>, un esempio di <a href="http://msdn.microsoft.com/library/hh563947(v=vs.110)">uso della libreria di classi portabile con MVVM (Model-View-View Model)</a>, ulteriori <a href="http://msdn.microsoft.com/library/windowsphone/develop/jj714086(v=vs.105).aspx">istruzioni</a> e informazioni sulla <a href="http://msdn.microsoft.com/library/hh871422(v=vs.110)">gestione delle risorse</a> nei progetti di libreria di classi portabile.

Oltre a queste istruzioni di carattere generale, Visual Studio fornisce anche funzionalità specifiche per il riutilizzo del codice di Servizi mobili su diversi progetti di app client, argomento che verrà discusso nelle sezioni seguenti. Per informazioni più generali sull'uso di Visual Studio 2013 per la creazione di app multipiattaforma, vedere l'argomento relativo allo [sviluppo multipiattaforma in Visual Studio](http://msdn.microsoft.com/library/dn771552.aspx).

### App Windows universali

Visual Studio 2013 Update 2 include il supporto per progetti di app Windows universali. Le app universali sono soluzioni che includono progetti di app di Windows Store 8.1 e Windows Phone Store 8.1, oltre a un progetto di codice condiviso. In questo tipo di progetto, il codice condiviso viene trattato come parte sia dei progetti Windows Store sia di quelli Windows Phone. Per altre informazioni, vedere [Creazione di app Windows universali per tutti i dispositivi Windows]. Le app Windows universali possono essere scritte sia in C#/XAML sia in JavaScript/HTML.

Per impostazione predefinita, la scheda Quickstart disponibile nel [portale di gestione di Azure] genera, per iniziare, una versione "app Windows universale" dell'app di esempio TodoList. È possibile scegliere di scaricare una versione C#/XAML o una versione JavaScript/HTML del progetto. Per altre informazioni, vedere l'[introduzione a Servizi mobili](../mobile-services-windows-store-get-started.md).

>[AZURE.NOTE]La versione C# del progetto di app di guida introduttiva disponibile nella scheda Guida introduttiva del portale di gestione condivide la pagina code-behind MainPage.xaml.cs, ma non usa un modello di visualizzazione. Per un esempio dell'app TodoList come progetto di app Windows universale in C# che usa MVVM, vedere l'argomento relativo a un [progetto di app Windows universale per Servizi mobili di Azure mediante MVVM].

###<a id="xamarin"></a>Sviluppo con Xamarin

È possibile sfruttare al massimo l'esperienza di sviluppo in Visual Studio e C# usando Xamarin e Visual Studio o Xamarin Studio per creare app per iOS e Android. Xamarin usa un'implementazione multipiattaforma di .NET Framework che permette di utilizzare codice C# per sviluppare app per iOS e Android. Con Xamarin è possibile riutilizzare il codice esistente di progetti Windows in cui la libreria client .NET di Servizi mobili viene usata per accedere al servizio mobile in uso. Per altre informazioni, vedere l'argomento relativo allo [sviluppo multipiattaforma in Visual Studio](http://msdn.microsoft.com/library/dn771552.aspx).

Per iniziare a creare app di Xamarin che usano Servizi mobili, vedere le esercitazioni con guide rapide di Xamarin ([iOS](mobile-services-ios-get-started.md) / [Android](mobile-services-android-get-started.md)).


### App di Windows Store e Windows Phone Silverlight

Per lo sviluppo di app, in Windows Phone 8.1 è possibile scegliere di usare il precedente XAML basato su Silverlight oppure lo XAML basato su Windows Runtime, che consente la creazione di app Windows universali. Per altre informazioni sulle app di Windows Phone 8.1 Silverlight e Windows Phone Store 8.1, vedere l'argomento relativo alle [novità di Windows Phone 8 per gli sviluppatori].

La libreria client .NET di Servizi mobili supporta app sia di Windows Phone Store 8.1 sia di Windows Phone Silverlight 8.1. Poiché le app di Windows Runtime e Windows Phone Silverlight non possono essere sviluppate a partire dallo stesso progetto, si consiglia di adottare una strategia di riutilizzo del codice, ad esempio PCL e MVVM, come descritto sopra.

>[AZURE.NOTE]Per usare l'autenticazione client Single Sign-On tramite un account Microsoft nelle app per Windows Runtime Windows Phone Silverlight, è necessario innanzitutto registrare l'app per Windows Runtime nel dashboard di Windows Store. Dopo aver creato una registrazione Live Connect per Windows Phone non è infatti più possibile crearne una per Windows Store. Per ulteriori informazioni su come eseguire questa operazione, vedere l'argomento **Autenticazione dell'app di Windows Store con l'accesso Single Sign-On di Live Connect** ([Windows Store][SSO Windows Store]/[Windows Phone][SSO Windows Phone]).


<!-- URLs -->
[portale di gestione di Azure]: https://manage.windowsazure.com
[Hub di notifica di Azure]: /develop/net/how-to-guides/service-bus-notification-hubs/
[SSO Windows Store]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[SSO Windows Phone]: /develop/mobile/tutorials/single-sign-on-wp8/
[Tutorials and resources]: /develop/mobile/resources/
[Get started with Notification Hubs]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Utilizzo di Hub di notifica per inviare notifiche agli utenti tra piattaforme diverse]: /manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Get started with push Windows dotnet]: /develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
[Get started with push Windows js]: /develop/mobile/tutorials/get-started-with-push-js-vs2012/
[Get started with push Windows Phone]: /develop/mobile/tutorials/get-started-with-push-wp8/
[Get started with push iOS]: /develop/mobile/tutorials/get-started-with-push-ios/
[Get started with push Android]: /develop/mobile/tutorials/get-started-with-push-android/
[Dynamic schema]: http://msdn.microsoft.com/library/windowsazure/jj193175.aspx
[uso di un client .NET con Servizi mobili]: documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[oggetto push]: http://msdn.microsoft.com/library/windowsazure/jj554217.aspx
[TemplatePushMessage]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.templatepushmessage.aspx
[PhoneGap]: mobile-services-javascript-backend-phonegap-get-started.md
[Sencha]: partner-sencha-mobile-services-get-started.md
[Appcelerator]: ../partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started.md
[SendAsync]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[novità di Windows Phone 8 per gli sviluppatori]: http://msdn.microsoft.com/library/windows/apps/dn655121(v=vs.105).aspx
[Creazione di app Windows universali per tutti i dispositivi Windows]: http://go.microsoft.com/fwlink/p/?LinkId=509905
[progetto di app Windows universale per Servizi mobili di Azure mediante MVVM]: http://code.msdn.microsoft.com/Universal-Windows-app-for-db3564de

<!--HONumber=54--> 