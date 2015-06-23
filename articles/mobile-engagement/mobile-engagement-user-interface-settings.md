<properties 
   pageTitle="Interfaccia utente di Azure Mobile Engagement - Impostazioni" 
   description="Informazioni su come gestire le impostazioni globali dell'applicazione usando Azure Mobile Engagement" 
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

# Come gestire le impostazioni globali dell'applicazione
Le opzioni del menu Impostazioni disponibili per un'applicazione variano a seconda della piattaforma dell'applicazione e delle autorizzazioni di cui si dispone per l'applicazione. Sono disponibili le impostazioni seguenti: Dettagli, Progetti, Push nativo, Velocità push, SDK, Rilevamento, Info App, Pressione commerciale e Autorizzazioni. Solo l'opzione di menu Info app della sezione Impostazioni dell'interfaccia utente contiene elementi che possono essere gestiti con la funzionalità "Tag" dell'API dispositivo. Il glossario nella pagina relativa ai concetti contiene le definizioni di termini e abbreviazioni, ad esempio: APNS, GCM, IDFA, API, SDK, chiave API, chiave SDK, ID applicazione (ID app), ID AppStore, piano di tag, ID utente, ID dispositivo, delegato dell'app, analisi dello stack e deep linking.

### Vedere anche
- [Documentazione API - API dispositivo][Link 4], [Concetti - Glossario][Link 6], [Guida alla risoluzione dei problemi - Assistenza][Link 24]

  ![settings1][46]

## Dettagli
Consente di modificare il nome e la descrizione dell'applicazione e di visualizzare il proprietario dell'applicazione e le autorizzazioni del ruolo. Configurazione di Analytics: consente di visualizzare o modificare il giorno da cui iniziano le settimane e il tempo di assorbimento in giorni:
 
  ![settings2][47]
 
## Progetti
Consente di selezionare tutti i progetti in cui deve essere visualizzata l'applicazione. È inoltre possibile eseguire la ricerca di un progetto e visualizzare il nome, la descrizione, il proprietario e le autorizzazioni del ruolo di qualsiasi progetto di cui l'applicazione fa parte.

