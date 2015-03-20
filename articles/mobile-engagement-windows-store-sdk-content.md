<properties 
	pageTitle="Contenuto dell'SDK Windows Store per Azure Mobile Engagement" 
	description="Ultimi aggiornamenti e procedure per l'SDK Windows Store per Azure Mobile Egagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#Contenuto SDK

In questo documento viene elencato e descritto il contenuto dell'archivio SDK.

##Cartella  `Root` 

Questa cartella contiene copie delle licenze software e un collegamento alla documentazione in linea.

`documentation.html` : Contiene un collegamento alla documentazione in linea nell'SDK Engagement per l'applicazione C\ # Metro per Windows 8.

Questa cartella contiene inoltre i file di licenza.

##Cartella  `/lib`

Questa cartella contiene informazioni su dove ottenere l'SDK Engagement.

`azuresdk-mobileengagement-windows-X.X.X.nupkg` : Pacchetto NuGet per l'integrazione SDK.

##Cartella  `/Resources`

Questa cartella contiene tutte le risorse richieste da Engagement. È inoltre possibile personalizzarle in modo da adattarle all'app.

`EngagementConfiguration.xml` : Il file di configurazione di Engagement, dove è possibile personalizzare le impostazioni di Engagement (stringa di connessione di Engagement, report di arresto anomalo...).

### Cartella /HTML

`EngagementNotification.html` : Il progetto html della visualizza Web  `Notifica`.

`EngagementAnnouncement.html` : Il progetto html della visualizzazione Web  `Annuncio` .

### /images folder

`EngagementIconNotification.png` : L'icona del marchio visualizzata a sinistra di una notifica.

`EngagementIconOk.png` : L'icona  `Ok` delle pagine di contenuto di copertura per il pulsante di azione o di convalida.

`EngagementIconNOK.png` : L'icona  `NOK` utilizzata quando il pulsante di convalida delle pagine di contenuto di portata è disabilitato.

`EngagementIconClose.png` : L'icona  `Chiudi` delle notifiche di copertura e il contenuto per il pulsante Elimina.

### Cartella /overlay 

`EngagementOverlayAnnouncement.xaml` : Il progetto xaml di  `Annuncio`.

`EngagementOverlayAnnouncement.xaml.cs` : Il codice collegato  `EngagementOverlayAnnouncement.xaml`.

`EngagementOverlayNotification.xaml` : Il progetto xaml di  `Notifica`.

`EngagementOverlayNotification.xaml.cs` : Il codice collegato  `EngagementOverlayNotification.xaml`.

`EngagementPageOverlay.cs` : Il codice di visualizzazione annunci e notifiche di  `Sovrapposizione`.

##Cartella  `/src/agent`

Questa cartella contiene EngagementPage.

`EngagementPage.cs` : La classe base per le pagine che consentono di segnalare automaticamente un'attività a Engagement.

<!--HONumber=47-->
