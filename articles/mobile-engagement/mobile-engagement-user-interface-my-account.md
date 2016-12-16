---
title: Interfaccia utente di Azure Mobile Engagement - Account personale
description: Informazioni su come gestire il profilo dell&quot;account e i dispositivi di test usando Azure Mobile Engagement
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 22832678-3959-4b8c-9fb2-f2ff5974e5d1
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8430f80b9b2ec596d43b0de5e7ef022d4b3261bd


---
# <a name="how-to-manage-your-account-profile-and-test-devices"></a>Come gestire il profilo dell'account e i dispositivi di test
Questo articolo descrive la **Home** page del portale **Mobile Engagement**. Utilizzare il portale **Mobile Engagement** per monitorare e gestire le app per dispositivi mobili. 

Per ottenere la pagina **account personale** , fare clic sul proprio account nella parte superiore della pagina.

Nella sezione Account personale dell'interfaccia utente è possibile visualizzare e modificare le impostazioni associate al proprio account, incluse le impostazioni del profilo e gli ID dei dispositivi di test. Queste impostazioni contengono elementi a cui è possibile accedere anche tramite l'API di dispositivo.

![MyAccount1][7]  

## <a name="profile"></a>Profilo:
È possibile visualizzare o modificare le impostazioni del proprio account mostrate di seguito: È anche possibile assegnare a un altro utente l'autorizzazione per usare l'applicazione in base all'indirizzo di posta elettronica da [Home](mobile-engagement-user-interface-home.md).

![MyAccount2][8]  

## <a name="devices"></a>Dispositivi:
È possibile visualizzare, aggiungere o rimuovere gli ID dei dispositivi di test che si possono usare per testare le campagne di **copertura** o **push**. Istruzioni contestuali per trovare l'ID dei dispositivi per ogni piattaforma (iOS, Android, Windows Phone e così via) vengono visualizzate quando si fa clic su "Nuovo dispositivo". 

![MyAccount3][9]  

Per utilizzare l'API Push o l'API dispositivo è necessario conoscere l'identificatore univoco del dispositivo degli utenti (il parametro deviceid). Esistono diversi modi per recuperarlo:

1. Dal back-end, è possibile utilizzare la funzionalità "Get" dell'API dispositivo per ottenere l'elenco completo degli identificatori dei dispositivi.
2. Dall'app, è possibile ottenerlo utilizzando l'SDK (su Android chiamare la funzione getDeviceID() della classe Agent, su iOS leggere la proprietà deviceid della classe Agent).
3. Da un annuncio Reach, se l'URL dell'azione associato all'annuncio contiene il modello {deviceid}, questo verrà sostituito automaticamente dall'identificatore del dispositivo che attiva l'azione.
   http://<example>.com/registeruser?deviceid={deviceid}&otherparam=myparamdata verrà sostituito con: http://<example>.com/registeruser?deviceid=XXXXXXXXXXXXXXXX&otherparam=myparamdata 
4. Da un annuncio Web Reach, se il codice HTML dell'annuncio contiene il modello {deviceid}, questo verrà sostituito automaticamente dall'identificatore del dispositivo di visualizzazione dell'annuncio Web.
   Ecco l'identificatore del dispositivo: {deviceid} verrà sostituito da: Ecco l'identificatore del dispositivo: XXXXXXXXXXXXXXXX
5. Aprire l'applicazione sul dispositivo ed eseguire un evento nell'app contrassegnata.
   Da "Interfaccia utente - app - Monitoraggio - Eventi - Dettagli", trovare l'evento eseguito nell'elenco.
   Fare clic su questo evento in Monitoraggio.
   L'ID del dispositivo sarà presente nell'elenco dei dispositivi che hanno eseguito l'evento.
   Quindi, è possibile copiare tale ID del dispositivo e registrarlo in "Interfaccia utente - Account personale - Dispositivi - Nuovo dispositivo - Seleziona la piattaforma del dispositivo".
   >Tenere presente che quando IDFA è disabilitato per iOS, l'ID del dispositivo potrebbe cambiare nel tempo se si disinstalla e si reinstalla l'app.

## <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi
* [Guida per la risoluzione dei problemi - Assistenza][Collegamento 24]

## <a name="see-also"></a>Vedere anche
* [Documentazione dell'interfaccia utente - Home page][Collegamento 13]

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
[Collegamento 1]: mobile-engagement-user-interface.md
[Collegamento 2]: mobile-engagement-troubleshooting-guide.md
[Collegamento 3]: mobile-engagement-how-tos.md
[Collegamento 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Collegamento 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Collegamento 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Collegamento 7]: https://account.windowsazure.com/PreviewFeatures
[Collegamento 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Collegamento 9]: http://azure.microsoft.com/services/mobile-engagement/
[Collegamento 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Collegamento 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Collegamento 12]: mobile-engagement-user-interface-navigation.md
[Collegamento 13]: mobile-engagement-user-interface-home.md
[Collegamento 14]: mobile-engagement-user-interface-my-account.md
[Collegamento 15]: mobile-engagement-user-interface-analytics.md
[Collegamento 16]: mobile-engagement-user-interface-monitor.md
[Collegamento 17]: mobile-engagement-user-interface-reach.md
[Collegamento 18]: mobile-engagement-user-interface-segments.md
[Collegamento 19]: mobile-engagement-user-interface-dashboard.md
[Collegamento 20]: mobile-engagement-user-interface-settings.md
[Collegamento 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Collegamento 22]: mobile-engagement-troubleshooting-guide-apis.md
[Collegamento 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Collegamento 24]: mobile-engagement-troubleshooting-guide-service.md
[Collegamento 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Collegamento 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Collegamento 27]: ../mobile-engagement-how-tos-first-push.md
[Collegamento 28]: ../mobile-engagement-how-tos-test-campaign.md
[Collegamento 29]: ../mobile-engagement-how-tos-personalize-push.md
[Collegamento 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Collegamento 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Collegamento 32]: ../mobile-engagement-how-tos-text-view.md
[Collegamento 33]: ../mobile-engagement-how-tos-web-view.md







<!--HONumber=Nov16_HO3-->


