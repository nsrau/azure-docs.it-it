---
title: Interfaccia utente di Azure Mobile Engagement - Reach
description: Informazioni su come raggiungere gli utenti dell'applicazione con le notifiche push usando Azure Mobile Engagement
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: d96e2590-08dd-4481-a352-2c18f26a1643
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: ce30456e41ff1a2f4824bcb64246ee115fdd1ef7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>Come raggiungere gli utenti dell'applicazione con le notifiche push
In questo articolo viene descritta la scheda **REACH** del portale **Mobile Engagement**. Utilizzare il portale **Mobile Engagement** per monitorare e gestire le app per dispositivi mobili. Si noti che per iniziare a utilizzare il portale, è innanzitutto necessario creare un account **Azure Mobile Engagement** . Per ulteriori informazioni, vedere [Creare un account Azure Mobile Engagement](mobile-engagement-create.md).

La sezione Reach dell'interfaccia utente è lo strumento di gestione delle campagne push dove è possibile creare, modificare, attivare, terminare, monitorare e ottenere statistiche per le campagne di notifica push nonché funzionalità a cui è possibile accedere anche tramite l'API Reach e alcuni elementi dell'API Push di basso livello. Tenere presente che, indipendentemente dall'uso delle API o dell'interfaccia utente, è necessario integrare Azure Mobile Engagement e Reach nell'applicazione per ogni piattaforma con l'SDK prima di poter usare le campagne Reach.

> [!NOTE]
> Molte sezioni dell'interfaccia utente del portale **Mobile Engagement** contengono il pulsante **MOSTRA GUIDA**. Premere questo pulsante per ottenere ulteriori informazioni contestuali su una sezione.
> 
> 

## <a name="four-types-of-push-notifications"></a>Quattro tipi di notifiche push
1. Annunci: consentono di inviare messaggi pubblicitari agli utenti che li reindirizzano a un'altra posizione all'interno dell'app o di inviarli a una pagina Web o a un archivio esterno all'app. 
2. Sondaggi: consentono di raccogliere informazioni dagli utenti finali ponendo loro domande.
3. Push di dati: consentono di inviare un file di dati binario o base64. Le informazioni contenute in un push di dati vengono inviate all'applicazione per modificare l'esperienza corrente degli utenti nell'app. L'applicazione deve essere in grado di elaborare i dati di un push di dati.

## <a name="campaign-details"></a>Dettagli della campagna
È possibile modificare, duplicare, eliminare o attivare campagne che non sono state ancora attivate passando il puntatore sopra i relativi nomi oppure fare clic per aprirle. È possibile duplicare campagne che sono già state attivate passando il puntatore sopra i nomi o fare clic per aprirle. Tuttavia, non è possibile modificare una campagna dopo che è stata attivata.

![Reach1][18]

## <a name="reach-feedback"></a>Feedback di Reach
Per visualizzare i dettagli di una campagna di copertura, fare clic su **Statistiche** . La visualizzazione **Semplice** offre una rappresentazione visiva in forma di istogramma a barre di ciò che accade dopo l'attivazione di una campagna. La visualizzazione **Avanzata** offre informazioni più granulari sulla campagna push. Tali informazioni non saranno disponibili se si invia una campagna di prova, ad esempio una campagna push, a un dispositivo di test. Le informazioni devono essere interpretate nel modo seguente:

1. **Con push** : specifica il numero di messaggi inviati ai dispositivi. Questo numero dipenderà dai destinatari specificati durante la creazione della campagna push. Se non si specificano destinatari, i messaggi verranno inviati a tutti i dispositivi registrati. Come per tutti gli altri servizi push, le notifiche push non vengono inviate direttamente ai dispositivi, ma ai rispettivi servizi di notifica push (Push Notification Service, PNS - APN/GCM/WNS) specifici della piattaforma in modo che recapitino le notifiche ai dispositivi. 
2. **Recapitati** : specifica il numero di messaggi recapitati correttamente dal servizio PNS al dispositivo e confermati come ricevuti da Mobile Engagement SDK. 
   
   *Motivi per cui il numero dei messaggi Recapitati può essere inferiore al numero dei massaggi Inviati:*
   
   1. Se l'utente ha disinstallato l'app dal dispositivo, ma il PNS non ne è a conoscenza al momento dell'invio del push, il messaggio verrà eliminato.
   2. Se nel dispositivo è installata l'app, ma i dispositivi sono stati offline per lunghi periodi di tempo, il PNS non riuscirà a recapitare il messaggio al dispositivo. 
   3. Se il messaggio viene recapitato al dispositivo ma Mobile Engagement SDK nell'app non riconosce il contenuto del messaggio, il messaggio viene eliminato. Questo problema può verificarsi se la personalizzazione della notifica nell'app genera un'eccezione che viene rilevata nell'SDK e il messaggio viene eliminato. Ciò può verificarsi anche se l'app nel dispositivo usa una versione di Mobile Engagement SDK che non è in grado di comprendere la versione più recente del messaggio push inviato dalla piattaforma, ma solo quando l'app è stata aggiornata dopo l'invio della notifica dalla piattaforma del servizio. La scheda **Avanzate** indicherà il numero di messaggi eliminati. 
   4. Nei dispositivi iOS, i messaggi a volte non vengono recapitati se la batteria del dispositivo è scarica o se l'app utilizza una quantità significativa di alimentazione durante l'elaborazione delle notifiche remote. Si tratta di una limitazione dei dispositivi iOS.   
