<properties
   pageTitle="Domande frequenti sul Catalogo dati di Azure"
   description="Domande frequenti relative all'anteprima del Catalogo dati di Azure, incluse le funzionalità per la gestione, l'annotazione e l'individuazione dell'origine dati."
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
   ms.date="02/04/2016"
   ms.author="maroche"/>

# Domande frequenti sul Catalogo dati di Azure

In questo articolo vengono fornite le risposte alle domande frequenti relative al servizio **Catalogo dati di Microsoft Azure**.

## D: cos’è il **Catalogo dati di Azure**?

R: il **Catalogo dati di Microsoft Azure** è un servizio completamente gestito ospitato nel cloud di Microsoft Azure che funge da sistema di registrazione e individuazione per le origini dati aziendali. Il **Catalogo dati di Azure** offre funzionalità che consentono a qualsiasi utente, analisti, ricercatori di dati per gli e sviluppatori, di registrare, individuare, comprendere e utilizzare le origini dati.

## D: quali richieste dei clienti risolve il Catalogo dati di Azure?

Il **Catalogo dati di Azure** risolve la richiesta di individuazione dell’origine dati e dei "dati scuri" consentendo agli utenti di identificare e comprendere le origini dati aziendali.

## D: chi sono i destinatari del Catalogo dati di Azure?

Il **Catalogo dati di Azure** offre funzionalità per gli utenti tecnici, tra cui:

- Sviluppatori di dati e professionisti di analisi e business intelligence: responsabili della produzione di dati e contenuti di analisi ad uso di altri utenti
-	Amministratori dei dati: dispongono di informazioni relative ai dati, al significato dei dati, al metodo d'uso e allo scopo
- Consumer di dati: devono essere in grado di individuare, comprendere e connettersi ai dati necessari con facilità per svolgere il lavoro mediante uno strumento di propria scelta
- Team IT centrale: necessita di rendere individuabili centinaia di origini dati per gli utenti aziendali e si occupa della supervisione della modalità d'uso dei dati e degli utenti che li usano

## D: qual è la disponibilità delle aree geografiche per il Catalogo dati di Azure?

Durante l'anteprima, i servizi del **Catalogo dati di Azure** sono disponibili solo nei data center seguenti:

- Stati Uniti occidentali
- Stati Uniti Orientali
- Europa occidentale
- Europa settentrionale
- Australia orientale
- Asia sudorientale

## D: quali sono i limiti sul numero di asset di dati nel Catalogo dati di Azure?

L'edizione Free del **Catalogo dati di Azure** è limitata a 5000 asset di dati registrati.

L'edizione Standard del **Catalogo dati di Azure** supporta fino a 100.000 asset di dati registrati.

## D: quali sono i tipi di origine dati e di asset supportati?

Per l'elenco delle origini dati attualmente supportate, vedere [Origini dati supportate da Catalogo dati di Azure](data-catalog-dsr.md).


## D: come si richiede il supporto per un'altra origine dati?

