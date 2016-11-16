---
title: Definire la strategia di Mobile Engagement | Documentazione Microsoft
description: Informazioni su come implementare e ottimizzare Mobile Engagement con analisi e notifiche push.
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 7533e318-81b9-4360-aace-b7be8225985b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8cb91a8cdc6d16070034c79515731be7b820d389


---
# <a name="define-your-mobile-engagement-strategy"></a>Definire la strategia di Mobile Engagement
*Le app vengono scritte per un motivo ben preciso: essere usate dagli utenti.*

È certamente necessario molto impegno per riuscire a realizzare un'app di qualità che piaccia ai propri utenti. È anche probabile che sia stata investita una parte considerevole del budget di marketing disponibile per acquisire il maggior numero possibile di utenti. Ma, dopo un incoraggiante picco iniziale, è facile assistere a una graduale diminuzione degli utenti dell'app. *Mobile Engagement serve proprio a evitare queste situazioni*: con questo servizio, gli autori possono promuovere l'uso di app e migliorarne le funzionalità grazie a test e informazioni sull'utilizzo.

L'approccio Microsoft al miglioramento della fidelizzazione e dell'utilizzo si basa sulla stimolazione dell'interesse degli utenti dell'app con notifiche push e messaggi in-app, ma in modo del tutto speciale: i messaggi e le comunicazioni sono personalizzati e si basano sul comportamento degli utenti durante l'uso dell'app. L'obiettivo è abilitare la comunicazione con il pubblico desiderato nel luogo giusto e al momento opportuno.

Per realizzare questo obiettivo, è necessario prima di tutto *comprendere le esigenze degli utenti*, quindi creare gruppi basati sulle loro azioni o caratteristiche (in questo caso si parla di segmenti) e infine creare comunicazioni appropriate per ogni segmento.

## <a name="mobile-engagement-serves-your-objectives"></a>Mobile Engagement al servizio degli obiettivi degli autori di app
*Fidelizzazione e utilizzo: due concetti chiave*

Lo sviluppo di una strategia di Mobile Engagement presuppone prima di tutto una valutazione degli obiettivi e degli indicatori di prestazioni chiave (KPI) dell'app.

La definizione di tali obiettivi e KPI aiuterà a esaminare i propri casi di utilizzo dalla giusta prospettiva.

I casi di utilizzo consistono in un semplice elenco di campagne che si vorrebbero realizzare per comunicare con gli utenti e che possono spaziare da un semplice benvenuto fino a sofisticate utilità di notifica attivate dal proprio reparto IT. Un caso di utilizzo ben strutturato deve includere almeno tre elementi fondamentali, *cosa-chi-quando*:

1. Una designazione molto breve (ad esempio una "Campagna di benvenuto").
2. **Cosa**: un messaggio di esempio (come "Benvenuto! Ricorda di effettuare l'accesso per ottenere il tuo primo mese di uso gratuito."). Questo messaggio non è affatto definitivo e potrà essere modificato in qualsiasi momento, ma in genere è utile per iniziare a pensare a cosa dire.
3. **Chi**: il segmento che riceverà questo messaggio (ad esempio "Tutti gli utenti che hanno avviato l'app per la prima volta tre giorni fa, hanno visitato la pagina di accesso ma non hanno eseguito l'accesso").
   * Con Azure Mobile Engagement ciò è molto facile.
   * Anche in questo caso, il segmento non deve necessariamente essere quello finale, perché è possibile definire i segmenti in qualsiasi momento, ma è importante definire il criterio di segmentazione nelle fasi iniziali per essere certi di raccogliere i dati corretti.
4. **Quando**: il momento di attivazione della campagna. Può essere in una determinata data, in seguito all'esecuzione di un'azione specifica oppure in base a un trigger di attivazione. Mobile Engagement offre un numero notevole di possibilità per inviare le comunicazioni al momento giusto.

Una volta definiti i casi di utilizzo, vengono fornite delle linee guida per definire i dati che dovranno essere raccolti all'interno dell'applicazione. Questo ruolo è affidato al cosiddetto *"piano di tag"*. Un piano di tag consente di fare in modo che la raccolta dei dati venga specificata agli sviluppatori. Questi ultimi possono pertanto incorporare Mobile Engagement con la giusta configurazione che consentirà di condurre le campagne usando le informazioni corrette. L'esecuzione dei test sarà anch'essa di estrema importanza per garantire la correttezza dell'integrazione e della raccolta dei dati necessari.

In base al livello di integrazione, una volta pubblicate le applicazioni, come addetti al marketing si sarà in grado di visualizzare dati analitici in tempo reale, segmentare i gruppi di destinatari e iniziare a inviare notifiche push intelligenti per coinvolgere gli utenti finali all'interno o all'esterno dell'app.

