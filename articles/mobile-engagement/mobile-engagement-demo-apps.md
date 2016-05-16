<properties
	pageTitle="App demo di Azure Mobile Engagement"
	description="Descrive come scaricare, usare e trarre vantaggio dall'uso dell'app demo di Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="erikre" 
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2016"
	ms.author="piyushjo" />

# App demo di Azure Mobile Engagement

Microsoft ha pubblicato un'app demo di Azure Mobile Engagement per le piattaforme **iOS**, **Android** e **Windows** che consente di trovare facilmente risorse utili e altre informazioni su Azure Mobile Engagement.

L'app consente di:

1. Trovare facilmente collegamenti utili a risorse specifiche di Azure Mobile Engagement quali video, documentazione, forum di supporto, dove è possibile inserire richieste di funzionalità e così via. 
2. Provare notifiche di esempio supportate da Azure Mobile Engagement per ottenere idee per le proprie applicazioni per dispositivi mobili. 
3. Offre un'implementazione di riferimento che può essere usata per studiare le modalità di implementazione di Mobile Engagement nell'app per: 

	- Raccogliere dati di analisi 
	- Implementare scenari di notifica avanzati di tipi come *Schermo intero intermedio* o *Popup*
	- Implementare i sondaggi/indagini
	- Implementare scenari di push di dati/push non interattivi   

## Installazione di app
Queste app sono disponibili nei rispettivi App Store:

1. **App universale di Windows - Demo**

	- [Collegamento per il download dell'Archivio applicazioni di Windows ](https://www.microsoft.com/it-IT/store/apps/azure-mobile-engagement/9nblggh4qmh2) 
	- L'app è stata sviluppata come app universale di Windows 10 e il codice sorgente è disponibile in [Github](https://github.com/Azure/azure-mobile-engagement-app-ios)

2. **App demo per iOS**

	- [Collegamento per il download ad Apple Store](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090) 
	- L'app è stata sviluppata in iOS Swift e il codice sorgente è disponibile in [Github](https://github.com/Azure/azure-mobile-engagement-app-ios)

3. **App demo per Android**

	- [Collegamento per il download a Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement)
	- Il codice sorgente è disponibile in [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android)

![][1]

![][2] ![][3]


## Utilizzo

È possibile usare queste app nei modi seguenti:

**1) Scaricare le app nel dispositivo dai collegamenti all'archivio applicazioni riportati sopra.**

>[AZURE.IMPORTANT] Non è necessario un account Azure né connettere l'app a qualsiasi back-end. L'app funzionerà in modo indipendente.

- Dopo avere installato l'app nel dispositivo è possibile usare i collegamenti nel menu a sinistra per trovare tutte le risorse utili relative ad Azure Mobile Engagement. 
- È stato anche aggiunto anche il [feed RSS del servizio](https://aka.ms/azmerssfeed) direttamente nell'applicazione per essere sempre aggiornati sugli ultimi aggiornamenti dei prodotti.
- È anche possibile scorrere gli scenari di notifica di esempio per provare il tipo di notifiche supportate da Azure Mobile Engagement per ogni piattaforma. Queste notifiche possono essere provate in locale, ovvero è possibile fare clic sui pulsanti disponibili nelle schermate per vedere il funzionamento delle notifiche, che sarà identico all'invio delle notifiche dalla piattaforma Azure Mobile Engagement. 

![][4]
    
![][5] ![][6]

**2) Scaricare il codice sorgente dai collegamenti a Github riportati sopra.**

