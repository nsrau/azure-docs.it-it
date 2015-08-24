<properties 
   pageTitle="Interfaccia utente di Azure Mobile Engagement - Campagna Reach" 
   description="Informazioni sulla creazione e sulla gestione di campagne di notifica push usando Azure Mobile Engagement" 
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


# Come creare e gestire le campagne di notifica push
È possibile usare la sezione Reach dell'interfaccia utente per creare una nuova campagna di push con una formula complessa fornendo tutte le informazioni necessarie per inviare una notifica push. Le opzioni di una campagna di push variano leggermente in base ai quattro tipi di campagna: annunci, sondaggi, push di dati e riquadri (solo per Windows Phone).

### L'opzione si applica a:
- Lingue: tutti (annunci, sondaggi, push di dati e riquadri)
- Campagna: tutti (annunci, sondaggi, push di dati e riquadri)
- Notifica: annunci e sondaggi
- Contenuto: univoco per ogni tipo di campagna
- Destinatari: tutti (annunci, sondaggi, push di dati e riquadri)
- Intervallo di tempo: annunci, sondaggi e riquadri
- Test: tutti (annunci, sondaggi, push di dati e riquadri)
 
![Reach-Campaign1][20]

## Lingue
È possibile usare il menu a discesa Lingue per inviare una versione diversa del push ai dispositivi configurati per l'uso di lingue diverse. Per impostazione predefinita, tutti i dispositivi riceveranno lo stesso push indipendentemente dalla lingua usata. Gli utenti con il dispositivo impostato su una lingua diversa riceveranno la versione del push nella lingua predefinita. Molte opzioni della campagna di push consentono di specificare contenuto alternativo per ognuna delle lingue aggiuntive selezionate.
 
![Reach-Campaign2][21]

### Le differenze di lingua si applicano a:
- Lingue: è possibile selezionare lingue univoche oltre a quella predefinita
- Campagna: uguale per tutte le lingue
- Notifica: univoca per ogni lingua oltre a quella predefinita
- Contenuto: univoco per ogni lingua oltre a quella predefinita
- Destinatari: possono essere filtrati in base a un criterio di lingua distinto
- Intervallo di tempo: uguale per tutte le lingue
- Test: può essere inviato a tutte le lingue contemporaneamente
 
### Lingue supportate:
- Arabo (ar) 
- Bulgaro (bg) 
- Catalano (ca) 
- Cinese (zh) 
- Croato (h) 
- Ceco (cs) 
- Danese (da) 
- Olandese (nl) 
- Inglese (en) 
- Finlandese (fi) 
- Francese (fr) 
- Tedesco (de) 
- Greco (el) 
- Ebraico (he) 
- Hindi (hi) 
- Ungherese (hu) 
- Indonesiano (id) 
- Italiano (it) 
- Giapponese (ja) 
- Coreano (ko) 
- Lettone (lv) 
- Lituano (lt) 
- Malese (macrolanguage) (ms) 
- Norvegese Bokmål (nb) 
- Polacco (pl) 
- Portoghese (pt) 
- Romeno (ro) 
- Russo (ru) 
- Serbo (sr) 
- Slovacco (sk) 
- Sloveno (sl) 
- Spagnolo (es) 
- Svedese (sv) 
- Tagalog (tl) 
- Tailandese (th) 
- Turco (tr) 
- Ucraino (Regno Unito) 
- Vietnamita (vi) 
 
## Campagna
È possibile usare la sezione Campagna per impostare il nome e la categoria della campagna nonché, se si intende ignorare la sezione dei destinatari di una campagna di push, inviare invece la campagna tramite l'API Reach (e alcuni elementi con l'API Push di basso livello). Le categorie possono essere usate con un modello di notifica personalizzato per controllare le notifiche in-app in base alle impostazioni predefinite. È possibile ottenere un elenco di "Categorie" esistenti tramite l'API Reach.