### <a name="usecases-to-get-started"></a>Casi di utilizzo per iniziare
1. Strategia di benvenuto: creare varie campagne di notifica push in base al comportamento degli utenti finali durante il lancio dell'applicazione, in modo da riprendere contatto con loro dopo 2, 5, 10 o 15 giorni dalla prima sessione e aumentare la fidelizzazione dopo il primo utilizzo.
2. Promuovere un nuovo contenuto (funzionalità, articolo/video o prodotto) in base al comportamento dell'utente finale, in modo da inviare le informazioni solo agli utenti finali che con ogni probabilità saranno più interessati.
3. Valutare l'applicazione: rivolgersi a meno dell'1% della propria base utenti che con maggiore probabilità valuterà l'app con cinque stelle nello store.
4. Aumentare le sottoscrizioni: promuovere contenuti utili per utenti finali che ancora non li conoscono allo scopo di aumentare il numero di sottoscrizioni.
5. Esercitazione: non più esercitazioni obbligatorie per tutti gli utenti. Meglio realizzare utili esercitazioni in-app attivabili tramite messaggi in-app solo quando l'utente non sembra fare uso dell'app o sembra avere problemi con una funzionalità.

## <a name="why-do-you-need-analytics-to-engage"></a>Importanza dei dati analitici per coinvolgere gli utenti
Come si sarà già intuito, la semplice trasmissione di una notifica push non è sufficiente. Il concetto centrale di Mobile Engagement è aiutare sviluppatori e addetti al marketing a coinvolgere l'utente finale appropriato, nel posto giusto e al momento opportuno. Per mettere in pratica questi tre elementi fondamentali è essenziale raccogliere dati analitici dalle proprie applicazioni e usarli per segmentare i destinatari. Questa pratica si rivela ancora più efficace quando i segmenti basati sul comportamento vengono abbinati a dati provenienti da altri database, sistemi CRM o canali incrociati. Mobile Engagement consente di recuperare dati da qualsiasi origine e di usarli per rivolgersi ai giusti destinatari.

È quindi consigliabile essere il più contestuali possibile quando ci si rivolge alla propria utenza ed è fondamentale essere consapevoli del comportamento degli utenti finali per conoscerne lo stato in tempo reale. La raccolta dei dati consente agli addetti al marketing di concentrarsi su ciò che conta veramente per individuare i casi di utilizzo e raggiungere gli obiettivi della strategia di Mobile Engagement. Per raggiungere gli obiettivi definiti in precedenza, è anche necessario raccogliere solo i dati che permettono di concentrarsi su ciò che si vuole apprendere e sui casi di utilizzo e non tutti i dati delle analisi indistintamente. Questo è un buon inizio. Provare, testare e apprendere come usare la soluzione e indirizzare notifiche push intelligenti per aumentare la fedeltà degli utenti a un'applicazione portandola così al livello di un caso di successo.

> [!NOTE]
> Ricordare sempre che troppi dati sono controproducenti.
> 
> 

### <a name="usecases-and-best-practices"></a>Casi di utilizzo e procedure consigliate
Nelle sezioni seguenti sono illustrati in breve alcuni casi di utilizzo principali rilevati tra i clienti Microsoft.

#### <a name="media"></a>Contenuti multimediali
Raccogliere dati sui tipi di contenuti che vengono utilizzati dagli utenti finali e quindi segmentare i destinatari in base a tale comportamento per indirizzare un tipo di contenuto specifico solo a quei destinatari che presentano maggiori probabilità di farne uso. Questo consente di evitare lo spamming nell'intera base utenti e garantisce un migliore assorbimento dell'utenza.

#### <a name="mcommerce"></a>M-commerce
Raccogliere le categorie di prodotto più visitate all'interno dell'applicazione e selezionare i destinatari tra cui promuovere sconti o nuovi prodotti nelle categorie in cui è più probabile che l'utente finale completi un acquisto. Puntare all'aumento dei ricavi. Anche qui, l'obiettivo è evitare lo spamming.

#### <a name="gaming"></a>Giochi
Raccogliere dati sul livello di gioco di un utente finale e sul tempo di gioco trascorso in un determinato periodo per rivolgersi ai destinatari che potrebbero essere bloccati e che passerebbero volentieri a un livello successivo con un'offerta bonus.

Comunicare eventi specifici, incentivando gli utenti che non giocano più da un certo periodo di tempo, per invogliarli a tornare.

#### <a name="retail"></a>Vendita al dettaglio
Raccogliere dati su prodotti o marche che un certo gruppo di destinatari potrebbe consumare con maggiore probabilità, in base alle preferenze e ai comportamenti, e indirizzare gli utenti ai punti vendita con l'obiettivo di incrementare i ricavi sugli acquisti.

#### <a name="banking"></a>Banking
Raccogliere dati degli utenti finali che hanno creato un account al primo avvio dell'applicazione. Puntare all'implementazione di una strategia di benvenuto con una notifica push mirata e all'aumento del numero di sottoscrizioni di account.

