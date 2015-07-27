<properties
	pageTitle="Definire la strategia di Mobile Engagement"
	description="Informazioni su come usare, caricare e ottimizzare Mobile Engagement con le funzionalità di analisi e le notifiche push."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="kpiteira"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/10/2015"
	ms.author="kapiteir" />

#Definire la strategia di Mobile Engagement

Le app vengono scritte per un motivo ben preciso: essere usate dagli utenti.

È certamente necessario molto impegno per riuscire a realizzare un'app di qualità che piaccia ai propri utenti. È anche probabile che sia stata investita una parte considerevole del budget di marketing disponibile per acquisire il maggior numero possibile di utenti. Ma, dopo un incoraggiante picco iniziale, è facile assistere a una graduale diminuzione dell'utenza, fino ad arrivare al punto in cui molti utenti smettono completamente di usare l'app. **Mobile Engagement serve proprio a evitare queste situazioni**: con questo servizio, gli autori possono promuovere l'uso di app e migliorarne le funzionalità grazie a test e informazioni sull'utilizzo.

L'approccio Microsoft al miglioramento della fidelizzazione e dell'utilizzo si basa sulla stimolazione dell'interesse degli utenti dell'app con notifiche push e messaggi in-app, ma in modo del tutto speciale: i messaggi e le comunicazioni sono personalizzate e si basano sul comportamento degli utenti durante l'uso dell'app. L'obiettivo è abilitare la comunicazione con il pubblico desiderato nel luogo giusto e al momento opportuno.

Per realizzare questo obiettivo, è necessario innanzitutto **comprendere le esigenze dei propri utenti**, quindi creare gruppi (o segmenti) sulla base delle loro azioni o caratteristiche e infine creare comunicazioni appropriate per ogni segmento.

##Mobile Engagement per soddisfare gli obiettivi degli autori di app

*Fidelizzazione e utilizzo: due concetti chiave*

**Lo sviluppo di una strategia di Mobile Engagement presuppone innanzitutto una valutazione degli obiettivi e degli indicatori di prestazioni chiave (KPI) dell'app.**

La definizione di tali obiettivi e KPI aiuterà a esaminare i propri casi di utilizzo dalla giusta prospettiva.

I casi di utilizzo consistono in un semplice elenco di campagne che si vorrebbero realizzare per comunicare con gli utenti e che possono spaziare da un semplice benvenuto fino a sofisticate utilità di notifica attivate dal proprio reparto IT. Un caso di utilizzo ben strutturato deve includere almeno tre elementi fondamentali, *cosa-chi-quando*:

1. Una designazione molto breve (ad esempio una "Campagna di benvenuto")
2. **Cosa**: un esempio di messaggio (come "Siamo lieti di averti in squadra! Ricorda di effettuare l'accesso per ottenere il tuo primo mese di uso gratuito."). Questo messaggio non è affatto definitivo e potrà essere modificato in qualsiasi momento, tuttavia può essere un buon inizio per cominciare a comunicare il messaggio desiderato.
3. **Chi**: il segmento che riceverà questo messaggio (ad esempio "Tutti gli utenti che hanno avviato l'app per la prima volta tre giorni fa, hanno visitato la home page ma non hanno eseguito l'accesso").
	- Con Azure Mobile Engagement ciò è molto facile.
	- Anche in questo caso, il messaggio non deve necessariamente essere quello finale, poiché è possibile definire i segmenti in qualsiasi momento. È tuttavia importante definire il criterio di segmentazione nelle fasi iniziali per essere certi che vengano raccolte le informazioni corrette.
4. **Quando**: il momento di attivazione della campagna. Può essere in una determinata data, in seguito all'esecuzione di un'azione specifica oppure in base a un trigger di attivazione. Mobile Engagement offre un numero notevole di possibilità per temporizzare correttamente le comunicazioni.

Una volta definiti i casi di utilizzo, vengono fornite delle linee guida per definire i dati che dovranno essere raccolti all'interno dell'applicazione. Questo ruolo è affidato al cosiddetto *"piano di tag"*. Un piano di tag consente di fare in modo che la raccolta dei dati venga specificata dagli sviluppatori. Questi ultimi possono pertanto incorporare Mobile Engagement con la giusta configurazione che consentirà di condurre le campagne usando le informazioni corrette. L'esecuzione dei test sarà anch'essa di estrema importanza per garantire la correttezza dell'integrazione e della raccolta dei dati necessari.

In base al livello di integrazione, una volta pubblicata l'applicazione, come addetti al marketing si sarà in grado di visualizzare dati analitici in tempo reale, segmentare i gruppi di destinatari e iniziare a inviare notifiche push mirate per rivolgersi a utenti finali all'interno o all'esterno dell'app.

