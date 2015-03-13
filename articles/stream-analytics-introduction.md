<properties 
	pageTitle="Introduzione ad Analisi dei flussi | Azure" 
	description="Informazioni su Analisi dei flussi di Azure" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="2/10/2015" 
	ms.author="jgao"/>


# Introduzione ad Analisi dei flussi di Azure

Analisi dei flussi di Azure è un servizio completamente gestito che consente l'elaborazione di eventi complessi con bassa latenza, elevata disponibilità e scalabilità per lo streaming di dati nel cloud.

# Contenuto dell'articolo

+ [Panoramica e motivazioni](#motivation) 
+ [Funzionalità principali](#capabilities)
+ [Motivazioni aziendali per la scelta di Analisi dei flussi di Azure](#decision)
+ [Scenari e casi di utilizzo](#scenarios)
+ [Passaggi successivi](#neststeps)


##<a name="motivation"></a>Panoramica e motivazioni

Al giorno d'oggi, vi sono quantità enormi di dati trasferite ad alta velocità sulla rete.  Le organizzazioni in grado di agire sui dati elaborandoli in tempo reale possono migliorare notevolmente la propria efficienza distinguendosi sul mercato.  Gli scenari che riguardano l'analisi dei flussi in tempo reale sono presenti praticamente in tutti i settori: analisi e avvisi personalizzati in tempo reale per i mercati azionari, offerti da aziende di servizi finanziari; rilevamento di frodi in tempo reale; servizi per la protezione di dati e identità; acquisizione e analisi accurate di dati generati da sensori e attuatori incorporati in oggetti fisici (IoT); analisi dei flussi di clic nel Web (Web Click Stream Analysis); applicazioni CRM con invio di avvisi quando si verifica un degrado dell'esperienza utente in un determinato arco temporale.  Le aziende cercano il modo più flessibile, affidabile ed economico di eseguire l'analisi dei dati correlati ai flussi di eventi in tempo reale, in modo da poter ottenere un vantaggio competitivo nel mondo del business moderno.  

La creazione di sistemi di elaborazione dei flussi è un'attività complessa.  Le operazioni di streaming quali correlazioni e aggregazioni non solo devono essere implementate in modo efficiente, ma devono anche essere scalabili e a tolleranza di errore.  A ciò si aggiunge la necessità di affrontare ulteriori sfide operative, quali la distribuzione, il debug e il monitoraggio,  con rapido aumento dei costi associati alla creazione e alla manutenzione della soluzione.  Le aziende più grandi si sono rassegnate al pagamento di tali costi elevati implementando soluzioni personalizzate, mentre quelle più piccole spesso rinunciano all'opportunità a causa dell'insormontabile ostacolo costituito dall'accesso a queste tecnologie e dai costi proibitivi associati.  Analisi dei flussi di Azure consente di affrontare queste sfide.

Analisi dei flussi di Azure è un servizio di calcolo dei flussi in tempo reale completamente gestito e ospitato in Microsoft Azure, che fornisce l'elaborazione di eventi complessi nei flussi di dati con alta resilienza, bassa latenza e scalabilità.  Analisi dei flussi di Azure consente agli sviluppatori di combinare facilmente flussi di dati con record cronologici o di fare riferimento ai dati per estrarre informazioni aziendali approfondite in modo semplice e rapido.  

Con pochi clic nel portale di Azure, i clienti possono creare un processo di streaming usando un linguaggio simile a SQL per specificare trasformazioni e monitorare dimensioni e velocità complessive della propria pipeline di streaming. Il servizio può essere facilmente ridimensionato da pochi kilobyte a un gigabyte od oltre di eventi elaborati al secondo.  La maggior parte delle altre soluzioni di streaming attualmente disponibili richiedono la scrittura da parte del cliente di codice personalizzato complesso, mentre con Analisi dei flussi di Azure è possibile scrivere in modo semplice e dichiarativo usando il familiare linguaggio SQL.

Analisi dei flussi di Azure fornisce una gamma di operatori che vanno da semplici filtri a complesse correlazioni.  La definizione di operazioni in finestre, quali aggregati in finestra, la correlazione di più flussi per il rilevamento di modelli come sequenze o il confronto tra condizioni attuali e valori e modelli cronologici, sono tutte operazioni eseguibili nel giro di pochi minuti usando un semplice set di operatori del linguaggio di query di Analisi dei flussi, simile a SQL.  Per la specifica di una pipeline di streaming è sufficiente configurarne gli input ed output e creare una query simile a SQL che descriva le trasformazioni desiderate.  Mentre ciò è sufficiente per la maggior parte dei casi più semplici, le operazioni su scala più ampia e gli scenari più complessi traggono vantaggio dalla configurabilità di Analisi dei flussi.  Gli utenti possono determinare quanta potenza di calcolo dedicare a ogni fase della pipeline per ottenere la velocità effettiva di picco desiderata.

Analisi dei flussi di Azure attualmente si connette direttamente a Hub eventi di Azure per l'inserimento dei flussi nonché a BLOB di Azure per i dati cronologici.  Usando l'intervallo di interfacce di input e output dell'Hub eventi di Azure, è semplice integrare Analisi dei flussi di Azure con altre origini dati e motori di elaborazione senza perdere la natura di streaming dei calcoli.  È possibile scrivere dati da Analisi dei flussi all'archiviazione di Azure, da cui possono successivamente essere rielaborati come serie di eventi o usati in altre modalità di analisi batch con HDInsight.  Analisi dei flussi di Azure si basa su anni di lavoro di Microsoft Research nello sviluppo di motori di streaming altamente ottimizzati l'elaborazione in tempi ridotti, nonché per l'integrazione di linguaggi per la specifica intuitiva di tali elaborazioni.  Analisi dei flussi di Azure è stato creato sfruttando le community open source di Hadoop, YARN e REEF per l'elaborazione a scalabilità orizzontale.


##<a name="capabilities"></a>Funzionalità principali

###Semplicità d'uso
Analisi dei flussi supporta un semplice modello di query dichiarative per descrivere le trasformazioni.  Per ottimizzare la semplicità d'uso, è stata selezionata una variante di SQL come il linguaggio specifico di dominio, evitando agli utenti le considerevoli complessità tecniche sottostanti i sistemi di streaming.  Usando il linguaggio di query di Analisi dei flussi è possibile implementare funzioni temporali in modo semplice e rapido, compresi join temporali, aggregati in finestre, filtri temporali, nonché altre operazioni comuni quali join, aggregati, proiezioni, filtri e così via.  

Mentre da un lato è stata estesa la semantica di SQL in modi che risultano familiari per gli utenti di SQL, dall'altro sono stati mantenuti i limiti sintattici dello standard SQL per agevolare l'implementazione e l'uso dello strumento. Per altre informazioni su questo linguaggio di query, vedere la sezione "Risorse per Analisi dei flussi di Azure".

###Scalabilità
Analisi dei flussi è in grado di gestire elevati volumi di eventi con velocità effettive fino a 1 GB al secondo. L'integrazione con Hub eventi di Azure consente alla soluzione di acquisire milioni di eventi al secondo provenienti, ad esempio, da dispositivi connessi, da flussi di clic, da file di log e così via. A tale scopo, viene sfruttata la funzionalità di partizionamento di Hub eventi, che è in grado di raggiungere velocità pari a 1 MB al secondo per partizione. Analisi dei flussi fornisce il supporto per il partizionamento durante l'elaborazione degli eventi inseriti sia sull'asse orizzontale che su quello verticale.  All'interno della definizione di query, un utente può suddividere il calcolo in un numero di passaggi logici, ciascuno con la possibilità di essere ulteriormente suddiviso in elementi di dati per l'esecuzione in parallelo.  Con il passare del tempo, Analisi dei flussi si ridimensionerà automaticamente in base al tasso di inserimento degli eventi, alla complessità dell'elaborazione e alle latenze previste, per consentire agli utenti di personalizzare i propri carichi di lavoro nel modo più appropriato.  Per altri dettagli sull'implementazione della scalabilità, vedere i collegamenti nella sezione dedicata alle risorse.  

###Ripristino affidabile, ripetibile e rapido
Analisi dei flussi garantisce totale assenza di perdita di dati e fornisce continuità aziendale anche in presenza di errori di nodo, tramite le relative funzionalità di ripristino incorporato e di checkpoint.  Tali funzionalità sono offerte dall'architettura basata sul modello client-anchor di Analisi dei flussi.  Il servizio è progettato per rendere persistente lo stato e ottimizzare la ripresa da errori di nodo, le rielaborazioni e l'output della cache per gestire con efficacia gli errori downstream.  

Grazie alla capacità di mantenere lo stato internamente, il servizio può fornire risultati ripetibili grazie a cui è possibile archiviare gli eventi e applicare nuovamente l'elaborazione in futuro, ottenendo gli stessi risultati.  Questa funzionalità consente agli utenti di tornare indietro nel tempo ed esaminare i calcoli per scenari quali analisi delle cause principali e analisi di simulazione.  Le funzionalità combinate offrono il ripristino rapido da errori dei nodi di elaborazione e una rapida rielaborazione dello stato perso tornando indietro nel tempo. 

###Bassa latenza
Analisi dei flussi è attualmente ottimizzato per latenze end-to-end osservabili nell'intervallo delle frazioni di secondo.  Ciò consente al sistema di eseguire le elaborazioni senza interruzioni e a un'elevata velocità effettiva. Il modello di comunicazione è un modello di invio in batch adattivo e basato su pull, che opera in base ai valori di timeout ai e limiti di dimensione configurati.  Gli eventi e altri record vengono riuniti in batch fino al raggiungimento dei limiti.  Di conseguenza, anche con una velocità effettiva elevata, il sistema è in grado di fornire basse latenze.

###Dati di riferimento
Analisi dei flussi fornisce agli utenti la possibilità di specificare e usare dati di riferimento.  Può trattarsi di dati cronologici o di dati che cambiano meno frequentemente nel tempo.  Il sistema semplifica l'uso dei dati di riferimento in modo che vengano trattati come qualsiasi altro flusso di eventi in ingresso da unire ad altri flussi di eventi acquisiti in tempo reale per eseguire delle trasformazioni.  Per altre informazioni sulla creazione e sull'uso di dati di riferimento, vedere la sezione relativa alle risorse.



##<a name="decision"></a> Motivazioni aziendali per la scelta di Analisi dei flussi di Azure

###Costo
Il servizio Analisi dei flussi è ottimizzato per consentire agli utenti di creare e gestire soluzioni di analisi in tempo reale a costi estremamente contenuti.  Il servizio viene impostato in modo che l'utente possa pagare in base all'utilizzo.  L'utilizzo viene dedotto in base al volume di eventi elaborati e alla quantità di potenza di calcolo fornita all'interno del cluster per gestire i rispettivi processi di streaming.  

###Operatività in pochi minuti
Con Analisi dei flussi, è possibile ottenere una soluzione altamente scalabile per l'elaborazione di eventi in tempo reale senza costi di hardware o altri tipi di costi anticipati e nessun dispendio di tempo per le attività di installazione e configurazione.  L'intero processo di provisioning viene gestito interamente da Azure.  È possibile ottenere l'operatività nel giro di pochi minuti semplicemente effettuando l'iscrizione al servizio tramite il portale di Azure.  L'intuitiva interfaccia utente nel portale fornisce una dettagliata guida introduttiva per configurare ed eseguire il test dell'origine di input, dell'archivio di output e un editor di query con funzionalità di completamento automatico e di visualizzazione dei suggerimenti.  

###Sviluppo rapido
Analisi dei flussi consente di ridurre attrito e complessità durante lo sviluppo di funzioni di analisi per la scalabilità orizzontale dei sistemi distribuiti.  Gli sviluppatori devono semplicemente descrivere la trasformazione desiderata in un linguaggio di query basato su SQL e il sistema gestirà automaticamente la distribuzione del carico di lavoro per mantenere scalabilità, prestazioni e resilienza, eliminando la necessità di eseguire la programmazione procedurale necessaria per la maggior parte delle soluzioni di elaborazione di flussi.  Grazie alle funzionalità incorporate pronte all'uso e all'offerta di un portale intuitivo, Analisi dei flussi consente a sviluppatori di alto livello di eseguire elaborazioni in tempo reale in modo semplice e rapido, senza doversi preoccupare di fattori quali l'approvvigionamento delle infrastrutture, la manutenzione periodica e il ridimensionamento. 

##<a name="scenarios"></a>Scenari e casi di utilizzo

###Analisi in tempo reale per "Internet delle cose" (IoT, Internet of Things)
Man mano che i dispositivi diventano sempre più intelligenti e con l'aumentare di quelli costruiti con funzionalità di comunicazione, le aspettative riguardo alle possibilità offerte dai dati generati e raccolti da tali dispositivi continuano a evolversi sia nei mercati commerciali che in quelli dominati dai consumatori. Si prevede che con l'enorme quantità di dati disponibili, sarà possibile combinare ed elaborare questi ultimi rapidamente, ottenendo informazioni più dettagliate sull'ambiente circostante e sui dispositivi usati con regolarità. Uno scenario canonico di IoT può essere descritto usando l'esempio del distributore automatico. I distributori automatici inviano regolarmente dati su quantità, stato e temperatura dei prodotti a un gateway campo (se il distributore non è abilitato a IP) o a un gateway nel cloud (se lo è) per il relativo inserimento nel sistema. Il flusso di dati in ingresso viene elaborato e trasformato in modo che l'output calcolato possa essere immediatamente re-inviato tramite il gateway al dispositivo per l'esecuzione di un'azione corrispondente.  Se ad esempio il distributore è surriscaldato, potrebbe essere sufficiente un riavvio del dispositivo o l'esecuzione automatica di un aggiornamento del firmware senza necessità di intervento umano.  L'output elaborato può però attivare anche altri avvisi e notifiche affinché venga pianificato l'intervento di un tecnico in base ai tipi di eventi generati. 

Con l'aumentare dei dati raccolti ed elaborati, sarà anche possibile usare la funzionalità di Machine Learning per lo sviluppo e l'apprendimento dai modelli individuati nel sistema. Sarà ad esempio possibile prevedere, in base ai flussi di eventi in tempo reale inseriti nel sistema, quando potrebbe essere necessario intervenire sulla macchina o inserire operazioni di manutenzione preventiva nella pianificazione degli interventi tecnici prima che si verifichi il guasto.

Questo modello di invio delle informazioni da parte del dispositivo a un sistema di elaborazione, e di potenziale intervento sulla base dei risultati elaborati in tempo reale, può essere comunemente associato ai casi di utilizzo di IoT.  Altri scenari includono automobili connesse, analisi di flussi di clic, gestione di strutture e così via. Al fine di ottimizzare il ciclo di feedback per bassa latenza ed elevata velocità effettiva, è possibile usare Analisi dei flussi per acquisire dati da Hub eventi di Azure, elaborarli e quindi re-inviarli allo stesso Hub eventi per il rispettivo dispositivo sottoscritto, affinché questo esegua l'azione appropriata.  


![Azure Stream Analytics real time analysis for the Internet of Things (IoT)][img.stream.analytics.scenario1]


###Telemetria e analisi dei log tramite dashboard
Con l'aumento dei volumi di dispositivi, macchine e applicazioni, un caso di utilizzo comune nella conduzione delle aziende è rappresentato dalla necessità di monitorare e rispondere al mutare delle esigenze aziendali creando analisi complesse quasi in tempo reale. Lo scenario di telemetria e analisi dei log canonico può essere descritto usando l'esempio di servizio o applicazione online, sebbene il modello sia frequentemente osservabile anche tra le aziende che raccolgono e generano report sulla base della telemetria delle proprie applicazioni e dei propri dispositivi. L'applicazione o servizio raccoglie regolarmente i dati sull'integrità.  Vengono raccolti dati che rappresentano lo stato corrente dell'applicazione o dell'infrastruttura in un determinato momento, le richieste di log da parte degli utenti e altri dati che rappresentano azioni o attività eseguite all'interno dell'applicazione. I dati vengono cronologicamente salvati in un BLOB o in altri tipi di archivi dati per ulteriore elaborazione. Con le recenti tendenze verso il dashboarding in tempo reale, oltre a salvare i dati in un BLOB o altro tipo di archivio per l'analisi cronologica, i clienti cercano un modo per elaborare e trasformare il flusso di dati in ingresso direttamente, in modo che tali dati possano essere resi immediatamente disponibili agli utenti finali sotto forma di dashboard e/o di notifiche quando si rende necessario intraprendere delle azioni. Ad esempio se un sito di servizio si arresta, il personale operativo può ricevere una notifica relativa all'avvio dei rilievi per una rapida risoluzione del problema. In molti di questi casi di utilizzo, una persona esegue in genere il monitoraggio di un dashboard in tempo reale basato su set di dati aggiornati in seguito all'elaborazione di dati acquisiti tramite Analisi dei flussi. 
 
![Azure Stream Analytics telemetry and log analysis via dashboards][img.stream.analytics.scenario2]

###Archiviazione di eventi per elaborazione successiva
Le aspettative di esecuzione rapida e flessibile in ambito aziendale sono in continuo aumento. Le aziende e gli sviluppatori optano attualmente per piattaforme facili da usare, basate su cloud, per affrontare le richieste di maggiore flessibilità e cercano piattaforme che consentano loro di acquisire ed elaborare un flusso continuo di dati generati dai propri sistemi quasi in tempo reale.  Questi clienti non sono oggi in grado di sfruttare un servizio ottimizzato per la scrittura a bassa latenza in un archivio dati e pertanto finiscono per perdere numerosi set di dati strategici che potrebbero contenere informazioni preziose per la propria azienda.  Lo scenario di archiviazione eventi canonico può essere descritto come segue: I dati provenienti da piattaforme e dispositivi diversi e distribuiti geograficamente in tutto il mondo vengono inviati a un agente di raccolta dati centralizzato. Quando i dati hanno raggiunto l'ubicazione centrale, vengono eseguite su di essi operazioni di trasformazione senza stato, ad esempio la pulitura di informazioni personali, l'aggiunta di tag geografici, la ricerca di IP e così via. I dati trasformati vengono quindi archiviati nell'archiviazione BLOB in modo da poter essere facilmente usati da HDInsight o da altri strumenti per l'elaborazione offline.

![Azure Stream Analytics event archival for future processing][img.stream.analytics.scenario3]
 







<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
##<a name="nextsteps"></a>Passaggi successivi

- [Introduzione all'uso di Analisi dei flussi di Azure][stream.analytics.get.started]
- [Guida per gli sviluppatori di Analisi dei flussi di Azure][stream.analytics.developer.guide]
- [Ridimensionare i processi di Analisi dei flussi di Azure][stream.analytics.scale.jobs]
- [Limitazioni e problemi noti di Analisi dei flussi di Azure][stream.analytics.limitations]
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure][stream.analytics.query.language.reference]
- [Informazioni di riferimento sulle API REST di gestione di Analisi dei flussi di Azure][stream.analytics.rest.api.reference] 



<!--Image references-->
[img.stream.analytics.scenario1]: ./media/stream-analytics-introduction/Introduction-to-Azure-Stream-Analytics_01.png
[img.stream.analytics.scenario2]: ./media/stream-analytics-introduction/Introduction-to-Azure-Stream-Analytics_02.png
[img.stream.analytics.scenario3]: ./media/stream-analytics-introduction/Introduction-to-Azure-Stream-Analytics_03.png


<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=46--> 
