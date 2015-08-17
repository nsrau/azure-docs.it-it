<properties 
	pageTitle="Contenuto dell'SDK per app di Windows universali" 
	description="Informazioni sul contenuto di Azure Mobile Engagement SDK per app di Windows universali" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/06/2015" 
	ms.author="piyushjo" />

#Contenuto dell'SDK per app di Windows universali

Questo documento elenca e descrive il contenuto distribuito dall'SDK nell'applicazione.

##Cartella `/Resources`

Questa cartella contiene tutte le risorse richieste da Mobile Engagement. È inoltre possibile personalizzarle in modo da adattarle all'app.

- `EngagementConfiguration.xml`: il file di configurazione di Mobile Engagement, in cui è possibile personalizzare le impostazioni di Mobile Engagement (stringa di connessione di Mobile Engagement, segnalazione di arresto anomalo e così via).

### Cartella /html

- `EngagementNotification.html`: progettazione HTML della visualizzazione Web `Notification`.

- `EngagementAnnouncement.html`: progettazione HTML della visualizzazione Web `Announcement`.

### Cartella /images

- `EngagementIconNotification.png`: l'icona del marchio visualizzata a sinistra di una notifica. Sostituirla con un'icona personalizzata.

- `EngagementIconOk.png`: l'icona `Ok` delle pagine di contenuto Reach per il pulsante di azione o convalida.

- `EngagementIconNOK.png`: l'icona `NOK` usata quando il pulsante di convalida delle pagine di contenuto Reach è disabilitato.
 
- `EngagementIconClose.png`: l'icona `Close` per il pulsante che consente di ignorare il contenuto e le notifiche Reach.

### Cartella /overlay

- `EngagementOverlayAnnouncement.xaml`: progettazione XAML di `Announcement`.

- `EngagementOverlayAnnouncement.xaml.cs`: codice collegato di `EngagementOverlayAnnouncement.xaml`.
 
- `EngagementOverlayNotification.xaml`: progettazione XAML di `Notification`.
 
- `EngagementOverlayNotification.xaml.cs`: codice collegato di `EngagementOverlayNotification.xaml`.
 
- `EngagementPageOverlay.cs`: codice di visualizzazione annunci e notifiche di `Overlay`.
  

<!---HONumber=August15_HO6-->