---
title: Etica e uso responsabile - Personalizza esperienze
titleSuffix: Azure Cognitive Services
description: Queste linee guida hanno lo scopo di consentire un'implementazione della personalizzazione che generi fiducia nell'azienda e nel servizio. Assicurarsi di dedicare tempo alla ricerca, all'apprendimento e alla valutazione dell'impatto della personalizzazione sulla vita delle persone. In caso di dubbi, chiedere indicazioni.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 06/12/2019
ms.author: edjez
ms.openlocfilehash: 783bfcd5ad9e114a5329892449f35f6ed8316948
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428440"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Linee guida per l'implementazione responsabile di Personalizza esperienze

Affinché le persone e la società sfruttino tutto il potenziale dell'intelligenza artificiale, è necessario che le implementazioni siano progettate in modo tale conquistare la fiducia sia di coloro che aggiungono l'intelligenza artificiale alle proprie applicazioni che degli utenti delle applicazioni create con l'intelligenza artificiale. Queste linee guida hanno lo scopo di consentire un'implementazione di Personalizza esperienze che generi fiducia nell'azienda e nel servizio. Assicurarsi di dedicare tempo alla ricerca, all'apprendimento e alla valutazione dell'impatto della personalizzazione sulla vita delle persone. In caso di dubbi, chiedere indicazioni.

Queste linee guida non sono da intendersi come una consulenza legale ed è necessario assicurarsi in separata sede che l'applicazione sia conforme ai rapidi sviluppi normativi in quest'area e nel proprio settore.

Quando si progetta un'applicazione con Personalizza esperienze, inoltre, è necessario considerare le numerose responsabilità associate allo sviluppo di qualsiasi sistema di intelligenza artificiale incentrato sui dati, che includono etica, privacy, sicurezza, inclusione, trasparenza e responsabilizzazione. Per altre informazioni in merito, vedere la sezione [Letture consigliate](#recommended-reading).

È possibile usare il contenuto seguente come un elenco di controllo di partenza, da personalizzare e perfezionare in base al proprio scenario. Questo documento include due sezioni principali. La prima è dedicata alle considerazioni per l'uso responsabile al momento della scelta degli scenari, delle caratteristiche e delle ricompense per Personalizza esperienze. La seconda illustra un insieme di valori che Microsoft ritiene debba essere tenuto in considerazione quando si creano sistemi di intelligenza artificiale, con suggerimenti di utilità pratica e i rischi di influire su tali valori con l'uso di Personalizza esperienze. 


## <a name="your-responsibility"></a>Responsabilità

Tutte le linee guida per l'implementazione responsabile si basano sul fatto che gli sviluppatori e le aziende che usano Personalizza esperienze sono responsabili e devono rendere conto degli effetti dell'uso di questi algoritmi nella società. Se si sviluppa un'applicazione che verrà distribuita dall'organizzazione cui si appartiene, è necessario riconoscere il proprio ruolo e la propria responsabilità nel funzionamento dell'applicazione e nel relativo impatto sulle persone. Se si progetta un'applicazione che verrà distribuita da terze parti, giungere a un'intesa condivisa su chi sarà il responsabile ultimo del comportamento dell'applicazione e documentare tale intesa.

La fiducia si basa sul concetto di impegni rispettati. Considerare gli utenti, la società e il quadro giuridico in cui operano le applicazioni per identificare i possibili impegni espliciti e impliciti.

Microsoft si impegna continuamente per offrire strumenti e documenti che consentano di rispettare tali responsabilità. Se si ritiene che per implementare queste linee guida per l'uso di Personalizza esperienze sarebbero utili altri strumenti, documenti e funzionalità di prodotto, [inviare commenti e suggerimenti a Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D).


## <a name="factors-for-responsibly-implementing-personalizer"></a>Fattori per l'implementazione responsabile di Personalizza esperienze

L'implementazione di Personalizza esperienze può essere di grande valore per gli utenti e il business. Per implementare Personalizza esperienze in modo responsabile, prendere prima di tutto in considerazione queste linee guida nelle fasi seguenti:

