<properties 
 pageTitle="Interfaccia utente di Azure Mobile Engagement - Copertura " 
 description="Panoramica dell'interfaccia utente per la sezione Copertura di Azure Mobile Engagement" 
 services="mobile-engagement" 
 documentationCenter="mobile" 
 authors="v-micada" 
 manager="mattgre" 
 editor=""/>

<tags
 ms.service="mobile-engagement"
 ms.devlang="Java"
 ms.topic="article"
 ms.tgt_pltfrm="mobile"
 ms.workload="required" 
 ms.date="02/17/2015"
 ms.author="v-micada"/>

# Azure Mobile Engagement - Interfaccia utente

<div class="dev-center-tutorial-selector sublanding">
<a href="../mobile-engagement-user-interface" title="Introduction">Introduzione</a>
<a href="../mobile-engagement-user-interface-navigation" title="Navigation">Navigazione</a>
<a href="../mobile-engagement-user-interface-home/" title="Home">Home</a>
<a href="../mobile-engagement-user-interface-my-account" title="My Account">Account personale</a>
<a href="../mobile-engagement-user-interface-analytics" title="Analytics">Analytics</a>
<a href="../mobile-engagement-user-interface-monitor" title="Monitor">Monitoraggio</a>
<a href="../mobile-engagement-user-interface-reach" title="Reach">Copertura</a>
<a href="../mobile-engagement-user-interface-segments" title="Segments">Segmenti</a>
<a href="../mobile-engagement-user-interface-dashboard" title="Dashboard">Dashboard</a>
<a href="../mobile-engagement-user-interface-settings" title="Settings">Impostazioni</a>
</div>

# <a name="Reach">Copertura</a>
 
La sezione Copertura dell'interfaccia utente costituisce lo strumento di gestione delle campagne push dove è possibile creare, modificare, attivare, terminare, monitorare e ottenere statistiche per le campagne di notifica push nonché funzionalità a cui è possibile accedere anche tramite l'API Copertura e alcuni elementi dell'API Push di basso livello. Tenere presente che a prescindere che si utilizzino le API o l'interfaccia utente, è necessario integrare Azure Mobile Engagement e Copertura nell'applicazione per ogni piattaforma con l'SDK prima di poter utilizzare le campagne di copertura.

**Vedere anche:** 

- [Documentazione API - API Copertura][Link 4], [Documentazione API - API Push][Link 4], [Guida per la risoluzione dei problemi - Push/Copertura][Link 2]
- <a href="#ReachCampaign" title="Reach Campaign">Campagna di copertura</a>, <a href="#ReachCriterion" title="Reach Criterion">Criterio di copertura</a>, <a href="#ReachContent" title="Reach Content">Contenuto della copertura</a>
 
