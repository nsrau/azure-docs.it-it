---
title: Interfaccia utente di Azure Mobile Engagement - Procedure di Reach
description: Panoramica dell&quot;interfaccia utente di Azure Mobile Engagement
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 30af87e6-c816-4cce-8609-6cbd3e83de14
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 3a423e42441c6b3b2f1917ba02f80078c759ec69
ms.openlocfilehash: 4c114f470631ae6b06968c3cc8601bc1c76bc1da


---
# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>Come iniziare a usare e gestire le notifiche push per raggiungere gli utenti finali
Dopo l'integrazione completa dell'SDK nell'app, è possibile iniziare a usare la sezione Reach dell'interfaccia utente per inviare notifiche push agli utenti dell'app.  

## <a name="do-your-first-push-notification-campaign"></a>Creare la prima campagna di notifica push
* Verificare che Reach sia integrato nell'app con l'SDK. 
* Selezionare l'applicazione.

![First1][1]

* Andare alla sezione "Reach" e fare clic su "Nuovo annuncio".

![First2][2]

* Creare una nuova campagna e assegnarle un nome.
  
![First3][3]

* Selezionare la modalità di recapito della notifica, ad esempio Solo in-app.

![First4][4]

* Creare il messaggio di cui si desidera effettuare il push.

![First5][5]

* È possibile scrivere un titolo nella notifica (facoltativo).
* Scrivere il contenuto del messaggio push.
* È possibile caricare un'immagine. Tenere presente che le dimensioni del file non possono superare i 32.768 byte.
* È anche possibile selezionare altre opzioni, ma, per l'obiettivo di questa esercitazione, se ne parlerà in un secondo momento.
* Selezionare il tipo di contenuto Solo notifica.

![First6][6]

* Creare la campagna push che verrà visualizzata nell'elenco di campagne.

![First7][7]

## <a name="test-your-push-notification-campaign"></a>Testare la campagna di notifica push
![Test1][8]

* Registrare il dispositivo.
* Fare clic sulla casella di controllo del dispositivo di cui si vuole effettuare il push.
* Fare clic sul pulsante "Test" per inviare il push al dispositivo.

![Test2][9]

* Attivare la campagna.

![Test3][10]

* Ora che la campagna è stata creata, è sufficiente attivarla per effettuare il push della notifica agli utenti.

## <a name="send-personalized-pushes"></a>Inviare push personalizzati
* Questo esempio crea un push in cui viene immesso un codice di sconto personalizzato nella notifica push.

![Personalize1][11]

La personalizzazione viene ottenuta sostituendo un marcatore da un tag app info, quindi sarà necessario assicurarsi che prima siano stati definiti i valori app-info appropriati per l'utente. In questo esempio, per gli utenti di destinazione verrà definito un tag app info denominato rebate_code.
Come si può vedere sopra, il contenuto della notifica push include il marker ${rebate_code} che indicherà che deve essere sostituito dal contenuto effettivo del tag app info.

> [!WARNING]
> se il tag app info non è definito per l'utente, l'utente non riceverà il push.

* Risultato