* Scelta dei casi d'uso per applicare la personalizzazione.
* Creazione delle [funzioni di ricompensa](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-rewards.md).
* Scelta delle [caratteristiche](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-features.md) relative a contesto e azioni possibili che verranno usate per la personalizzazione.


## <a name="choosing-use-cases-for-personalizer"></a>Scelta dei casi d'uso per Personalizza esperienze

Usare un servizio che apprende come personalizzare il contenuto e le interfacce utente è utile. Un servizio di questo tipo può tuttavia essere usato in modo improprio se la modalità di personalizzazione genera effetti collaterali negativi nel mondo reale e se gli utenti non sono a conoscenza della personalizzazione del contenuto. 

Gli usi di Personalizza esperienze con elevato potenziale di effetti collaterali negativi o mancanza di trasparenza includono ad esempio gli scenari in cui la "ricompensa" dipende da molti fattori complessi a lungo termine che, se ipersemplificati in una ricompensa immediata, possono avere risultati sfavorevoli per gli utenti. Queste scelte sono in genere considerate "consequenziali", perché comportano un rischio di danno. Ad esempio: 


* **Settore finanziario**: personalizzazione di offerte di prestiti o prodotti finanziari e assicurativi i cui fattori di rischio sono basati su dati che gli utenti non conoscono, non possono ottenere o non possono contestare. 
* **Formazione**: personalizzazione delle classificazioni dei corsi scolastici e degli istituti di istruzione con consigli che potrebbero diffondere pregiudizi e ridurre la conoscenza di altre opzioni da parte degli utenti.
* **Democrazia e partecipazione civica**: personalizzazione del contenuto per gli utenti allo scopo di influenzare le opinioni, con effetto consequenziale e manipolativo.
* **Valutazione di terze parti per le ricompense**: personalizzazione degli elementi in modo da basare la ricompensa su una successiva valutazione dell'utente eseguita da terze parti, anziché generare una ricompensa in base al comportamento dell'utente.
* **Esplorazione non tollerata**: qualsiasi situazione in cui il comportamento di esplorazione di Personalizza esperienze potrebbe causare danni.

Quando si scelgono i casi d'uso per Personalizza esperienze:

* Iniziare il processo di progettazione considerando i vantaggi offerti agli utenti dalla personalizzazione.
* Considerare le conseguenze negative che si possono verificare nel mondo reale se alcuni elementi non vengono classificati per gli utenti a causa dei modelli di personalizzazione o dell'esplorazione.
* Considerare i cicli di profezia autoavverante, che possono verificarsi se una ricompensa della personalizzazione determina il training di un modello in modo che possa successivamente escludere ulteriormente un gruppo demografico dall'accesso a contenuto rilevante. Ad esempio, se la maggior parte delle persone in un quartiere a basso reddito non ottiene un'offerta assicurativa Premium, gradualmente l'offerta tenderà a non essere visualizzata affatto ai residenti del quartiere.
* Salvare copie dei modelli e dei criteri di apprendimento nel caso in cui sia necessario riprodurre Personalizza esperienze in futuro. È possibile eseguire questa operazione periodicamente o in ogni periodo di aggiornamento dei modelli.
* Considerare il livello di esplorazione adeguato per lo spazio e come può essere usato come strumento per mitigare gli effetti "camera d'eco".


## <a name="selecting-features-for-personalizer"></a>Selezione delle caratteristiche per Personalizza esperienze

La personalizzazione del contenuto dipende dalla disponibilità di informazioni utili sul contenuto e sull'utente. Tenere presente che, per determinati settori e applicazioni, alcune caratteristiche degli utenti possono essere considerate direttamente o indirettamente discriminatorie e potenzialmente illegali.

Considerare l'effetto delle caratteristiche descritte di seguito.

