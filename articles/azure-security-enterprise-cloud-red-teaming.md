<properties
   pageTitle="Red Teaming per i servizi cloud aziendali Microsoft"
   description="Questo articolo illustra la strategia Microsoft e l'esecuzione del Red Teaming e dei test di penetrazione di siti attivi a fronte di applicazioni, servizi e dell'infrastruttura cloud gestita di Microsoft. Si apprenderà come Microsoft simula violazioni reali, esegue il monitoraggio continuo della sicurezza e mette in pratica la risposta agli eventi imprevisti della sicurezza per convalidare e migliorare la sicurezza di Microsoft Azure e Office 365."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/17/2016"
   ms.author="yuridio"/>


#Red Teaming per i servizi cloud aziendali Microsoft

Il Red Teaming è una metodologia che va ben oltre gli accreditamenti relativi alla conformità o alla soddisfazione di altri requisiti di settore. Offre infatti a Microsoft e ai suoi clienti la garanzia aggiuntiva che i servizi cloud Microsoft eseguano costantemente il monitoraggio, i test e gli aggiornamenti della sicurezza a fronte delle minacce in continua evoluzione che sia i clienti che Microsoft devono affrontare.

##Introduzione

Le organizzazioni possono prepararsi meglio all'impatto delle minacce attuali e future simulando attacchi reali e mettendo in pratica le tattiche, tecniche e procedure (TTP, Tactics, Techniques and Procedures) usate da avversari determinati e tenaci durante le violazioni. Invece di cercare semplicemente di impedire che si verifichino eventi imprevisti correlati alla sicurezza, è fondamentale presupporre che tali eventi possono avvenire e avverranno. Le informazioni ottenute con il Red Teaming e l'esecuzione dei test di penetrazione di siti attivi contribuiscono a rafforzare in modo significativo le difese, migliorare le strategie di risposta, formare i difensori e favorire una maggiore efficacia dell'intero programma di sicurezza.

Le organizzazioni non possono identificare in modo completo i gap nel rilevamento delle minacce alla sicurezza e la relativa risposta concentrandosi unicamente sulle strategie di prevenzione delle violazioni. Comprendere non solo come proteggersi dalle violazioni, ma anche come rilevarle e rispondere è altrettanto importante, se non di più, dell'intraprendere prima di tutto le azioni necessarie per prevenire che una violazione si verifichi. Pianificando i peggiori scenari immaginabili tramite wargame, ovvero esercitazioni e penetrazione con giochi da tavolo, e Red Teaming, ovvero attacchi e penetrazione in ambiente reale, le organizzazioni possono sviluppare le necessarie capacità per rilevare i tentativi di penetrazione e migliorare in modo significativo le risposte associate alle violazioni della sicurezza.

###Red Teaming per i servizi cloud aziendali Microsoft 

Questo articolo illustra in che modo Microsoft usa il Red Teaming, un metodo di test di penetrazione di siti attivi, sull'infrastruttura, sulle applicazioni e sui servizi gestiti da Microsoft. Si apprenderà anche in che modo Microsoft simula violazioni reali, monitora costantemente la sicurezza e mette in atto le risposte agli eventi imprevisti per testare e migliorare la sicurezza di Microsoft Azure e Office 365 (O365). Si noti tuttavia che non vengono deliberatamente usati dati dei clienti durante il Red Teaming e i test di penetrazione di siti attivi. I test vengono eseguiti sull'infrastruttura e sulle piattaforme Microsoft Azure e O365, oltre che su tenant, applicazioni e dati di Microsoft. I tenant, le applicazioni e i dati dei clienti ospitati in Microsoft Azure o in O365 non vengono mai coinvolti.

###Oltre la prevenzione 

Le tecnologie e le strategie di prevenzione correlate alla sicurezza non possono garantire la protezione da qualsiasi attacco. Considerato il panorama delle minacce attuali, è fondamentale riconoscere che una violazione si è già verificata o che è solo una questione di tempo prima che si verifichi. È più probabile che un'organizzazione sia già stata compromessa, ma che semplicemente non l'abbia ancora scoperto. Una gestione sulla base di questo presupposto consente di modificare le strategie di rilevamento e risposta, in modo da superare i limiti riguardanti l'infrastruttura, le persone, i processi e le tecnologie di qualsiasi organizzazione.

![Citazione NSA](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig1.png)