![Personalize2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>È possibile personalizzare ulteriormente il testo della notifica
![Personalize3][13]

* Includendo il titolo della notifica
* e il contenuto del messaggio.
* Scegliere il tipo di annuncio (visualizzazione testo o Web)

![Personalize4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>Il corpo di un annuncio può anche essere personalizzato con:
* L'URL di azione, nel caso in cui si voglia personalizzare la pagina di destinazione
* Il titolo
* Il corpo del messaggio

## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>Differenziare la notifica push (all'interno o all'esterno dell'app)
* Scegliere il tipo di notifica di cui si effettuerà il push, selezionare l'applicazione, andare alla sezione "Reach", selezionare o creare una campagna push e andare alla sezione "Notifica".
* Fare clic su "modalità di recapito" desiderata.
* Fare clic sulla casella di controllo "Limita attività" per inviare la notifica solo quando vengono eseguite attività specifiche (schermate).

![Differentiate1][15]

### <a name="out-of-app-only-delivery-mode"></a>Modalità di recapito "Solo all'esterno dell'app"
![Differentiate2][16]

La modalità di recapito "Solo all'esterno dell'app" fornisce la notifica push quando l'applicazione è chiusa. Questa è la notifica push standard.
Quando si seleziona "Solo all'esterno dell'app", è necessario aver già fornito i certificati dalla piattaforma su cui si basa l'applicazione (servizio APN o GCM).

### <a name="see-also"></a>Vedere anche
* [Apple Push Notification Service - Certificati](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), [Google Cloud Messaging - Certificato](http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>Modalità di recapito "Solo in-app"
![Differentiate3][17]

La modalità di recapito "Solo in-app" fornisce la notifica push quando l'applicazione è in esecuzione.
Per questa notifica, non è necessario passare attraverso il servizio APN e il sistema GCM.
È possibile usare il sistema di recapito in-app per raggiungere gli utenti finali.
È possibile personalizzare completamente la notifica e decidere in quale attività (schermata) visualizzare la notifica.

### <a name="anytime-delivery-mode"></a>Modalità di recapito "Sempre"
È possibile scegliere la modalità di recapito "Sempre", che consente di raggiungere l'utente finale indipendentemente dal fatto che l'applicazione sia in esecuzione o meno.
Quando si seleziona "Sempre", è necessario aver già fornito i certificati dalla piattaforma su cui si basa l'applicazione (servizio APN o GCM). 

## <a name="schedule-a-push-campaign"></a>Pianificare una campagna push
### <a name="plan-to-start-a-campaign"></a>Pianificare l'inizio di una campagna
![Shedule1][18]

È il 21 marzo e si vuole diffondere un annuncio alla mezzanotte del 22 marzo. Non è necessario essere davanti all'interfaccia per effettuare un push. È possibile pianificare in anticipo il minuto esatto in cui le notifiche verranno inviate.

* Deselezionare la casella di controllo "Nessuna" e selezionare un'ora di inizio 
* Scegliere la data e ora in cui avviare la campagna push.

### <a name="plan-to-end-a-campaign"></a>Pianificare la fine di una campagna
![Shedule2][19]

Si vuole interrompere la campagna il 25 marzo alle 15, ma si sa che non si sarà presenti per poterlo fare.
Non è necessario essere davanti all'interfaccia per effettuare un push. È possibile pianificare in anticipo il minuto esatto in cui la campagna verrà interrotta.

* Fare clic sulla casella di controllo "Nessuna" e selezionare un'ora di fine
* Scegliere la data e ora in cui terminare la campagna push.

### <a name="end-a-campaign-manually"></a>Terminare manualmente una campagna
![Shedule3][20]

Per impostazione predefinita, le caselle di controllo "Nessuna" sono selezionate.
La campagna inizierà al momento dell'attivazione nella sezione Reach e terminerà quando verrà interrotta in questa stessa sezione.

> [!NOTE]
> Le campagne create senza una data di fine memorizzano la notifica push nel dispositivo e consentono di visualizzare al successivo avvio dell'app, anche se la campagna è stata interrotta manualmente.

## <a name="enhance-a-push-notification-with-a-text-view"></a>Migliorare una notifica push con una visualizzazione testo
### <a name="what-is-a-text-view"></a>Cos'è una visualizzazione testo?
![TextView1][21]

Una visualizzazione testo è un popup con contenuto testuale. Questo popup viene visualizzato quando l'utente finale fa clic sulla notifica push.
Una visualizzazione testo consente di presentare più contenuto all'utente finale. È anche un'opportunità per presentare una chiamata a un'azione, ad esempio il passaggio a una pagina dell'app, il reindirizzamento a un archivio, l'apertura di una pagina Web, l'invio di un messaggio di posta elettronica, l'avvio di una ricerca basata sulla posizione geografica e così via.

### <a name="example-text-view"></a>Esempio: visualizzazione testo
* Creare la campagna di notifica push nella sezione "Reach" e assegnare un nome alla campagna.

![TextView2][22]

* Scrivere il messaggio che verrà visualizzato nella notifica.
* Selezionare "testo" come tipo di contenuto per l'annuncio.

![TextView3][23]

> [!NOTE]
> Quando si effettua il push di una visualizzazione testo, prima viene sempre visualizzata una notifica. 

* Definire il testo. Dopo aver selezionato il contenuto dell'annuncio di testo, apparirà la sottosezione che consente di definire il testo da visualizzare.

![TextView4][24]

* Scrivere il titolo che verrà visualizzato nella parte superiore del messaggio.
* Scrivere il contenuto principale della visualizzazione testo.
* Scrivere il contenuto che verrà visualizzato sul pulsante di azione. Un pulsante di azione consente all'applicazione di eseguire un'azione specifica, ad esempio l'apertura di una pagina dell'applicazione, il reindirizzamento a un archivio di app o a qualsiasi tipo di origine sia possibile fornire.
* Scrivere il contenuto che verrà visualizzato sul pulsante di uscita. Facendo clic sul pulsante di uscita, la visualizzazione testo scompare.
* Creare la campagna di notifica push che verrà visualizzata nell'elenco di campagne.

![TextView5][25]

* Attivare la campagna di notifica push per inviare la visualizzazione testo agli utenti.

![TextView6][26]

* Risultato

![TextView7][27]

* L'utente riceve la notifica e fa clic su di essa.
* La visualizzazione testo appare come un popup con cui l'utente può interagire.

## <a name="enhance-a-push-notification-with-a-web-view"></a>Migliorare una notifica push con una visualizzazione Web
### <a name="what-is-a-web-view"></a>Cos'è una visualizzazione Web?
![WebView1][28]

Una visualizzazione Web è un popup con contenuto Web. Questo popup viene visualizzato quando l'utente finale fa clic sulla notifica push.
Una visualizzazione Web consente una maggiore interazione con l'utente finale.
È anche un'opportunità per presentare una chiamata a un'azione, ad esempio il reindirizzamento a un archivio di app, l'apertura di una pagina Web, l'invio di un messaggio di posta elettronica, l'avvio di una ricerca basata sulla posizione geografica e così via.

### <a name="example-web-view"></a>Esempio: visualizzazione Web
* Creare la campagna push nella sezione "Reach" e assegnare un nome alla campagna.

![WebView2][29]

* Scrivere il messaggio che verrà visualizzato nella notifica.
* Selezionare "Web" come tipo di contenuto per l'annuncio.

![WebView3][30]

### <a name="about-announcement-types"></a>Informazioni sui tipi di annuncio:
* Solo notifica: una semplice notifica standard. Vale a dire che se un utente fa clic, non apparirà alcuna ulteriore visualizzazione, ma si verificherà semplicemente l'azione associata.
* Annuncio di testo: una notifica che invita l'utente a esaminare una visualizzazione testo.
* Annuncio Web: una notifica che invita l'utente a esaminare una visualizzazione Web.
  Selezionare il contenuto "Annuncio Web".

> [!NOTE]
> quando si effettua il push di una visualizzazione Web, prima viene sempre visualizzata una notifica.

* Definire il contenuto Web. Dopo aver selezionato il contenuto dell'annuncio Web, apparirà la sottosezione che consente di definire il contenuto della visualizzazione Web da visualizzare.

![WebView4][31]

* Scrivere il titolo che verrà visualizzato nella parte superiore del messaggio (facoltativo).
* Scrivere qui il codice HTML.
* Fare clic sul pulsante della modalità di modifica dell'origine per passare da un'edizione all'altra e verificarne l'aspetto.
* Scrivere il contenuto che verrà visualizzato sul pulsante di azione. Un pulsante di azione consente all'applicazione di eseguire un'azione specifica, ad esempio l'apertura di una pagina dell'applicazione, il reindirizzamento a un archivio o a qualsiasi tipo di origine sia possibile fornire.
* Scrivere il contenuto che verrà visualizzato sul pulsante di uscita. Facendo clic sul pulsante di uscita, la visualizzazione Web scompare.
* Risultato

![WebView5][32]

* L'utente riceve la notifica e fa clic su di essa.
* La visualizzazione testo appare come un popup con cui l'utente può interagire.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md




<!--HONumber=Feb17_HO1-->