3. **Visualizzati** : specifica il numero di messaggi visualizzati correttamente dall'utente dell'app sul dispositivo sotto forma di una notifica di sistema push o out-of-app nel centro notifiche o di una notifica in-app all'interno dell'app per dispositivi mobili.  La scheda **Avanzate** indicherà il numero di notifiche di sistema e il numero di notifiche in-app. 
   
   *Motivi per cui il numero dei messaggi Visualizzati può essere inferiore al numero dei massaggi Recapitati (in attesa di visualizzazione)*
   
   1. Se la campagna di notifica ha una data di fine, è possibile che la notifica sia stata recapitata, ma che la campagna fosse già scaduta nel momento in cui la notifica doveva essere aperta e visualizzata all'utente dell'app e che quindi non sia mai stata visualizzata.   
   2. Se la notifica è una notifica in-app, viene visualizzata solo quando l'utente dell'app apre l'app. Nei casi in cui l'app non è stata aperta dall'utente, l'SDK segnalerà che la notifica è stata recapitata ma non ancora visualizzata, fino all'apertura dell'app. 
   3. Se la notifica è una notifica in-app ed è configurata per la visualizzazione in un'attività/schermata specifica, anche in questo caso la notifica verrà segnalata come recapitata ma non ancora visualizzata fino all'apertura di una schermata specifica dell'app da parte dell'utente. 
4. **Interazioni dell'utente** : specifica il numero di messaggi con cui l'utente dell'app ha interagito e includerà i messaggi attivati o chiusi. 
   
   * *L'utente dell'app può attivare una notifica in uno dei modi seguenti:*
     
     1. Se la notifica è una notifica di sistema o out-of-app o una notifica in-app inviata come sola notifica, l'utente dell'app deve fare clic sulla notifica.
     2. Se la notifica è una notifica in-app con un testo, una visualizzazione Web o sondaggi, l'utente dell'app deve fare clic sul pulsante di attivazione nella notifica.
     3. Se la notifica è una notifica in-app con una visualizzazione Web, l'utente dell'app deve fare clic su un URL nella visualizzazione Web [solo per Android]
   * *L'utente dell'app può chiudere una notifica in uno dei modi seguenti:*
     
     1. Facendo clic sul pulsante di chiusura direttamente nella notifica. 
     2. Facendo scorrere rapidamente con un dito o eliminando la notifica. 
     3. Le notifiche in-app con testo o contenuto Web e sondaggi vengono in genere visualizzate dall'utente dell'app con un processo in due passaggi. Viene prima visualizzata una notifica e, facendo clic su di essa, viene visualizzato il testo, il contenuto Web o i sondaggi. L'utente dell'app può chiudere una notifica con uno di questi passaggi e i dettagli vengono acquisiti nella visualizzazione Avanzata. 
5. **Attivati** : specifica il numero di messaggi esplicitamente attivati dall'utente dell'app. Si tratta del dato più interessante, in quanto indica il numero di utenti dell'app interessati al messaggio inviato nella notifica. 

> [!NOTE]
> Nelle piattaforme iOS e Windows, se l'utente ha l'app aperta e la campagna era di tipo "AnyTime", è possibile che le notifiche out-of-app e in-app vengano visualizzate nello stesso momento. Il numero di messaggi Visualizzati può pertanto essere superiore al numero dei messaggi Recapitati. Se l'utente interagisce con la notifica o la attiva, anche il numero di Interazioni utente o di messaggi Attivati può essere superiore al numero dei messaggi Recapitati. 
> 
> 

![Reach2][19]

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

