---
title: App demo di Azure Mobile Engagement | Microsoft Docs
description: Descrive come scaricare e usare l&quot;app demo di Azure Mobile Engagement e i vantaggi che offre.
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: f624d5aa-254b-4ad0-96a3-f00e6c3a2c97
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8381edb569e19a85c1259f7791b477cfa6e51ea3


---
# <a name="azure-mobile-engagement-demo-app"></a>App demo di Azure Mobile Engagement
Microsoft ha pubblicato un'app demo di Azure Mobile Engagement per le piattaforme **iOS**, **Android** e **Windows** che consente di trovare facilmente risorse utili e altre informazioni su Mobile Engagement.

L'app consente di:

* Trovare facilmente collegamenti utili a risorse di Mobile Engagement quali video, documentazione, forum di supporto e dove è possibile inserire richieste di funzionalità.
* Provare notifiche di esempio supportate da Mobile Engagement per ottenere idee per le proprie applicazioni per dispositivi mobili.
* Usare un'implementazione di riferimento per studiare le modalità di implementazione di Mobile Engagement nella propria app. È possibile apprendere come:
  
  * Raccogliere dati di analisi.
  * Implementare scenari di notifica avanzati di tipi come *Schermo intero intermedio* o *Popup*.
  * Implementare indagini e sondaggi.
  * Implementare scenari di push di dati e push non interattivi.   

## <a name="app-installation"></a>Installazione di app
Questa app è disponibile negli App Store seguenti:

* **App universale di Windows - Demo**:
  
  * Scaricare l'app dall’[archivio applicazioni di Windows](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2).
  * L'app è stata sviluppata come app universale di Windows 10. Il codice sorgente è disponibile in [GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows).
* **App demo per iOS**:
  
  * Scaricare l'app dall’ [Apple store](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090).
  * L'app è stata sviluppata in iOS Swift. Il codice sorgente è disponibile in [GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios).
* **App demo per Android**:
  
  * Scaricare l'app dal [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement).
  * Il codice sorgente è disponibile in [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android).

![App universale di Windows - Demo][1]

![App demo per iOS][2]
![App demo per Android][3]

## <a name="usage"></a>Uso
È possibile usare l’app nei modi seguenti:

**Scaricare l’app nel dispositivo dai collegamenti all'archivio applicazioni riportati sopra:**

> [!IMPORTANT]
> Non è necessario un account Azure né connettere l'app a un back-end. L'app funzionerà in modo indipendente.
> 
> 

