<properties 
   pageTitle="Interfaccia utente di Azure Mobile Engagement - Contenuti di Reach" 
   description="Informazioni su come gestire il contenuto univoco dei diversi tipi di campagne di notifica push in Azure Mobile Engagement" 
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
   ms.date="08/10/2015"
   ms.author="piyushjo"/>

# Come gestire il contenuto univoco dei diversi tipi di campagne di notifica push
 
È possibile usare la sezione Contenuto di una nuova campagna di copertura per modificare il contenuto di annunci, sondaggi, push di dati e riquadri (solo per Windows Phone). L'impostazione del contenuto delle campagne di push è specifico per il tipo di campagna.
 
### Tipi di contenuti:
- Annunci
- Sondaggi
- Push di dati
- Riquadri (solo per Windows Phone)
 
## Contenuto degli annunci
 ![Reach-Content1][30]

### Scegliere il tipo di annuncio:
-    Solo notifica: una semplice notifica standard. Vale a dire che se un utente fa clic, non apparirà alcuna ulteriore visualizzazione, ma si verificherà semplicemente l'azione associata.
-    Annuncio di testo: una notifica che invita l'utente a esaminare una visualizzazione testo.
-    Annuncio Web: una notifica che incoraggia l'utente a esaminare una visualizzazione Web.

### Vedere anche
- [Reach - Procedure - Annunci][Link 3] 

### Informazioni sugli annunci di visualizzazione Web:
Le ricorrenze del modello "{deviceid}" nel codice HTML o nel codice JavaScript fornito in questa sezione verranno automaticamente sostituite dall'identificatore del dispositivo che visualizza l'annuncio. Si tratta di un metodo semplice per recuperare gli identificatori dei dispositivi di Azure Mobile Engagement in un servizio Web esterno ospitato sul back-office. Se si desidera creare una visualizzazione Web a schermo intero (senza i pulsanti di azione e uscita predefiniti forniti), è possibile usare le funzioni seguenti dal codice JavaScript dell'annuncio di visualizzazione Web:

-    Per eseguire l'azione di annuncio: ReachContent.actionContent()
-    Per uscire dall'annuncio: ReachContent.exitContent()
 
### Scegliere l'azione:

### Informazioni sugli URL di azione:
qualsiasi URL che può essere interpretato dal sistema operativo di un dispositivo di destinazione può essere usato come un URL di azione. Qualsiasi URL dedicato che potrebbe essere supportato dall'applicazione (ad esempio per far sì che gli utenti passino a una schermata specifica) può essere usato anche come URL di azione. Ogni ricorrenza del modello {deviceid} verrà automaticamente sostituita dall'identificatore del dispositivo che esegue l'azione. Questo metodo consente di recuperare facilmente gli identificatori dei dispositivi di Azure Mobile Engagement tramite un servizio Web esterno ospitato sul back-office.

- **Azioni di Android e iOS**
    - Aprire una pagina Web
    - http://[web-site-domain] 
    - Esempio:http://www.azure.com
    - Inviare un messaggio di posta elettronica
    - mailto:[destinatario]?subject=[oggetto]&body=[messaggio] 
    - Esempio:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Inviare un SMS
    - sms:[numero di telefono] 
    - Esempio:sms:2125551212
    - Comporre un numero di telefono
    - tel:[numero di telefono] 
    - Esempio:tel:2125551212
- **Azioni solo di Android**
    - Scaricare un'applicazione in Play Store
    - market://details?id=[app pacchetto] 
    - Esempio:market://details?id=com.microsoft.office.word
    - Avviare una ricerca di localizzazione geografica
    - geo:0,0?q=[query di ricerca] 
    - Esempio:geo:0,0?q=starbucks,paris
