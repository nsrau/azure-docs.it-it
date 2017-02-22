---
title: Interfaccia utente di Azure Mobile Engagement - Impostazioni
description: Informazioni su come gestire le impostazioni globali dell&quot;applicazione usando Azure Mobile Engagement
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 858f4cb4-14de-4bb5-826f-28cadbfc928b
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f3c20ee0d5abb40d7650eeaa722e87142275448d


---
# <a name="how-to-manage-the-global-settings-of-your-application"></a>Come gestire le impostazioni globali dell'applicazione
Le opzioni del menu **Impostazioni** disponibili per un'applicazione variano a seconda della piattaforma dell'applicazione e delle autorizzazioni di cui si dispone per l'applicazione. Sono disponibili le impostazioni seguenti: Dettagli, Progetti, Push nativo, Velocità push, Tag (info app) e Pressione commerciale. L'opzione di menu Tag (info app) della sezione Impostazioni può essere gestita dall'applicazione (utilizzando l’SDK) o dal back-end (utilizzando l'API dispositivo). 

> [!NOTE]
> Molte sezioni dell'interfaccia utente del portale **Mobile Engagement** contengono il pulsante **MOSTRA GUIDA**. Premere questo pulsante per ottenere ulteriori informazioni contestuali su una sezione.
> 
> 

## <a name="details"></a>Dettagli
Consente di modificare il nome e la descrizione dell'applicazione e di visualizzare il proprietario dell'applicazione e le autorizzazioni del ruolo. 

La configurazione di Analytics consente di visualizzare o modificare il giorno da cui iniziano le settimane e il tempo di conservazione in giorni.

  ![settings1][46]

## <a name="projects"></a>Progetti
Consente di selezionare tutti i progetti in cui deve essere visualizzata l'applicazione. 

È inoltre possibile eseguire la ricerca di un progetto e visualizzare il nome, la descrizione, il proprietario e le autorizzazioni del ruolo di qualsiasi progetto di cui l'applicazione fa parte.

Per altre informazioni, vedere: [Documentazione UI - Home][Link 13]

  ![settings3][48]

## <a name="native-push"></a>Push nativo
Consente di registrare un nuovo certificato o di eliminare un certificato esistente per l'utilizzo con push nativo. Il push nativo consente a Azure Mobile Engagement di effettuare il push all'applicazione in qualsiasi momento, anche quando non è in esecuzione. 

Dopo aver fornito le credenziali o i certificati per almeno un servizio di push nativo, è possibile selezionare "In qualsiasi momento" in fase di creazione di campagne Reach, nonché usare il parametro "notifier" nell'API PUSH.

### <a name="apple-push-notification-service-apns"></a>Apple Push Notification Service (APNS)
Per abilitare il push nativo tramite Apple Push Notification Service è necessario registrare il certificato. Occorre specificare il tipo di certificato: sviluppo (DEV) o produzione (PROD) e quindi caricare il certificato e la password.

Per altre informazioni, vedere: [Documentazione SDK - iOS - Come preparare l'applicazione per le notifiche push Apple][Link 5]

![settings4][49]

### <a name="windows-push-notification-service-wpns"></a>Servizio notifica Push Windows (WPNS)
Per abilitare il push nativo tramite il Servizio notifica Push Windows, è necessario fornire le credenziali dell'applicazione. Sono necessari l'identificatore di sicurezza del pacchetto (SID) e la chiave segreta.

![settings5][50]

### <a name="google-cloud-messaging-for-android-gcm"></a>Google Cloud Messaging per Android (GCM)
Per abilitare il push nativo tramite GCM, è necessario seguire le istruzioni fornite da Google. È necessario, quindi, incollare una chiave API semplice del server, configurata senza restrizioni IP. Richiede l'integrazione con SDK per Android v1.12.0+.

Per altre informazioni, vedere: 

* [Documentazione SDK - Android - Come integrare GCM][Link 5]
* [Guida GCM di Google per sviluppatori](http://developer.android.com/guide/google/gcm/gs.html)

### <a name="amazon-device-messaging-for-android-adm"></a>Amazon Device Messaging for Android (ADM)
Per abilitare il push nativo mediante ADM, è necessario specificare le <OAuth credentials> Amazon costituite da ID e segreto client (è necessaria l'integrazione con l'SDK per Android v2.1.0+).

Per altre informazioni, vedere: 

* [Documentazione SDK - Android - Come integrare ADM][Link 5]
* [Documentazione ADM di Amazon per sviluppatori](https://developer.amazon.com/sdk/adm/credentials.html#Getting)

![settings6][51]

## <a name="push-speed"></a>Velocità di push
Mostra la velocità di push corrente dell'applicazione e consente di definire la velocità di push dell'applicazione.

  ![settings7][52]

## <a name="tag-app-info"></a>Tag (info app)
![settings11][56]

## <a name="commercial-pressure"></a>Pressione commerciale
![settings12][57]

## <a name="see-also"></a>Vedere anche
* [Concetti][Link 6]
* [Guida alla risoluzione dei problemi - Assistenza][Link 24]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

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




<!--HONumber=Feb17_HO3-->