> Avviso: se si usa l'opzione "Ignora i destinatari. Il push verrà inviato agli utenti tramite l'API" nella sezione "Campagna" di una campagna Reach, la campagna NON sarà inviata automaticamente, ma sarà necessario inviarla manualmente tramite l'API Reach.
 
![Reach-Campaign3][22]
 
### L'opzione si applica a:
- Nome: tutti
- Categoria: annunci e sondaggi
- Ignora destinatari. Il push verrà inviato agli utenti tramite l'API: tutti
 
## Notifica
È possibile usare la sezione Notifica per definire le impostazioni di base per il push, tra cui: il titolo del push, il messaggio, un'immagine in-app oppure se può essere ignorato. Molte impostazioni di notifica sono specifiche della piattaforma del dispositivo. È possibile scegliere se il push verrà inviato "in-app", "all'esterno dell'app" o in entrambi i modi. Tenere presente che gli utenti possono accettare o rifiutare esplicitamente i push "all'esterno dell'app" a livello di sistema operativo sui propri dispositivi e Azure Mobile Engagement non potrà eseguire l'override di questa impostazione. Tenere presente, inoltre, che l'API Reach gestisce i push "in-app" e "all'esterno dell'app". L'API Push può essere usata anche per gestire push "all'esterno dell'app". I push possono essere personalizzati con immagini o contenuto HTML, inclusi collegamenti diretti per il collegamento all'esterno dell'applicazione o a un'altra posizione nell'applicazione (è necessario l'SDK Android 2.1.0 o categorie successive). È possibile modificare l'icona o il badge iOS e inviare testo o contenuto Web (una finestra popup con contenuto HTML, un collegamento URL a un'altra posizione all'interno o all'esterno dell'applicazione). È inoltre possibile impostare lo squillo o la vibrazione dei dispositivi Android con il push. Tenere presente che perché un dispositivo squilli o vibri il file manifesto Android deve contenere le autorizzazioni dell'SDK corretto. Non esiste alcuno standard del settore per le dimensioni dell'immagine grande di Android perché le dimensioni dello schermo variano con ogni dispositivo. Tuttavia, le immagini da 400x100 sono adatte a quasi a tutte le dimensioni degli schermi.

### Tipi di recapito:
-    Solo all'esterno dell'app: la notifica sarà recapitata quando l'utente non usa l'applicazione.
- La notifica solo all'esterno dell'app richiede un certificato di Apple o Google (certificato APNS o GCM).
- Solo in-app: la notifica viene visualizzata solo quando l'applicazione è in esecuzione.
- La notifica usa il sistema di recapito Capptain per raggiungere l'utente. È possibile personalizzare completamente il layout o la visualizzazione del push.
- In qualsiasi momento: questa opzione assicura che una notifica venga inviata indipendentemente dal fatto che l'applicazione sia in esecuzione o meno.

 
![Reach-Campaign4][23]

### L'opzione si applica a:
- Notifica: annunci e sondaggi
 
## Contenuto
È possibile usare la sezione Contenuto per modificare il contenuto di annunci, sondaggi, push di dati e riquadri (solo per Windows Phone). L'impostazione del contenuto delle campagne di push è specifico per il tipo di campagna.

### Vedere anche
- [Documentazione dell'interfaccia utente - Reach - Contenuti del push][Link 29]
 
![Reach-Campaign5][24]

## Destinatari
È possibile usare la sezione Destinatari per definire un elenco standard di elementi per limitare la campagna in base a criteri personalizzati. L'insieme standard di opzioni per limitare i destinatari consente di effettuare il push solo per utenti nuovi, esistenti o nativi. È inoltre possibile impostare una quota per limitare il numero di utenti che ricevono il push. È possibile modificare manualmente l'espressione per la modalità di filtro della campagna in modo da includere uno o più criteri per gli utenti di destinazione. È possibile digitare manualmente un'espressione di destinatari. Tale espressione deve definire in modo esplicito la relazione tra i criteri. Un criterio viene descritto da un identificatore che deve iniziare con una lettera maiuscola e non può contenere spazi. La relazione tra i criteri può essere descritta usando gli operatori 'and', 'or', 'not' e '(',')'. Esempio: "Criterion1 or (Criterion1 and not Criterion2)".

> Nota: con numerosi destinatari inclusi nelle campagne, la ricerca dei destinatari sul lato server può risultare lenta, in particolare se si tenta di avviare più campagne contemporaneamente.

- Se possibile, iniziare una sola campagna alla volta.
- Al massimo, avviare quattro campagne alla volta.
- Eseguire il push solo per gli utenti attivi (casella di controllo "Effettua push solo degli utenti raggiungibili con Push nativo" e "Effettua push solo degli utenti attivi") in modo da individuare solo gli utenti che hanno l'app installata e che la usano ancora. Una volta definiti i destinatari, è possibile usare il pulsante di simulazione per scoprire quanti utenti riceveranno il push. Verrà calcolato il numero di utenti noti potenzialmente appartenenti al gruppo di destinatari (si tratta di una stima basata su un campione casuale di utenti). Tenere presente che anche gli utenti che hanno disinstallato l'applicazione fanno parte di questi destinatari, ma non possono essere raggiunti.

### Vedere anche
- [Documentazione dell'interfaccia utente - Reach - Nuovi criteri di push][Link 28]

![Reach-Campaign6][25]

### Modifica espressione
![Reach-Campaign7][26]
 
### L'opzione Limitare i destinatari si applica a:
- Effettua push solo di un sottoinsieme di utenti: tutti (annunci, sondaggi, push di dati e riquadri)
- Effettua push solo dei vecchi utenti: tutti (annunci, sondaggi, push di dati e riquadri)
- Effettua push solo dei nuovi utenti: tutti (annunci, sondaggi, push di dati e riquadri)
- Effettua push solo degli utenti inattivi: annunci, sondaggi e riquadri
- Effettua push solo degli utenti attivi: tutti (annunci, sondaggi, push di dati e riquadri)
- Effettua push solo degli utenti raggiungibili con Push nativo: annunci e sondaggi
 
## Intervallo di tempo
È possibile usare la sezione Intervallo di tempo per stabilire quando verrà inviato il push oppure omettere l'intervallo di tempo per avviare immediatamente la campagna. Si tenga presente che se si utilizza il fuso orario degli utenti finali, la campagna potrebbe iniziare un giorno prima del previsto per gli utenti finali in Asia, per cui è consigliabile inviare piccoli gruppi di push alla volta finché tutti i fusi orari del mondo corrispondono all'intervallo di tempo impostato per la campagna. L'utilizzo del fuso orario degli utenti finali, inoltre, può causare ritardi nelle campagne poiché è necessario chiedere l'ora al telefono prima di iniziare il push.

> Nota: quando le campagne non hanno data di fine, i push possono essere memorizzati nella cache locale ed essere ancora visualizzati dopo aver completato manualmente la campagna. Per evitare questo comportamento, specificare un'ora di fine per le campagne.

### Vedere anche
- [Reach - Procedure – Pianificazione][Link 3] 
 
![Reach-Campaign8][27]

### Le impostazioni di applicano a:
- Intervallo di tempo: annunci, sondaggi e riquadri
 
## Test
È possibile usare la sezione Test per inviare il push al dispositivo di test prima di salvare la campagna. Se sono state configurate lingue personalizzate per la campagna, è possibile testare il push in ciascuna lingua. È possibile configurare un dispositivo di test da "Account personale".
> Nota: non vengono registrati dati lato server quando si usa il pulsante per testare i push, vengono registrati solo i dati per le campagne di push reali.

### Vedere anche
- [Documentazione dell'interfaccia utente - Account personale][Link 14]
 
![Reach-Campaign9][28]

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
 

<!----HONumber=August15_HO7-->