* **Dati demografici degli utenti**: caratteristiche relative a sesso, genere, età, razza e religione potrebbero non essere consentite in alcune applicazioni per motivi normativi. Basare la personalizzazione su di esse, inoltre, potrebbe non essere etico perché diffonderebbe generalizzazioni e pregiudizi. Un esempio di questa diffusione di pregiudizi è un'offerta di lavoro per ingegneri che non viene visualizzata a destinatari anziani o in base al genere.
* **Informazioni sulle impostazioni locali**: in molti luoghi del mondo, le informazioni relative all'ubicazione (come codice postale o nome del quartiere) possono essere strettamente correlate al reddito, alla razza e alla religione.
* **Percezione dell'utente dell'equità**: anche nei casi in cui l'applicazione prende decisioni valide, considerare come gli utenti percepiscono il cambiamento del contenuto visualizzato nell'applicazione, che potrebbe sembrare correlato a caratteristiche che sarebbero discriminatorie.
* **Pregiudizio non intenzionale nelle caratteristiche**:  alcuni tipi di pregiudizi potrebbero essere introdotti usando caratteristiche che interessano solo un sottoinsieme della popolazione. È necessario prestare particolare attenzione se le caratteristiche vengono generate modo algoritmico, ad esempio quando si usa l'analisi di immagini per estrarre gli elementi di un'immagine o l'analisi del testo per individuare le entità in un testo. Conoscere le funzionalità dei servizi usati per creare queste caratteristiche.

Quando si scelgono le caratteristiche da inviare in contesti e azioni a Personalizza esperienze, applicare le procedure seguenti:

* Considerare se l'uso di determinate caratteristiche per alcune applicazioni è legale ed etico e se caratteristiche apparentemente inoffensive potrebbero rappresentarne altre da evitare.
* Garantire trasparenza agli utenti in merito all'uso di algoritmi e analisi dei dati per la personalizzazione delle opzioni visualizzate.
* È importante chiedersi: Gli utenti apprezzerebbero il fatto che io usi queste informazioni per personalizzare il contenuto per loro? Mi sentirei a mio agio se dovessi mostrare agli utenti come è stata presa la decisione di evidenziare o nascondere determinati elementi?
* Usare dati comportamentali anziché dati di classificazione o segmentazione basati su altre caratteristiche. Le informazioni demografiche venivano tradizionalmente usate dai rivenditori per motivi storici, perché risultavano semplici da raccogliere e sfruttare prima dell'era digitale. È tuttavia opportuno chiedersi quanto siano rilevanti le informazioni demografiche quando sono disponibili dati contestuali e cronologici sull'effettiva interazione che sono più strettamente correlati alle preferenze e all'identità degli utenti.
* Considerare come si può impedire che le caratteristiche vengano "falsificate" da utenti malintenzionati, che con un elevato numero di attacchi di spoofing potrebbero fuorviare il training di Personalizza esperienze in modo da disturbare, mettere in imbarazzo e molestare determinate classi di utenti. 
* Quando appropriato e fattibile, progettare l'applicazione in modo che gli utenti possano acconsentire esplicitamente all'uso di alcune caratteristiche personali o rifiutarlo esplicitamente. Queste caratteristiche potrebbe essere raggruppate, ad esempio, come "Informazioni sull'ubicazione", "Informazioni sul dispositivo", "Cronologia degli acquisti precedenti" e così via.


## <a name="computing-rewards-for-personalizer"></a>Calcolo delle ricompense per Personalizza esperienze

Personalizza esperienze cerca di migliorare la scelta dell'azione da ricompensare in base al punteggio di ricompensa fornito dalla logica di business dell'applicazione.

Un punteggio di ricompensa ben strutturato rappresenterà a breve termine un obiettivo di business collegato a una missione dell'organizzazione.

In caso di ricompensa per i clic, ad esempio, il servizio Personalizza esperienze cercherà i clic a spese di tutto il resto, anche se ciò che su cui viene fatto clic è fonte di distrazione o non collegato a un risultato di business.

