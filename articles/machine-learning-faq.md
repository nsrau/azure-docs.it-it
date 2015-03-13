<properties 
	pageTitle="Domande frequenti su Azure Machine Learning | Azure" 
	description="Domande frequenti su Microsoft Azure Machine Learning" 
	services="machine-learning" 
	documentationCenter="" 
	authors="pablissima" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="paulettm"/>

# Domande frequenti su Microsoft Azure Machine Learning

### GENERALI

**1. Cos'è Microsoft Azure Machine Learning?**

Microsoft Azure Machine Learning è un servizio completamente gestito che consente di creare, testare, mettere in funzione e gestire soluzioni di analisi predittiva nel cloud. Direttamente dal browser, è ora possibile iscriversi a Machine Learning di Azure, caricare i dati e avviare immediatamente esperimenti di apprendimento automatico. La composizione visuale, un'ampia scelta di moduli e una raccolta di modelli di base contribuiscono a facilitare e accelerare le attività di apprendimento automatico comuni. La conversione di un modello in un servizio Web è facile: bastano pochi clic e un modello creato in ML Studio può essere trasformato in un'API REST pubblica che incapsula la logica di trasformazione dei dati personalizzati e modelli di apprendimento automatico sofisticati.

**2. Cos'è Azure Machine Learning Studio?**

Azure ML Studio è un ambiente workbench accessibile tramite un Web browser. In ML Studio è disponibile una serie di moduli con un'interfaccia di composizione visuale che consente di creare un flusso di lavoro di analisi dei dati end-to-end sotto forma di esperimento. Sono disponibili moduli per l'inserimento e la trasformazione dei dati e la selezione di funzionalità per creare, classificare e valutare modelli predittivi. Alcuni degli algoritmi più avanzati di apprendimento automatico usati in Bing e Xbox sono creati in ML Studio. Sono inoltre inclusi pacchetti di apprendimento automatico open source scalabili come Vowpal Wabbit. ML Studio supporta R. È possibile importare il codice R esistente e incorporarlo nei propri esperimenti. ML Studio consente di combinare questi algoritmi con il codice R per creare modelli predittivi. ML Studio semplifica la collaborazione in quanto consente di invitare i membri del team nelle proprie aree di lavoro, in modo che possano visualizzare e modificare gli esperimenti.

**3. Cos'è il servizio Azure ML API?**

Il servizio ML API consente di distribuire modelli predittivi creati in ML Studio come un servizio Web scalabile con tolleranza di errore. I servizi Web creati tramite il servizio ML API sono API REST che forniscono un'interfaccia di comunicazione tra le applicazioni esterne e il modello di analisi predittivo. Il servizio Web consente di comunicare con un modello predittivo in tempo reale per ricevere risultati predittivi e incorporarli in qualsiasi applicazione client esterna. Il servizio ML API si basa su Microsoft Azure per la distribuzione, l'hosting e la gestione delle API REST di Azure ML. Quando si usa il servizio Azure ML API, vengono creati due tipi di servizi. Il servizio Esecuzione batch per l'accesso batch asincrono e il servizio Richiesta-risposta per la risposta sincrona a bassa latenza.

È possibile collocare un modello predittivo nella gestione temporanea all'interno dell'area di lavoro. Il servizio ML API genera anche le pagine della guida per i servizi Web. Nelle pagine della guida del servizio Web vengono forniti esempi di codice per richiamare il servizio Web in C#, R e Python. Per testare il servizio Web è possibile effettuare chiamate interattive al servizio. Il servizio Web nell'ambiente di gestione temporanea può quindi essere spostato nell'ambiente di produzione con pochi clic. Una volta in produzione, è possibile monitorare i servizi distribuiti e tracciarne l'utilizzo e gli eventuali errori nel portale di Azure. Aggiornare i servizi Web è facile, perché basta aggiornare il modello in ML Studio ed eseguire il push delle modifiche nel servizio nell'ambiente di gestione temporanea.

**4. Come si accede a Microsoft Azure Machine Learning?**

Per informazioni sul servizio Azure Machine Learning, vedere la pagina di [introduzione][introduzione]. Visitare la pagina [Azure Machine Learning Center][Azure Machine Learning Center] per ottenere aggiornamenti sul servizio, leggere le ultime novità nel blog del team di ML, partecipare alla community di Machine Learning attraverso i forum, accedere alle pagine della guida sul prodotto, visualizzare la raccolta di modelli e inviare commenti e suggerimenti sul servizio per contribuire a migliorarlo.