Le richieste di funzionalità e altri commenti possono essere inviati nel [forum del Catalogo dati di Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## D: come si inizia a usare il Catalogo dati di Azure?

Il modo migliore per iniziare consiste nel seguire le istruzioni dell’[introduzione al catalogo dati](../data-catalog-get-started/). In questo articolo è riportata una presentazione end-to-end delle funzionalità in anteprima pubblica.

## D: come si registrano i dati?

Per registrare i dati nel **Catalogo dati di Azure**, avviare lo strumento di registrazione del **Catalogo dati di Azure** nell'area "Pubblica" del **portale del Catalogo dati** di Azure. Nell'applicazione di pubblicazione del **Catalogo dati di Azure**, accedere usando le stesse credenziali usate per accedere al portale del **Catalogo dati di Azure**, quindi selezionare l'origine dati e gli asset specifici da registrare.

## D: quali proprietà vengono estratte per gli asset di dati registrati?

Le proprietà specifiche variano in base all'origine dati, ma in genere il servizio di pubblicazione del **Catalogo dati di Azure** estrae le informazioni seguenti:

- Nome dell’asset
- Tipo di risorsa
- Descrizione dell’asset
- Nomi di colonna/attributo
- Tipi di dati di colonna/attributo
- Descrizione di colonna/attributo

> [AZURE.IMPORTANT] La registrazione delle risorse di dati con **Catalogo dati di Azure** non sposta o copia i dati nel cloud. Registrando gli asset da un'origine dati si copiano i metadati degli asset in Azure, ma i dati rimangono nel percorso esistente dell’origine dati. L'unica eccezione a questa regola si rileva quando un utente sceglie di caricare i record di anteprima o un profilo dei dati durante la registrazione degli asset. Quando è prevista un'anteprima, vengono copiati fino a 20 record da ogni asset e vengono archiviati come snapshot nel **Catalogo dati di Azure**. Quando si include un profilo dei dati, le informazioni di aggregazione (ad esempio le dimensioni di tabelle, i valori null in percentuale per ogni colonna e i valori minimi, massimi e medi per le colonne) verranno calcolate e incluse nei metadati archiviati nel catalogo.

<br/>

> [AZURE.NOTE] Per le origini dati, ad esempio SQL Server Analysis Services che dispongono di una proprietà **Description** ottimale, l'applicazione di pubblicazione del **Catalogo dati di Azure** estrae il valore della proprietà. Per i database relazionali di SQL Server, che non dispongono di una proprietà **Description** ottimale, l’applicazione di pubblicazione del **Catalogo dati di Azure** estrae il valore dalla proprietà estesa ms\_description per oggetti e colonne. Per altre informazioni, vedere [Uso di proprietà estese su oggetti di database](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx) in TechNet.

## D: quanto tempo deve trascorrere prima che gli asset appena registrati vengano visualizzati nel Catalogo dati di Azure?

Dopo aver registrato gli asset nel **Catalogo dati di Azure** è possibile che trascorrano 5-10 secondi prima che vengano visualizzati nel portale del **Catalogo dati di Azure**.

## D: come si annotano e ampliano i metadati per l'asset di dati registrato?

Il modo più semplice di fornire i metadati agli asset registrati consiste nel selezionare l’asset nel portale del **Catalogo dati di Azure** e immettere i valori dei metadati nel riquadro delle proprietà o dello schema per l'oggetto selezionato.

È inoltre possibile fornire alcuni metadati, ad esempio esperti e tag, durante il processo di registrazione. I valori forniti nel servizio di pubblicazione del **Catalogo dati di Azure** vengono applicati a tutti gli asset che sono al momento in fase di registrazione. Per visualizzare gli oggetti registrati recentemente nel portale per annotazioni aggiuntive, selezionare il pulsante **Visualizza portale** nella schermata finale dell’applicazione di pubblicazione del **Catalogo dati di Azure**.

## D: come si eliminano gli oggetti dati registrati?

È possibile eliminare un oggetto dal **Catalogo dati di Azure** selezionando l'oggetto nel portale e quindi scegliendo il pulsante **Elimina**. I metadati dell'oggetto vengono così rimossi dal **Catalogo dati di Azure**, ma l'operazione non interessa l'effettiva origine dati sottostante.

## D: cos'è un esperto?

Un esperto è una persona che ha una prospettiva informata su un oggetto dati. Un oggetto può avere più esperti. Un esperto non deve necessariamente essere il "proprietario" dell'oggetto, ma è una persona che sa come i dati possono e devono essere usati.

## D: qual è il contratto di servizio per l'anteprima?

Durante l'anteprima del **Catalogo dati di Azure**, non esiste alcun contratto di servizio esplicito.

## D: come si condividono le informazioni con il team del Catalogo dati di Azure se si riscontrano problemi?

Usare il forum del **Catalogo dati di Azure** per segnalare problemi e condividere informazioni e domande. Il forum è disponibile in http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409

##D: il Catalogo dati di Azure funziona con un'altra origine dati?
Stiamo lavorando attivamente per aggiungere più origini dati nel **Catalogo dati di Azure**. Se si desidera che un'origine dati venga supportata, scrivere un suggerimento in proposito (o esprimere il proprio sostegno se già suggerita) nel [forum del Catalogo dati di Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## D: qual è la correlazione tra il Catalogo dati di Azure e il catalogo dati di Power BI per Office 365?

È possibile pensare al **Catalogo dati di Azure** come un'evoluzione del catalogo dati. Il **Catalogo dati di Azure** offre funzionalità simili per l'individuazione e la pubblicazione delle origini dati, ma supporta scenari più ampi e non dipende da Office 365. Non appena il **Catalogo dati di Azure** diventa genericamente disponibile, i due cataloghi vengono uniti in un unico servizio.

## D: di quali autorizzazioni deve disporre un utente per registrare gli asset nel Catalogo dati di Azure?

L'utente che esegue lo strumento di registrazione **Catalogo dati di Azure** deve disporre delle autorizzazioni sull'origine dati che consentano di leggere i metadati dall'origine. Se l'utente sceglie inoltre di includere un'anteprima, deve anche disporre delle autorizzazioni che consentono di leggere i dati dagli oggetti in fase di registrazione.

## Q: il Catalogo dati di Azure sarà disponibile anche per la distribuzione locale?

Il **Catalogo dati di Azure** è un servizio cloud che usa origini dati locali e del cloud, fornendo una soluzione ibrida di individuazione dell'origine dati. Non è attualmente prevista una versione del servizio **Catalogo dati di Azure** da eseguire in locale.

##D: è possibile estrarre metadati più numerosi o migliorati dalle origini dati registrate?

Stiamo lavorando attivamente per ampliare le funzionalità del **Catalogo dati di Azure**. Se ci sono altri metadati che si desidera estrarre dall'origine dati durante la registrazione, scrivere un suggerimento (o esprimere il proprio sostegno se già suggerita) nel [forum del Catalogo dati di Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). In futuro verrà consentito a terze parti di aggiungere nuovi tipi di origine dati tramite un'API di estensibilità.

## D: come si limita la visibilità degli asset di dati registrati, in modo che solo determinati utenti possano individuarli?

R: selezionare gli asset di dati nel **Catalogo dati di Azure** e fare clic sul pulsante "Assumi proprietà". I proprietari degli asset di dati nel **Catalogo dati di Azure** possono modificare le impostazioni di visibilità per consentire a tutti gli utenti del catalogo di individuare gli asset di proprietà o per limitare la visibilità a utenti specifici.

## Q: come si aggiorna la registrazione di un asset di dati in modo che le modifiche apportate nell'origine dati vengano riflesse nel catalogo?

R: per aggiornare i metadati degli asset di dati già registrati nel catalogo, è sufficiente registrare nuovamente l'origine dati che contiene gli asset. Le modifiche nell'origine dati, ad esempio l’aggiunta o la rimozione di colonne da tabelle o viste, vengono aggiornate nel catalogo, ma tutte le annotazioni fornite dagli utenti vengono conservate.

## D: come si pongono domande o si richiede assistenza quando si usa il Catalogo dati di Azure?

Se si verificano dei problemi o per richiedere assistenza per l'anteprima del **Catalogo dati di Azure**, è possibile scrivere nel [forum del Catalogo dati di Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## D: la domanda non ha ricevuto risposta: cosa occorre fare?

Tenere sotto controllo il [forum del Catalogo dati di Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409), dove le domande frequenti trovano risposta.

<!---HONumber=AcomDC_0211_2016-->