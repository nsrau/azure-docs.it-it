<properties 
   pageTitle="Interfaccia utente di Azure Mobile Engagement - Criterio Reach" 
   description="Informazioni su come usare criteri di definizione dei destinatari per inviare campagne push a un sottoinsieme selezionato di utenti mediante Azure Mobile Engagement" 
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
   ms.date="08/19/2016"
   ms.author="piyushjo"/>  


# Come usare criteri di definizione dei destinatari per inviare campagne di push a un sottoinsieme selezionato di utenti

La possibilità di definire i destinatari tramite criteri specifici con il pulsante "Nuovi criteri" è una delle funzioni più potenti di Azure Mobile Engagement. Consente infatti di inviare notifiche di push rilevanti a cui i clienti risponderanno, anziché mandare messaggi indesiderati a tutti gli utenti. È possibile limitare i destinatari in base ai criteri standard e simulare i push per stabilire quanti utenti riceveranno la notifica.

**Vedere anche:**

- [Documentazione dell'interfaccia utente - Reach - Nuova campagna di push][Link 27]

## I criteri dei destinatari possono includere:
- **Informazioni tecniche: ** è possibile stabilire i destinatari in base alle informazioni tecniche visualizzate nelle sezioni di analisi e monitoraggio. **Vedere anche:** [Documentazione dell'interfaccia utente - Analytics][Link 15], [Documentazione dell'interfaccia utente - Monitor][Link 16]
- **Posizione:** le applicazioni che usano la segnalazione della posizione in tempo reale con geofencing possono usare la posizione geografica per definire i destinatari in base alla posizione del GPS. È inoltre possibile usare la chiamata di segnalazione della posizione della Lazy Area per definire i destinatari in base alla posizione dei telefoni cellulari (queste due funzioni di segnalazione della posizione devono essere attivate dall'SDK). **Vedere anche:** [Documentazione dell'SDK - iOS - Integrazione][Link 5], [Documentazione dell'SDK - Android - Integrazione][Link 5]
- **Feedback di copertura:** è possibile definire i destinatari sulla base del loro feedback sulle precedenti notifiche di copertura usando il feedback di copertura derivante da annunci, sondaggi e push di dati. In questo modo, dopo due o tre campagne di copertura è possibile definire meglio i destinatari rispetto alla prima campagna. Il feedback può inoltre essere usato per filtrare gli utenti che hanno già ricevuto una notifica con contenuto simile, impostando una campagna che NON deve essere inviata agli utenti che hanno già ricevuto una specifica campagna precedente. È anche possibile escludere gli utenti inclusi in una campagna specifica ancora attiva in modo che non ricevano nuove notifiche push. **Vedere anche:** [Documentazione dell'interfaccia utente - Reach - Contenuti del push][Link 29]
- **Rilevamento installazione:** è possibile rilevare le informazioni in base alla posizione in cui gli utenti hanno installato l'app. **Vedere anche:** [Documentazione dell'interfaccia utente - Impostazioni][Link 20]
- **Profilo utente:** è possibile definire i destinatari in base alle informazioni standard sugli utenti e a informazioni sulle app create personalmente. Per la definizione del profilo utente, anziché prendere in considerazione solo la risposta alle campagne precedenti, vengono inclusi gli utenti attualmente connessi e quelli che hanno risposto a domande specifiche direttamente nell'app. Tutte le informazioni sull'app definite per l'app stessa vengono visualizzate nell'elenco.
- Segmenti: è possibile definire i destinatari sulla base dei segmenti creati a seconda del comportamento utente definito da più criteri. Tutti i segmenti definiti per l'app vengono visualizzati nell'elenco. **Vedere anche:** [Documentazione dell'interfaccia utente - Segmenti][Link 18]
- **Informazioni sulle app:** in "Impostazioni" è possibile creare tag personalizzati relativi alle informazioni sulle app per tenere traccia del comportamento degli utenti. **Vedere anche:** [Documentazione dell'interfaccia utente - Impostazioni][Link 20]

## Esempio: 
Se si vuole eseguire il push di un annuncio solo per un sottoinsieme di utenti che hanno eseguito un'azione di acquisto in-app.

1. Andare alla pagina delle impostazioni dell'applicazione, selezionare il menu "Informazioni sull'app" e selezionare "Nuove informazioni sull'app"
2. Registrare nuove informazioni booleane sull'app definite "inAppPurchase"
3. Fare in modo che l'applicazione imposti tali informazioni su "true" quando l'utente esegue correttamente un acquisto in-app tramite la funzione sendAppInfo ("inAppPurchase",...)
4. Se non si desidera eseguire questa operazione dall'applicazione, è possibile effettuarla dal back-end tramite l'API dispositivo
5. È quindi sufficiente creare l'annuncio con un criterio di limitazione dei destinatari agli utenti con "inAppPurchase" impostato su "true"
 
> Note: per la definizione dei destinatari in base a criteri diversi dai tag delle informazioni sulle app, è necessario che Azure Mobile Engagement raccolga informazioni dai dispositivi degli utenti prima che il push venga inviato, con conseguente possibile ritardo. Anche le opzioni di configurazione push complesse (ad esempio l'aggiornamento dei badge) possono determinare ritardi dei push. L'uso di una campagna "one-shot" dall'API Push è in assoluto il metodo di push più veloce offerto da Azure Mobile Engagement. L'uso dei soli tag delle informazioni sulle app come criteri di push per una campagna di copertura (dall'API Copertura o dall'interfaccia utente) è il secondo metodo più rapido, perché i tag delle informazioni sulle app vengono memorizzati nel server. L'uso di altri criteri di definizione dei destinatari per una campagna push è il metodo di push più flessibile ma più lento, poiché Azure Mobile Engagement deve interrogare i dispositivi per inviare la campagna.
 
![Reach-Criterion1][29]  

## Le opzioni dei criteri si applicano a:
- **Informazioni tecniche**
- Nome firmware: nome del firmware
- Versione firmware: versione del firmware
- Modello dispositivo: modello del dispositivo
- Produttore dispositivo: produttore del dispositivo
- Versione applicazione: versione dell'applicazione
- Nome operatore: nome dell'operatore, non definito
- Paese operatore: paese dell'operatore, non definito
- Tipo di rete: tipo di rete
- Impostazioni locali: impostazioni locali
- Dimensioni schermo: dimensioni dello schermo
- **Posizione**
- Ultima area nota: paese, regione, località
- Geo-fencing in tempo reale: elenco di punti di interesse (nome, azioni), POI circolare (nome, latitudine, longitudine, raggio in metri)
- **Feedback di copertura**
- Feedback annuncio: annuncio, feedback
- Feedback sondaggio: sondaggio, feedback
- Feedback risposta al sondaggio: feedback di risposta al sondaggio, domanda, opzione
- Feedback push di dati: push di dati, feedback
- **Rilevamento installazione**
- Archivio: archivio, non definito
- Source: origine, non definita
- **Profilo utente**
- Sesso: maschio o femmina, non definito
- Data di nascita: operatore, data, non definita
- Consenso: true o false, non definito
- **Informazioni sulle app**
- Stringa: stringa, non definita
- Data: operatore, data, non definita
- Numero intero: operatore, numero, non definito
- Booleano: true o false, non definito
- **Segmento**
- Nome di segmenti (dall'elenco a discesa), esclusione (utenti di destinazione che non fanno parte del segmento).

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
 

<!---HONumber=AcomDC_0824_2016-->