Un sito di notizie, al contrario, potrebbe voler impostare ricompense collegate ad aspetti più significativi dei clic, ad esempio se l'utente abbia dedicato tempo sufficiente alla lettura del contenuto o se abbia fatto clic su articoli o riferimenti rilevanti. Con Personalizza esperienze è possibile creare facilmente uno stretto collegamento tra metriche e ricompense. Prestare tuttavia attenzione a non confondere l'engagement a breve termine degli utenti con risultati validi.

### <a name="unintended-consequences-from-reward-scores"></a>Conseguenze impreviste dei punteggi di ricompensa
Pur essendo creati con le migliori intenzioni, i punteggi di ricompensa possono comunque generare conseguenze o risultati imprevisti nella classificazione del contenuto da parte di Personalizza esperienze. 

Si considerino gli esempi seguenti:

* Basando la ricompensa per la personalizzazione di contenuto video sulla percentuale di lunghezza del video visualizzata si tenderà probabilmente a classificare i video più brevi.
* La ricompensa per le condivisioni sui social media, senza analisi del sentiment in relazione alla modalità di condivisione o al contenuto stesso, potrebbe determinare la classificazione di contenuto offensivo, non moderato o provocatorio che tende a suscitare un "engagement" considerevole ma aggiunge scarso valore.
* Una ricompensa per l'azione basata su elementi dell'interfaccia utente che gli utenti non si aspettano vengano modificati può interferire con l'usabilità e la prevedibilità dell'interfaccia utente, i cui pulsanti cambieranno inaspettatamente posizione o scopo senza preavviso, rendendo più difficile per alcuni gruppi di utenti rimanere produttivi.

Implementare queste procedure consigliate:

* Eseguire esperimenti offline con il sistema usando diversi approcci di ricompensa per comprendere l'impatto e gli effetti collaterali.
* Valutare le funzioni di ricompensa e chiedersi come potrebbero influenzare l'interpretazione di una persona estremamente ingenua determinando risultati indesiderati.


## <a name="responsible-design-considerations"></a>Considerazioni per la progettazione responsabile

Di seguito sono illustrate le aree della progettazione per implementazioni responsabili dell'intelligenza artificiale. Per altre informazioni su questa struttura, vedere [The Future Computed](https://news.microsoft.com/futurecomputed/) (Il calcolo del futuro).