- Dopo aver scaricato il codice sorgente, aprirlo nell'ambiente di sviluppo corrispondente, ovvero XCode per iOS, Android Studio per Android e Visual Studio per Windows. 
- Si dovrà quindi seguire la [procedura di integrazione dell'SDK di base](mobile-engagement-windows-store-dotnet-get-started.md) per poter connettere questa app alla relativa istanza del back-end di Mobile Engagement. 
	- Sarà necessario configurare una stringa di connessione nell'app.  
	- Sarà anche necessario configurare la piattaforma di notifica push per l'app. 
- Si noterà che questa app è instrumentata con Azure Mobile Engagement e quindi quando si apre l'app dopo la connessione back-end, si potranno visualizzare la sessione utente, le attività, gli eventi e così via nella scheda di monitoraggio. 
- Si potranno anche inviare notifiche all'app dalla propria istanza di Mobile Engagement invece di usare notifiche locali. 
	- Qui è possibile aggiungere il dispositivo come dispositivo di test usando la voce di menu **Get the Device ID** dell'app che fornirà un ID dispositivo da registrare come dispositivo di test con l'istanza del back-end della piattaforma. 

	![][7]
	    
	![][8] ![][9]

## Funzionalità principali dell'app demo

1. Come indicato in precedenza, con questa app si avranno tutte le risorse chiave per Azure Mobile Engagement a portata di mano. È possibile scorrere i collegamenti nel menu a sinistra. 

2. Esperienza notifiche out-of-app per ogni piattaforma. Queste notifiche possono essere fornite come **solo notifica**, ovvero facendo clic sulla notifica verrà semplicemente aperta una schermata nativa dell'applicazione nativa (tramite **deep linking**) o come **annuncio Web** che consente di distribuire il contenuto HTML aggiuntivo dal back-end di Mobile Engagement che verrà visualizzato quando si fa clic sulla notifica.

	![][29]

	
	- In iOS è necessario chiudere l'app per visualizzare le notifiche out-of-app o push di sistema. È possibile vedere l'implementazione per l'aggiunta di **pulsanti di azione** simili a quelle aggiunte a questa notificaout-of-app per *Feedback* e *Share* in modo che l'utente possa agire direttamente dalla notifica. 
	    
	![][11] ![][14]
	
	
	- In Android verranno visualizzate le opzioni supportate da Android sotto forma di aggiunta di testo su più righe (**Big Text**) o aggiunta di un'immagine nella notifica (**Big Picture**) per la notifica con i **pulsanti di azione** supportati anche da iOS. 
	
	![][12] ![][15]
	
	
	- In Windows 10 è possibile visualizzare l'aspetto delle sul PC. Questa notifica verrà visualizzata anche nel **Centro notifiche** in Windows 10. Al momento non è disponibile il supporto per l'aggiunta di **pulsanti di azione** con Windows SDK. 
	
	![][10] ![][13]

3. Provare le notifiche out-of-app predefinite per ogni piattaforma. Si tratta di un'esperienza in due fasi in cui una finestra **Notifica** viene visualizzata per prima quando si fa clic e viene aperta una finestra a schermo intero **Annuncio**, illustrata di seguito. Il contenuto di questo annuncio deriva dall'istanza del back-end di Mobile Engagement. L'SDK include i modelli per la notifica e l'annuncio, che possono essere personalizzati facilmente come in questa app demo con l'aggiunta di logo e colori.

	![][16]
	
	![][17] **iOS** ![][18] **Android**

4. È anche possibile usare la funzionalità **Categoria** di Azure Mobile Engagement per personalizzare questa esperienza predefinita. Nell'app demo sono stati illustrati due modi comuni per modificare l'esperienza delle notifiche. Si noti che la funzionalità Categoria non è ancora supportata in Windows SDK.

	**Schermo intero intermedio**
	
	![][30]

	![][21] ![][22]

	**Notifica popup**

	![][31]
	
	![][19] ![][20]

5. Azure Mobile Engagement supporta anche un tipo specializzato di notifica in-app chiamato **Sondaggi** che consente di inviare sondaggi rapidi a utenti segmentati dell'app. È possibile aggiungere domande e opzioni per ogni domande, in modo analogo alle seguenti, che verranno quindi visualizzate come notifica in-app per l'utente dell'app.

	![][32]

	![][26]
	    
	![][27] **iOS** ![][28] **Android**

5. Azure Mobile Engagement supporta anche l'invio non interattivo di notifiche **Push dei dati** all'utente, in cui è possibile inviare dati dal servizio, ad esempio i dati JSON nell'esempio seguente, che è possibile gestire nell'app e intraprendere un'azione. Ad esempio, come viene modificato il prezzo di un elemento in modo selettivo usando la notifica Push di dati.

	![][33]

	![][23]
	    
	![][24] **iOS** ![][25] **Android**

> [AZURE.NOTE] Si noti che è possibile visualizzare istruzioni dettagliate per una qualsiasi di queste notifiche facendo clic su *Click here for instructions on how to send these notifications from Mobile Engagement platform* in una schermata di notifica di esempio.


[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png

<!---HONumber=AcomDC_0504_2016-->