## Quattro tipi di notifiche Push:
1. Annunci: consentono di inviare messaggi pubblicitari agli utenti che li reindirizzano a un'altra posizione all'interno dell'app o di inviarli a una pagina Web o a un archivio esterno all'app. 
2. Sondaggi: consentono di raccogliere informazioni dagli utenti finali ponendo loro domande.
3. Push di dati: consentono di inviare un file di dati binario o base64. Le informazioni contenute in un push di dati vengono inviate all'applicazione per modificare l'esperienza corrente degli utenti nell'app. L'applicazione deve essere in grado di elaborare i dati di un push di dati.
4. Riquadri (solo Windows Phone): consentono di utilizzare Servizi notifica Push di Microsoft (MPNS) per inviare push Windows nativi contenenti dati XML (supportato a partire dall'SDK versione 0.9.0. Il payload finale per i riquadri non può superare i 32 KB).

**Vedere anche:** 

- [Concetti - Glossario][Link 6]

## Tre categorie di statistiche in tempo reale visualizzate per ogni campagna: 
1. Push inviati: il numero di push inviati in base ai criteri specificati nella campagna. 
2. Risposte: il numero di utenti che ha reagito alla notifica aprendola all'esterno dell'applicazione o chiudendola nell'applicazione. 
3. Considerate: il numero di utenti che hanno fatto clic sul collegamento nella notifica per essere reindirizzati a una nuova posizione nell'applicazione, a un archivio o a un browser Web. 

> Nota: sono disponibili statistiche della campagna più dettagliate tramite le statistiche dell'API Copertura

**Vedere anche:** 

- [Concetti - Glossario][Link 6], [Documentazione API - API Copertura - Statistiche][Link 4]


## Dettagli campagna:
È possibile modificare, duplicare, eliminare o attivare campagne che non sono state ancora attivate passando il puntatore sopra i relativi nomi oppure fare clic per aprirle. È possibile duplicare campagne che sono già state attivate passando il puntatore sopra i nomi o fare clic per aprirle. Tuttavia, non è possibile modificare una campagna dopo che è stata attivata.
 
![Reach1][18]

## Feedback di copertura:
È possibile passare dalla visualizzazione dei dettagli a quella della statistiche di una campagna che è già stata attivata e passare dalla visualizzazione semplice a quella avanzata delle statistiche per ottenere informazioni più dettagliate (a seconda delle autorizzazioni). È inoltre possibile utilizzare le informazioni di feedback di copertura di una campagna precedente come criteri di definizione dei destinatari di una nuova campagna. Le statistiche di feedback di copertura possono essere raccolte anche tramite "Statistiche" dall'API Copertura. È inoltre possibile personalizzare i destinatari delle campagne di push in base a campagne precedenti.


**Vedere anche:** 

- <a href="#ReachCampaign">Documentazione dell'interfaccia utente - Copertura - Nuova campagna di push</a>, [Documentazione API - API Copertura - Statistiche][Link 4]
![Reach2][19]

## <a name="ReachCampaign">Copertura - Nuova campagna di push:</a>
 
È possibile utilizzare la sezione Copertura dell'interfaccia utente per creare una nuova campagna di push con una formula complessa fornendo tutte le informazioni necessarie per inviare una notifica push. Le opzioni di una campagna di push variano leggermente in base ai quattro tipi di campagna: annunci, sondaggi, push di dati e riquadri (solo Windows Phone).

**L'opzione si applica a:**

- Lingue: tutti (annunci, sondaggi, push di dati, riquadri)
- Campagna: tutti (annunci, sondaggi, push di dati, riquadri)
- Notifica: annunci, sondaggi
- Contenuto: univoco per ogni tipo di campagna
- Destinatari: tutti (annunci, sondaggi, push di dati, riquadri)
- Intervallo di tempo: annunci, sondaggi, riquadri
- Test: tutti (annunci, sondaggi, push di dati, riquadri)
 
![Reach-Campaign1][20]

## Lingue:
È possibile utilizzare il menu a discesa Lingue per inviare una versione diversa del push ai dispositivi configurati per utilizzare lingue diverse. Per impostazione predefinita, tutti i dispositivi riceveranno lo stesso push indipendentemente dalla lingua utilizzata. Gli utenti con il dispositivo impostato su una lingua diversa riceveranno la versione del push nella lingua predefinita. Molte opzioni della campagna di push consentono di specificare contenuto alternativo per ognuna delle lingue aggiuntive selezionate. 
 
![Reach-Campaign2][21]

**Le differenze di lingua si applicano a:**

- Lingue: è possibile selezionare lingue univoche oltre alla lingua predefinita
- Campagna: uguale per tutte le lingue
- Notifica: univoca per ogni lingua oltre a quella predefinita
- Contenuto: univoco per ogni lingua oltre a quella predefinita
- Destinatari: possono essere filtrati in base a un criterio di lingua distinto
- Intervallo di tempo: uguale per tutte le lingue
- Test: può essere inviato a tutte le lingue contemporaneamente
 
**Lingue supportate:**

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
 
## Campagna:
È possibile utilizzare la sezione Campagna per impostare il nome e la categoria della campagna nonché, se si intende ignorare la sezione dei destinatari di una campagna di push, inviare invece la campagna tramite l'API Copertura (e alcuni elementi con l'API Push di basso livello). Le categorie possono essere utilizzate con un modello di notifica personalizzato per controllare le notifiche in-app in base alle impostazioni predefinite. È possibile ottenere un elenco di "Categorie" esistenti tramite l'API Copertura.

> Avviso: se si utilizza l'opzione "Ignora destinatari, il push verrà inviato agli utenti tramite l'API" nella sezione "Campagna" di una campagna di copertura, la campagna NON sarà inviata automaticamente, ma sarà necessario inviarla manualmente tramite l'API Copertura.
 
![Reach-Campaign3][22]
 
**L'opzione si applica a:**

- Nome: Tutti
- Categoria: annunci, sondaggi
- Ignora destinatari, il push verrà inviato agli utenti tramite l'API: Tutti
 