### FATTURAZIONE

**5. Come funziona la fatturazione di Machine Learning?**

Il servizio Azure ML Studio viene fatturato in base alle ore di calcolo per la sperimentazione attiva e le ore parziali vengono calcolate in modo proporzionale. Il servizio Azure ML API viene fatturato per ogni 1.000 chiamate API di predizione o in base all'ora di calcolo, quando una predizione è in esecuzione. La fatturazione viene ripartita in modo proporzionale per le quantità inferiori a 1.000 chiamate e per le ore di calcolo parziali.

Gli addebiti vengono aggregati per area di lavoro della sottoscrizione. In ogni area di lavoro vengono visualizzati gli addebiti per le tre voci seguenti:

-   Ore sperimentazione Studio: questo misuratore aggrega tutti gli addebiti di calcolo accumulati tramite l'esecuzione di esperimenti in ML Studio e l'esecuzione di stime nell'ambiente di gestione temporanea.
-   Ore stima servizio API: questo misuratore include gli addebiti di calcolo accumulati dai servizi Web in esecuzione nell'ambiente di produzione.
-   Stime servizio API (in millesimi): questo misuratore include gli addebiti accumulati per ogni chiamata al servizio Web nell'ambiente di produzione.

Per i dettagli sui prezzi, vedere la pagina Prezzi all'indirizzo [http://azure.microsoft.com/pricing/details/machine-learning/][http://azure.microsoft.com/pricing/details/machine-learning/].

**6. È disponibile una versione di valutazione di Azure Machine Learning?**

Azure ML fa parte della versione di valutazione gratuita di Azure. Quando si effettua l'iscrizione alla versione di valutazione gratuita di Azure, è possibile provare tutti i servizi di Azure per un mese. Per altre informazioni sulla versione di valutazione gratuita di Azure, vedere [http://azure.microsoft.com/pricing/free-trial-faq/][http://azure.microsoft.com/pricing/free-trial-faq/].

### MACHINE LEARNING STUDIO

**7. Quali origini dati sono supportate da Azure ML support?**

È possibile caricare i dati in ML Studio in due modi: caricando i file locali come un set di dati o usando un modulo lettore per l'importazione dei dati. È possibile caricare i file locali come set di dati, aggiungendo nuovi set di dati in ML Studio. Per altre informazioni sui formati di file supportati, vedere l'argomento della guida **Recupero dei dati** per ML Studio.

Il modulo **Lettore** consente di leggere i dati dai servizi tabelle di Azure, BLOB di Azure, database SQL (Azure) o HDInsight. È inoltre possibile caricare i dati da un'origine dati tramite HTTP. Per informazioni dettagliate, vedere l'argomento della guida relativo al modulo **Lettore** per ML Studio.

**8. Quali sono le dimensioni massime consentite per i set di dati?**

ML Studio supporta set di dati di formazione di dimensioni fino a 10 GB. Non sono previsti limiti per le dimensioni di set di dati per i servizi Web. È anche supportato il campionamento di set di dati di dimensioni più elevati tramite query Hive o SQL prima dell'inserimento. Se i dati da usare superano i 10 GB, è possibile creare più set di dati e usare i moduli 'Partizione e campionamento', 'Divisione' o 'Unione' per ricombinarli in ML Studio e creare set di formazione per la creazione di modelli predittivi. Per altre informazioni su questi moduli, vedere l'argomento relativo ai moduli nella guida per ML Studio.

Per i set di dati con dimensioni pari a 2 GB, è consigliabile caricare i dati nel servizio di archiviazione di Azure o nel database SQL (Azure) o usare HDInsight invece di caricarli direttamente dal file locale.

**9. Quali sono gli algoritmi di apprendimento automatico esistenti supportati in ML Studio?**

ML Studio include algoritmi ML all'avanguardia, ad esempio alberi di decisione con boosting scalabili, sistemi di raccomandazione bayesiani, reti neurali basate su machine deep learning e Decision Jungle sviluppati presso Microsoft Research. Sono inoltre inclusi pacchetti di apprendimento automatico open source scalabili come Vowpal Wabbit. ML Studio supporta algoritmi di apprendimento automatico per classificazione, regressione e clustering multiclasse e binari. Per l'elenco completo di algoritmi di apprendimento automatico, vedere la Guida di ML Studio.