### Vedere anche
-    [Documentazione dell'interfaccia utente - Home page][Link 13]
 
  ![settings3][48]

## Push nativo
Consente di registrare un nuovo certificato o di eliminare un certificato esistente per l'utilizzo con push nativo. Il push nativo consente a Azure Mobile Engagement di effettuare il push all'applicazione in qualsiasi momento, anche quando non è in esecuzione. Dopo aver fornito le credenziali o i certificati per almeno un servizio di push nativo, è possibile selezionare "In qualsiasi momento" in fase di creazione di campagne Reach, nonché usare il parametro "notifier" nell'API PUSH.

### Vedere anche
- [Documentazione API - API Reach][Link 4],[Documentazione API - API Push][Link 4], [Documentazione dell'interfaccia utente - Reach - Nuova campagna di push][Link 27]

### Apple Push Notification Service (APNS)
Per abilitare il push nativo tramite Apple Push Notification Service è necessario registrare il certificato. Occorre specificare il tipo di certificato: sviluppo (DEV) o produzione (PROD) e quindi caricare il certificato e la password.

### Vedere anche
- [Documentazione SDK - iOS - Come preparare l'applicazione per le notifiche push Apple][Link 5]
 
![settings4][49]
 
### Servizio notifica Push Windows (WPNS)
Per abilitare il push nativo tramite il Servizio notifica Push Windows, è necessario fornire le credenziali dell'applicazione. Sono necessari l'identificatore di sicurezza del pacchetto (SID) e la chiave segreta.
 
![settings5][50]
 
### Google Cloud Messaging per Android (GCM)
Per abilitare il push nativo tramite GCM, è necessario seguire le istruzioni fornite da Google. È necessario, quindi, incollare una chiave API semplice del server, configurata senza restrizioni IP. Richiede l'integrazione con SDK per Android v1.12.0+.

### Vedere anche
- [Documentazione SDK - Android - Come integrare GCM][Link 5], [Sviluppatori Google - Guida GCM](http://developer.android.com/guide/google/gcm/gs.html), [Sviluppatori Google - Documentazione GCM](http://developer.android.com/google/gcm/index.html)
 
### Amazon Device Messaging for Android (ADM)
Per abilitare il push nativo mediante ADM, è necessario specificare le <OAuth credentials> Amazon costituite da ID e segreto client (è necessaria l'integrazione con l'SDK per Android v2.1.0+).

### Vedere anche
- [Documentazione SDK - Android - Come integrare ADM][Link 5], [Sviluppatori Amazon - Documentazione ADM](https://developer.amazon.com/sdk/adm/credentials.html#Getting)

![settings6][51]

## Velocità di push
Mostra la velocità di push corrente dell'applicazione e consente di definire la velocità di push dell'applicazione. La velocità di push definisce il numero massimo di push al secondo che saranno eseguiti dal modulo Reach. Questa impostazione può essere utile nel caso in cui i server sono sovraccarichi a causa di un numero eccessivo di richieste al secondo dopo l'attivazione di una campagna.
 
  ![settings7][52]

## Rilevamento
La funzionalità di rilevamento consente di rilevare le origini delle installazioni delle applicazioni iOS e Android. Grazie a questa funzionalità è possibile sapere da dove gli utenti hanno scaricato l'applicazione (ad esempio da quale archivio di applicazioni) e la posizione di origine da cui sono partiti (vale a dire campagna pubblicitaria, blog, sito Web, posta elettronica, SMS e così via). La funzionalità Rilevamento di Azure Mobile Engagement deve essere integrata nell'applicazione dall'SDK come passaggio separato.

### Vedere anche
- [Documentazione SDK - Android - Come integrare][Link 5], [Documentazione SDK - iOS - Come integrare][Link 5]
 
### Archivi
Consentono di registrare tutti gli archivi da cui è possibile scaricare l'applicazione in base ai relativi nomi e agli URL di download associati. In questo modo è possibile creare percorsi per gli URL di rilevamento host. Gli archivi possono essere creati o eliminati da questa pagina. Utilizzando l'icona per creare un nuovo URL di rilevamento si raggiunge la pagina degli URL di rilevamento descritta di seguito. Esistono diversi modi per rilevare la pagina da cui gli utenti scaricano l'app:

-    Usare un Server Active Directory di terze parti (Azure Mobile Engagement supporta attualmente [SmartAd](http://smartadserver.fr/) e [Surikate](http://www.surikate.com/)).
-    Usare un servizio di attribuzione di terze parti (Azure Mobile Engagement supporta attualmente [Mobile App Tracking](http://www.mobileapptracking.com/), mentre [Trademob](http://www.trademob.com/) potrebbe essere supportato in un prossimo futuro).
-    Usare un referrer di installazione di terze parti (Azure Mobile Engagement supporta attualmente [Google Play Install Referrer](https://developers.google.com/app-conversion-tracking/docs/third-party-trackers/), solo per Android).
-    Usare la segnalazione manuale.
 
  ![settings8][53]
 
### Campagne pubblicitarie
Consentono di creare nuove campagne pubblicitarie costituite da un nome di server Active Directory, un ID campagna e l'origine da cui è possibile scaricare l'applicazione.
 
  ![settings9][54]
 
### URL di rilevamento
Consente di creare gli URL di rilevamento da utilizzare come URL di destinazione nelle origini (campagna pubblicitaria, blog, sito web, posta elettronica, SMS e così via) che possono reindirizzare gli utenti agli archivi da cui è possibile scaricare l'applicazione. Consente, inoltre, di visualizzare tutti gli di URL di rilevamento già creati. Un URL di rilevamento può essere utilizzato come URL di azione di una campagna pubblicitaria o di un annuncio di Reach per invitare gli utenti a scaricare una delle app da un'altra app. Un URL di rilevamento reindirizza all'URL associato all'archivio selezionato, consentendo al sistema di rilevamento di registrare l'archivio da cui l'applicazione viene scaricata al momento dell'installazione. Se viene fornita un'origine, viene registrata dal sistema di rilevamento, consentendo di distinguere tra le diverse campagne pubblicitarie create per l'applicazione.

La creazione di un nuovo URL di rilevamento richiede di specificare un archivio e di impostare un'origine personalizzata o un'origine server Active Directory o nessuna origine.

-    Se non viene impostata alcuna origine, viene creato un URL di rilevamento predefinito.
-    Un'origine personalizzata consente di specificare un URL su un server esterno per scaricare l'applicazione.
-    Un'origine server Active Directory crea un URL di rilevamento predefinito in un server Active Directory denominato predefinito.
 
### Vedere anche
- [Documentazione dell'interfaccia utente - Reach - Nuova campagna di push][Link 27] 
 
### Creare un URL di rilevamento
È inoltre possibile creare un URL di rilevamento che consente agli utenti di scaricare una delle applicazioni dall'interno della sezione di contenuto di una nuova campagna Reach.

### Vedere anche
- [Documentazione dell'interfaccia utente - Reach - Contenuti del push][Link 29]

![settings10][55]

## Info app
Consente di registrare informazioni aggiuntive associate agli utenti dell'applicazione. È possibile inserire tali informazioni tramite l'applicazione (utilizzando l'SDK) o tramite il back-end (utilizzando l'API dispositivo).

### Vedere anche
- [Documentazione API - API dispositivo][Link 4]

La registrazione di tag di informazioni dell'applicazione consente di segmentare le campagne Reach creando criteri di destinatari di Reach specifici. È possibile visualizzare il nome e il tipo di tag di info app esistenti o aggiungere nuove info app in base al nome e al tipo: stringa, data, numero intero o booleano.

### Vedere anche
- [Documentazione dell'interfaccia utente - Reach - Nuova campagna di push][Link 27]
 
![settings11][56]
 
## Pressione commerciale
Le quote di push consentono di definire il numero massimo di volte per cui un dispositivo può essere destinazione di push per un determinato periodo. Le quote di push sono utilizzate solo per le campagne Reach con l'opzione "Applica quote di push" abilitata. Le quote di push possono essere gestite per segmento o per impostazione predefinita. Le quote possono essere impostate per inviare solo un numero massimo di notifiche push in un periodo scorrevole dell'ultima ora, giorno, settimana o mese.

### Vedere anche
- [Documentazione dell'interfaccia utente - Reach - Nuova campagna di push][Link 27], [Documentazione dell'interfaccia utente - Segmenti][Link 18]

### Quote:
- Quota per impostazione predefinita: questa quota viene applicata agli utenti che non hanno corrispondenza con alcun segmento nella sezione Quota per segmento riportata di seguito. Per impostazione predefinita, non è impostata alcuna quota.
- Quota per segmento: dal momento che una quota deve essere applicata a un gruppo di utenti, è necessario creare un segmento per identificare gli utenti a cui applicarla. Può trattarsi di un segmento per utenti frequenti, definito in base al numero di sessioni avviate o da qualsiasi altra caratteristica che si ritiene interessante ai fini della definizione del gruppo. Se un dispositivo corrisponde a più segmenti con una quota definita, viene applicata solo la quota con il numero massimo più alto di push all'ora.

![settings12][57]
 
## Autorizzazioni
Consente di cercare e visualizzare il livello di messaggio di posta elettronica, nome, organizzazione e autorizzazione degli utenti dell'applicazione. Il concetto di autorizzazioni è un'aggiunta al ruolo di progetto. Consente di associare un gruppo di autorizzazioni a un utente specifico che ha accesso all'applicazione.

### I livelli di autorizzazioni correnti disponibili sono:
-    "Creatore della campagna Reach" (un utente che può creare le campagne Reach) 
-    "Responsabile della campagna Reach" (un utente che può creare campagne Reach e attivarle, sospenderle, terminarle ed eliminarle)

> Nota: quando un utente ha un ruolo di progetto e dispone anche di un gruppo di autorizzazioni per una determinata applicazione, viene usato il concetto più permissivo. Di conseguenza, quando si utilizzano le autorizzazioni, è consigliabile impostare il ruolo di progetto degli utenti su "Visualizzatore" (il ruolo più restrittivo) e aggiungere autorizzazioni più permissive a livello di applicazione.

### Vedere anche
- [Documentazione dell'interfaccia utente - Home page][Link 13]  
 
![settings13][58]

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

<!--HONumber=54--> 