## Notifica:
È possibile utilizzare la sezione Notifica per definire le impostazioni di base per il push, ad esempio  il titolo del push, il messaggio, un'immagine in-app, nonché per stabilire se è revocabile. Molte impostazioni di notifica sono specifiche della piattaforma del dispositivo. È possibile scegliere se il push verrà inviato nell'applicazione, fuori dall'applicazione o in entrambi i modi. Tenere presente che gli utenti possono accettare o rifiutare esplicitamente i push fuori dall'applicazione a livello del sistema operativo sui propri dispositivi e Azure Mobile Engagement non potrà eseguire l'override di questa impostazione. Tenere presente, inoltre, che l'API Copertura gestisce i push nell'applicazione e fuori dall'applicazione. L'API Push può essere utilizzata anche per gestire push esterni all'applicazione. I push possono essere personalizzati con immagini o contenuto HTML, inclusi collegamenti diretti per il collegamento all'esterno dell'applicazione o a un'altra posizione nell'applicazione (è necessario l'SDK Android 2.1.0 o categorie successive). È possibile modificare l'icona o il badge iOS e inviare testo o contenuto Web (una finestra popup con contenuto HTML, un collegamento URL a un'altra posizione all'interno o all'esterno dell'applicazione). È inoltre possibile impostare lo squillo o la vibrazione dei dispositivi Android con il push. Tenere presente che perché un dispositivo squilli o vibri il file manifesto Android deve contenere le autorizzazioni dell'SDK corretto. Non esiste alcuno standard del settore per le dimensioni dell'immagine grande di Android perché le dimensioni dello schermo variano con ogni dispositivo. Tuttavia, le immagini da 400x100 sono adatte a quasi a tutte le dimensioni degli schermi.

## Tipi di recapito:
- Solo fuori app: la notifica sarà recapitata quando l'utente non usa l'applicazione
- La notifica solo esterna all'app richiede un certificato di Apple o Google (certificato APNS o GCM).
- Solo in-app: la notifica viene visualizzata solo quando l'applicazione è in esecuzione.
- La notifica utilizza il sistema di recapito Capptain per raggiungere l'utente. È possibile personalizzare completamente il layout o la visualizzazione del push.
- In qualsiasi momento: questa opzione assicura che una notifica venga inviata a prescindere che l'applicazione sia in esecuzione o meno.

 
![Reach-Campaign4][23]

**L'opzione si applica a:**

- Notifica: annunci, sondaggi
 
## Contenuto:
È possibile utilizzare la sezione Contenuto per modificare il contenuto di annunci, sondaggi, push di dati e riquadri (solo Windows Phone). L'impostazione del contenuto delle campagne di push è specifico per il tipo di campagna. 

**Vedere anche:** 

- <a href="#ReachContent">Documentazione dell'interfaccia utente - Copertura - Contenuto del push</a>
 
![Reach-Campaign5][24]

## Destinatari:
È possibile utilizzare la sezione Destinatari per definire un elenco standard di elementi per limitare la campagna in base a criteri personalizzati. L'insieme standard di opzioni per limitare i destinatari consente di effettuare il push solo per utenti nuovi, esistenti o nativi. È inoltre possibile impostare una quota per limitare il numero di utenti che ricevono il push. È possibile modificare manualmente l'espressione per la modalità di filtro della campagna in modo da includere uno o più criteri per gli utenti di destinazione. È possibile digitare manualmente un'espressione di destinatari. Tale espressione deve definire in modo esplicito la relazione tra i criteri. Un criterio viene descritto da un identificatore che deve iniziare con una lettera maiuscola e non può contenere spazi. La relazione tra i criteri può essere descritta con gli operatori  'and', 'or', 'not' e '(',')'. Esempio: "Criterion1 or (Criterion1 and not Criterion2)".

> Nota: con numerosi destinatari inclusi nelle campagne, la ricerca dei destinatari sul lato server può risultare lenta, soprattutto se si tenta di avviare più campagne contemporaneamente.

- Se possibile, iniziare una sola campagna alla volta.
- Al massimo, avviare quattro campagne alla volta.
- Eseguire il push solo per gli utenti attivi (casella di controllo "Solo utenti raggiungibili con push nativo " e "Solo utenti attivi") in modo che dovranno essere individuati solo gli utenti che hanno l'app installata e che la utilizzano ancora.
Una volta definiti i destinatari, è possibile utilizzare il pulsante di simulazione per scoprire quanti utenti riceveranno il push. Verrò calcolato il numero di utenti noti potenzialmente appartenenti al gruppo di destinatari (si tratta di una stima basata su un campione casuale di utenti). Tenere presente che anche gli utenti che hanno disinstallato l'applicazione fanno parte di questi destinatari, ma non possono essere raggiunti.

**Vedere anche:** 

- <a href="#ReachCriterion">Documentazione dell'interfaccia utente - Copertura - Nuovo criterio di push</a>

![Reach-Campaign6][25]

## Modifica espressione
 
![Reach-Campaign7][26]
 
**L'opzione Limita destinatari si applica a:**

- Solo un sottoinsieme di utenti: tutti (annunci, sondaggi, push di dati, riquadri)
- Solo vecchi utenti: tutti (annunci, sondaggi, push di dati, riquadri)
- Solo nuovi utenti: tutti (annunci, sondaggi, push di dati, riquadri)
- Solo utenti inattivi: annunci, sondaggi, riquadri
- Solo utenti attivi: tutti (annunci, sondaggi, push di dati, riquadri)
- Solo utenti raggiungibili con push nativo: annunci, sondaggi
 
## Intervallo di tempo:
È possibile utilizzare la sezione Intervallo di tempo per stabilire quando verrà inviato il push oppure omettere l'intervallo di tempo per avviare immediatamente la campagna. Si tenga presente che se si utilizza il fuso orario degli utenti finali, la campagna potrebbe iniziare un giorno prima del previsto per gli utenti finali in Asia, per cui è consigliabile inviare piccoli gruppi di push alla volta finché tutti i fusi orari del mondo corrispondono all'intervallo di tempo impostato per la campagna. L'utilizzo del fuso orario degli utenti finali, inoltre, può causare ritardi nelle campagne poiché è necessario chiedere l'ora al telefono prima di iniziare il push.

> Nota: quando le campagne non hanno data di fine, i push possono essere memorizzati nella cache locale ed essere ancora visualizzati dopo aver completato manualmente la campagna. Per evitare questo comportamento, specificare un'ora di fine per le campagne.

**Vedere anche:** 

- [Procedure - Pianificazione][Link 3] 
 
![Reach-Campaign8][27]

**Le impostazioni di applicano a:**

- Intervallo di tempo: annunci, sondaggi, riquadri
 
## Test:
È possibile utilizzare la sezione Test per inviare il push al dispositivo di test prima di salvare la campagna. Se sono state configurate lingue personalizzate per la campagna, è possibile testare il push in ciascuna lingua. È possibile configurare un dispositivo di test da "Account personale".
> Nota: non vengono registrati dati lato server quando si utilizza il pulsante per testare i push, vengono registrati solo i dati per le campagne di push reali.

**Vedere anche:** 

- [Documentazione dell'interfaccia utente - Account personale][Link 14]
 
![Reach-Campaign9][28]

## <a name="ReachCriterion">Copertura - Nuovo criterio di push (per la definizione dei destinatari)</a>

La possibilità di definire i destinatari tramite criteri specifici con il pulsante "Nuovi criteri" è una delle funzioni più potenti di Azure Mobile Engagement in quando consente di inviare notifiche di push rilevanti a cui i clienti risponderanno anziché inviare spamming a tutti gli utenti. È possibile limitare i destinatari in base ai criteri standard e simulare i push per stabilire quanti utenti riceveranno la notifica.

**Vedere anche:** 

- <a href="#ReachCampaign">Documentazione dell'interfaccia utente - Copertura - Nuova campagna di push</a>

## I criteri dei destinatari possono includere:

- **Informazioni tecniche: **è possibile stabilire i destinatari in base alle informazioni tecniche visualizzate nelle sezioni Analisi e Monitoraggio. **Vedere anche:** [Documentazione dell'interfaccia utente - Analytics][Link 15], [Documentazione dell'interfaccia utente - Monitoraggio][Link 16]
- **Posizione:** le applicazioni che utilizzano la segnalazione della posizione in tempo reale con geofencing possono utilizzare la posizione geografica per definire i destinatari in base alla posizione del GPS. È possibile, inoltre, utilizzare la chiamata di segnalazione della posizione della Lazy Area per definire destinatari in base alla posizione dei telefoni cellulari (queste due funzioni di segnalazione della posizione devono essere attivate dall'SDK). **Vedere anche:**[Documentazione SDK - iOS - Integrazione][Link 5], [Documentazione SDK - Android - Integrazione][Link 5]
- **Feedback di copertura:** è possibile definire i destinatari in base al loro feedback sulle notifiche di copertura precedenti tramite il feedback di copertura derivante da annunci, sondaggi e push di dati. Ciò consente di definire meglio i destinatari dopo due o tre campagne di copertura rispetto alla prima volta. Il feedback può essere utilizzato, inoltre, per filtrare gli utenti che hanno già ricevuto una notifica con contenuto simile, impostando una campagna che NON deve essere inviata agli utenti che hanno già ricevuto una specifica campagna precedente. È anche possibile escludere gli utenti inclusi in una campagna specifica ancora attiva in modo che non ricevano nuove notifiche push. **Vedere anche:**<a href="#ReachContent">Documentazione dell'interfaccia utente - Copertura - Contenuto del push </a>
- **Rilevamento installazione:** è possibile rilevare le informazioni in base alla posizione in cui gli utenti hanno installato l'applicazione. **Vedere anche:** [Documentazione dell'interfaccia utente - Impostazioni][Link 20]
- **Profilo utente:** è possibile definire i destinatari in base alle informazioni standard sugli utenti e a info app create personalmente. Sono inclusi gli utenti connessi e gli utenti che hanno risposto a domande specifiche che si è chiesto loro di impostare nell'app stessa anziché in base a come hanno risposto alle campagne precedenti. Tutte le info app definite per l'app vengono visualizzate nell'elenco.
- Segmenti: è possibile definire i destinatari in base ai segmenti creati a seconda del comportamento utente specifico definito da più criteri. Tutti i segmenti definiti per l'app vengono visualizzati nell'elenco. **Vedere anche:** [Documentazione dell'interfaccia utente - Segmenti][Link 18]
- **Info app:** i tag di info app personalizzate possono essere creati tramite "Impostazioni" per verificare il comportamento degli utenti. **Vedere anche:** [Documentazione dell'interfaccia utente - Impostazioni][Link 20]
## Esempio: 
Se si desidera eseguire il push di un annuncio solo per un sottoinsieme di utenti che hanno eseguito un'azione di acquisto in-app.

1. Andare alla pagina delle impostazioni dell'applicazione, selezionare il menu "Info app" e selezionare "Nuove info app"
2. Registrare nuove info app booleane definite "inAppPurchase"
3. Fare in modo che l'applicazione imposti tali info app su "true" quando l'utente esegue correttamente un acquisto in-app (tramite la funzione sendAppInfo ("inAppPurchase",...))
4. Se non si desidera eseguire questa operazione dall'applicazione, è possibile effettuarla dal back-end tramite l'API dispositivo)
5. Quindi, è sufficiente creare l'annuncio con un criterio di limitazione dei destinatari agli utenti con "inAppPurchase" impostato su "true")
 
> Nota: la definizione dei destinatari in base a criteri diversi dai tag di info app richiede che Azure Mobile Engagement raccolga informazioni dai dispositivi degli utenti prima che il push venga inviato, per cui può provocare un ritardo. Anche le opzioni di configurazione push complesse (ad esempio l'aggiornamento dei badge) possono determinare ritardi dei push. L'utilizzo di una campagna "one-shot" dall'API Push è in assoluto il metodo di push più veloce offerto da Azure Mobile Engagement. L'utilizzo dei soli tag di info app come criteri di push per una campagna di copertura (dall'API Copertura o dall'interfaccia utente) è il successivo metodo più rapido perché i tag di info app vengono memorizzati nel server. L'utilizzo di altri criteri di definizione dei destinatari per una campagna di push è il metodo di push più flessibile ma più lento poiché Azure Mobile Engagement deve interrogare i dispositivi per inviare la campagna.
 
![Reach-Criterion1][29] 

**Le opzioni dei criteri si applicano a:**

- **Informazioni tecniche** 
- Nome firmware: Nome del firmware
- Versione firmware: Versione del firmware
- Modello dispositivo: Modello del dispositivo
- Produttore dispositivo: Produttore del dispositivo
- Versione applicazione: Versione dell'applicazione
- Nome operatore: Nome dell'operatore, non definito
- Paese operatore: Paese dell'operatore, non definito
- Tipo di rete: Tipo di rete
- Impostazioni locali: Impostazioni locali
- Dimensioni schermo: Dimensioni dello schermo
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
- **Info app** 
- Stringhe: stringa, non definita
- Data: operatore, data, non definita
- Numero intero: operatore, numero, non definito
- Booleano: true o false, non definito
- **Segmento** 
- Nome di segmenti (dall'elenco a discesa), esclusione (utenti di destinazione che non fanno parte del segmento).

## <a name="ReachContent">Copertura - Contenuto del push (per ogni tipo di campagna)</a>
 
È possibile utilizzare la sezione Contenuto di una nuova campagna di copertura per modificare il contenuto di annunci, sondaggi, push di dati e riquadri (solo per Windows Phone). L'impostazione del contenuto delle campagne di push è specifico per il tipo di campagna. 
 
## Tipi di contenuto
- Annunci
- Sondaggi
- Push di dati
- Riquadri (solo Windows Phone)
 
## Contenuto degli annunci
 
 ![Reach-Content1][30] 

## Scegliere il tipo di annuncio:
- Solo notifica: una semplice notifica standard. Vale a dire che se un utente fa clic, non apparirà alcuna ulteriore visualizzazione, ma si verificherà semplicemente l'azione associata.
- Annuncio di testo: una notifica che incoraggia l'utente a esaminare una visualizzazione di testo.
- Annuncio Web: una notifica che incoraggia l'utente a esaminare una visualizzazione Web.

**Vedere anche:** 

- [Procedure - Annunci][Link 3] 

**Informazioni sugli annunci di visualizzazione Web:**

Le ricorrenze del modello "{deviceid}" nel codice HTML o nel codice JavaScript fornito in questa sezione verranno automaticamente sostituite dall'identificatore del dispositivo che visualizza l'annuncio. Si tratta di un metodo semplice per recuperare gli identificatori dei dispositivi di Azure Mobile Engagement in un servizio Web esterno ospitato sul back-office.
Se si desidera creare una visualizzazione Web a schermo intero (senza i pulsanti di azione e uscita predefiniti forniti), è possibile utilizzare le seguenti funzioni dal codice JavaScript dell'annuncio di visualizzazione Web: 

- Eseguire l'azione dell'annuncio: ReachContent.actionContent()
- uscire dall'annuncio: ReachContent.exitContent()
 
## Scegliere l'azione:

**Informazioni sugli URL di azione:**

qualsiasi URL che può essere interpretato dal sistema operativo di un dispositivo di destinazione può essere utilizzato come un URL di azione.
Qualsiasi URL dedicato che potrebbe essere supportato dall'applicazione (ad esempio per far sì che gli utenti passino a una schermata specifica) può essere utilizzato anche come URL di azione.
Ogni ricorrenza del modello {deviceid} verrà automaticamente sostituita dall'identificatore del dispositivo che esegue l'azione. Questo metodo consente di recuperare facilmente gli identificatori dei dispositivi di Azure Mobile Engagement tramite un servizio Web esterno ospitato sul back-office.

- **Azioni di Android e iOS**
 - Aprire una pagina Web
 - http://[dominio-sito-web] 
 - Esempio:http://www.azure.com
 - Inviare un messaggio di posta elettronica
 - mailto:[destinatario-messaggio]?subject=[oggetto]&body=[messaggio] 
 - Esempio:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
 - Inviare un SMS
 - sms:[numero-telefono] 
 - Esempio:sms:2125551212
 - Comporre un numero di telefono
 - tel:[numero-telefono] 
 - Esempio:tel:2125551212
- **Azioni solo di Android**
 - Scaricare un'applicazione in Play Store
 - market://details?id=[pacchetto app] 
 - Esempio:market://details?id=com.microsoft.office.word
 - Avviare una ricerca di localizzazione geografica
 - geo:0,0?q=[query di ricerca] 
 - Esempio:geo:0,0?q=starbucks,paris
- **Azioni solo di iOS**
 - Scaricare un'applicazione in App Store
 - http://itunes.apple.com/[paese]/app/[nome app ]/id[id app]?mt=8 
 - Esempio:http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
 - Azioni Windows
 - Aprire una pagina Web
 - http://[dominio-sito-web] 
 - Esempio:http://www.azure.com
 - Inviare un messaggio di posta elettronica
 - mailto:[destinatario-messaggio]?subject=[oggetto]&body=[messaggio] 
 - Esempio:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
 - Inviare un SMS (è necessario Skype Store App)
 - sms:[numero-telefono] 
 - Esempio:sms:2125551212
 - Comporre un numero di telefono (è necessario Skype Store App)
 - tel:[numero-telefono] 
 - Esempio:tel:2125551212
 - Scaricare un'applicazione in Play Store
 - ms-windows-store:PDP?PFN=[ID pacchetto app] 
 - Esempio:ms-windows-store:PDP?PFN=4d91298a-07cb-40fb-aecc-4cb5615d53c1
 - Avviare una ricerca in Bing Mappe
 - bingmaps:?q=[query ricerca] 
 - Esempio:bingmaps:?q=starbucks,paris
 - Utilizzare uno schema personalizzato
 - [schema personalizzato]://[parametri schema personalizzato] 
 - Esempio:myCustomProtocol://myCustomParams
 - Utilizzare dati di un pacchetto (è necessario App Store per la lettura dell'estensione)
 - [cartella][dati].[estensione]
 - Esempio:myfolderdata.txt
 
## Creare un URL di rilevamento:
- Per istruzioni sulla creazione di un URL di rilevamento che consente agli utenti di scaricare una delle altre applicazioni, vedere la sezione "Impostazioni" della <Documentazione dell'interfaccia utente>.
 
## Definire il testo dell'annuncio
Compilare il titolo, il contenuto e il testo dei pulsanti dell'annuncio. 
È possibile definire i destinatari di una campagna futura in base al feedback di copertura sulla risposta degli utenti alla campagna corrente. È possibile definire i destinatari a seconda che la campagna sia stata solo oggetto di push, che gli utenti abbiano risposto, abbiano eseguito un'azione o si siano scollegati.

**Vedere anche:** 
- <a href="#ReachCriterion">Documentazione dell'interfaccia utente - Copertura - Nuovo criterio di push</a>

## Contenuto dei sondaggi
 
![Reach-Content2][31] 
Compilare il titolo, la descrizione e il testo dei pulsanti dell'annuncio. 
Aggiungere quindi domande e opzioni per le risposte alle domande.
È possibile definire i destinatari di una campagna futura in base al feedback di copertura sulla risposta degli utenti alla campagna corrente. È possibile definire i destinatari a seconda che la campagna sia stata solo oggetto di push, che gli utenti abbiano risposto, eseguito un'azione o si siano scollegati. La definizione dei destinatari può essere basata anche sul feedback di risposta ai sondaggi, dove domande e opzioni di risposta vengono utilizzate come criteri.

**Vedere anche:** 

- <a href="#ReachCriterion">Documentazione dell'interfaccia utente - Copertura - Nuovo criterio di push</a>
 
## Contenuto dei push di dati
 
![Reach-Content3][32] 

## Scegliere il tipo di dati 
- Testo
- Dati binari
- Dati Base64 

## Definire il contenuto dei dati
- Se si seleziona il push dei dati di testo, copiare e incollare il testo nella casella "contenuto".
- Se si seleziona il push dei dati binari o base64, utilizzare il pulsante "carica il file" per caricare il file.
- È possibile definire i destinatari di una campagna futura in base al feedback di copertura sulla risposta degli utenti alla campagna corrente. È possibile definire i destinatari a seconda che la campagna sia stata solo oggetto di push, che gli utenti abbiano risposto, eseguito un'azione o si siano scollegati.

**Vedere anche:** 

- <a href="#ReachCriterion">Documentazione dell'interfaccia utente - Copertura - Nuovo criterio di push</a>

## Contenuto dei riquadri (solo Windows Phone)

![Reach-Content4][33]

## Definire il contenuto del riquadro
Il payload dei riquadri è il testo da visualizzare nel riquadro dell'app sui dispositivi Windows Phone.
Un push di riquadri è la versione Servizio di notifica Push di Microsoft (MPNS) di un push nativo per Windows Phone. Il tipo di push di riquadri è l'unico tipo di push che non ha risposta e pertanto non è possibile creare i destinatari delle campagne future in base ai risultati di una campagna di push di riquadri. 

**Vedere anche:** 

- [Documentazione dell'API - API Copertura - Push nativo][Link 4]

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
[20]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach/Reach-Content4.png
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
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/

<!--HONumber=47-->