**10. L'algoritmo, l'origine dati, il formato di dati o l'operazione di conversione dati di apprendimento automatico non è disponibile in Azure ML Studio. Quali sono le alternative?**

È possibile visitare il [forum con i commenti e suggerimenti degli utenti][forum con i commenti e suggerimenti degli utenti] per visualizzare le richieste di funzionalità a cui stiamo dando seguito. Se la funzionalità cercata è già stata richiesta, aggiungere il proprio voto alla richiesta. Se la funzionalità cercata non esiste, creare una nuova richiesta. Sempre in questo forum, è possibile visualizzare lo stato della richiesta. Questo elenco viene controllato regolarmente e lo stato di disponibilità delle funzionalità viene aggiornato di frequente.

**11. È possibile importare il codice esistente in ML Studio?**

ML Studio supporta R ed è possibile importare il codice R in ML Studio ed eseguirlo nello stesso esperimento con i motori di apprendimento forniti da Azure ML e che il progetto venga pubblicato come servizio Web tramite Azure ML. Azure ML è il modo più rapido per convertire le risorse di analisi in R in servizi Web di produzione di classe enterprise. Per informazioni su come importare il codice e la visualizzazione R in ML Studio, vedere l'argomento relativo all'**estendibilità con R** nella guida di ML Studio.

**12. Quali pacchetti R sono disponibili in ML Studio?**

Al momento ML Studio supporta oltre 350 pacchetti R e l'elenco è in continuo aumento. Per informazioni su come ottenere l'elenco dei pacchetti R supportati, vedere l'argomento relativo all'**estendibilità con R** nella guida di ML Studio. Se il pacchetto desiderato non compare nell'elenco, specificare il nome del pacchetto nel [forum dei commenti e suggerimenti degli utenti][forum con i commenti e suggerimenti degli utenti].

### SERVIZIO ML API

**13. In quali casi è necessario eseguire il modello predittivo come servizio Esecuzione batch anziché come servizio Web Richiesta-risposta?**

Il servizio Richiesta-risposta è un servizio Web a bassa latenza e scalabilità elevata che fornisce un'interfaccia a modelli senza stato creati e pubblicati dall'ambiente di sperimentazione. Il servizio Esecuzione batch è un servizio per l'assegnazione di punteggi asincrona di un batch di record di dati. L'input per il servizio Esecuzione batch è simile all'input di dati per il servizio Richiesta-risposta. La differenza principale consiste nel fatto che il servizio Esecuzione batch legge un blocco di record da un'ampia gamma di origini, ad esempio origini BLOB e tabelle di Azure, database SQL (Azure), HDInsight (Query Hive) e HTTP. L'output dei risultati dell'assegnazione di punteggi viene eseguito in un file nel servizio di archiviazione BLOB di Azure e l'endpoint di archiviazione viene restituito nella risposta.

Il servizio Esecuzione batch è utile negli scenari in cui è necessario assegnare punteggi a una grande quantità di dati, in batch, oppure quando gran parte dei dati è già in formato file in una risorsa di archiviazione di Azure o in un cluster Hadoop. Il servizio Web può convertire i dati che legge prima di inviarli al modello, affinché sia sufficiente indirizzare i dati delle transazioni settimanali a un servizio Batch che li convertirà e fornirà i risultati.

Il servizio Richiesta-risposta è utile nei casi in cui sono necessarie analisi predittive in tempo quasi reale per popolare un dashboard attivo o guidare l'azione dell'utente o il contenuto presentato tramite un'applicazione mobile o Web.

**14. Come è possibile aggiornare il modello per i servizi di produzione già distribuiti?**

L'aggiornamento di un modello predittivo per un servizio già distribuito è semplice. È sufficiente modificare l'esperimento usato per creare e salvare il modello di apprendimento ed eseguirlo di nuovo. Quando è disponibile la nuova versione del modello di apprendimento, ML Studio chiederà all'utente se vuole aggiornare il servizio Web nell'ambiente di gestione temporanea. Una volta applicato l'aggiornamento al servizio Web nell'ambiente di gestione temporanea, lo stesso aggiornamento diventerà disponibile per il servizio Web nell'ambiente di produzione. Per informazioni dettagliate su come aggiornare un servizio Web distribuito, vedere l'argomento relativo all'**aggiornamento del servizio Web** nella Guida di ML Studio.