Secondo il "2014 Data Breach Investigations Report" di Verizon, in circa l'80% dei casi l'organizzazione compromessa non aveva rilevato la violazione (pag. 12, <http://www.verizonenterprise.com/DBIR/2014/>[)](http://www.verizonenterprise.com/DBIR/2014/). È stata invece informata della violazione da una parte esterna, ad esempio un cliente, le forze dell'ordine, un servizio di terze parti o un'agenzia esterna di rilevamento delle frodi. Per di più, questi dati si basano solo sugli eventi imprevisti in cui l'utente malintenzionato è stato infine rilevato. Non includono i casi in cui l'utente malintenzionato non è ancora stato rilevato.

Le sezioni che seguono illustrano il passaggio a una nuova strategia di sicurezza usata in tutti i servizi cloud Microsoft, ad esempio Microsoft Azure e Office 365. Questa strategia di sicurezza, detta "presunzione di violazione", è una metodologia e al tempo stesso un'innovazione culturale, che prevede l'alterazione dei presupposti di base di progettazione, ingegneria e funzionamento, supponendo che gli utenti malintenzionati abbiano già sfruttato le vulnerabilità, ottenuto l'accesso con privilegi e siano già stabilmente attivi nei servizi di produzione.

##Metodologia di sicurezza tradizionale 

Le metodologie di sicurezza tradizionali sono in gran parte focalizzate sulla prevenzione. La prevenzione è una strategia difensiva volta a eliminare le vulnerabilità e, di conseguenza, a prevenire le violazioni della sicurezza prima che si verifichino. Nel portfolio dei Microsoft Online Services, tra cui Microsoft Azure, Office 365, CRM Online e altri, questo approccio comporta miglioramenti continui dei processi di sicurezza con i programmi [*Security Development*](http://www.microsoft.com/security/sdl/default.aspx) [*Operational Security Assurance*](http://www.microsoft.com/download/details.aspx?id=40872) (OSA). Modellazione delle minacce, analisi del codice statico e test di sicurezza sono utili per l'enumerazione, la riduzione e la gestione delle superfici di attacco, ma non eliminano tutti i rischi per la sicurezza.

Un esempio di strategia di prevenzione è il modo in cui Microsoft limita l'accesso come operatore/amministratore ai dipendenti che hanno un'esigenza dimostrata dell'accesso e che soddisfano i requisiti di idoneità. Ad esempio, hanno superato un controllo dei comportamenti pregressi, hanno soddisfatto tutti i requisiti di conformità e sicurezza in una funzione lavorativa o un ruolo che richiede l'accesso e così via. Gli amministratori non hanno inoltre alcuna autorizzazione permanente, ma vengono assegnati loro i privilegi di accesso Just-In-Time (JIT)[1] e Just Enough Administration (JEA)[2]. Altri esempi includono l'isolamento dell'ambiente di posta elettronica del dipendente dall'ambiente di produzione e l'uso di workstation specializzate e con livelli di sicurezza avanzati per l'esecuzione di operazioni sensibili.

Quando possibile, l'intervento umano viene sostituito da processi automatizzati, ampiamente controllati e basati su strumenti. Le funzioni di routine includono, ad esempio, la distribuzione, il debug, la raccolta dei dati di diagnostica e l'amministrazione del servizio. I Microsoft Online Services continuano a investire in sicurezza dei sistemi e automazione delle operazioni per ridurre l'esposizione a potenziali rischi di sicurezza.

Dal punto di vista della tecnologia operativa, gli investimenti nella prevenzione delle violazioni limitano l'esposizione, ma non possono mai eliminarla completamente. Mentre l'adozione e la costante evoluzione dei programmi, ad esempio SDL e OSA, e delle tecnologie di sicurezza Microsoft rimangono strumenti importanti per prevenire le violazioni della sicurezza, è opportuno che tutti accettino il fatto che queste violazioni avranno un impatto sulla propria organizzazione, sui servizi e sugli utenti.

###Minacce nuove ed emergenti 

Negli ultimi cinque anni circa si è discusso molto più ampiamente di una categoria specifica di minacce. Advanced Persistent Threat (APT) è un termine coniato per indicare i tentativi promossi da stati nazionali per infiltrarsi in basi industriali della difesa o militari e in reti governative con lo scopo specifico di sottrarre dati sensibili. Oggi, il termine APT viene ampiamente usato negli ambienti della sicurezza e dei mezzi di comunicazione per descrivere qualsiasi attacco apparentemente destinato a una singola organizzazione o ritenuto di natura tecnicamente degna di nota, indipendentemente dal fatto che l'attacco sia stato in effetti avanzato o persistente. Le caratteristiche comuni di un'APT includono:

- Pianificazione sofisticata

- Obiettivo specifico/sequenziale

- Ricognizione efficace

- Uso di strumenti comprovati

- "Ingegneria sociale"

Attualmente gli avversari hanno a disposizione risorse e orchestrazioni importanti. Indipendentemente dal livello di complessità dell'avversario, le tendenze degli eventi imprevisti della sicurezza dalla fine del 2009 a oggi sono una chiara indicazione che la maggiore probabilità di un evento e i rischi sono in aumento. La maggiore complessità e la natura mirata delle minacce alla sicurezza, abbinate alla loro frequenza crescente, suggeriscono che, prima o poi, le violazioni della sicurezza influiranno su tutti gli utenti e tutte le organizzazioni.

Nell'attuale scenario delle minacce un'attenzione rivolta esclusivamente alla prevenzione non è sufficiente per affrontare avversari determinati e tenaci. Inoltre, con strumenti di sicurezza comuni, quali i sistemi antivirus e di rilevamento delle istruzioni (IDS, Intrusion Detection Systems), è difficile acquisire o attenuare l'ampia gamma delle violazioni attuali. I controlli al perimetro della rete possono tener fuori i dilettanti, ma gli utenti malintenzionati più esperti e motivati troveranno sempre il modo di penetrare in questi perimetri virtuali. Di conseguenza, le organizzazioni sono troppo spesso impreparate di fronte alla necessità di rispondere alla profondità e all'estensione di una violazione.

Con l'evoluzione dell'IT e l'adozione del cloud non sarà più possibile definire i limiti dell'azienda con un perimetro di rete gestito fisicamente o virtualmente attraverso firewall. I dati aziendali, compresi i dati e le applicazioni sensibili, sono disponibili quasi ovunque: in locale, nei data center privati, nel cloud, presso i partner e su una vasta gamma di dispositivi dell'utente. Tutti richiedono una strategia di sicurezza fondamentalmente diversa, nonché un cambiamento nelle metodologie di sicurezza usate dalla maggior parte delle organizzazioni.

La risposta alle violazioni ha sempre presentato numerose problematiche, inclusa l'identificazione dell'ambito della violazione, la notifica tempestiva a stakeholder e clienti, l'analisi della perdita dei dati e il ripristino degli asset compromessi. Se si combinano gli avversari attuali e l'evoluzione dell'IT, la risposta alle violazioni non è mai stata tanto impegnativa quanto lo è oggi. Di conseguenza, invece di concentrarsi sulla semplice prevenzione delle violazioni, una strategia di sicurezza efficace deve presupporre che avversari determinati e tenaci riescano ad aprire una breccia in qualsiasi difesa.

##Metodologia di presunzione di violazione 

L'attuale panorama delle minacce richiede che strategie di sicurezza efficaci bilancino gli investimenti nella prevenzione con programmi di rilevamento e risposta. Tramite un'analisi dettagliata delle tendenze in ambito di sicurezza, Microsoft ha iniziato a promuovere ed evidenziare la necessità di ulteriori investimenti in processi e tecnologie di sicurezza reattivi, focalizzati sul rilevamento e la risposta alle minacce emergenti, invece della semplice prevenzione di tali minacce. Inoltre, a seguito dei cambiamenti nel panorama delle minacce e di un'analisi approfondita, Microsoft ha deciso di perfezionare la propria strategia di sicurezza, andando oltre la sola prevenzione delle violazioni per passare a una strategia meglio attrezzata per affrontare le violazioni quando si verificano, considerando la previsione dei principali eventi correlati alla sicurezza non come una questione di "se", ma di "quando".

Mentre i processi di presunzione di violazione di Microsoft sono già in vigore da molti anni, la maggior parte dei clienti è inconsapevole del lavoro svolto dietro le quinte per la protezione avanzata dei servizi cloud Microsoft. La presunzione di violazione è un'attitudine che guida gli investimenti di sicurezza, le decisioni di progettazione e le procedure di sicurezza operativa. La presunzione di violazione limita la fiducia verso applicazioni, servizi, identità e reti, considerando tutti gli elementi, sia interni che esterni, come non sicuri e probabilmente già compromessi. Anche se la strategia di presunzione di violazione non ha avuto origine da una violazione di servizi cloud o aziendali Microsoft, è stata un riconoscimento del fatto che molte organizzazioni, in tutto il settore, subivano violazioni nonostante ogni tentativo di evitarle.

Mentre la prevenzione delle violazioni è una parte essenziale delle attività operative di qualsiasi organizzazione, queste procedure devono essere continuamente testate ed estese per poter affrontare con efficacia avversari moderni, come le APT. Perché le organizzazioni si preparino ad affrontare una violazione, devono prima di tutto creare e gestire procedure di risposta ai problemi di sicurezza, che siano affidabili, ripetibili e completamente testate.

Mentre i processi di sicurezza per la prevenzione delle violazioni, ad esempio la modellazione delle minacce, le revisioni del codice e i test di sicurezza, possono essere comuni nei cicli di vita dello sviluppo protetto, la presunzione di violazione offre numerosi vantaggi che consentono di considerare la sicurezza complessiva, mettendo in atto e misurando le capacità reattive in caso di violazione. In Microsoft si è deciso di realizzare questo obiettivo con esercizi basati su wargame e test di penetrazione di siti attivi dei propri piani di risposta ai problemi di sicurezza, con lo scopo di migliorare le funzionalità di rilevamento e risposta.

![Prevenire le violazioni e presumere le violazioni](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig2.png)

*Figure 1: Modelli di prevenzione e presunzione di violazione*

Con la presunzione di violazione l'attenzione verso la sicurezza cambia, focalizzandosi su identificazione e risoluzione dei gap nelle attività seguenti:

- Rilevamento di attacchi e penetrazione

- Risposta ad attacchi e penetrazione

- Ripristino da perdita di dati, manomissione o compromissione

- Prevenzione di attacchi futuri e penetrazione

La presunzione di violazione consente di verificare che i meccanismi di protezione, rilevamento e risposta siano implementati correttamente, riducendo anche le potenziali minacce da parte di utenti malintenzionati "esperti", che usano asset legittimi, ad esempio account e computer compromessi.

La natura dei modelli di cloud e hybrid computing aggiunge significato al ruolo che i test di penetrazione svolgono nella gestione di un ambiente operativo sicuro, inclusa la preparazione necessaria per gestire le violazioni. Poiché molti aspetti dei servizi cloud Microsoft non sono sotto il controllo diretto del cliente, Microsoft è impegnata non solo nella protezione, ma anche nel rilevamento e nella risposta agli attacchi contro l'infrastruttura, la piattaforma e i servizi.

Microsoft svolge quindi esercizi basati su wargame e regolari attività di Red Teaming per valutare a migliorare la preparazione di Microsoft nell'ambito della presunzione di violazione. Con la modellazione di attacchi reali, oltre ai test di penetrazione, Microsoft può verificare la propria capacità di gestire gli attacchi, identificare i gap nelle operazioni di rilevamento e risposta e dedicare risorse per risolverli. Con l'esercitazione a rispondere agli eventi imprevisti della sicurezza, l'esecuzione continua di monitoraggio, analisi per scopi legali e ripristino, Microsoft è impegnata a sviluppare le funzionalità critiche necessarie per affrontare le violazioni.

###Esecuzione della presunzione di violazione 

La presunzione di violazione nei servizi cloud Microsoft è stata portata avanti inizialmente tramite wargame e poi con esercizi basati su violazioni effettive, chiamate violazioni del Red Team, finalizzate alla simulazione di attacchi reali. Con le violazioni del Red Team vengono testate le capacità di Microsoft di rispondere ad attacchi mirati e persistenti con l'obiettivo di ridurre significativamente il tempo medio di rilevamento (MTTD, Mean-Time to Detect)[3] e il tempo medio di ripristino (MTTR, Mean-Time to Recovery)[4].


![Ciclo di esecuzione della presunzione di violazione](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig3.png)

*Figura 2. Ciclo di esecuzione della presunzione di violazione*

I wargame sono esercizi da tavolo che servono come un'esercitazione antincendio rispetto a un incendio reale. Gli esercizi del Red Team forniscono simulazioni di violazioni end-to-end che presentano, nel modo più realistico possibile, eventi imprevisti della sicurezza per preparare le persone coinvolte a gestire le violazioni effettive.

Wargame e violazioni del Red Team offrono la possibilità di provare a rispondere agli eventi imprevisti della sicurezza prima che si verifichino effettivamente. Infatti più si sperimenta, più si sarà preparati nel caso di un evento imprevisto o di una violazione reale.


###Wargame 

Prima di dedicare risorse a tutte le violazioni della sicurezza del Red Team, in Microsoft si è iniziato con giochi da tavolo chiamati wargame. Gli esercizi associati ai wargame sono simili alla [modellazione delle minacce SDL](http://msdn.microsoft.com/magazine/dd148644.aspx)[,](http://msdn.microsoft.com/magazine/dd148644.aspx)[ ](http://msdn.microsoft.com/magazine/dd148644.aspx)ma sono incentrati sul processo di risposta ai problemi di sicurezza e al personale di un'organizzazione o servizio che gestisce un attacco. L'intento dei wargame consiste nel migliorare le procedure di risposta agli eventi imprevisti correlati alla sicurezza, coinvolgendo il personale di diversi gruppi all'interno di Microsoft, dalla sicurezza alla progettazione, fino alle attività operative. Dopo avere avviato e continuato l'esecuzione dei wargame sempre più in dettaglio, è diventato chiaro quali fossero i gruppi o i rappresentanti mancanti che dovevano essere coinvolti.

Tutte le persone o i gruppi ritenuti probabilmente necessari e coinvolti in una violazione effettiva sono stati aggiunti come stakeholder agli esercizi basati su wargame. L'elenco dei rappresentanti è aumentato, includendo non solo il personale dei gruppi di sicurezza, progettazione e attività operative, ma anche rappresentanti di quasi tutti i gruppi all'interno della società, ad esempio legale, risorse umane, marketing e anche dirigenti. Il coinvolgimento di un set esteso di gruppi e singoli ha consentito di creare le relazioni necessarie tra discipline e business unit, che hanno assicurato lo sviluppo di piani completi di risposta alle violazioni. In molti casi, questi esercizi sono serviti anche per fornire una base informativa e assicurare la consapevolezza dei rischi e delle esigenze associate alla risposta alle violazioni all'interno di Microsoft.

L'obiettivo principale degli esercizi basati su wargame è garantire la completezza delle procedure operative standard (SOP, Standard Operating Procedures) e migliorare il rilevamento e la risposta. Esistono tre (3) fasi principali di esercizi basati su wargame:

-   Identificazione dello scenario di attacco

-   Processo di attacco e di risposta fittizio

-   Relazione finale

L'identificazione dello scenario di attacco è iniziata dalla formulazione di un elenco di attacchi potenziali da modellare. Questi scenari di attacco hanno assunto molte forme diverse e spesso hanno combinato vari scenari di attacco, ad esempio:

-   Denial of Service

-   Attacco malware

-   Esecuzione di codice in modalità remota

-   Compromissione dei dati del cliente

-   Utente malintenzionato interno

-   Compromissione del servizio

Gli scenari sono stati spesso scelti e classificati in ordine di priorità rispetto a quelli considerati comuni o esaminando gli eventi imprevisti di sicurezza segnalati in precedenza, noti per avere influito su Microsoft, sui suoi concorrenti o perché si sono verificati in tutto il settore.

Una volta scelto uno scenario di attacco, uno o più membri del gruppo, in genere un membro del team di sicurezza, assume il ruolo di avversario (offesa) e descrive l'esecuzione dello scenario di attacco. Questo è l'inizio del processo di risposta e di attacco fittizio.

Gli altri membri che partecipano ai wargame assumono il ruolo di difesa e descrivono come potrebbero rilevare o rispondere allo scenario di attacco, facendo riferimento a eventuali procedure operative standard già esistenti o identificando quelle mancanti o incomplete. Così come il team di difesa risponde con le procedure per rilevare, analizzare, attenuare o eseguire il ripristino dall'attacco fittizio, il team di offesa risponde con metodi di contrattacco, che spesso annullano i presupposti di difesa e impongono ai difensori di concepire una soluzione di rilevamento e risposta più approfondita.

Ad esempio, se il team di difesa suggerisce di identificare e bloccare l'indirizzo IP che gli utenti malintenzionati hanno usato nell'attacco, il team di offesa farà notare la facilità di modificare gli indirizzi IP di origine o di sfruttare l'infrastruttura di attacco comunemente disponibile, ad esempio le botnet, per avviare l'attacco da numerosi indirizzi IP di origine. Questo costante andirivieni durante i giochi da tavolo comporta un aumento significativo di SOP e l'identificazione di numerose esigenze di investimento in sicurezza, nonché molte preziose discussioni.

Anche se sono molto utili, i wargame presentano limitazioni su ciò che si può misurare, soprattutto in relazione al modo di affrontare gli attacchi mirati in corso e gli avversari tenaci. Di conseguenza, Microsoft ha deciso di valutare e misurare veramente lo stato di preparazione ad affrontare attacchi sofisticati da parte di avversari determinati e tenaci. A parte gestire le violazioni della sicurezza effettive, non si è potuto pensare a un modo migliore se non fare proprio questo, ovvero generare violazioni della protezione effettive, usando un concetto detto Red Teaming. Il Red Teaming si riferisce all'uso di tattiche di violazione reali per l'attacco e la penetrazione. Il Red Teaming usa l'aspetto teorico dei wargame e lo rende reale.

### Red Teaming 

La strategia di sicurezza di presunzione di violazione viene eseguita da due (2) gruppi di base: il Red Team (utenti malintenzionati) e il Blue Team (difensori)[5]. Noto come Red Teaming, l'approccio consiste nel testare le operazioni e i sistemi Microsoft Azure e Office 365 usando le stesse tattiche, tecniche e procedure (TTP, Tactics, Techniques and Procedures) come avversari reali, contro l'infrastruttura di produzione attiva, senza la conoscenza preventiva dell'infrastruttura e della piattaforma dei team di progettazione o delle attività operative.

Questo test verifica il rilevamento di sicurezza e la capacità di risposta e consente di individuare le vulnerabilità di produzione, gli errori di configurazione, le presunzioni non valide o altri problemi di sicurezza in modo controllato. Ogni violazione del Red Team è seguita dalla completa rivelazione di tutti i dettagli tra Red Team e Blue Team per identificare i gap, valutare i risultati e migliorare significativamente la risposta alle violazioni.

#### Red Team 

Il Red Team è un gruppo di dipendenti Microsoft a tempo pieno focalizzato sulla violazione dell'infrastruttura e della piattaforma Microsoft, nonché dei tenant e delle applicazioni Microsoft. Sono avversari dedicati (un gruppo di pirati informatici etici) che eseguono attacchi mirati e persistenti contro gli Online Services, ovvero l'infrastruttura, le piattaforme e le applicazioni Microsoft, ma non le applicazioni e i dati dei clienti finali.

Il ruolo di un Red Team consiste nell'attaccare e penetrare ambienti usando le stesse procedure della catena di attacco di un avversario[6], illustrata nella figura 3:

![Fasi della violazione](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig4.png)

*Figura 3. Fasi della violazione*

Ricercare e comprendere le tendenze nel panorama delle minacce e gli eventi imprevisti del settore, per tenere sotto controllo le tecniche di attacco e gli strumenti più recenti usati dagli avversari, sono quindi una parte essenziale dell'approccio di qualsiasi Red Team. Essendo una delle proprietà Internet più attaccate al mondo, Microsoft Online Services genera una quantità di dati sulle minacce emergenti. Il Red Team usa queste ricerche e informazioni non solo per modellare, ma anche per eseguire tattiche reali associate alla catena di attacco di un avversario.

Oltre alla ricerca e modellazione degli avversari noti, il Red Team sviluppa e deduce nuove tecniche per compromettere Microsoft Azure e Office 365 usando strumenti di penetrazione e metodi di attacco personalizzati. Al pari di avversari tenaci, il Red Team utilizza minacce miste ed emergenti per operare compromissioni, cambiando tattica quando si imbatte in nuovi ostacoli o difese.

Poiché gli utenti malintenzionati più esperti e motivati violano le difese perimetrali, lo stesso deve fare il Red Team. I controlli perimetrali possono tenere fuori i dilettanti, ma gli avversari tenaci riescono sempre a superarli. Una volta all'interno, il Red Team riesce di solito ad acquisire i privilegi degli utenti interni, che usano per introdursi lateralmente e penetrare più a fondo nell'infrastruttura. Inoltre, come gli avversari più esperti, il Red Team stabilisce un testa di ponte da cui può mantenere una posizione persistente e modificare continuamente l'approccio adottato per eludere il rilevamento.

Ad esempio, il Red Team può installare strumenti personalizzati, come robot, controllo remoto e così via, che consentono l'accesso continuo a una risorsa compromessa e il recupero di informazioni ogni volta che lo desiderano. I meccanismi di questo tipo di attacco consentono al Red Team non solo di sottrarre dati sensibili, ma anche di sfruttare i dati compromessi, anche in questo caso solo dati di Microsoft, non quelli dei clienti.

A causa della natura sensibile e critica del lavoro che svolgono, i dipendenti che partecipano ai Red Team in Microsoft vengono sottoposti a standard di sicurezza e conformità estremamente elevati. Sono soggetti a una convalida supplementare, un'analisi dei comportamenti pregressi e a un percorso di formazione prima di poter partecipare a qualsiasi scenario di attacco. Anche se il Red Team non attacca mai deliberatamente i dati dei clienti, ha gli stessi requisiti di accesso a questi dati

(ATCD, Access To Customer Data) del personale addetto alle attività operative che distribuisce, gestisce e amministra Microsoft Azure e Office 365. Inoltre, i Red Team attaccano solo l'infrastruttura e le piattaforme gestite da Microsoft. Invece di attaccare i dati o le applicazioni dei clienti finali, il Red Team attacca i tenant, le applicazioni e i dati nel cloud che Microsoft possiede e gestisce.

I Red Team sono tuttavia soggetti a un codice di condotta rigoroso. I cinque (5) principi guida fondamentali del codice di condotta dei Red Team includono:

1.  Non causare intenzionalmente alcun tipo di impatto o tempo di inattività in relazione al contratto di servizio dei clienti.

2.  Non accedere intenzionalmente ai dati dei clienti né modificarli.

3.  Non eseguire intenzionalmente azioni distruttive.

4.  Non indebolire i meccanismi di protezione per la sicurezza in atto.

5.  Proteggere vulnerabilità e altre informazioni critiche all'interno del Red Team e condividerle solo quando necessario.

Inoltre, i Red Team di Azure e O365 devono seguire un set documentato di regole di ingaggio (ROE, Rules of Engagement), sviluppate per garantire che il codice di condotta venga rispettato. Queste regole di ingaggio sono sottoscritte dalla leadership di Microsoft.

Ecco alcune metriche di sicurezza critiche che ogni Red Team deve registrare durante l'esecuzione delle violazioni:

-   Tempo medio di compromissione (MTTC, Mean Time to Compromise)

-   Tempo medio richiesto per l'escalation dei privilegi o "Pwnage" (MTTP, Mean Time to Privilege Escalation)

Il tempo medio di compromissione misura l'inizio degli esercizi fino al momento in cui il Red Team è riuscito a compromettere un asset. In questa fase, il Red Team stabilisce in genere l'appiglio iniziale nell'ambiente di destinazione. Il tempo medio richiesto per l'escalation dei privilegi, o ciò che alcuni avversari preferiscono chiamare "Pwnage", è il tempo dall'inizio dell'esercizio fino alla completa compromissione. Ad esempio, negli ambienti basati su Active Directory questo è il momento in cui il Red Team acquisisce i privilegi di amministratore di dominio e/o compromette il controller di dominio.

Si può considerare MTTP come "fine del gioco", perché la maggior parte delle organizzazioni non è preparata a gestire questo livello di violazione. Tuttavia, è anche importante osservare che gli scenari di "fine del gioco" non sono gli unici obiettivi o scenari dimostrati dal Red Teaming. Obiettivi delle violazioni del Red Team possono includere, tra gli altri, furto dei dati,compromissioni a livello di tenant o Denial of Service. Le metriche per MTTC e MTTP possono anche essere calcolate per ogni esercizio o registrate e calcolate per ogni asset di destinazione o tipo di asset per una maggiore granularità.

Il ruolo dei Read Team Microsoft consiste nell'identificare i gap nei controlli di sicurezza della destinazione. La registrazione di MTTC e MTTP consente a Microsoft di stabilire una baseline da cui migliorare costantemente. Sfruttando i punti deboli della sicurezza e mettendo contemporaneamente in evidenza i gap a livello di monitoraggio e ripristino, il Red Teaming migliora gli strumenti e i processi di risposta agli eventi imprevisti. Altre informazioni più avanti in questo articolo. Ancora più importante, il Red Teaming produce un impatto dimostrabile, attraverso le compromissioni, che dimostra l'esigenza di una presunzione di violazione. Di conseguenza, il Red Teaming enumera i rischi aziendali e giustifica le risorse, le priorità e la necessità di investimenti per la difesa.

#### Blue Team 

Il Blue Team è costituito da un gruppo dedicato di persone o membri della sicurezza delle organizzazioni che si occupano di progettazione, attività operative e risposta agli eventi imprevisti della sicurezza. Indipendentemente dalla loro composizione sono indipendenti e operano separatamente dal Red Team. Il Blue Team segue processi di sicurezza definiti e usa gli strumenti e le tecnologie più recenti per rilevare e rispondere ad attacchi e penetrazione.

Esattamente come gli attacchi reali, il Blue Team non sa quando e come si verificheranno gli attacchi del Red Team o quali metodi possono essere usati. Il suo lavoro, che si tratti di un attacco del Red Team o di un vero attacco, consiste nel rilevare e rispondere a tutti gli eventi imprevisti correlati alla sicurezza. Per questo motivo, il Blue Team è attivo 24x7, 365 giorni all'anno e deve reagire in caso di violazioni del Red Team esattamente come per qualsiasi altro avversario.

Quando un avversario, ad esempio un Red Team, viola un ambiente, il Blue Team deve:

-   Raccogliere le prove lasciate dall'avversario

-   Rilevare le prove come un'indicazione di compromesso

-   Avvisare i team di progettazione e delle attività operative appropriati

-   Valutare gli avvisi per stabilire se richiedono ulteriori indagini

-   Raccogliere il contesto dall'ambiente per definire l'ambito della violazione

-   Formare un piano di correzione e aggiornamento per arginare o rimuovere l'avversario

-   Eseguire il piano di correzione e procedere al ripristino dalla violazione

Questi passaggi costituiscono la catena di attacco in risposta agli eventi imprevisti della sicurezza che funziona in parallelo alla catena di attacco dell'avversario, come illustrato nella figura 4:

![Fasi di risposta alla violazione](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig5.png)

*Figura 4. Fasi di risposta alla violazione*

In breve, le violazioni del Red Team consentono di mettere alla prova la capacità del Blue Team di rilevare e rispondere agli attacchi reali end-to-end. In particolare, consente di mettere in atto un'efficace risposta agli eventi imprevisti della sicurezza prima di una vera violazione, da parte di un avversario molto meno collaborativo. Inoltre, in seguito alle violazioni del Red Team, il Blue Team migliora la consapevolezza della situazione che può essere utile nella gestione delle violazioni future, sia dal Red Team che da un altro avversario.

Durante il processo di rilevamento e risposta il Blue Team produce informazioni fruibili e ottiene la visibilità delle condizioni effettive dell'ambiente che sta provando a difendere. Questa operazione viene spesso eseguita con l'analisi dei dati e per scopi legali, eseguita dal Blue Team in risposta agli attacchi del Red Team, e con la definizione degli indicatori delle minacce, come quelli di compromissione.

In modo analogo al Red Team che identifica i gap nello storico della sicurezza, i Blue Team identificano i gap nella capacità di rilevare e rispondere. Inoltre, dato che i Red Team modellano attacchi reali, il Blue Team può essere accuratamente valutato per la capacità, o l'incapacità, di affrontare avversari determinati e tenaci.

Infine, le violazioni del Red Team misurano sia la preparazione che l'impatto della risposta alle violazioni dell'organizzazione.

Le metriche che in genere vengono valutate dal Blue Team includono:

- Tempo stimato per il rilevamento (ETTD, Estimated Time to Detection)

- Tempo stimato per il ripristino (ETTR, Estimated Time to Recovery)

Il motivo per cui questi sono "tempi stimati" è che durante una violazione, indipendentemente da se o quando si è verificato il rilevamento, il Blue Team non può essere sicuro al 100% del momento in cui il Red Team è riuscito a un mettere a segno una compromissione. Allo stesso modo, il Blue Team non può essere completamente certo di avere eliminato del tutto correttamente il Red Team e ripristinato completamente l'ambiente da una violazione. Una mancanza di prove della presenza del Red Team non è una prova dell'eliminazione dell'avversario o del ripristino da una violazione. Questo è ciò che rende così complessa la risposta agli eventi imprevisti della sicurezza. È come mettere insieme un puzzle, una tessera alla volta, senza sapere qual è l'immagine finale del puzzle e quali tessere possono mancare per completare l'immagine.

Con una maggiore consapevolezza del panorama delle minacce complessivo e una migliore visibilità delle condizioni effettive all'interno dell'ambiente in cui opera, il Blue Team può integrare le procedure di risposta agli eventi imprevisti della sicurezza per migliorare la propria preparazione nell'affrontare una violazione.


#### Red e Blue 

Rispetto ai test di penetrazione tradizionali, le violazioni del Red Team provano a simulare minacce reali compresa l'intrinseca e cosiddetta "nebbia di guerra"[7]. Vale a dire che il Blue Team non sa cosa sta facendo il Red Team e viceversa. Quando il Red Team compromette un asset, può attivare un avviso di sicurezza o un evento imprevisto senza accorgersene.

Al contrario, il Red Team può attivare intenzionalmente avvisi per verificare e convalidare i presupposti di monitoraggio o come tattica diversiva. In modo analogo, durante un esercizio il Red Team può osservare un'attività di risposta, come la reimpostazione di password, il ripristino di asset compromessi, la rimozione di meccanismi di persistenza o addirittura la rimozione completa dell'esperienza, anche se quest'ultimo caso può essere raro.

A questo riguardo, le violazioni del Red Team possono osservare gli effetti di rilevamento e ripristino. Tuttavia, sia il rilevamento che il ripristino non possono essere valutati accuratamente solo dal Red Team o dal Blue Team. Sia il tempo medio per il rilevamento (MTTD) che il tempo medio di risposta (MTTR) possono essere calcolati solo alla fine di un esercizio, quando avviene la divulgazione completa tra il Red Team e il Blue Team. Per incidere completamente su rilevamento e risposta, un Red Team deve:

- Emulare un'ampia gamma di TTP fondate sulle informazioni relative agli avversari che un'organizzazione deve affrontare o affronterà

- Utilizzare le TTP nella catena di attacco che verrà applicata e convalidare rilevamenti e risposta

- Utilizzare procedure complesse, sia tecniche che operative

- Utilizzare la ripetizione per garantire che i miglioramenti a livello di rilevamento e risposta siano sufficienti per combattere avversari determinati e tenaci

Per garantire che l'esercizio includa l'avvio e il coinvolgimento del Blue Team, il Red Team può attivare intenzionalmente il rilevamento e la risposta. Ciò accade solitamente dopo che il Red Team ha completato tutti i propri obiettivi principali e se non è stata osservata una qualsiasi attività del Blue Team.

Un esempio di attivazione di rilevamento e risposta consiste nel causare intenzionalmente un'analisi antimalware di un asset, ad esempio il controller di dominio, per creare un avviso tramite un file di test EICAR[8]. La presenza di un avviso di sicurezza imprevisto, in particolare in un asset critico come il controller di dominio, attiverà le procedure di risposta del Blue Team. La creazione di un evento imprevisto della sicurezza garantisce che venga eseguita una violazione end-to-end, dalla compromissione alla risposta. A meno che il Blue Team sia coinvolto, la risposta alla violazione potrebbe non essere eseguita completamente.

Idealmente il Blue Team non sa, all'inizio, se un evento imprevisto è stato attivato da un vero utente malintenzionato o dal Red Team interno. Quando possibile, il Blue Team deve considerare gli eventi imprevisti della sicurezza come un problema reale, almeno fino a quando non si può stabilire e verificare che è attribuibile al Red Team. Questo approccio alle simulazioni di violazioni reali è il metodo migliore per creare difese reali.


#### Violazione finale del Red Team 

Alla fine di ogni violazione del Red Team entrambi i team si riuniscono per una relazione finale che valuta la violazione e la risposta. È in questa fase in cui entrambi i team condividono le tattiche e le informazioni apprese, fornendo una divulgazione completa. Il Red Team fornisce tutti i dettagli su quando e come ha violato l'ambiente, quali asset sono stati compromessi e controllati dal Red Team, se il Blue Team è riuscito a eliminare completamente o parzialmente il Red Team e se il ripristino dalla violazione è stato completato.

Il Blue Team fornisce anche tutti i dettagli su come e quando è stato rilevato il Red Team, o se non lo stato affatto, quali asset sono stati identificati come compromessi, quali meccanismi di persistenza del Red Team sono stati individuati e quali procedure di risposta sono state eseguite nel tentativo di eliminare il Red Team e procedere al ripristino dalla violazione. Questo è uno degli elementi più esclusivi e critici del Read Teaming: il Red Team è uno dei soli avversari che può fornire e condivide i dettagli della violazione, oltre a commenti e suggerimenti sull'esito positivo o negativo delle operazioni di rilevamento e risposta. La possibilità per il Red Team e il Blue Team di confrontare gli appunti è fondamentale per garantire che tutti i gap di rilevamento e risposta vengano identificati.

Solo durante la relazione finale, quando avviene la divulgazione completa, si possono calcolare esattamente le metriche del tempo medio per il rilevamento (MTTD) e del tempo medio di ripristino (MTTR), ad esempio, confrontare la differenza tra l'MTTC del Red Team e l'ETTD del Blue Team per calcolare l'MTTD effettivo. Queste metriche di rilevamento e risposta, durante e dopo l'evento imprevisto, sono utili per enumerare i rischi aziendali associati ai gap di rilevamento e ripristino della sicurezza identificati come parte dell'esercizio.

Buona parte del resto della relazione finale è dedicato alla collaborazione tra i Red e Blue Team per identificare le vulnerabilità e gli investimenti necessari per migliorare la protezione, il rilevamento e la risposta. Con ogni violazione del Red Team, entrambi i Red e Blue Team identificano gli investimenti in sicurezza necessari per migliorare la protezione contro gli attacchi futuri, rallentare gli avversari e velocizzare le operazioni di rilevamento e ripristino. In altre parole, aumentare l'MTTC e ridurre l'MTTR e l'MTTD.

L'ultimo passaggio della riunione finale per entrambi i team consiste nel fornire report scritti dettagliati. Questi report includono le sequenze temporali delle violazioni del Red Team e del Blue Team, il riepilogo dell'impatto aziendale dell'esercizio, nonché un elenco dettagliato di vulnerabilità, risultati e investimenti necessari per migliorare il rilevamento e la risposta alle violazioni.

### Principi di sicurezza 

Un vantaggio chiave del Red Teaming è il consolidamento delle risorse di sicurezza avanzate e di quelle di pirateria informatica etica. Come per qualsiasi altra professione, il personale addetto alla progettazione e alle attività operative è specializzato in alcune aree specifiche, che non possono includere le competenze relative ad attacchi e penetrazione. In Microsoft chi opera nelle organizzazioni di progettazione e attività operative sono esperti in SDL/OSA e criteri di progettazione comuni (CEC, Common Engineering Criteria), ma il Red Team assume la responsabilità dei test di sicurezza per i limiti esterni di un ambiente di produzione, che altrimenti sarebbe troppo complesso da affrontare.

Investendo in Red Teaming con il solo scopo di comprendere, comunicare e sfruttare le minacce più recenti durante gli attacchi a siti attivi, Microsoft si impegna a testare e migliorare continuamente le capacità reattive e seguire i principi di protezione principali:

- Evitare di ancorare una strategia di sicurezza a **scenari di attacco statici** o presupporre che gli avversari agiscano solo da **una posizione fissa**

- Utilizzare **livelli di controlli di sicurezza complementari**, che producono un effetto cumulativo, migliorando le difese

- Il **numero e la distribuzione** dei controlli di sicurezza sono più importanti dell'efficienza individuale

- Provare a **ritardare e rispondere** invece di **impedire** un attacco

## Riepilogo 

Le aziende di tutto il settore devono affrontare la dura realtà di essere forse vissute in uno stato costante di compromissione. Questa situazione è resa peggiore dal fatto che moltissime aziende sono felicemente inconsapevoli del fatto che, anch'esse, sono state violate. L'attuale panorama delle minacce richiede una riduzione dell'esposizione agli attacchi, incluse le minacce interne. La modifica più urgente richiede alle organizzazioni di ridurre in modo significativo il tempo medio di rilevamento e ripristino di una violazione.

Questo white paper evidenzia la necessità per le organizzazioni di diventare più agili nel risolvere i gap della sicurezza. Tramite una strategia di sicurezza basata sulla presunzione di violazione Microsoft continua nell'incessante ricerca di investimenti nella sicurezza più ampi ed estesi. Tutte le organizzazioni possono trarre vantaggio dall'adozione di strategie di sicurezza simili per combattere le minacce emergenti e in costante evoluzione.

Microsoft sviluppa funzionalità per la gestione degli eventi imprevisti correlati alla sicurezza attraverso la pianificazione anticipata delle compromissioni e la simulazione di attacchi e penetrazione con wargame e un Red Team attivo. Il Red Teaming rende reali gli scenari teorici esponendo i gap nella sicurezza, sfruttando le vulnerabilità in tempo reale e fornendo una prova concreta della necessità di una strategia di presunzione di violazione.

Il mondo impegnativo del Blue Teaming mette in pratica la possibilità di rilevare e rispondere a fronte di attacchi. Eseguendo esercizi sulle violazioni con Red team e Blue Team, le organizzazioni che si occupano di sicurezza possono concentrarsi qui principali vettori di attacco, sviluppare contromisure ed evolvere i propri meccanismi di risposta critici necessari per prepararsi a contrastare avversari determinati e tenaci.

Il Red Teaming è diventato una delle parti più importanti dello sviluppo e della protezione dell'infrastruttura, della piattaforma e dei servizi Microsoft. I Red Team di Microsoft Azure e Office 365 rappresentano avversari sofisticati e consentono a Microsoft di convalidare e migliorare la sicurezza, potenziare le difese e promuovere una maggiore efficacia dei propri programmi di sicurezza per i servizi cloud aziendali.

Con regolari attacchi e penetrazione di siti attivi, le violazioni del Red Team forniscono i mezzi critici per esercitarsi nella risposta agli eventi imprevisti della sicurezza, oltre che per valutare accuratamente la preparazione, nonché l'impatto degli attacchi reali. I clienti possono avere piena fiducia che Microsoft continuerà a migliorare la protezione, il rilevamento e la risposta tenendo fede all'impegno di offrire servizi cloud più sicuri.

## Riferimenti e letture di approfondimento 

Le risorse seguenti sono disponibili per fornire altre informazioni generali sui servizi di Microsoft Azure e Office 365, nonché elementi specifici a cui si fa riferimento nel testo principale:

- [Home page di Microsoft Azure](http://azure.microsoft.com): informazioni generali e collegamenti relativi a Microsoft Azure

- [Home page di Microsoft Office](http://products.office.com): informazioni generali e collegamenti relativi a Microsoft Office

- [Centro protezione Microsoft Azure](https://www.microsoft.com/TrustCenter/CloudServices/Azure)

- [Centro protezione di Microsoft Office 365](https://www.microsoft.com/TrustCenter/CloudServices/Office-365)

- [Microsoft Security Response Center](http://www.microsoft.com/security/msrc/default.aspx)

- Microsoft Azure o Office 365 possono essere segnalati] a [**http://www.microsoft.com/security/msrc/default.aspx*](http://www.microsoft.com/security/msrc/default.aspx)[ ] (http://www.microsoft.com/security/msrc/default.aspx) o tramite posta elettronica a **secure@microsoft.com*

- [Intervista con Mark Russinovich su Microsoft Azure e la sicurezza: aggiornamento sui test di penetrazione](http://blogs.technet.com/b/matthewms/archive/2013/07/02/an-interview-with-mark-russinovich-on-windows-azure-amp-security-update-on-penetration-testing.aspx)

- [Red e Blue: test interni di penetrazione della sicurezza per Microsoft Azure e O365](http://azure.microsoft.com/documentation/videos/red-vs-blue-internal-security-penetration-testing-of-microsoft-azure/)



## APPENDICE 

### Differenza tra Red Teaming e test di penetrazione 

Le tecniche dei test di penetrazione standard usano strumenti comuni per provare a usare gli exploit, ma raramente sfruttano tutte le capacità di un avversario competente.

Il test di penetrazione tradizionale rappresenta solo un determinato momento e quindi termina senza porre ulteriormente in evidenza l'ambiente e la risposta agli eventi imprevisti, come fa un avversario reale. Il Red Teaming è continuativo e non si arresta una volta completato il test. Il Red Teaming trova elementi che altre metodologie di test di penetrazione spesso non rilevano. Per un riepilogo, vedere la tabella 1 seguente:

**TEST DI PENETRAZIONE TRADIZIONALE - RED TEAMING**

| | Test di penetrazione tradizionale | Red Teaming |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Persistenza** | Si arresta quando l'ambiente risulta compromesso | Stabilisce un meccanismo di persistenza per mantenere l'accesso e valutare il completamento del ripristino dalla violazione |
| **Strumenti** | Usa solo quelli disponibili al momento e quindi è necessario attendere fino a quando non si affronta il ciclo di test successivo | Ricerca continuamente nuovi exploit e vulnerabilità e implementa un attacco con i nuovi strumenti non appena vengono individuati |
| **Attività post-violazione** | Si tratta di "white hat", detti anche hacker etici, che tentano solo di ottenere l'accesso e, se riescono, in genere non eseguono altre attività | Sfrutta la violazione per sottrarre e sfruttare dati critici per l'avvio di altri attacchi |
| **Tecniche di fuga ed evasione** | I test statici non possono provare a sottrarsi al rilevamento e si arrestano se vengono rilevati | I test dinamici provano a evitare il rilevamento |
| **Avversari competenti** | Tende a preferire i sistemi di sicurezza di cui è nota la presenza, perché può avere una conoscenza approfondita dei sistemi IT, verifica se sono distribuiti in modo corretto o usa exploit noti | Fa ricerche per vedere cosa è *e* non è stato distribuito e scoprire le vulnerabilità, usa le informazioni interne e strumenti personalizzati per attaccare i sistemi, incluso lo sviluppo di exploit personalizzati e precedentemente sconosciuti |
| **Sito attivo** | Non attacca quasi mai interi sistemi di produzione, è usato più spesso per i test dei sistemi pre-distribuzione | Attacca i sistemi di produzione, a tutti i livelli dello stack |
| **Motivazione** | Crea ipotesi sull'ambiente, può non eseguire il test dove non viene specificata la destinazione | Non fa alcuna ipotesi, prova a compromettere elementi qualsiasi, si adatta e cambia tattiche se e quanto necessario |
| **Risultati** | Mostra quali test sono stati condotti e i risultati (riusciti/non riusciti) | Misura e valuta accuratamente MTTC, MTTP, MTTD e MTTR. Fornisce una prova dettagliata su quando e come violare l'ambiente, quali asset sono stati compromessi e se il rilevamento e la risposta sono riusciti. |


**Tabella 1: Riepilogo delle differenze tra i test di penetrazione tradizionali e Red Teaming**


##Perché attaccare un ambiente di produzione 

Test e pre-produzione non corrispondono mai alla produzione. Le modifiche apportate durante la distribuzione, le operazioni dei servizi in corso e le differenze nell'uso da parte dei clienti di produzione contribuiscono a una variabilità dello stato. Questa variabilità esiste per una buona ragione, perché l'ambiente di produzione ha esigenze diverse per quanto riguarda il carico previsto, il tempo di inattività accettabile, nonché la sicurezza, la conformità e i requisiti normativi e contrattuali.

Il Red Teaming attacca i sistemi reali con test di penetrazione di siti attivi invece di attaccare semplicemente un ambiente di sviluppo o test in un lab con carichi di lavoro ipotetici o componenti anche reali ma con dati simulati. È uno degli aspetti principali che distingue Red Teaming dalla più tradizionale delle strategie di sicurezza: server reali, tenant reali e dati reali. I test di penetrazione di siti attivi di produzione fornisce una misura molto più affidabile e accurata dell'impatto associato a una violazione per il cliente e l'azienda.

La penetrazione di siti attivi di produzione offre vantaggi aggiuntivi. Mette alla prova le funzionalità difensive di rilevamento e risposta. Gli attacchi reali verificano l'affidabilità delle protezioni distribuite in produzione e delle funzionalità di rilevamento e risposta o la loro assenza. Fa sì che per questi investimenti venga accettato il fatto che l'ambiente di produzione è sotto attacco, può essere attualmente in uno stato compromesso e rappresenta una forza trainante per la risposta agli eventi imprevisti della sicurezza, per mettere regolarmente in pratica i metodi consigliati per gestire gli attacchi mirati e gli avversari tenaci.

##Funzionamento di un Red Team 

L'adozione della presunzione di violazione non richiede assolutamente alcun investimento in risorse per il Red Team. Molti dei vantaggi possono essere realizzati semplicemente modificando l'approccio all'infrastruttura e i test di sicurezza dell'applicazione e la distribuzione. Ad esempio, comprendere che una violazione dei dati può rimanere attiva ben oltre la compromissione iniziale, può consentire di progettare misure di sicurezza per proteggere le informazioni e gli asset critici, anche nel caso di violazione.

Tuttavia, il Red Teaming è un metodo efficace per verificare l'accuratezza delle misure di sicurezza e dei processi, stimolare la consapevolezza del panorama attuale e tenere informati delle minacce emergenti gli addetti alla progettazione, alle attività operative, alla sicurezza, nonché il management. Per determinare quali devono essere le priorità di investimento, considerare quanto segue:

-   **Prevenire le violazioni e presumere le violazioni** un approccio non sostituisce l'altro. La prevenzione delle violazioni è l'implementazione critica delle funzionalità di sicurezza per difendersi dagli attacchi. La presunzione di violazione va ben oltre queste protezioni, tenendo conto del rilevamento e della risposta prima, durante e dopo una violazione.

-   **Identificare le "gemme preziose"** per definire la priorità degli investimenti usando l'approccio basato sulla presunzione di violazione. Quali sono i dati più critici per l'azienda Dove sono archiviati i dati Quali sono gli asset più critici per la gestione del business

-   **Identificare gli esercizi rilevanti** per definire la priorità degli esercizi di sicurezza in base alle tendenze relative alle violazioni e alle TTP degli utenti malintenzionati applicabili all'ambiente.

-   **Ottenere il coinvolgimento** in particolare a livello esecutivo e garantire l'approvazione legale. I vari gruppi di lavoro o le divisioni dell'azienda devono anche essere consapevoli e accettare le attività del Red Teaming. L'attacco contro il gruppo delle attività operative, che non è mai stato attaccato prima, potrebbe causare interruzioni del servizio impreviste e l'insoddisfazione dei clienti.


Dopo aver creato un piano per il Red Teaming, è necessario proteggere le attività operative:

-   **Personale**: assicurarsi di avere risorse competenti su entrambi i lati dell'approccio di presunzione di violazione (Red Team e Blue Team). Il Blue Team deve considerare tutti gli eventi imprevisti della sicurezza, incluse le violazioni del Red Team, come un problema reale. Tenere presente il compromesso tra le risorse a tempo pieno e l'outsourcing, inclusa la sicurezza della proprietà intellettuale generata e compromessa durante il Red Teaming. Se si coinvolge un fornitore di terze parti, *controllarne l'ambito*, l'affidabilità e la reputazione.

-   **Protezione**: oltre ad avere le competenze tecniche necessarie, i membri del team dovranno essere affidabili. Eseguire quindi ricerche approfondite sui dipendenti potenziali, inclusi controlli accurati dei comportamenti pregressi.

-   **Training**: se si vogliono assumere dipendenti a tempo pieno, è consigliabile includerli in un programma di formazione standard su etica, sicurezza, privacy e conformità, oltre a eventuali requisiti univoci dell'azienda o del settore. Le agenzie esterne devono essere certificate per garantire un'attività di formazione regolare.

-   **Controllo**: è fondamentale che l'attività del Red Team sia correttamente monitorata, registrata e controllata prima e durante gli esercizi del Red Team stesso. Questo serve come una specie di "lasciapassare" nel caso il Red Team dovesse essere accusato di danni non risultanti dall'esercizio. Consente di verificare che il Red Team segua un rigido codice di condotta e rispetti le regole di ingaggio. Questi record di log e controllo sono spesso utili anche per il calcolo di MTTC, MTTD e MTTR, nonché per fornire un report dettagliato post-esercizio.

-   **Sicurezza operativa**: è assolutamente fondamentale che i membri del Red Team applichino una rigida sicurezza operativa per quanto riguarda TTP, strumenti, risultati sulla sicurezza, report, exploit, backdoor e altri asset. Se un utente malintenzionato dovesse ottenere l'accesso agli asset del Red Team, potrebbe riuscire a ricostruire rapidamente e riprodurre gli attacchi contro vulnerabilità note, con effetti devastanti.

##Informazioni aggiuntive

Il test di penetrazione su scala aziendale richiede la partecipazione di più parti all'interno dell'organizzazione. Poiché questo documento tratta indicazioni sulle procedure, è progettato per i responsabili della valutazione di ambienti, sistemi e processi in funzione dei rischi di sicurezza. Si noti che i dettagli forniti nel presente documento costituiscono una panoramica del modo in cui i team di sicurezza per Microsoft Azure e Office 365 gestiscono il Red Teaming e i test di penetrazione di siti attivi. Non sono istruzioni sul modo in cui i clienti possono attaccare l'infrastruttura cloud, la piattaforma o i servizi Microsoft.

Anche se Microsoft esegue regolari test di penetrazione per migliorare i processi e i controlli di sicurezza nel cloud, è consapevole del fatto che anche le valutazioni della sicurezza sono una parte importante delle attività di sviluppo e distribuzione delle applicazioni dei clienti. Di conseguenza, sono stati concepiti criteri per consentire ai clienti di eseguire test di penetrazione autorizzati sulle proprie applicazioni, come quelle ospitate in Microsoft Azure.

Dal momento che questi test possono essere confusi con un attacco reale, è importante che i clienti conducano i test di penetrazione solo dopo avere ottenuto l'approvazione dal Supporto tecnico di Microsoft Azure. I test di penetrazione dei clienti devono essere condotti in conformità alle condizioni di Microsoft. Le richieste per i test di penetrazione devono essere inviate con almeno 7 giorni di anticipo. Per altre informazioni, visitare il [Microsoft Trust Center]https://www.microsoft.com/TrustCenter/CloudServices/Azure).

## Riferimenti

[1] Accesso Just-In-Time (JIT) ed elevazione dei privilegi: elevazione dei privilegi concessa secondo necessità e in base al momento in cui servono. Spesso implementati richiedendo una o più approvazioni prima che venga concesso l'accesso.

[2] Just Enough Administration (JEA): una soluzione progettata per proteggere i sistemi consentendo a utenti specifici di eseguire attività amministrative senza consentire diritti di amministratore e quindi tenendo sotto controllo tutte le azioni che gli utenti eseguono. Per altre informazioni, vedere l'[articolo su Just Enough Administration nella Raccolta TechNet](https://gallery.technet.microsoft.com/Just-Enough-Administration-6b5ad370)

[3] Nel contesto di una violazione della sicurezza, MTTD si riferisce alla quantità di tempo necessaria per rilevare una violazione.

[4] MTTR si riferisce al tempo necessario per il ripristino da una violazione, una volta rilevata.

[5] Il personale di Microsoft Azure e Office 365 a tempo pieno è diviso tra Red Team e Blue Team.

[6] Per altre informazioni, vedere la pubblicazione di Lockheed Martin ["Intelligence-Driven Computer Network Defense Informed by Analysis of Adversary Campaigns and Intrusion Kill Chains"](http://www.lockheedmartin.com/content/dam/lockheed/data/corporate/documents/LM-White-Paper-Intel-Driven-Defense.pdf), che fornisce una descrizione di come difendere una rete di computer basata su informazioni ottenute dall'analisi di campagne di avversari e catene di attacco contro le intrusioni.

[7] Nebbia di guerra: una tecnica usata per descrivere l'incertezza nella consapevolezza di una situazione, sperimentata dai partecipanti riguardo alla propria capacità, alla capacità dell'avversario e alle intenzioni dell'avversario.

[8] File di test EICAR: conosciuto anche come EICAR Standard Anti-Virus Test File, è un file di computer sviluppato da European Institute for Computer Antivirus Research (EICAR) e Computer Antivirus Research Organization (CARO) per testare la risposta dei programmi antimalware. Invece di usare malware reale, che potrebbe causare danni reali, questo file di test consente di testare il software antimalware senza usare un virus reale.


*(c) 2014 Microsoft Corporation. Tutti i diritti sono riservati. Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Internet, sono soggette a modifica senza preavviso. L'utente si assume il rischio dell'uso di questo documento. Alcuni esempi vengono forniti a scopo puramente illustrativo e sono fittizi. Nessuna associazione reale è intenzionale o presupposta. Il presente documento non fornisce all'utente alcun diritto legale rispetto a qualsiasi proprietà intellettuale in qualsiasi prodotto Microsoft. È possibile copiare e usare il presente documento per scopi interni e di riferimento.*

<!---HONumber=AcomDC_0218_2016-->