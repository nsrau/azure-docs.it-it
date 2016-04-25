<properties
   pageTitle="Interfaccia utente di Azure Mobile Engagement - Home"
   description="Informazioni su come gestire l'applicazione e i progetti esistenti usando Azure Mobile Engagement"
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

# Come gestire l'applicazione e i progetti esistenti


In questo articolo viene descritta la **Home page** del portale **Mobile Engagement**. Utilizzare il portale **Mobile Engagement** per monitorare e gestire le app per dispositivi mobili. Si noti che per iniziare a utilizzare il portale, è innanzitutto necessario creare un account **Azure Mobile Engagement**.

Per accedere alla home page, fare clic su **Home** nella parte superiore sinistra della pagina. Contiene l'elenco di tutte le applicazioni che fanno parte della raccolta selezionata. In questa pagina è possibile vedere una rapida panoramica delle applicazioni.

La home page contiene inoltre tutti i progetti che possono includere tutte le applicazioni presenti nell'account. Si noti che chiunque può accedere alla home page dell'interfaccia utente creando un account. Tuttavia, è necessario concedere l'autorizzazione agli altri utenti affinché possano accedere alle applicazioni personalizzate presenti in **Progetti personali**.

È inoltre possibile visualizzare il grafico di confronto per le applicazioni selezionate. In alternativa, è possibile scegliere di visualizzare il grafico di confronto per le applicazioni selezionate in un progetto.

![Home1][0]


## Applicazioni personali

La panoramica rapida delle applicazioni consente di selezionare l'applicazione da aprire per visualizzare in dettaglio le opzioni della barra multifunzione. È possibile selezionare il nome dell'applicazione per tornare al percorso della barra multifunzione visitato più di recente nell'applicazione oppure è possibile selezionare l'icona a ingranaggio per accedere direttamente alla pagina delle impostazioni dell'applicazione. È possibile cercare, filtrare oppure ordinare le informazioni visualizzate nelle tabelle delle applicazioni. È possibile trascinare e rilasciare le intestazioni della colonne per modificarne l'ordine.

Tra le altre cose, la panoramica delle applicazioni include:

- **Tendenza nuovi utenti**: evoluzione dei nuovi utenti nelle ultime due settimane.
- **Utenti attivi**: numero di utenti attivi negli ultimi 30 giorni.
- **Tendenza utenti attivi**: evoluzione degli utenti attivi nelle ultime due settimane.
- **Sessioni**: una sessione indica un uso dell'applicazione da parte di un utente, dal momento in cui inizia a usarla fino a quando finisce.
- **Tendenze della sessione**: evoluzione delle sessioni nelle ultime due settimane.

Dopo avere fatto clic su un'applicazione, è possibile avviare il monitoraggio e la gestione delle app tramite l'interfaccia utente. Ad esempio:

- [Monitorare in tempo reale i dati relativi all'applicazione](mobile-engagement-user-interface-monitor.md)
- [Analizzare i dati cronologici sull'applicazione](mobile-engagement-user-interface-analytics.md)
- [Creare e gestire segmenti di utenti per identificare modelli di utilizzo](mobile-engagement-user-interface-segments.md)
- [Raggiungere gli utenti dell'applicazione con le notifiche push](mobile-engagement-user-interface-reach.md)

## Progetti personali

È possibile utilizzare i progetti per raggruppare le applicazioni e concedere le autorizzazioni alle applicazioni stesse ad altri utenti. Si assegnano autorizzazioni ad altri utenti fornendo l'indirizzo di posta elettronica. Il pulsante **Nuovo progetto** consente di creare un nuovo progetto inserendo soltanto un "nome" e una "descrizione". Una volta creato un progetto, è possibile selezionarne il nome per modificare la descrizione e il nome del prodotto e per selezionare tutte le applicazioni che si desidera visualizzare nel progetto.


![Home6][60]

I ruoli includono:

- **Visualizzatore**: un visualizzatore può soltanto visualizzare le applicazioni associate a un progetto. Un visualizzatore può accedere alle analisi, monitorare i dati e consultare i risultati Reach. Un visualizzatore non può modificare le informazioni, né gestire le applicazioni o gli utenti. Un visualizzatore non può creare o modificare lo stato di una campagna Reach.
- **Sviluppatore**: uno sviluppatore può eseguire le stesse attività di un visualizzatore nonché gestire le applicazioni. Uno sviluppatore può attivare o disattivare le applicazioni, modificare le informazioni delle applicazioni (pacchetto e firma, ad esempio) e creare campagne Reach. Uno sviluppatore non può gestire utenti.
- **Amministratore**: un amministratore può eseguire tutte le attività di uno sviluppatore e gestire gli utenti. Un amministratore può invitare gli utenti a partecipare a un progetto, modificare i ruoli degli utenti e le informazioni del progetto. Nelle impostazioni è anche possibile impostare le autorizzazioni a livello di applicazione.


Fare clic su un progetto per visualizzare tutte le applicazioni che ne fanno parte. Nell'immagine seguente viene mostrato il grafico di confronto per le applicazioni selezionate.

![Home2][3]


## Vedere anche

- [Concetti][Link 6]
- [Guida per la risoluzione dei problemi - Assistenza][Link 24]

<!--Image references-->
[0]: ./media/mobile-engagement-user-interface-home/home0.png
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[60]: ./media/mobile-engagement-user-interface-home/home6.png
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

<!---HONumber=AcomDC_0413_2016-->