* Dopo avere installato l'app nel dispositivo, è possibile usare i collegamenti nel menu a sinistra per trovare le risorse utili relative a Mobile Engagement.
* È stato aggiunto il [feed RSS del servizio](https://aka.ms/azmerssfeed) nell'applicazione per essere sempre aggiornati sugli ultimi aggiornamenti del prodotto.
* È anche possibile scorrere gli scenari di notifica di esempio per provare il tipo di notifiche supportate da Mobile Engagement per ogni piattaforma. Queste notifiche possono essere provate in locale, ovvero è possibile fare clic sui pulsanti disponibili nelle schermate per vedere il funzionamento delle notifiche, che sarà identico all'invio delle notifiche dalla piattaforma Mobile Engagement.

![Menu dell’app per Windows][4]

![Menu dell'app per iOS][5]
![Menu dell'app per Android][6]

**Scaricare il codice sorgente dai collegamenti a GitHub riportati sopra:**

* Dopo aver scaricato il codice sorgente, aprirlo nell'ambiente di sviluppo corrispondente, ovvero XCode per iOS, Android Studio per Android e Visual Studio per Windows.
* Si dovrà quindi seguire la [procedura di integrazione dell'SDK di base](mobile-engagement-windows-store-dotnet-get-started.md) per poter connettere questa app alla relativa istanza del back-end di Mobile Engagement.
  * È necessario configurare una stringa di connessione nell'app.
  * È anche necessario configurare la piattaforma di notifica push per l'app.
* Si noterà che questa app è instrumentata con Mobile Engagement. Quindi, quando si apre l'app dopo la connessione al back-end, si potranno visualizzare la sessione utente, le attività, gli eventi e così via nella scheda **Monitoraggio** .
* Si potranno anche inviare notifiche all'app dalla propria istanza di Mobile Engagement invece di usare notifiche locali.
  
  * Qui è possibile aggiungere il dispositivo come dispositivo di test usando la voce di menu **Get the Device ID** dell’app. In questo modo viene fornito un ID dispositivo da registrare come dispositivo di test con l'istanza del back-end della piattaforma.
    
    ![ID dispositivo in Windows][7]
    
    ![ID del dispositivo in iOS][8]
    ![ID del dispositivo in Android][9]

## <a name="key-features-of-the-demo-app"></a>Funzionalità principali dell'app demo
* Come indicato in precedenza, con questa app si avranno a portata di mano tutte le risorse chiave per Mobile Engagement. È possibile scorrere i collegamenti nel menu a sinistra.
* Si possono provare le notifiche out-of-app per ogni piattaforma. Queste notifiche possono essere di tipo **Solo notifiche**, ovvero facendo clic sulla notifica verrà semplicemente aperta una schermata nativa dell'applicazione (tramite **deep linking**), oppure di tipo **Annuncio Web**. In questo caso sarà possibile distribuire contenuto HTML aggiuntivo dal back-end di Mobile Engagement che verrà visualizzato dopo aver selezionato la notifica.
  
    ![Notifiche out-of-app][29]
* In iOS è necessario chiudere l'app per visualizzare le notifiche out-of-app o le notifiche push di sistema. È possibile vedere l'implementazione per l'aggiunta di **pulsanti di azione** simili a quelli aggiunti a questa notifica out-of-app per *Feedback* (Commenti) e *Share* (Condividi), in modo che l'utente possa agire direttamente dalla notifica.
  
    ![Notifiche out-of-app in iOS][11] ![Visualizzazione di notifiche out-of-app in iOS][14]
* In Android le opzioni supportate sono l’aggiunta di testo multilinea (**Testo grande**) o di un'immagine (**Immagine grande**) alla notifica, insieme ai **pulsanti di azione** supportati da iOS.
  
    ![Notifiche out-of-app in Android][12] ![Visualizzazione di notifiche out-of-app in Android][15]
* In Windows 10 è possibile visualizzare l'aspetto delle notifiche sul PC. Questa notifica verrà visualizzata anche nel **Centro notifiche**di Windows 10. Al momento non è disponibile il supporto per l'aggiunta di **pulsanti di azione** in Windows SDK.
  
    ![Notifiche out-of-app in Windows][10] ![Visualizzazione out-of-app in Windows][13]
* Si possono provare le notifiche “in-app” per ogni piattaforma. Si tratta di un'operazione in due fasi in cui viene prima visualizzata una finestra di **notifica** . Quando si fa clic su di essa, viene visualizzata una schermata a schermo intero di **annuncio**, come illustrato nella schermata seguente. Il contenuto di questo annuncio deriva dall'istanza del back-end di Mobile Engagement. L’SDK include i modelli sia per le notifiche che per gli annunci. È possibile personalizzarli con facilità, come illustrato in questa app demo, con l'aggiunta del logo e di colori.  
  
    ![Notifiche in-app in Windows][16]
  
    ![Notifiche in-app in iOS][17]  ![Notifiche in-app in Android][18]
  
    **iOS**, **Android**
* È anche possibile usare la funzionalità **Categoria** di Mobile Engagement per personalizzare questa esperienza predefinita. Nell'app demo sono stati illustrati due modi comuni per modificare l'esperienza delle notifiche. Si noti che la funzionalità Categoria non è ancora supportata in Windows SDK.
  
    **Schermo intero intermedio:**
  
    ![Notifica in-app - Categoria schermo intermedio][30]
  
    ![Categoria schermo intermedio in iOS][21]     ![Categoria schermo intermedio in Android][22]
  
    **Notifica popup:**
  
    ![Notifica in-app - Categoria popup][31]
  
    ![Notifica popup in iOS][19]    ![Notifica popup in Android][20]

**iOS**, **Android**

* Mobile Engagement supporta anche un tipo specifico di notifica in-app chiamato **Sondaggi**, che consente di inviare sondaggi rapidi a utenti segmentati dell’app. È possibile aggiungere domande e opzioni per ogni domanda come illustrato nella schermata seguente. Il sondaggio verrà quindi visualizzato come notifica in-app per l'utente dell'app.   
  
    ![Notifiche di sondaggi][32]
  
    ![Sondaggi in Windows][26]
  
    ![Sondaggi in iOS][27]   ![Sondaggi in Android][28]

**iOS**, **Android**

* Mobile Engagement supporta anche l'invio automatico di notifiche **Push di dati**. Con queste notifiche è possibile inviare dati dal servizio (ad esempio dati JSON nell'esempio seguente), che possono essere gestiti nell'app e sottoposti a un’azione Ecco un esempio di come si modifica il prezzo di un elemento in modo selettivo usando la notifica push di dati.
  
    ![Notifica push di dati][33]
  
    ![Notifica push di dati in Windows][23]
  
    ![Notifica push di dati in iOS][24]  ![Notifica push di dati in Android][25]

**iOS**, **Android**

> [!NOTE]
> È possibile visualizzare istruzioni dettagliate per una qualsiasi di queste notifiche facendo clic su **Fare clic qui per ottenere istruzioni sull’invio di queste notifiche dalla piattaforma Mobile Engagement** in una schermata di notifica di esempio.
> 
> 

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



<!--HONumber=Nov16_HO3-->