###Casi di utilizzo per iniziare
1. Strategia di benvenuto: creare varie campagne di notifica push in base al comportamento degli utenti finali durante il lancio dell'applicazione, in modo da riprendere contatto con loro dopo 2, 5, 10 o 15 giorni dalla prima sessione e aumentare la fidelizzazione dopo il primo utilizzo.
2. Promuovere un nuovo contenuto (funzionalità, articolo/video, prodotto e così via) in base al comportamento dell'utente finale, in modo da inviare l'informazione solo agli utenti che presentano maggiori probabilità di interessamento.
3. Valutare l'applicazione: rivolgersi a meno dell'1% della propria base utenti che presenta le maggiori probabilità di valutare l'app con cinque stelle nell'archivio applicazioni.
4. Aumentare le sottoscrizioni: promuovere contenuti utili, ma non ancora disponibili, per gli utenti al fine di aumentare il numero di sottoscrizioni.
5. Esercitazione: non più esercitazioni obbligatorie per tutti gli utenti. Meglio realizzare utili esercitazioni in-app attivabili tramite messaggi in-app solo quando l'utente non sembra fare uso di una determinata funzionalità o dimostra di sperimentare difficoltà nel farlo.

##Perché sono necessari i dati analitici per coinvolgere gli utenti?

Come si sarà già intuito, la semplice trasmissione di una notifica push non è sufficiente. Il concetto centrale di Mobile Engagement è aiutare sviluppatori e agenti addetti al marketing a coinvolgere l'utente finale giusto, nel posto giusto e al momento opportuno. Per mettere in pratica questi tre elementi fondamentali è essenziale raccogliere dati analitici dalle proprie applicazioni e usarli per segmentare i destinatari. Questa pratica si rivela ancora più efficace quando i segmenti basati sul comportamento vengono abbinati a dati provenienti da altri database, sistemi CRM o canali incrociati. Mobile Engagement consente di recuperare dati da qualsiasi origine e di usarli per rivolgersi ai giusti destinatari.

È quindi consigliabile essere il più contestuali possibile quando ci si rivolge alla propria utenza ed è fondamentale essere consapevoli del comportamento degli utenti finali per conoscerne lo stato in tempo reale. La raccolta dei dati consente agli agenti di marketing di concentrarsi su ciò che conta veramente per individuare i casi di utilizzo e raggiungere i propri obiettivi con strategie di coinvolgimento. Per raggiungere gli obiettivi menzionati sopra, inoltre, le procedure consigliate prevedono l'inclusione nelle raccolte analitiche non qualsiasi genere di dato, ma solo i dati che permettono di concentrarsi su ciò che si vuole apprendere e sui casi di utilizzo. Questo è un buon inizio. Provare, testare e apprendere come usare la soluzione e indirizzare notifiche push intelligenti per aumentare l'assorbimento dell'utenza di un'applicazione portandola così al livello di un caso di successo.

>[AZURE.NOTE]Ricordare sempre che troppi dati annullano i dati.

###Casi di utilizzo e procedure consigliate

Per aiutare i clienti a iniziare con Mobile Engagement, di seguito verranno brevemente illustrati alcuni casi di utilizzo principali rilevati tra i clienti Microsoft.

####Contenuti multimediali

Raccogliere i tipi di contenuti che vengono consumati dagli utenti finali, quindi segmentare i destinatari in base a tale comportamento per indirizzare un tipo di contenuto specifico solo a quei destinatari che presentano maggiori probabilità di farne uso. Questo consente di evitare lo spamming nell'intera base utenti e garantisce un migliore assorbimento dell'utenza.

####M-commerce

Raccogliere le categorie di prodotto più visitate all'interno dell'applicazione e selezionare i destinatari tra cui promuovere sconti o nuovi prodotti nelle categorie in cui è più probabile che l'utente finale completi un acquisto. Puntare all'aumento dei ricavi. Anche qui, l'obiettivo è evitare lo spamming.

####Giochi

Raccogliere il livello di gioco di un utente finale e il tempo di gioco trascorso in un determinato periodo per rivolgersi ai destinatari che potrebbero essere bloccati e che passerebbero volentieri a un livello successivo con un'offerta bonus.

Comunicare eventi specifici, incentivando quegli utenti che non giocano più da un certo periodo di tempo per cercare di recuperarli.

####Vendita al dettaglio

Raccogliere dati su prodotti o marche che un certo segmento di destinatari potrebbe consumare con maggiore probabilità, in base alle preferenze e ai comportamenti, e indirizzare gli utenti ai punti vendita con l'obiettivo di incrementare i ricavi sugli acquisti.

####Banking

