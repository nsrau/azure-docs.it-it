<properties
	pageTitle="Funzionalità principali di Azure Mobile Engagement"
	description="Descrive le funzionalità principali di Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="erikre" 
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/01/2016"
	ms.author="piyushjo" />

# Funzionalità principali di Azure Mobile Engagement

L'articolo fornisce una panoramica generale sulle funzionalità principali della piattaforma Mobile Engagement.

## **Generale**

- **SDK per tutte le principali piattaforme** SDK disponibili per tutte le principali piattaforme: iOS, Android, Universal Windows, Windows Phone Silverlight, Kindle, Cordova. Vengono forniti SDK facilmente integrabili e documentazione utile per iniziare a usare la piattaforma scelta. 

- **Portale SaaS separato** Consente di contattare in modo semplice il team di marketing senza accedere dal portale di gestione di Azure.

- **Disponibilità di API REST aperte** Per integrare e automatizzare sistemi CRM/CMS/IT, sono disponibili API REST aperte e .NET SDK per il relativo utilizzo, consentendo una semplice integrazione e automatizzazione con Mobile Engagement. Vedere [qui](mobile-engagement-api-authentication.md) per informazioni dettagliate.

- **Disponibilità del connettore Power BI** È anche possibile estrarre i principali grafici analitici in un dashboard di Power BI. Vedere questa [guida](https://powerbi.microsoft.com/it-IT/documentation/powerbi-content-pack-azure-mobile/)

- **Garanzia di sicurezza e privacy** Azure Mobile Engagement fa parte della famiglia di prodotti Azure e segue tutte le procedure consigliate standard relative alla sicurezza e alla privacy previste per un servizio cloud.

## **Analisi operativa**

- **Monitorare i dati in tempo reale** È possibile tenere traccia delle analisi in tempo reale utilizzando il modulo di monitoraggio che mostra dettagli tra cui sessioni, eventi, errori e arresti anomali del sistema in tempo reale. Vedere questo [articolo](mobile-engagement-concepts.md) per comprendere i concetti di base. 

	![][1]

	![][2]

- **Visualizzare i dati aggregati** È anche possibile ottenere una visualizzazione più completa dei dati analitici aggregati usando il modulo Analytics che consente di filtrarli facilmente sulla base della versione dell'app e dei periodi di tempo.

	![][3]

- **Acquisire informazioni dettagliate sul modello di assorbimento e sugli utenti**

	![][4]

- **Acquisire informazioni dettagliate sulla provenienza degli utenti e sul tempo che trascorrono nella schermata**

	![][5]
	
	![][6]

- **Identificare le schermate visitate dagli utenti dell'applicazione e le modalità di ottimizzazione del percorso utente** Ciò consente di far individuare agli utenti schermate e funzionalità specifiche.

	![][7]
	
	![][8]

- **Acquisire informazioni dettagliate sugli eventi più frequenti nell'applicazione e sul processo aziendale basato su di essi**

	![][9]

- **Tenere traccia di arresti anomali ed errori comuni e acquisire informazioni per il team di sviluppo**

	![][10]
	
	![][11]

- **Acquisire informazioni sui dispositivi e sulle reti utilizzati per l'accesso all'applicazione dagli utenti, per ottimizzare l'applicazione**

	![][12]
	
## **Notifiche push mirate e personalizzate**

- **Creare un segmento basato su uno qualsiasi dei dati raccolti** A tale scopo è possibile utilizzare uno dei dati di Evento/Sessione/Attività/Processo/Arresto anomalo/Errore/Tag .

	![][13]

	![][14]

- **Tenere traccia della cronologia dei segmenti creati giornalmente**

	![][15]

- **Inviare notifiche mirate** destinate a un segmento di uso frequente, come vecchi/nuovi utenti e così via, o a un segmento personalizzato

	![][16]

- **Inviare notifiche push in-app basate su HTML formattato e notifiche out-of-app/di sistema in base al proprio scenario**

	![][17]

	![][18]

- **Impostare notifiche in-app mirate da visualizzare in una schermata/attività specifica nell'applicazione**

	![][19]

- **Specificare "un'azione" associata alla selezione di una notifica da parte dell'utente** Potrebbe trattarsi di un'azione semplice come l'apertura di una pagina Web o lo spostamento a una schermata specifica all'interno dell'applicazione.

	![][20]
	
- **Inviare notifiche localizzate** per attirare l'interesse degli utenti dell'applicazione nella loro lingua d'uso.

	![][21]

- **Specificare un'ora di inizio e fine per le campagne**

	![][22]

- **Testare facilmente le notifiche** registrando un dispositivo di test e inviando la notifica di prova solo a questo dispositivo.

	![][23]

- **Configurare facilmente una notifica in-app da visualizzare come rapido sondaggio**

	![][24]
	
- **Ottenere statistiche della campagna push** delle notifiche per valutare la relativa riuscita

	![][25]

- **Personalizzare facilmente e dare carattere alle notifiche tramite app-info/tag ed emoji**

	![][26]

	![][27]

- **Impostare limiti push per impedire lo spamming degli utenti** Evitare di inviare molti push agli utenti dell'applicazione, dando l'impressione di spamming. Questa funzione è particolarmente utile perché permette di configurare dei limiti alla granularità di un segmento.

	![][28]

<!-- Images -->
[1]: ./media/mobile-engagement-key-features/monitor1.png
[2]: ./media/mobile-engagement-key-features/monitor2.png
[3]: ./media/mobile-engagement-key-features/analytics-filter.png
[4]: ./media/mobile-engagement-key-features/retention.png
[5]: ./media/mobile-engagement-key-features/analytics-geomap.png
[6]: ./media/mobile-engagement-key-features/analytics-session-length.png
[7]: ./media/mobile-engagement-key-features/analytics-activities.png
[8]: ./media/mobile-engagement-key-features/analytics-userpath.png
[9]: ./media/mobile-engagement-key-features/analytics-events.png
[10]: ./media/mobile-engagement-key-features/analyics-errors.png
[11]: ./media/mobile-engagement-key-features/analyics-errors-details.png
[12]: ./media/mobile-engagement-key-features/technicals.png
[13]: ./media/mobile-engagement-key-features/segment.png
[14]: ./media/mobile-engagement-key-features/segment-creation.png
[15]: ./media/mobile-engagement-key-features/segment-history.png
[16]: ./media/mobile-engagement-key-features/segment-push.png
[17]: ./media/mobile-engagement-key-features/out-of-app.png
[18]: ./media/mobile-engagement-key-features/in-app-push.png
[19]: ./media/mobile-engagement-key-features/push-in-activity.png
[20]: ./media/mobile-engagement-key-features/push-action.png
[21]: ./media/mobile-engagement-key-features/push-languages.png
[22]: ./media/mobile-engagement-key-features/push-timeframe.png
[23]: ./media/mobile-engagement-key-features/push-test.png
[24]: ./media/mobile-engagement-key-features/push-poll.png
[25]: ./media/mobile-engagement-key-features/push-stats.png
[26]: ./media/mobile-engagement-key-features/push_personalized.png
[27]: ./media/mobile-engagement-key-features/push_emoji.png
[28]: ./media/mobile-engagement-key-features/push_limits.png

<!---HONumber=AcomDC_0406_2016-->