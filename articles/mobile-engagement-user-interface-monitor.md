<properties 
   pageTitle="Interfaccia utente di Azure Mobile Engagement - Monitoraggio" 
   description="Informazioni su come monitorare in tempo reale i dati dell&#39;applicazione usando Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="piyushjo"/>

# Come monitorare in tempo reale i dati relativi all'applicazione
Nella sezione Monitoraggio dell'interfaccia utente vengono fornite informazioni di analisi in tempo reale ed è possibile impostare gli avvisi quando vengono raggiunte le soglie per la maggior parte delle informazioni cronologicamente disponibili anche nella sezione "Analisi" dell'interfaccia utente. Nel "Glossario" in "Concetti" sono riportate le definizioni dei termini e delle abbreviazioni usati in Analisi e Monitoraggio, ad esempio: utente attivo, nuovo utente, utente assorbito, sessione, grafico percorso utenti, mapping utenti, URL di rilevamento, tendenze, attività, evento, processo, errore, informazioni supplementari, arresto anomalo e informazioni sull'app.

### Vedere anche
-  [Concetti - Glossario][Link 6], [Guida alla risoluzione dei problemi - Analisi][Link 21]

## Monitoraggio: sessioni, processi, eventi, errori e arresti anomali
È possibile verificare quanti utenti sono attualmente in sessione e su schermi specifici o stanno eseguendo particolari azioni. È possibile visualizzare l'attività dell'utente per sessioni, processi, eventi, errori e arresti anomali. È possibile visualizzare le informazioni correnti e mostrare le informazioni dall'ora, del giorno o della settimana più recente. È possibile visualizzare tutte le informazioni in ogni categoria o ordinarle per sessione, processo, evento, errore e arresto anomalo specifico. Il monitoraggio in tempo reale è utile durante eventi quali una campagna di push per verificare se esiste un aumento nell'azione subito dopo l'invio di una notifica push.
 
![Monitor1][14]

## Risoluzione dei problemi con Monitoraggio - Eventi - Dettagli
La generazione di un evento nell'applicazione dal dispositivo di test e la relativa ricerca in Monitoraggio - Eventi - Dettagli è uno dei modi più semplici per trovare l'ID del dispositivo di test e per confermare che l'integrazione Azure Mobile Engagement di Analisi, Monitoraggio e Segmenti funzioni dall'applicazione. Dopo aver ottenuto l'ID del dispositivo di test, è possibile aggiungerlo ai dispositivi di test in "Account personale – Dispositivi". Se non è possibile generare un evento, assicurarsi che Azure Mobile Engagement sia correttamente integrato nell'app Android/iOS/Web/Windows/Windows Phone con l'SDK.

### Vedere anche
-  [Documentazione SDK][Link 5]

![Monitor2][15]

## Risoluzione dei problemi con Monitoraggio - Arresti anomali - Dettagli
È possibile esaminare le informazioni sull'arresto anomalo dell'app da Monitoraggio - Arresti anomali - Dettagli per determinare il motivo per cui l'app si blocca. È consigliabile, inoltre, cercare i problemi noti di ogni versione dell'SDK nelle note di rilascio che accompagnano ogni versione dell'SDK per Android/iOS/Web/Windows/Windows Phone.

### Vedere anche
-  [Documentazione SDK - Note di rilascio][Link 5]

![Monitor3][16]

## Monitoraggio - Avvisi
È inoltre possibile specificare condizioni per gli avvisi da inviare automaticamente all'utente tramite posta elettronica o messaggistica istantanea \(sono supportati tutti i servizi compatibili con XMPP come GTalk di Google o iChat Apple\). Gli avvisi sono basati su una soglia di rilevamento predefinita maggiore \(\>\) o minore \(\<\) di un numero specifico di sessioni, processi, eventi, errori o arresti anomali al secondo, minuto o ora. Tramite Avvisi è possibile monitorare tutte le attività di un determinato tipo o semplicemente l'attività di un particolare processo, evento o errore. È inoltre possibile specificare una velocità minima di rilevamento, ossia la quantità minima di tempo che separa due notifiche per lo stesso avviso, per assicurarsi che quando viene attivato l'avviso non si riceva mai più di una notifica ogni X minuti.
 
![Monitor4][17]

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
[Link 27]: mobile-engagement-how-tos-first-push.md
[Link 28]: mobile-engagement-how-tos-test-campaign.md
[Link 29]: mobile-engagement-how-tos-personalize-push.md
[Link 30]: mobile-engagement-how-tos-differentiate-push.md
[Link 31]: mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: mobile-engagement-how-tos-text-view.md
[Link 33]: mobile-engagement-how-tos-web-view.md

<!--HONumber=52-->