### SICUREZZA E DISPONIBILITÀ

**15. Quali utenti possono accedere per impostazione predefinita all'endpoint HTTP per il servizio Web distribuito nell'ambiente di produzione? Come è possibile limitare l'accesso all'endpoint?**

Dopo che il modello predittivo è stato distribuito nell'ambiente di produzione, nel portale di Azure viene indicato l'URL per i servizi Web distribuiti. È possibile accedere agli URL dei servizi nell'ambiente di gestione temporanea dalla sezione Servizi Web dell'ambiente ML Studio e agli URL dei servizi dell'ambiente di produzione dalla sezione Machine Learning del portale di Azure. Per i servizi Web di gestione temporanea e di produzione, le chiavi di accesso vengono fornite rispettivamente nel dashboard del servizio Web in ML Studio e nel portale di Azure. Le chiavi di accesso sono necessarie per effettuare le chiamate al servizio Web sia nell'ambiente di produzione che nell'ambiente di gestione temporanea.

**16. Come è possibile monitorare un servizio Web distribuito nell'ambiente di produzione?**

Dopo che il modello predittivo è stato distribuito nell'ambiente di produzione, è possibile monitorarlo nel portale di Azure. Per ogni servizio distribuito è disponibile un dashboard dedicato in cui vengono visualizzate le informazioni di monitoraggio per il servizio in questione.

### SUPPORTO E FORMAZIONE

**17. Dove è possibile trovare risorse di formazione per Azure ML?**

In [Azure Machine Learning Center][Azure Machine Learning Center] sono disponibili esercitazioni video e procedure dettagliate. Nelle procedure dettagliate viene fornita un'introduzione ai servizi e viene descritto il ciclo di vita delle operazioni di importazione dei dati, pulizia dei dati, creazione di modelli predittivi e importazione dei modelli nell'ambiente di produzione con Azure ML.

Le esercitazioni video offrono invece una panoramica visiva di ML Studio e del servizio ML API. Nelle esercitazioni video viene fornita una panoramica del servizio e vengono descritti i moduli di ingresso, pulizia ed elaborazione dei dati più comuni e le procedure per la creazione e la successiva distribuzione dei modelli predittivi. Nelle esercitazioni video vengono anche illustrate attività quali il provisioning dell'area di lavoro e la distribuzione dei modelli di gestione temporanea nell'ambiente di produzione.

Microsoft aggiungerà regolarmente nuovo materiale in Machine Learning Center. È possibile inviare richieste di ulteriore materiale di formazione nel [forum dei commenti e suggerimenti degli utenti][forum dei commenti e suggerimenti degli utenti] di Machine Learning Center.

**18. Come è possibile richiedere supporto per Azure ML?**

Il supporto per Azure ML è incluso nell'offerta di supporto per Azure. Per richiedere supporto tecnico per Azure ML, selezionare il servizio 'Machine Learning'. Scegliere una delle categorie di argomenti per inviare il ticket di supporto. Per altre informazioni sull'offerta di supporto di Azure, vedere [http://azure.microsoft.com/support/options/][http://azure.microsoft.com/support/options/]

È inoltre disponibile un forum della community di Azure Machine Learning su MSDN, in cui è possibile porre domande relative ad Azure ML. Il forum è monitorato dal team di Azure ML. Visitare il [forum di Azure][forum di Azure].

  [introduzione]: http://go.microsoft.com/fwlink/?LinkId=404226
  [Azure Machine Learning Center]: http://azure.microsoft.com/documentation/services/machine-learning/
  [http://azure.microsoft.com/pricing/details/machine-learning/]: http://azure.microsoft.com/pricing/details/machine-learning/
  [http://azure.microsoft.com/pricing/free-trial-faq/]: http://azure.microsoft.com/pricing/free-trial-faq/
  [forum con i commenti e suggerimenti degli utenti]: http://go.microsoft.com/fwlink/?LinkId=404231
  [forum dei commenti e suggerimenti degli utenti]: https://windowsazure.uservoice.com/forums/257792-machine-learning
  [http://azure.microsoft.com/support/options/]: http://azure.microsoft.com/support/options/
  [forum di Azure]: http://social.msdn.microsoft.com/Forums/windowsazure/it-it/home?forum=MachineLearning

<!--HONumber=46--> 