- **Azioni solo di iOS**
    - Scaricare un'applicazione in App Store
    - http://itunes.apple.com/[country]/app/[app name]/id[app id]?mt=8 
    - Esempio:http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
    - Azioni di Windows
    - Aprire una pagina Web
    - http://[web-site-domain] 
    - Esempio:http://www.azure.com
    - Inviare un messaggio di posta elettronica
    - mailto:[destinatario]?subject=[oggetto]&body=[messaggio] 
    - Esempio:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Inviare un SMS (è necessario Skype Store App)
    - sms:[numero di telefono] 
    - Esempio:sms:2125551212
    - Comporre un numero di telefono (è necessario Skype Store App)
    - tel:[numero di telefono] 
    - Esempio:tel:2125551212
    - Scaricare un'applicazione in Play Store
    - ms-windows-store:PDP?PFN=[ID pacchetto app] 
    - Esempio:ms-windows-store:PDP?PFN=4d91298a-07cb-40fb-aecc-4cb5615d53c1
    - Avviare una ricerca in Bing Mappe
    - bingmaps:?q=[query di ricerca] 
    - Esempio:bingmaps:?q=starbucks,paris
    - Usare uno schema personalizzato
    - [schema personalizzato]://[parametri schema personalizzato] 
    - Esempio:myCustomProtocol://myCustomParams
    - Usare dati di un pacchetto (è necessario App Store per la lettura dell'estensione)
    - [cartella][dati].[estensione] 
    - Esempio:myfolderdata.txt
 
### Creare un URL di rilevamento:
-    Per istruzioni sulla creazione di un URL di rilevamento che consente agli utenti di scaricare una delle altre applicazioni, vedere la sezione "Impostazioni" della <UI Documentation>.
 
### Definire il testo dell'annuncio
Compilare il titolo, il contenuto e il testo dei pulsanti dell'annuncio. È possibile definire i destinatari di una campagna futura in base al feedback di copertura sulla risposta degli utenti alla campagna corrente. È possibile definire i destinatari a seconda che la campagna sia stata solo oggetto di push, che gli utenti abbiano risposto, abbiano eseguito un'azione o si siano scollegati.

### Vedere anche
- [Documentazione dell'interfaccia utente - Reach - Nuovi criteri push][Link 28]

## Contenuto dei sondaggi
![Reach-Content2][31] Compilare il titolo, la descrizione e il testo dei pulsanti dell'annuncio. Aggiungere quindi domande e opzioni per le risposte alle domande. È possibile definire i destinatari di una campagna futura in base al feedback di copertura sulla risposta degli utenti alla campagna corrente. È possibile definire i destinatari a seconda che la campagna sia stata solo oggetto di push, che gli utenti abbiano risposto, eseguito un'azione o si siano scollegati. La definizione dei destinatari può essere basata anche sul feedback di risposta ai sondaggi, dove domande e opzioni di risposta vengono usate come criteri.

### Vedere anche
- [Documentazione dell'interfaccia utente - Reach - Nuovi criteri push][Link 28]
 
## Contenuto dei push di dati
![Reach-Content3][32]

### Scegliere il tipo di testo dei dati:
- Testo
- Dati binari
- Dati Base64

### Definire il contenuto dei dati
- Se si seleziona il push dei dati di testo, copiare e incollare il testo nella casella "contenuto".
- Se si seleziona il push dei dati binari o base64, usare il pulsante "carica il file" per caricare il file.
- È possibile definire i destinatari di una campagna futura in base al feedback di copertura sulla risposta degli utenti alla campagna corrente. È possibile definire i destinatari a seconda che la campagna sia stata solo oggetto di push, che gli utenti abbiano risposto, eseguito un'azione o si siano scollegati.

### Vedere anche
- [Documentazione dell'interfaccia utente - Reach - Nuovi criteri push][Link 28]

## Contenuto dei riquadri (solo per Windows Phone)
![Reach-Content4][33]

### Definire il contenuto del riquadro
Il payload dei riquadri è il testo da visualizzare nel riquadro dell'app sui dispositivi Windows Phone. Un push di riquadri è la versione Servizio di notifica Push di Microsoft (MPNS) di un push nativo per Windows Phone. Il tipo di push di riquadri è l'unico tipo di push che non ha risposta e pertanto non è possibile creare i destinatari delle campagne future in base ai risultati di una campagna di push di riquadri.

### Vedere anche
- [Documentazione dell'API - API Reach - Push nativo][Link 4]

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
 

<!---HONumber=August15_HO7-->