Raccogliere dati che indichino se gli utenti finali hanno creato o meno un account al primo avvio dell'applicazione. Puntare all'implementazione di una strategia di benvenuto con notifiche push mirate e all'aumento del numero di sottoscrizioni o creazioni di account.

###Come creare un buon piano di tag

Un piano di tag deve essere come una descrizione del percorso dell'utente o una sorta di flusso di lavoro dell'applicazione che permetta di raccogliere tutti i tag (dati) necessari e di avere le informazioni analitiche necessarie per capire i comportamenti e segmentare opportunamente la base utenti. Non si tratta di un processo tecnico. Di conseguenza, gli addetti al marketing sono in grado di specificare i dati da raccogliere sulla base della propria strategia di Mobile Engagement.

L'intervento minimo consiste nell'applicare tag in tutte le schermate (denominate `Activities` in Mobile Engagement) di un'applicazione. Questo permetterà di comprendere il percorso dell'utente.

Un'attività può incorporare `Events` che raccolgono informazioni relative ad azioni, ad esempio il clic su un pulsante. Ciò consente la raccolta dei dati di interazione all'interno dell'applicazione. Di conseguenza, gli addetti al marketing possono sapere quali schermate vengono visitate dagli utenti e quali azioni eseguono questi ultimi in tali schermate.

I `Jobs` sono azioni con una durata. Si tratta di elementi particolarmente utili per gli addetti al marketing, in quanto consentono loro di comprendere, ad esempio, il tempo impiegato da un utente per creare un account o per eseguire l'accesso. Ciò potrebbe risultare utile anche per gli sviluppatori che vogliono monitorare il tempo necessario per chiamare un servizio Web.

È possibile monitorare anche gli `Errors` per sapere se gli utenti riscontrano problemi con l'app. Ad esempio, potrebbero sperimentare frequenti problemi di connessione.

Tutti questi tipi di dati possono essere estesi con parametri (`extra-information` in Mobile Engagement) consentendo la raccolta di dati dinamici dall'applicazione. Questo è importante per consentire la massima granularità di segmentazione. Gli addetti al marketing potrebbero ad esempio segmentare gli utenti in base al tipo di contenuto di cui fanno uso. Il tipo di contenuto corrisponderà alle informazioni dinamiche di un'`activity` o un `event`.

`App-information` corrisponde a dati che consentono di conoscere in tempo reale lo stato dell'applicazione o dell'utente. Questi consentono anche di categorizzare una base di destinatari e di selezionarli velocemente per l'indirizzamento mirato. può essere ad esempio uno stato true/false relativo all'esecuzione o meno dell'accesso da parte dell'utente oppure la data di scadenza della sua sottoscrizione.

####Esempio di tag

*Caso di utilizzo: segmentare il comportamento dei destinatari per indirizzare all'utente finale idoneo il giusto contenuto di notifica push*

1.	Inviare notifiche push per promuovere una categoria di prodotto: raccogliere dati di comportamento per segmentare i destinatari in base alla categoria di prodotto visitato X volte in un determinato periodo o in base a un articolo specifico che hanno aggiunto a un carrello. I dati raccolti consentiranno la suddivisione in segmenti e quindi l'invio di notifiche push ai destinatari selezionati.
2.	Valutazione dell'app: raccogliere dati in base al contenuto condiviso dagli utenti nei social network. L'obiettivo è segmentare l'utenza determinando quali sono gli "ambasciatori" dell'app, ovvero i migliori destinatari a cui chiedere di valutare l'app con cinque stelle negli archivi tramite una notifica push in-app.

	![][1]

*Caso di utilizzo: dati dichiarativi* 1. Avvisi di novità da inviare ai segmenti: raccogliere dati dichiarativi per segmentare i destinatari in base alle loro preferenze. Consente di inviare notifiche push su un argomento specifico di particolare interesse per uno specifico gruppo di destinatari. 2. Segmentare i destinatari in base allo stato di accesso. Raccogliere dati per sapere se un utente è connesso o se ha creato un account. Individuare gli utenti finali che non hanno effettuato l'accesso e inviare loro notifiche push per convincerli a registrarsi. ![][2]

### Passaggi successivi
- Per informazioni dettagliate sui concetti di base di Mobile Engagement, vedere la pagina [Concetti relativi a Mobile Engagement].
- Per altre informazioni sull'implementazione, vedere la pagina [Esercitazioni].

<!-- Images. -->
[1]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case1.png
[2]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case2.png

<!-- URLs. -->
[Concetti relativi a Mobile Engagement]: http://azure.microsoft.com/documentation/articles/mobile-engagement-concepts/
[Esercitazioni]: http://azure.microsoft.com/documentation/articles/mobile-engagement-ios-get-started/
 

<!---HONumber=July15_HO3-->