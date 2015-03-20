<properties 
	pageTitle="Integrazione dell'SDK Windows Phone per Azure Mobile Engagement" 
	description="Informazioni sul contenuto dell'SDK Windows Phone SDK per Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />
	
#Contenuto SDK

In questo documento viene elencato e descritto il contenuto dell'archivio SDK.

##Cartella  `Root`

Questa cartella contiene copie delle licenze software e un collegamento alla documentazione in linea.

`documentation.html` : Contiene un collegamento alla documentazione in linea dell'SDK Engagement per Windows Phone.

Questa cartella contiene inoltre i file di licenza.

##Cartella  `/lib`

Questa cartella contiene informazioni su dove ottenere l'SDK Engagement.

`azuresdk-mobileengagement-windowsphone-X.X.X.nupkg` : Pacchetto NuGet per l'integrazione SDK.

##Cartella  `/Resources`

Questa cartella contiene tutte le risorse richieste da Engagement. È inoltre possibile personalizzarle in modo da adattarle all'app.

`EngagementIconNotification.png` : L'icona del marchio visualizzata a sinistra di una notifica.

`EngagementIconOk.png` : L'icona "Ok" della barra delle applicazioni delle pagine di copertura.

`EngagementIconCancel.png` : L'icona "Annulla" della barra delle applicazioni delle pagine di copertura.

`EngagementIconCloseLight.png` : L'icona "Chiudi" della notifica di copertura di Engagement per il tema chiaro di Windows Phone.

`EngagementIconCloseDark.png` : L'icona "Chiudi" della notifica di copertura di Engagement per il tema scuro di Windows Phone.

`EngagementConfiguration.xml` : Il file di configurazione di Engagement, dove è possibile personalizzare le impostazioni di Engagement (stringa di connessione di Engagement, report di arresto anomalo...).

##Cartella  `/src/agent`

Questa cartella contiene EngagementPage.

`EngagementPage.cs` : La classe base per le pagine che consentono di segnalare automaticamente un'attività a Engagement.

##Cartella  `/src/reach`

Infine, in questa cartella, è possibile trovare l'XAML predefinito (e le relative controparti C\#) di ogni pagina.

È possibile utilizzarle come base per le proprie pagine. Seguire i commenti per sapere come procedere esattamente.

### Annuncio TextView

`EngagementDefaultTextViewAnnouncementPage.xaml`

`EngagementDefaultTextViewAnnouncementPage.xaml.cs`

### Annuncio WebView

`EngagementDefaultWebViewAnnouncementPage.xaml`

`EngagementDefaultWebViewAnnouncementPage.xaml.cs`

### Sondaggio

`EngagementDefaultPollPage.xaml`

`EngagementDefaultPollPage.xaml.cs`

### Notifica

`EngagementBasicNotificationView.xaml`

`EngagementBasicNotificationView.xaml.cs`

<!--HONumber=47-->
