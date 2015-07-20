<properties 
   pageTitle="Interfaccia utente di Azure Mobile Engagement - Segmenti" 
   description="Informazioni su come creare e gestire segmenti di utenti per identificare modelli di utilizzo mediante Azure Mobile Engagement" 
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

# Come creare e gestire segmenti di utenti per identificare modelli di utilizzo
La sezione Segmenti dell'interfaccia utente consente di dividere gli utenti in segmenti in base al comportamento e all'analisi che è possibile ottenere dall'applicazione ed è possibile accedervi anche tramite l'API Segmenti. I segmenti vengono calcolati per la prima volta 24 ore dopo che sono stati creati e quindi ricalcolati ogni 24 ore in base alle informazioni di analisi più recenti. Una volta calcolato un segmento, viene visualizzato un grafico di cronologia giorno per giorno ogni giorno.

### Vedere anche
- [Documentazione API - API Segments][Link 4], [Guida alla risoluzione dei problemi - Analytics][Link 21]

## Creare segmenti
È possibile creare un segmento basato su criteri (fino a 10) in base a un periodo specifico di massimo 60 giorni precedenti dalla sezione di analisi. Ad esempio, è possibile creare un segmento in base alle persone che hanno visualizzato alcune pagine o che hanno cercato contenuto specifico all'interno dell'app negli ultimi 10 giorni. Queste informazioni sono disponibili nella sezione di analisi. È possibile utilizzarle per creare un segmento e quindi configurare una notifica push da indirizzare a questo sottoinsieme di utenti per fare in modo che tornino all'applicazione.
 
> Nota: una volta calcolato, un segmento non può essere modificato; può essere solo duplicato (copiato) o distrutto (eliminato). Un segmento può essere duplicato all'interno della stessa applicazione (con lo stesso AppID) e anche in altre applicazioni (con un AppID diverso).
 
 ![segments1][35]

## Esempi di segmento
 ![segments2][36]

I segmenti consentono di suddividere gli utenti finali dell'applicazione. La segmentazione degli utenti è un'importante strategia di marketing. Azure Mobile Engagement consente di ottenere dati cronologici e creare segmenti personalizzati. Questo potente strumento consente di acquisire informazioni sull'esperienza dei clienti con l'applicazione. È possibile analizzare facilmente i segmenti e utilizzarli come destinazioni di push. Un caso di utilizzo comune si verifica quando si desidera eseguire il push di una notifica per incoraggiare gli utenti finali a valutare l'applicazione nell'archivio. Anziché inviare una notifica a tutti gli utenti finali, è possibile creare un segmento che specifichi solo gli utenti che hanno utilizzato l'applicazione ogni giorno nel corso dell'ultimo mese e sono stati soddisfatti. Quando si invia un numero inferiore di notifiche push mirate, è possibile ottenere un ritorno sugli investimenti migliore.
 
 ![segments3][37]

### Segmenti che è possibile creare in base agli elementi principali di Azure Mobile Engagement:
- Evento: creare un segmento destinato a un solo evento specifico dell'applicazione che si è verificato più di due volte alla settimana. 
- Sessione: creare un segmento di utenti che hanno utilizzato l'applicazione più di 5 volte nell'ultima settimana.
- Attività: creare un segmento di utenti che hanno utilizzato una pagina o un contenuto più o meno di 10 volte nell'ultimo mese.
- Processo: creare un segmento di utenti che hanno completato un processo più di due volte al giorno.
- Arresto anomalo: creare un segmento di tutti gli utenti che hanno avuto un arresto anomalo più di 10 volte nel corso dell'ultima settimana (il push per questo segmento dovrebbe includere un messaggio di scuse ed eventualmente un buono).
- Errore: creare un segmento di tutti gli utenti che hanno incontrato un errore più di 100 volte negli ultimi 3 giorni.
- Info app: creare un segmento mirato a un'info app personalizzata che si è verificata negli ultimi 25 giorni.
 
 ![segments4][38]

Per utilizzare il segmento in modo ottimale, è necessario aver effettuato un'integrazione personalizzata dell'SDK nell'applicazione con un piano di tag "Info app". Quindi, passare alla home page dell'interfaccia, selezionare l'applicazione desiderata e scegliere la sezione "Segmenti".

1. Selezionare la sezione "Segmenti".
2. Fare clic su "Nuovo segmento" per creare un nuovo segmento.

## Esempio di vita reale: creare un segmento semplice in base alle informazioni di "Sessione"
Creare un segmento di tutti gli utenti finali che hanno utilizzato l'app almeno 50 volte nell'ultima settimana. Da qui, individuare solo gli utenti finali che hanno trascorso almeno 30 secondi per sessione nell'applicazione. Verranno visualizzati tutti gli utenti finali che hanno avuto un'esperienza positiva nell'applicazione. Quindi, il segmento creato potrebbe essere utilizzato per effettuare il push di una notifica a tali utenti finali per chiedere loro di valutare l'app nell'archivio.
 
 ![segments5][39]

1. Assegnare un nome al segmento per trovarlo rapidamente nell'elenco di "Segmenti".
2. Fare clic sul pulsante "Crea".
 
 ![segments6][40]

Selezionare Sessione.
 
 ![segments7][41]

1. Selezionare il periodo "Ultima settimana".
2. Fare clic su Avanti.
 
 ![segments8][42]

1. Selezionare l'operatore rilevante nell'elenco: =; ≥, ≤.
2. Immettere il numero desiderato.
3. Selezionare l'occorrenza desiderata. 
4. Fare clic su Avanti. In questo esempio nell'ultima settimana gli utenti corrispondenti sono quelli che hanno creato almeno 50 sessioni.
 
 ![segments9][43]

Per la segmentazione in base alla sessione, è possibile scegliere come criterio la lunghezza per sessione.

1. Selezionare l'operatore dall'elenco.
2. Specificare la lunghezza per sessione.
3. Fare clic su Avanti. In questo esempio, in tutte le sessioni segmentate in base alla sezione dell'occorrenza, devono essere selezionati solo gli utenti che hanno trascorso più di 30 secondi per sessione.
 
 ![segments10][44]

Assegnare il nome al criterio per recuperarlo nel relativo grafico completo e fare clic su Fine.
 
 ![segments11][45]

Al termine dell'impostazione, il criterio viene visualizzato nel grafico del segmento. Poiché un segmento è basato su dati di analisi, i segmenti vengono calcolati una volta al giorno. In questo esempio, il 47,7% degli utenti finali totali corrispondeva al criterio. Tali utenti sono quelli che hanno avuto un'esperienza ottima e che probabilmente esprimeranno una valutazione più alta se ricevono una notifica push che chiede loro di valutare l'applicazione nell'archivio.

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
 

<!---HONumber=July15_HO2-->