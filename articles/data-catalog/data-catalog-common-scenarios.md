<properties
   pageTitle="Scenari comuni del Catalogo dati di Azure"
   description="Rivedere gli scenari comuni nel Catalogo dati di Azure: registrazione, miglioramento, esplorazione, comprensione, utilizzo di origini dati e rimozione di metadati di origini dati."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/31/2015"
   ms.author="maroche"/>


# Scenari comuni del Catalogo dati di Azure

In questo articolo vengono presentati scenari comuni in cui il Catalogo dati di Azure può aiutare le organizzazioni a incrementare il valore delle origini dati esistenti.

## Scenario 1: registrazione delle origini dati centrali

Le organizzazioni hanno spesso un numero di origini dati di valore elevato. Queste origini dati includono sistemi OLTP LOB, data warehouse e database di business intelligence/analisi. Spesso il numero dei sistemi e la sovrapposizione tra i sistemi cresce nel tempo in base alle necessità dell’evoluzione aziendale con acquisizioni e fusioni.

Spesso è difficile per gli utenti sapere dove si trovano i dati all'interno delle origini dati. Domande come queste sono molto diffuse:

- Tra i tre sistemi HR utilizzati all'interno della società, quale è opportuno usare per creare questo tipo di report?
- Dove è necessario rivolgersi per ottenere i numeri delle vendite certificate per l'anno fiscale appena finito?
- A chi è necessario rivolgersi o qual è il processo per ottenere l'accesso al data warehouse?
- Se non si è certi che i numeri siano corretti, a chi è possibile rivolgersi per ottenere informazioni su come questi dati devono essere utilizzati prima di condividere questo dashboard con il team?

In questo scenario, il Catalogo dati di Azure può essere di aiuto. Le origini dati centrali, di valore elevato e gestite da team IT, che vengono utilizzate nell'organizzazione sono spesso il punto di partenza logico per il popolamento del catalogo. Sebbene qualsiasi utente possa registrare un’origine dati , se il catalogo viene avviato con le origini di dati che hanno più probabilità di fornire valore a un maggior numero di utenti, si contribuisce all'adozione e all'uso del sistema. Per i clienti che iniziano a usare il Catalogo dati di Azure, l'identificazione e la registrazione delle origini dati principali utilizzate da molti team diversi di consumer di dati può essere il primo passaggio per il successo.

Questo scenario presenta anche un'opportunità per annotare le origini dati di valore elevato per facilitarne la comprensione e l'accesso. Un aspetto fondamentale di questa operazione consiste nell'includere informazioni su come gli utenti possono richiedere l'accesso all'origine dati. Il catalogo dati di Azure consente agli utenti di fornire l'indirizzo di posta elettronica dell'utente o del team responsabile del controllo di accesso all'origine dati, collegamenti a strumenti esistenti o documenti o testo libero che descrive il processo di richiesta di accesso. Con queste informazioni incluse nel catalogo, gli utenti che individuano origini dati registrate, ma che non dispongono ancora delle autorizzazioni per accedere ai dati possono richiedere facilmente l'accesso utilizzando i processi definiti e controllati dai proprietari di origine dati.

## Scenario 2: business intelligence self-service

Anche se le soluzioni di business intelligence aziendali tradizionali continuano a essere scenari di dati di molte organizzazioni, i vari cambiamenti di mercato hanno reso la soluzione BI self-service sempre più importante. BI self-service consente agli information worker e agli analisti di creare report, cartelle di lavoro e dashboard senza basarsi su un team IT centrale oppure senza limitazioni di pianificazione e disponibilità del team IT.

In scenari di Business Intelligence self-service, è comune per gli utenti combinare dati da più origini, molte delle quali potrebbero non essere ancora state utilizzate per l'analisi e Business Intelligence. Sebbene alcune di queste origini dati potrebbero essere già note, è spesso disponibile un processo per scoprire cosa fare per individuare e valutare le potenziali origini dati per una determinata attività.

In genere, questo processo di individuazione è manuale: gli analisti utilizzano le connessioni di rete peer per identificare altre persone che lavorano con i dati cercati. Una volta trovata, l'origine dati può essere utilizzata, ma il processo viene ripetuto nuovamente per ogni successiva attività di BI self-service, con più utenti che eseguono lo stesso processo manuale ridondante di individuazione.

Con il Catalogo dati di Azure, gli utenti possono interrompere questo ciclo di attività ridondanti. Una volta individuata un'origine dati tramite mezzi tradizionali, un analista può registrare l'origine dati per renderla più facilmente individuabile in futuro. Sebbene l'utente possa aggiungere più valore annotando gli asset dei dati registrati, questa operazione non è necessaria contestualmente alla registrazione. Gli utenti possono contribuire in futuro, come permesso dalle pianificazioni, aggiungendo gradualmente valore alle origini dati registrate nel catalogo.

Questa crescita strutturale del contenuto del catalogo è un complemento naturale alla registrazione iniziale delle origini dati centrali. La precompilazione del catalogo con i dati di molti utenti può essere una motivazione per l’utilizzo iniziale e l'individuazione. Consentendo agli utenti di registrare e annotare altre origini, si offre un modo per mantenere l’impegno di utenti e colleghi.

È inoltre importante notare che sebbene questo scenario sia incentrato specificamente su Business Intelligence self-service, i modelli e le richieste si applicano anche a progetti di BI aziendali su larga scala. Qualsiasi attività che prevede un processo manuale di individuazione dell’origine dati è un impegno che può risultare utile per l'organizzazione mediante l'utilizzo del Catalogo dati di Azure.

## Scenario 3: acquisizione di conoscenze specifiche

Di seguito sono riportate informazioni per sapere quali dati devono essere eseguiti dal processo e dove si trovano.

Se si ha una certa familiarità con il processo, probabilmente già si hanno le conoscenze necessarie. L’utente ha intrapreso gradualmente il processo di apprendimento e nel corso tempo ha imparato a utilizzare le origini dati che sono fondamentali quotidianamente.

Quando un nuovo dipendente viene aggiunto al team probabilmente non sa quali dati sono necessari per eseguire il lavoro e dove si trovano

e quindi pone la domanda.

Il trasferimento delle conoscenze specifiche in atto fa parte del processo di individuazione delle origini dati di piccole e grandi aziende. I membri del team più esperti hanno acquisito competenze nel corso degli anni e i membri del team più recenti hanno imparato a rivolgersi agli esperti per le domande. Spesso le informazioni più importanti sono presenti solo nelle teste di alcune persone chiave e quando tali utenti sono in vacanza o lasciano il team, l'organizzazione soffre.

Talvolta questi esperti di dati si applicano per documentare le proprie conoscenze e condividerle tramite posta elettronica o in documenti di Word in un sito di SharePoint del team. Sebbene tutto ciò possa essere utile, è necessario considerare un nuovo problema di individuazione, ossia come fanno gli utenti a sapere quale documentazione è disponibile e dove reperirla.

Il Catalogo dati di Azure fornisce un percorso per la condivisione di queste conoscenze specifiche e per renderle facilmente individuabili. Gli esperti di dati possono annotare direttamente gli asset di dati e possono includere anche i collegamenti alla documentazione esistente. In tal modo non solo si acquisisce conoscenza, ma inserisce la competenza nella stessa esperienza impiegata per l'individuazione dell’origine dati. Quando un utente utilizza il catalogo per individuare un'origine dati, non solo trova l'origine, ma ha inoltre a disposizione le informazioni di un esperto che esistevano in precedenza solo nella mente dell’esperto stesso.

<!---HONumber=August15_HO6-->