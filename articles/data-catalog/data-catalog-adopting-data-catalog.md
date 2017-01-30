---
title: Approccio e processo per l&quot;adozione di Azure Data Catalog | Documentazione Microsoft
description: Questo articolo presenta un approccio e un processo per le organizzazioni intenzionate ad adottare Azure Data Catalog, inclusa la definizione di una visione, l&quot;identificazione dei principali casi d&quot;uso di business e la scelta di un progetto pilota.
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 0c771e7a-6fcd-417f-9247-897177719567
ms.service: data-catalog
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/23/2017
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 15e72950ef30b8540d0303292fb9882f500c875e
ms.openlocfilehash: 646a7611f5680a4c08e9a5f5ff4e2a3e5631276f


---
# <a name="approach-and-process-for-adopting-azure-data-catalog"></a>Approccio e processo per l'adozione di Azure Data Catalog
Questo articolo illustra come iniziare ad adottare **Azure Data Catalog** nell'organizzazione. Per adottare **Azure Data Catalog**, è opportuno tenere presenti tre elementi chiave: definire il proprio punto di vista, identificare i principali casi d'uso di business nell'organizzazione e scegliere un progetto pilota.

## <a name="introducing-the-azure-data-catalog"></a>Introduzione a Catalogo dati di Azure
Nel mondo del lavoro le aspettative su come poter trovare informazioni avanzate sugli asset di dati sono cambiate. Oggi, con la diffusione degli strumenti di social media, ad esempio Yammer, sul posto di lavoro, le persone si aspettano di ottenere velocemente assistenza e suggerimenti su una vasta serie di argomenti. **Catalogo dati di Azure** consente alle aziende e ai team di consolidare le informazioni sugli asset di dati aziendali in un archivio centrale in cui i consumer dei dati possono individuare le origini dati disponibili e ottenere informazioni a cui contribuiscono gli esperti di dominio.

Questo articolo presenta un approccio per iniziare a usare **Catalogo dati di Azure**. L'articolo descrive un piano tipico di adozione di Catalogo dati per la società fittizia Adventure Works.

**Che cos'è il Catalogo dei dati di Azure?**