### <a name="how-to-create-a-great-tag-plan"></a>Come creare un buon piano di tag
Un piano di tag deve essere come una descrizione del percorso dell'utente o una sorta di flusso di lavoro dell'applicazione che permetta di raccogliere tutti i tag (dati) necessari e di avere le informazioni analitiche necessarie per capire i comportamenti e segmentare opportunamente la base utenti. Non si tratta di un processo tecnico. Di conseguenza, gli addetti al marketing sono in grado di specificare i dati da raccogliere sulla base della propria strategia di Mobile Engagement.

In un piano di tag è necessario come minimo includere tutte le schermate (dette *attività* in Mobile Engagement) di un'applicazione. In questo modo è possibile determinare il percorso dell'utente.

Un'attività può incorporare *eventi* che raccolgono informazioni relative ad azioni come il clic su un pulsante. Ciò consente la raccolta dei dati di interazione all'interno dell'applicazione. Di conseguenza, gli addetti al marketing possono sapere quali schermate vengono visitate dagli utenti e quali azioni eseguono questi ultimi in tali schermate.

`Jobs` sono azioni con una durata. Si tratta di elementi particolarmente utili per gli addetti al marketing, perché consentono loro di comprendere, ad esempio, il tempo impiegato da un utente per creare un account o per eseguire l'accesso. Ciò potrebbe risultare utile anche per gli sviluppatori che vogliono monitorare il tempo necessario per chiamare un servizio Web.

`Errors` per sapere se gli utenti riscontrano problemi con l'app. Ad esempio, potrebbero sperimentare frequenti problemi di connessione.

Tutti questi tipi di dati possono essere estesi con parametri (*informazioni aggiuntive* in Mobile Engagement), in modo da raccogliere dati dinamici dall'applicazione. Si tratta di un aspetto importante per consentire una segmentazione con granularità fine. Gli addetti al marketing potrebbero ad esempio segmentare gli utenti in base al tipo di contenuto utilizzato. Il tipo di contenuto sarà costituito dalle informazioni dinamiche di un'attività o un evento.

*informazioni sull'app* sono dati che consentono di conoscere in tempo reale lo stato dell'applicazione o dell'utente. Questi consentono anche di categorizzare una base di destinatari e di selezionarli velocemente. Ad esempio, è possibile usare uno stato true/false per indicare se l'utente esegue o meno l'accesso oppure la data di scadenza della sottoscrizione.

#### <a name="example-of-tags"></a>Esempio di tag
*Caso di utilizzo: segmentare il comportamento dei destinatari per indirizzare all'utente finale idoneo notifiche push con contenuto appropriato*

1. Inviare notifiche push per promuovere una categoria di prodotti: raccogliere dati di comportamento per segmentare i destinatari in base alla categoria di prodotti visitata X volte in un determinato periodo o in base a un articolo specifico aggiunto a un carrello. I dati raccolti consentiranno la segmentazione e quindi l'invio di notifiche push ai destinatari appropriati.
2. Valutare l'app: raccogliere dati in base al contenuto condiviso dagli utenti nei social network. Lo scopo è quello di segmentare i destinatari determinando gli *ambasciatori* dell'app. Tali utenti sono i migliori destinatari a cui chiedere, tramite una notifica push in-app, di valutare l'app con cinque stelle nello store.
   
   ![][1]

*Caso di utilizzo: dati dichiarativi*

1. Avvisi di novità da inviare ai segmenti: raccogliere dati dichiarativi per segmentare i destinatari in base alle loro preferenze. Consente di inviare notifiche push su un argomento specifico di particolare interesse per un determinato gruppo di destinatari.
2. Segmentare i destinatari in base allo stato di accesso. Raccogliere dati per sapere se un utente è connesso o se ha creato un account. Consente di individuare gli utenti finali che non hanno ancora eseguito l'accesso e inviare loro una notifica push per convincerli a registrarsi.
   ![][2]

### <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sui concetti di base di Mobile Engagement, vedere [Concetti relativi a Mobile Engagement] .
* Vedere [Creare un'app Azure Mobile Engagement](mobile-engagement-create.md) per creare una nuova Raccolta di app di Mobile Engagement in Azure e iniziare a gestire le app tramite il portale di Mobile Engagement.
* Per altre informazioni, vedere [Procedure consigliate](mobile-engagement-getting-started-best-practices.md) .
* Per informazioni sull'implementazione di Mobile Engagement con un'app di gioco di esempio, vedere [uno scenario di app di gioco](mobile-engagement-gaming-scenario.md) . 
* Per informazioni sull'implementazione di Mobile Engagement con un'app multimediale di esempio, vedere [uno scenario di app multimediale](mobile-engagement-media-scenario.md) . 
* Per altre informazioni sull'implementazione, vedere la pagina [Esercitazioni] .

<!-- Images. -->
[1]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case1.png
[2]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case2.png

<!-- URLs. -->
[Concetti relativi a Mobile Engagement]: http://azure.microsoft.com/documentation/articles/mobile-engagement-concepts/
[Esercitazioni]: http://azure.microsoft.com/documentation/articles/mobile-engagement-ios-get-started/




<!--HONumber=Nov16_HO2-->