![Valori per l'intelligenza artificiale da The Future Computed](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Affidabilità
*Le persone che progettano e distribuiscono sistemi di intelligenza artificiale devono rendere conto del funzionamento dei sistemi*. 

* Creare linee guida interne su come implementare Personalizza esperienze, quindi documentarle e comunicarle al team, ai dirigenti e ai fornitori.
* Riesaminare periodicamente il modo in cui vengono calcolati i punteggi di ricompensa, eseguire valutazioni offline per verificare quali caratteristiche influiscono su Personalizza esperienze e usare i risultati per eliminare le caratteristiche superflue e inutili.
* Comunicare chiaramente agli utenti come viene usato il servizio Personalizza esperienze, a quale scopo e con quali dati.
* Archiviare le informazioni e gli asset usati per il funzionamento di Personalizza esperienze, come modelli, criteri di apprendimento e altri dati, per poter riprodurre i risultati.

### <a name="transparency"></a>Trasparenza
*I sistemi di intelligenza artificiale devono essere riconoscibili*. Con Personalizza esperienze:

* *Offrire agli utenti informazioni sul modo in cui il contenuto è stato personalizzato.* È ad esempio possibile visualizzare agli utenti un pulsante con l'etichetta `Why These Suggestions?` che mostra le principali caratteristiche dell'utente e le azioni che hanno svolto un ruolo nei risultati di Personalizza esperienze.
* Assicurarsi di specificare nelle condizioni per l'utilizzo che si useranno le informazioni e il comportamento degli utenti per personalizzare l'esperienza.

### <a name="fairness"></a>Equità
*I sistemi di intelligenza artificiale devono trattare tutte le persone con equità*.

* Non usare Personalizza esperienze per casi d'uso con risultati a lungo termine, consequenziali o associati a danni reali.
* Non usare caratteristiche non appropriate per la personalizzazione del contenuto o che potrebbero contribuire a diffondere pregiudizi indesiderati. Ad esempio, a chiunque abbia circostanze finanziarie simili dovrebbero essere visualizzati gli stessi consigli personalizzati di prodotti finanziari.
* Comprendere i pregiudizi che potrebbero risiedere in caratteristiche originate da editor, da strumenti algoritmici o dagli utenti stessi.

### <a name="reliability-and-safety"></a>Affidabilità e sicurezza
*I sistemi di intelligenza artificiale devono essere eseguiti in modo affidabile e sicuro*. Per Personalizza esperienze:

* *Non includere in Personalizza esperienze azioni che non devono essere scelte*. Ad esempio, i filmati inappropriati dovrebbero essere esclusi tramite filtro dalle azioni da personalizzare se si crea un consiglio per un utente anonimo o minorenne.
* *Gestire il modello di Personalizza esperienze come un asset aziendale*.  Valutare con quale frequenza è opportuno eseguire il salvataggio e il backup del modello e dei criteri di apprendimento alla base del ciclo di Personalizza esperienze e gestirli in ogni caso come un importante asset aziendale. La riproduzione dei risultati precedenti è importante a scopo di controllo e di misurazione dei miglioramenti.
* *Fornire canali per ottenere direttamente commenti e suggerimenti dagli utenti*. Oltre a includere nel codice controlli di sicurezza affinché il contenuto corretto venga visualizzato solo ai destinatari appropriati, fornire un meccanismo per commenti e suggerimenti che consenta agli utenti di segnalare il contenuto che può risultare inaspettato o disturbare. Soprattutto se il contenuto proviene da utenti o terze parti, valutare la possibilità di usare Microsoft Content Moderator o altri strumenti per esaminarlo e convalidarlo.
* *Eseguire frequenti valutazioni offline*. Ciò consentirà di monitorare le tendenze e di assicurarsi di conoscere l'efficacia.
* *Stabilire un processo per rilevare eventuali manipolazioni dannose e intervenire*. Esistono attori che sfruttano la capacità dei sistemi di Machine Learning e di intelligenza artificiale di apprendere dall'ambiente per indirizzare il risultato verso i propri obiettivi. Se l'uso di Personalizza esperienze può influenzare scelte importanti, assicurarsi di avere mezzi appropriati per rilevare e mitigare queste classi di attacchi, includendo la revisione umana in determinate circostanze.

### <a name="security-and-privacy"></a>Sicurezza e privacy
*I sistemi di intelligenza artificiale devono essere sicuri e rispettare la privacy*. Quando si usa Personalizza esperienze:

* *Informare gli utenti fin dall'inizio sui dati raccolti e su come vengono usati e ottenerne in anticipo il consenso*, in base alle normative locali e di settore.
* *Includere controlli utente per la protezione della privacy.* Per le applicazioni che archiviano informazioni personali, valutare la possibilità di includere un pulsante facile da trovare per funzioni come le seguenti: 
   * `Show me all you know about me`    
   * `Forget my last interaction` 
   * `Delete all you know about me`

In alcuni casi queste funzioni potrebbero essere obbligatorie per legge. Prendere in considerazione eventuali compromessi per rieseguire periodicamente il training dei modelli affinché non contengano tracce dei dati eliminati.

### <a name="inclusiveness"></a>Inclusività
*Rivolgersi a una vasta gamma di esperienze ed esigenze umane*.
* *Offrire esperienze personalizzate per le interfacce con funzionalità di accessibilità.* L'efficienza derivante da una personalizzazione valida, applicata per ridurre la quantità di operazioni, spostamenti e ripetizioni inutili nelle interazioni, può essere particolarmente vantaggiosa per le persone affette da disabilità.
* *Adattare il comportamento dell'applicazione al contesto*. È possibile usare Personalizza esperienze per evitare ambiguità nelle finalità in un chatbot, ad esempio, perché l'interpretazione corretta può dipendere dal contesto e un'unica soluzione potrebbe non essere adatta a tutti i casi. 


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Idoneità proattiva per una maggiore governance e protezione dei dati

È difficile prevedere cambiamenti specifici nei contesti normativi, ma in generale è opportuno andare oltre il quadro giuridico minimo per assicurare un uso rispettoso dei dati personali e offrire trasparenza e scelta in relazione al processo decisionale algoritmico.


* Valutare la possibilità di pianificare in anticipo situazioni in cui potrebbero essere presenti nuove restrizioni per i dati raccolti dagli utenti e in cui potrebbe essere necessario mostrare come sono stati usati per prendere decisioni.
* Prendere in considerazione una maggiore idoneità per i casi in cui gli utenti potrebbero includere popolazioni vulnerabili emarginate, bambini oppure utenti in condizioni di vulnerabilità economica o comunque influenzabili dalla manipolazione algoritmica.
* Considerare l'insoddisfazione diffusa per il modo in cui sono stati implementati i programmi e gli algoritmi di raccolta dati per il pubblico che influenzano i destinatari, nonché come evitare comprovati errori strategici.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Valutazioni proattive durante il ciclo di vita del progetto

Valutare la possibilità di creare metodi con cui i membri del team, gli utenti e i titolari dell'azienda potranno segnalare le problematiche relative all'uso responsabile e di stabilire un processo che dia priorità alla risoluzione di tali problematiche e impedisca ritorsioni.

Nel considerare gli effetti collaterali dell'uso di qualsiasi tecnologia, chiunque è limitato dalla propria prospettiva ed esperienza di vita. Ampliare la gamma di opinioni disponibile integrando più voci eterogenee nei team, tra gli utenti o nei comitati consultivi, in modo che possano esprimersi e siano incoraggiate a farlo. Prendere in considerazione materiali di formazione e apprendimento per ampliare ulteriormente le conoscenze del team in questo campo e per aumentare la capacità di affrontare argomenti complessi e sensibili.

Considerare le attività riguardanti l'uso responsabile come le altre attività trasversali nel ciclo di vita dell'applicazione, ad esempio quelle relative a esperienza utente, sicurezza o DevOps. Queste attività e i relativi requisiti non possono essere rimandati a un secondo momento. L'uso responsabile deve essere discusso e verificato in tutto il ciclo di vita dell'applicazione.
 
## <a name="questions-and-feedback"></a>Domande, commenti e suggerimenti

Microsoft si impegna continuamente per offrire strumenti e documenti che consentano di rispettare queste responsabilità. Il nostro team invita gli utenti a [inviare commenti e suggerimenti a Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D), se ritengono che per implementare queste linee guida per l'uso di Personalizza esperienze sarebbero utili altri strumenti, documenti e funzionalità di prodotto.

## <a name="recommended-reading"></a>Letture consigliate

* Vedere i sei principi di Microsoft per lo sviluppo responsabile di intelligenza artificiale che sono stati pubblicati nel libro di gennaio 2018 [The Future Computed](https://news.microsoft.com/futurecomputed/) (Il calcolo del futuro)
* [Who Owns the Future?](https://www.goodreads.com/book/show/15802693-who-owns-the-future) (A chi appartiene il futuro?) di Jaron Lanier
* [Armi di distruzione matematica](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction) di Cathy O'Neil
* [Ethics and Data Science](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) (Etica e data science) di DJ Patil, Hilary Mason, Mike Loukides
* [Codice deontologico di ACM (Association for Computing Machinery)](https://www.acm.org/code-of-ethics)
* [Genetic Information Nondiscrimination Act (GINA)](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [Principi FAT/ML per algoritmi responsabili](https://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Passaggi successivi

[Caratteristiche: azione e contesto](concepts-features.md).