**Catalogo dati di Azure** è un servizio completamente gestito in Azure, nonché un catalogo di informazioni (metadati) di livello aziendale che consente l'individuazione self-service di origini dati. Con Catalogo dati,è possibile registrare, individuare, annotare e connettersi agli asset di dati. Catalogo dati è progettato per gestire asset a livello di informazioni diversi facilitandone l'individuazione e consentendo agli utenti di comprendere gli asset di dati trovati e di connettersi a questi asset di dati, riducendo il tempo di analisi e aumentando valore delle organizzazioni. Per altre informazioni, vedere [Catalogo dati di Microsoft Azure](https://azure.microsoft.com/services/data-catalog/).

## <a name="azure-data-catalog-adoption-plan"></a>Piano di adozione di Catalogo dati di Azure
Un piano di adozione di **Azure Data Catalog** descrive come comunicare a stakeholder e utenti i vantaggi derivanti dall'uso del servizio Data Catalog, oltre al tipo di training che si offre agli utenti del servizio. Un elemento chiave del successo nell'adozione di Catalogo dati è la capacità di comunicare in modo efficace il valore del servizio a utenti e stakeholder. I principali destinatari in un piano di adozione iniziale sono gli utenti del servizio. Indipendentemente dall'adesione da parte degli stakeholder, se gli utenti, ovvero i clienti, dell'offerta relativa a Data Catalog non la incorporano nell'utilizzo, l'adozione non avrà successo. Questo articolo presuppone quindi che l'utente abbia l'adesione degli stakeholder e si concentra sulla creazione di un piano per l'adozione di Data Catalog da parte degli utenti.
Un piano di adozione efficace incoraggia le persone a considerare ciò che è possibile fare con Catalogo dati e offre informazioni e indicazioni per riuscirci. È necessario che gli utenti comprendano il valore di Catalogo dati per eseguire correttamente i vari processi. Quando le persone capiscono l'utilità di Data Catalog per ottenere risultati migliori con i dati, il valore dell'adozione di Data Catalog diventa chiaro. Il cambiamento non è semplice, quindi un piano efficace deve tenere in considerazione le difficoltà derivanti da questo cambiamento.

Un piano di adozione consente di comunicare ciò che è essenziale alle persone per raggiungere i propri obiettivi. Un piano tipico spiega come Data Catalog faciliterà le normali operazioni quotidiane degli utenti e include le parti seguenti:

* **Dichiarazione della visione**: consente di illustrare brevemente il piano di adozione a utenti e stakeholder. È il cosiddetto "elevator pitch".
* **Team pilota e influencer**: confrontarsi con un team pilota e con gli influencer consente di perfezionare il modo di presentare Data Catalog ai team e agli utenti. Gli influencer possono occuparsi del peer coaching dei colleghi. In questo modo è anche possibile identificare le persone contrarie e quelle favorevoli all'adozione.
* **Piano per le comunicazioni e il buzz** : consente agli utenti di comprendere l'utilità di Catalogo dati e può facilitare la naturale adozione nei team e infine nell'intera organizzazione.
* **Piano di training** : un training completo comporta in genere il successo dell'adozione e risultati positivi.

Ecco alcuni suggerimenti per definire un piano di adozione di **Catalogo dati di Azure** .

## <a name="define-your-data-catalog-project-vision"></a>Definire gli obiettivi del progetto Catalogo dati
Il primo passaggio per definire un piano di adozione di **Catalogo dati di Azure** prevede la stesura di una descrizione delle aspirazioni che si vogliono realizzare. L'ideale è che la dichiarazione della visione sia abbastanza generica, ma sufficientemente concisa da definire obiettivi specifici a breve termine e a lungo termine.

Ecco alcuni suggerimenti per definire la visione:

* **Identificare il driver di distribuzione chiave** : considerare le esigenze di gestione delle origini dati specifiche per il business, che possono essere soddisfatte con Catalogo dati. In questo modo sarà possibile determinare i vantaggi principali dell'uso di Catalogo dati. Ad esempio, possono esserci origini dati comuni che tutti i nuovi dipendenti devono conoscere e usare o origini dati importanti e complesse che solo poche persone chiave conoscono a fondo. **Catalogo dati di Azure** consente di individuare e comprendere facilmente queste origini dati, in modo che queste criticità note possano essere risolte direttamente e subito con l'adozione del servizio.
* **Essere precisi e chiari**: una conoscenza precisa della visione fa in modo che tutti si trovino d'accordo sul valore conferito da Data Catalog all'organizzazione e su come la visione supporti gli obiettivi dell'organizzazione.
* **Spingere le persone a voler usare Catalogo dati** : la visione e il piano di comunicazione devono indurre le persone a riconoscere che Catalogo dati può aiutarle a trovare e a connettersi alle origini dati per ottenere migliori risultati con i dati.
* **Specificare obiettivi e una sequenza temporale**: in questo modo è possibile assicurarsi che il piano di adozione preveda risultati finali specifici e raggiungibili. Una sequenza temporale fa in modo che tutti rimangano concentrati e consente ai checkpoint di valutare la riuscita.

Ecco un esempio di dichiarazione della visione per un piano di adozione di Catalogo dati per la società fittizia Adventure Works:

**Azure Data Catalog** consente al team Finanza di Adventure Works di collaborare alle origini dati chiave, in modo che ogni membro del team possa trovare e usare facilmente i dati necessari e condividere le proprie conoscenze con l'intero team.

Una volta ottenuta una precisa dichiarazione della visione, è consigliabile identificare un progetto pilota per Catalogo dati. Poiché gli scenari per Data Catalog sono in genere più di uno, la prossima sezione offre alcuni suggerimenti per identificare i casi d'uso pertinenti.

## <a name="identify-data-catalog-business-use-cases"></a>Identificare i casi d'uso di business di Catalogo dati
Per identificare i casi d'uso pertinenti a Catalogo dati e i problemi di business da risolvere, collaborare con gli esperti delle varie business unit. Esaminare le attuali difficoltà che le persone incontrano nell'identificazione e nella comprensione degli asset di dati. Ad esempio, i team ottengono informazioni sugli asset di dati solo dopo avere chiesto a più persone nell'organizzazione chi è in possesso delle origini dati rilevanti?

È meglio scegliere casi d'uso che rappresentano un obiettivo facile da raggiungere: casi importanti che però presentino un elevata probabilità di successo se risolti con Catalogo dati.

Ecco alcuni suggerimenti per identificare i casi d'uso:

* **Definire gli obiettivi del team** : in che modo il team raggiunge i propri obiettivi? Non concentrarsi ancora su Catalogo dati, perché in questa fase è importante essere obiettivi. Si ricordi che si sta parlando dei risultati di business, non della tecnologia.
* **Definire il problema di business** : quali sono le problematiche affrontate dal team relativamente alla ricerca e alla comprensione degli asset di dati? Ad esempio, le informazioni sulle origini dati importanti possono trovarsi in cartelle di lavoro di Excel in una cartella di rete e il team può impiegare molto tempo per individuare le cartelle di lavoro.
* **Comprendere il pensiero del team relativamente al cambiamento** : molte difficoltà dell'adozione riguardano la resistenza al cambiamento più che l'implementazione di un nuovo strumento. La risposta di un team al cambiamento è importante quando si identificano i casi d'uso perché il processo esistente potrebbe essere in atto perché "si è sempre fatto così" o perché "se funziona, non serve cambiarlo". L'adozione di un nuovo strumento o processo è sempre molto facile quando le persone capiscono il valore derivante dal cambiamento e sanno valutare l'importanza dei problemi da risolvere.
* **Mantenere l'attenzione sugli asset di dati** : quando si illustrano i problemi di business affrontati da un team, è necessario andare dritti al punto e concentrarsi su ciò che conta per sfruttare in modo più efficiente gli asset di dati aziendali.

Ecco alcuni casi d'uso di esempio correlati a Catalogo dati:

### <a name="example-use-cases"></a>Casi d'uso di esempio
* **Registrare le origini dati generiche centrali** : l'IT gestisce le origini dati usate nell'organizzazione. L'IT può iniziare a usare Catalogo dati registrando e annotando le origini dati aziendali comuni.
* **Registrare le origini dati basate sul team**: diversi team hanno origini dati line-of-business utili. Iniziare a usare **Azure Data Catalog** identificando e registrando le origini dati chiave usate da più team diversi e acquisire le conoscenze di base di ogni team nelle annotazioni di **Azure Data Catalog**.
* **Business intelligence self-service** : i team dedicano molto tempo a combinare dati da più origini. Registrare e annotare le origini dati in una posizione centrale per eliminare il processo manuale di individuazione delle origini dati.

Questi sono alcuni dei casi d'uso per **Catalogo dati di Azure**. Per altre informazioni sugli scenari di Catalogo dati, vedere [Scenari comuni del Catalogo dati di Azure](data-catalog-common-scenarios.md).

Una volta identificati alcuni casi d'uso per Catalogo dati, emergeranno gli scenari comuni. La sezione successiva illustra come identificare il primo progetto pilota basato su un caso d'uso.

## <a name="choose-a-data-catalog-pilot-project"></a>Scegliere un progetto pilota di Catalogo dati
Una fattore di successo chiave è semplificare e iniziare in piccolo. Un progetto pilota ben definito con un ambito limitato evita che il progetto possa crescere fino a diventare troppo complesso o a includere troppi partecipanti. È importante anche includere tipi diversi di utenti, dai primi utenti agli scettici. Gli utenti che hanno accolto la soluzione aiutano a perfezionare il futuro piano di comunicazione e di buzz. Gli scettici sono d'aiuto per identificare e affrontare gli utenti non favorevoli. Quando gli scettici diventano promotori, è possibile basarsi sul loro feedback per identificare i fattori di successo.

Il piano pilota deve inserire gradualmente gli obiettivi di business che si vuole raggiungere con Catalogo dati. Come è evidente dal progetto pilota iniziale, è possibile espandere la base degli utenti. Un progetto pilota iniziale chiuso va bene per stabilire le possibilità di successo misurabili, ma l'obiettivo finale è relativo alla crescita organica o virale. Con la crescita organica di Catalogo dati, gli utenti hanno il controllo dell'utilizzo dei dati e possono influenzare e incoraggiare gli altri ad adottare il catalogo e a contribuirvi.

### <a name="target-the-right-team"></a>Individuare il team appropriato
Quando si sceglie il progetto pilota, selezionare il team con gli scenari più interessanti per risolvere un problema di business esistente. Un business analyst, ad esempio, crea report da un database SQL Server. Il problema è che è venuta a conoscenza delle origini dati solo dopo avere parlato con diversi colleghi. Infine, dopo avere perso tempo per cercare di capire quali origini dati usare, ha trovato una cartella di lavoro di Excel contenente una descrizione di ogni origine dati. Anche se la cartella di lavoro di Excel descrizione correttamente le tabelle di cui ha bisogno, avrebbe trovato più velocemente queste origini dati se fossero state registrate e annotate in **Catalogo dati di Azure**.

### <a name="identify-data-heroes"></a>Identificare gli utenti principali dei dati
Il primo progetto pilota includerà poche persone che producono dati e che li utilizzano in modo che il team sia equamente rappresentato.

**produttori di dati** sono persone esperte di origini dati. Ad esempio, David in un altro team ha lavorato molto con le principali origini dati di Adventure Works. Prima dell'adozione di **Catalogo dati di Azure**, David ha creato una cartella di lavoro di Excel per acquisire informazioni sulle origini dati di Adventure Works.

**utilizzatori di dati** sono persone esperte nell'uso dei dati per risolvere i problemi di business. Ad esempio, Nancy è una business analyst e usa le origini dati di SQL Server di Adventure Works per analizzare i dati.

Uno dei problemi di business risolti da **Azure Data Catalog** è la necessità di mettere in contatto i **produttori di dati** con gli **utilizzatori di dati**. A questo scopo funge da archivio centrale per le informazioni sulle origini dati aziendali. Usando Catalogo dati, David registra le origini dati di SQL Server e Adventure Works. Usando il crowdsourcing gli utenti che individuano questa origine dati possono condividere le proprie opinioni sui dati, oltre a usare i dati trovati. Nancy, ad esempio, trova le origini dati cercando nel catalogo e condivide le sue conoscenze specialistiche sui dati.  Ora gli altri utenti dell'organizzazione possono sfruttare le conoscenze condivise semplicemente eseguendo una ricerca nel catalogo dati.

* Per altre informazioni sulla registrazione delle origini dati, vedere [Registrare le origini dati](data-catalog-get-started.md).
* Per altre informazioni sull'individuazione delle origini dati, vedere [Cercare le origini dati](data-catalog-get-started.md).

### <a name="start-small-and-focused"></a>Iniziare con un progetto piccolo e mirato
Per la maggior parte dei progetti pilota aziendali, è consigliabile inserire nel catalogo origini dati generiche in modo che gli utenti business possano comprendere rapidamente il valore di Catalogo dati. L'IT è l'ideale per iniziare a identificare le origini dati comuni che potrebbero interessare il team pilota. Per le origini dati supportate, ad esempio SQL Server, è consigliabile usare lo strumento di registrazione delle origini dati di **Catalogo dati di Azure** . Con lo strumento di registrazione delle origini dati, è possibile registrare un'ampia gamma di origini dati, inclusi i database SQL Server e Oracle e i report di SQL Server Reporting Services. Per un elenco completo delle origini dati correnti, vedere [Origini dati supportate da Catalogo dati di Azure](data-catalog-dsr.md).

Una volta identificate e registrate le principali origini dati, è anche possibile importare le relative descrizioni archiviate in altre posizioni. L'API di Catalogo dati consente agli sviluppatori di caricare le descrizioni e le annotazioni da un'altra posizione, ad esempio la cartella di lavoro di Excel che David ha creato e che gestisce.

La sezione successiva descrive un progetto di esempio della società Adventure Works.

### <a name="an-example-project"></a>Progetto di esempio
In questo esempio la business analyst Nancy crea report per il suo team, usando i dati di un database SQL Server. Il problema è che è venuta a conoscenza delle origini dati solo dopo avere parlato con diversi colleghi. Se queste origini dati fossero state registrate e annotate in una posizione centrale, ad esempio **Catalogo dati di Azure**, le avrebbe trovate velocemente.

Per far capire con quanta facilità Nancy e il suo team possono trovare le origini dati generiche, si usa lo strumento di registrazione delle origini dati per popolare il catalogo con le informazioni (metadati) sulle origini dati. In questo modo le informazioni sul database sono disponibili per il team e l'organizzazione e non solo per poche persone. Una volta registrate in Data Catalog le origini dati, Nancy e il suo team possono usarle facilmente. Il risultato è un catalogo dati più completo e pertinente per il suo team e per l'organizzazione. Man mano che sempre più team adottano Catalogo dati, le origini dati di business diventano più facili da trovare e da usare, favorendo così un approccio maggiormente basato sui dati per ottenere migliori risultati con i dati.

Per altre informazioni sullo strumento di registrazione delle origini dati, vedere [Introduzione a Catalogo dati di Azure](data-catalog-get-started.md).

Nell'ambito del progetto pilota, il team di Nancy usa anche le origini dati descritte in una cartella di lavoro di Excel gestita da David e dai suoi colleghi. Poiché anche altri team dell'azienda usano cartelle di lavoro di Excel per descrivere le origini dati, il team IT decide di creare uno strumento per eseguire la migrazione della cartella di lavoro di Excel a Catalogo dati. Usando l'API REST di Catalogo dati per importare le annotazioni esistenti, il team del progetto pilota può ottenere un catalogo dati completo costituito dai metadati estratti dalle origini dati usando lo strumento di registrazione delle origini dati, che include anche le informazioni documentate in precedenza dai produttori e dagli utilizzatori dei dati, senza bisogno di immetterli di nuovo manualmente. Man mano che il catalogo dati cresce, l'organizzazione può usare lo strumento di registrazione per le origini dati comuni e l'API di Catalogo dati per le origini personalizzate e gli scenari non comuni.

> [!NOTE]
> Abbiamo scritto uno strumento di esempio che usa l'API di **Catalogo dati di Azure** per eseguire la migrazione di una cartella di lavoro di Excel a Catalogo dati. Per altre informazioni sull'API di Data Catalog e sullo strumento di esempio, è possibile [scaricare il codice di esempio della cartella di lavoro ad hoc](https://azure.microsoft.com/documentation/samples/data-catalog-dotnet-excel-register-data-assets/) e vedere la documentazione sull'[API REST di Azure Data Catalog](https://msdn.microsoft.com/library/azure/mt267593.aspx).
>
>

Una volta completato il progetto pilota, è ora di eseguire il piano di adozione di Catalogo dati.

### <a name="execute"></a>Esegui
A questo punto i casi d'uso per Catalogo dati e il primo progetto sono stati identificati. Sono anche state registrate le origini dati chiave di Adventure Works e sono state aggiunte le informazioni contenute nella cartella di lavoro di Excel usando lo strumento compilato dall'IT. Ora si collaborerà con il team pilota per avviare il processo di adozione di Catalogo dati.

Ecco alcuni suggerimenti per iniziare:

* **Creare entusiasmo**: gli utenti business saranno entusiasti se penseranno che **Azure Data Catalog** semplifichi il loro lavoro. Cercare di orientare la discussione sulla soluzione e sui vantaggi offerti, non sulla tecnologia.
* **Facilitare il cambiamento** : iniziare in piccolo e comunicare il piano agli utenti business. Per la riuscita, è indispensabile coinvolgere gli utenti fin dall'inizio in modo che influenzino il risultato e sviluppino un senso di proprietà nei confronti della soluzione.
* **Formare i primi utenti**: i primi utenti sono utenti business che si appassionano a ciò che fanno e sono entusiasti di far conoscere i vantaggi di **Azure Data Catalog** ai colleghi.
* **Indirizzare il target** : non è necessario che gli utenti business sappiano tutto di Catalogo dati, quindi il training deve essere mirato al raggiungimento degli obiettivi specifici del team. Concentrarsi su ciò che gli utenti faranno e su come potrebbero cambiare alcune attività, per inserire **Catalogo dati di Azure** nella loro routine giornaliera.
* **Essere preparati agli errori** : se il progetto pilota non raggiunge i risultati desiderati, effettuare una nuova valutazione e identificare le aree su cui intervenire, per risolvere i problemi nel progetto pilota prima di passare a un ambito più ampio.

Prima che il team pilota inizi a usare Catalogo dati, pianificare una riunione introduttiva per illustrare le aspettative del progetto pilota e fornire il training iniziale.

### <a name="set-expectations"></a>Stabilire le aspettative
Stabilire le aspettative e gli obiettivi consente agli utenti business di concentrarsi su risultati finali specifici. Per rispettare i tempi prestabiliti per il progetto, assegnare regolarmente (ad esempio, ogni giorno o una volta alla settimana a seconda dell'ambito e della durata del progetto pilota) compiti da svolgere. Una delle funzionalità più utili di Catalogo dati è il crowdsourcing degli asset di dati che consente agli utenti business di sfruttare le conoscenze dei dati aziendali. Un compito molto utile per ogni membro del team pilota è quello di registrare o annotare almeno una delle origini dati usate. Vedere [Registrare un'origine dati](data-catalog-get-started.md) e [Come annotare le origini dati](data-catalog-get-started.md).

Pianificare riunioni periodiche con il team per riesaminare alcune delle annotazioni. Creare annotazioni corrette sulle origini dati è essenziale per il successo dell'adozione di Catalogo dati perché forniscono importanti informazioni sulle origini dati in una posizione centrale. Senza annotazioni corrette, le conoscenze sulle origini dati rimangono sparse in tutta l'organizzazione. Vedere [Come annotare le origini dati](data-catalog-get-started.md).

Il test finale per il progetto consiste ovviamente nel verificare se gli utenti riescono a individuare e comprendere le origini dati che devono usare. Gli utenti pilota devono testare regolarmente il catalogo per assicurarsi che le origini dati usate per il lavoro giornaliero siano pertinenti. Quando un'origine dati necessaria è mancante o non è annotata correttamente, questo deve diventare un promemoria per registrare altre origini dati o per fornire altre annotazioni. Questa procedura non solo accresce il valore delle attività del progetto pilota, ma genera anche utili abitudini che vengono trasmesse agli altri team al termine del progetto pilota.

### <a name="provide-training"></a>Fornire il training
Il training deve aiutare gli utenti a iniziare e deve basarsi sugli obiettivi specifici e sul livello di esperienza dei membri del team pilota. Per iniziare con il training, è possibile seguire i passaggi illustrati nell'articolo [Introduzione a Catalogo dati di Azure](data-catalog-get-started.md) . È anche possibile scaricare la [presentazione sul training per il progetto pilota di Catalogo dati di Azure](https://github.com/Azure-Samples/data-catalog-dotnet-get-started/blob/master/Azure%20Data%20Catalog%20Training.pptx?raw=true). Questa presentazione di PowerPoint è un valido aiuto per introdurre Catalogo dati ai membri del team pilota.

## <a name="conclusion"></a>Conclusioni
Una volta che il team pilota inizia a lavorare con una certa facilità e gli obiettivi iniziali vengono raggiunti, è consigliabile espandere l'adozione di Catalogo dati ad altri team. Applicare e perfezionare quanto si è appreso dal progetto pilota per espandere Catalogo dati in tutta l'organizzazione.

I primi utenti che hanno partecipato al progetto pilota possono essere di grande aiuto per far conoscere i vantaggi dell'adozione di Catalogo dati. Possono condividere con gli altri team come Catalogo dati ha consentito al loro team di risolvere problemi di business, individuare le origini dati più facilmente e condividere informazioni sulle origini dati usate. Ad esempio, i primi utenti del team pilota di Adventure Works possono mostrare agli altri quanto sia facile trovare informazioni sugli asset di dati di Adventure Works che prima era invece difficile trovare e comprendere.

Questo articolo ha illustrato come iniziare a usare **Catalogo dati di Azure** nell'organizzazione. A questo punto, non resta che avviare un progetto pilota di Catalogo dati ed espandere Catalogo dati a tutta l'organizzazione.

## <a name="more-information-about-azure-data-catalog"></a>Altre informazioni su Catalogo dati di Azure
* [Pagina del prodotto Catalogo dati di Azure](https://azure.microsoft.com/services/data-catalog/)
* [Documentazione di Catalogo dati di Azure](https://azure.microsoft.com/documentation/services/data-catalog/)
* [Scenari comuni del Catalogo dati di Azure](data-catalog-common-scenarios.md)
* [Registrare le origini dati](data-catalog-get-started.md)
* [Cercare le origini dati](data-catalog-get-started.md)
* [Annotare le origini dati](data-catalog-get-started.md)
* [Crowdsourcing di metadati](data-catalog-get-started.md)



<!--HONumber=Jan17_HO4-->


