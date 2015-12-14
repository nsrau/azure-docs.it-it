<properties 
   pageTitle="Interfaccia utente di Azure Mobile Engagement - Reach" 
   description="Informazioni su come raggiungere gli utenti dell'applicazione con le notifiche push usando Azure Mobile Engagement" 
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
   ms.date="11/29/2015"
   ms.author="piyushjo"/>


# Come raggiungere gli utenti dell'applicazione con le notifiche push

In questo articolo viene descritta la scheda **REACH** del portale **Mobile Engagement**. Utilizzare il portale **Mobile Engagement** per monitorare e gestire le app per dispositivi mobili. Si noti che per iniziare a utilizzare il portale, è innanzitutto necessario creare un account **Azure Mobile Engagement**. Per ulteriori informazioni, vedere [Creare un account Azure Mobile Engagement](mobile-engagement-create-account.md).

La sezione Reach dell'interfaccia utente è lo strumento di gestione delle campagne push dove è possibile creare, modificare, attivare, terminare, monitorare e ottenere statistiche per le campagne di notifica push nonché funzionalità a cui è possibile accedere anche tramite l'API Reach e alcuni elementi dell'API Push di basso livello. Tenere presente che, indipendentemente dall'uso delle API o dell'interfaccia utente, è necessario integrare Azure Mobile Engagement e Reach nell'applicazione per ogni piattaforma con l'SDK prima di poter usare le campagne Reach.

>[AZURE.NOTE]Molte sezioni dell’interfaccia utente del portale **Mobile Engagement** contengono il pulsante **MOSTRA GUIDA**. Premere questo pulsante per ottenere ulteriori informazioni contestuali su una sezione.

 
## Quattro tipi di notifiche push
1.    Annunci: consentono di inviare messaggi pubblicitari agli utenti che li reindirizzano a un'altra posizione all'interno dell'app o di inviarli a una pagina Web o a un archivio esterno all'app. 
2.    Sondaggi: consentono di raccogliere informazioni dagli utenti finali ponendo loro domande.
3.    Push di dati: consentono di inviare un file di dati binario o base64. Le informazioni contenute in un push di dati vengono inviate all'applicazione per modificare l'esperienza corrente degli utenti nell'app. L'applicazione deve essere in grado di elaborare i dati di un push di dati.


## Tre categorie di statistiche in tempo reale visualizzate per ogni campagna

1.    Push inviati: il numero di push inviati in base ai criteri specificati nella campagna. 
2.    Risposte: il numero di utenti che ha reagito alla notifica aprendola all'esterno dell'applicazione o chiudendola nell'applicazione. 
3.    Considerate: il numero di utenti che hanno fatto clic sul collegamento nella notifica per essere reindirizzati a una nuova posizione nell'applicazione, a un archivio o a un browser Web. 

> [AZURE.NOTE]\: sono disponibili statistiche della campagna più dettagliate tramite le statistiche dell'API Copertura



## Dettagli della campagna

È possibile modificare, duplicare, eliminare o attivare campagne che non sono state ancora attivate passando il puntatore sopra i relativi nomi oppure fare clic per aprirle. È possibile duplicare campagne che sono già state attivate passando il puntatore sopra i nomi o fare clic per aprirle. Tuttavia, non è possibile modificare una campagna dopo che è stata attivata.
 
![Reach1][18]

## Feedback di Reach

Per visualizzare i dettagli o le statistiche di una campagna, fare clic su di essa. È possibile quindi passare dalla visualizzazione dei dettagli a quella della statistiche di una campagna che è già stata attivata e passare dalla visualizzazione semplice a quella avanzata delle statistiche per ottenere informazioni più dettagliate (a seconda delle autorizzazioni). È inoltre possibile usare le informazioni di feedback di Reach di una campagna precedente come criteri di definizione dei destinatari di una nuova campagna. Le statistiche del feedback di Reach possono essere raccolte anche tramite **Stats** dell'API Reach. È inoltre possibile personalizzare i destinatari delle campagne di push in base a campagne precedenti.


![Reach2][19]


## Vedere anche

- [Concetti][Link 6]
- [Guida per la risoluzione dei problemi - Assistenza][Link 24]

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
 

<!---HONumber=AcomDC_1203_2015-->