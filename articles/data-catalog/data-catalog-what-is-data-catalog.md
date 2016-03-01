<properties
   pageTitle="Catalogo dati di Azure - Informazioni sul Catalogo dati"
   description="Panoramica del Catalogo dati di Microsoft Azure, incluse le funzionalità e i problemi per cui è stato progettato. Il Catalogo dati di Azure offre funzionalità che consentono a qualsiasi utente, analisti, ricercatori di dati per gli e sviluppatori, di registrare, individuare, comprendere e utilizzare le origini dati."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="02/08/2016"
   ms.author="maroche"/>

# Che cos'è il Catalogo dei dati di Azure?

Il **Catalogo dati di Microsoft Azure** è un servizio cloud completamente gestito che funge da sistema di registrazione e individuazione per origini dati aziendali. Il **Catalogo dati di Azure** offre funzionalità che consentono a qualsiasi utente - dagli analisti, ai ricercatori di dati e agli sviluppatori - di individuare, comprendere e utilizzare le origini dati, e di contribuire con le proprie conoscenze a compilare e supportare una community e una cultura dei dati.

## Descrizione del problema - panoramica e motivazione

Tradizionalmente, individuazione di origini dati aziendali è stato un processo organico basato su conoscenze specifiche. Questo presenta numerose sfide per le aziende che desiderano ottenere il massimo dalle loro risorse di informazioni.

-	Gli utenti non sono consapevoli che origini dati esistano, a meno che essi entrare in contatto con come parte di un altro processo. non esiste alcuna posizione centrale in cui origini dati vengono registrate.
-	A meno che un utente conosca il percorso di un'origine dati, è impossibile connettersi ai dati usando un'applicazione client. Le esperienze di utilizzo dei dati richiedono agli utenti di individuare la stringa di connessione o il percorso.
-	A meno che un utente conosca il percorso della documentazione dell'origine dati, egli non può comprendere gli usi previsti per i dati. La documentazione e le origini dati si trovano in posizioni diverse e vengono utilizzate tramite esperienze diverse.
-	Se un utente ha delle domande su un asset di informazioni, deve individuare l'esperto o il team responsabile dei dati e coinvolgere tali esperti offline. Non è disponibile alcuna connessione esplicita tra i dati e gli esperti sul relativo uso.
-  A meno che un utente sia in grado di comprendere il processo per richiedere l'accesso all'origine dati, l’individuazione dell'origine dati e della relativa documentazione non gli consente comunque di accedere ai dati necessari.

Mentre si trovano ad affrontare queste sfide consumer di dati, gli utenti responsabili per la produzione e Gestione risorse di informazioni ad affrontare sfide di proprie.

-	L'annotazione di origini dati con metadati descrittivi è spesso un lavoro perso; le applicazioni client in genere ignorano descrizioni archiviate nell'origine dati.
-	La creazione di documentazione per le origini dati è spesso un lavoro perso; sincronizzazione documentazione con l'origine dati è una responsabilità in corso e gli utenti non dispongono di attendibilità nella documentazione come viene spesso considerata viene aggiornato.
- La limitazione dell'accesso all'origine dati e la sicurezza che i fruitori di dati siano in grado di richiedere l'accesso costituisce una sfida costante.

La procedura di creazione e gestione della documentazione relativa a un'origine dati è lunga e complessa. La sfida di rendere disponibile tale documentazione a tutti coloro che usano l'origine dati spesso lo è ancora di più.

Queste sfide presentano un ostacolo significativo per le aziende che desiderano incoraggiare e promuovere l'uso e la comprensione dei dati aziendali.

## Descrizione del servizio

Il **Catalogo dati di Azure** è progettato per risolvere questi problemi e consentire alle aziende di ottenere il massimo dai propri asset di informazione esistenti, rendendoli facilmente individuabili e comprensibili agli utenti che necessitano dei dati che gestiti da tali asset.

Il **Catalogo dati di Azure** fornisce un servizio basato su cloud in cui le origini dati possono essere registrate. I dati rimangono nella posizione esistente, ma una copia dei metadati, insieme a un riferimento al percorso di origine dati, viene aggiunta al **Catalogo dati di Azure**. Questi metadati sono indicizzati anche per rendere facilmente individuabile ogni singola origine dati tramite ricerca e per renderla comprensibile agli utenti che la trovano.

Una volta registrata un'origine dati, i metadati possono essere arricchiti, dall'utente che ha eseguito la registrazione o da altri utenti nell'organizzazione. Tutti gli utenti possono annotare un'origine dati, fornendo descrizioni, tag o altri metadati, come ad esempio la documentazione e i processi per richiedere l’accesso all’origine dati. Questi metadati descrittivi integrano i metadati strutturali (ad esempio i nomi delle colonne e tipi di dati) registrato dall'origine dati, per semplificare l'individuazione e comprenderne.

Individuazione e informazioni sulle origini dati e il relativo utilizzo è lo scopo principale di registrazione delle origini. Quando gli utenti aziendali devono usare dati per le proprie attività (business intelligence, sviluppo di applicazioni, scienza dei dati o qualsiasi altra attività in cui sono richiesti dati corretti) possono usare il **Catalogo dati di Azure** per trovare rapidamente dati che soddisfino le proprie esigenze, comprenderli e valutare la loro idoneità allo scopo, e usarli aprendo l'origine dati in uno strumento di propria scelta. Allo stesso tempo, il **Catalogo dei dati di Azure** consente agli utenti di contribuire al catalogo per l'assegnazione di tag, per la documentazione, e per l'annotazione delle origini dei dati che sono già state registrate, e tramite la registrazione di nuovi dati origini che possono essere individuati, compresi e utilizzati dalla community di utenti del catalogo.

![Funzionalità del catalogo dati di Azure](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## Registrare le origini dati

La registrazione dell'origine dati viene eseguita utilizzando lo strumento di registrazione di origine dati del **Catalogo dati di Azure**. Questa applicazione può essere scaricata dal portale **Catalogo dati di Azure**.

Il processo di registrazione prevede tre passaggi di base:

1.	Connettersi a un'origine dati - l'utente specifica il percorso di origine dati e le credenziali per connettersi all'origine dati, ad esempio un'istanza di SQL Server.
2.	Selezionare gli oggetti da registrare - l'utente seleziona gli oggetti nel percorso specificato che deve essere registrato con il **Catalogo dati di Azure**. Potrebbe trattarsi di un set completo di tabelle in tutti i database nel server o un sottoinsieme di tabelle e viste specificamente selezionato.
3.	Completare la registrazione - l'utente completa il processo e lo strumento di registrazione di origine dati consente di estrarre i metadati strutturali dall'origine dati e invia tali metadati al servizio cloud del **Catalogo dati di Azure**.

> [AZURE.NOTE] L'anteprima di **Catalogo dati di Azure** supporta attualmente le origini dati e i tipi di asset seguenti: [Origini dati supportate da Catalogo dati di Azure](data-catalog-dsr.md).

Ulteriori origini dati e tipi di asset verranno aggiunti durante l’anteprima del **Catalogo dati di Azure**.

> [AZURE.IMPORTANT]La registrazione di un'origine dati nel **Catalogo dati di Azure** non copia i dati dall'origine dati, a meno che non si selezioni "Includi anteprima" nello strumento di registrazione di origine dati. Registrazione copia i metadati dell'origine dati, non i dati. Esempi di metadati includono i nomi delle tabelle e altri oggetti origine dati, insieme ai tipi di dati e i nomi delle colonne e altri attributi di origine dati. I metadati includono inoltre il percorso di origine dati, in modo che gli utenti che trovano l’origine dati con il **Catalogo dati di Azure** potranno connettersi all'origine dati. Se si seleziona "Includi anteprima", lo strumento di registrazione di origine dati copierà nel **Catalogo dati di Azure** un piccolo set di record che verrà visualizzato dagli utenti che individuano l'origine dati nel portale del **Catalogo dati di Azure**.

## Arricchire i metadati dell'origine dati

Dopo aver completato la registrazione, le origini dati possono essere individuate e usate, ma il valore reale del **Catalogo dati di Azure** deriva dal fatto che si hanno metadati descrittivi business insieme ai metadati strutturali estratti dall'origine dati. Questi metadati aggiuntivi offrono tre vantaggi significativi:

-	Le origini dei dati registrati sono più facilmente individuabili. I metadati forniti dall'utente vengono aggiunti all'indice di ricerca del **Catalogo dati di Azure**. Ciò consente agli utenti di individuare i dati utilizzando i termini ed i concetti che non possono essere presenti nell'origine dati. Ad esempio, se una tabella di database che contiene i dati dei clienti è denominata "tbl\_c45", fornire un nome descrittivo di "Customer" renderà più facilmente individuabili dagli utenti alla ricerca di dati dei clienti. Analogamente, fornendo una descrizione che include i nomi dei processi che utilizzano i dati, dashboard o rapporti semplificherà l'origine dati per gli utenti che utilizzano tali elementi a valle come i termini di ricerca per trovare.
-	Le origini dei dati registrati sono più facilmente comprensibile una volta individuati. I metadati forniti dall'utente vengono presentati a qualsiasi utente del **Catalogo dati di Azure** che visualizzi l'origine dati con annotazioni, e forniscono informazioni e contesto aggiuntivi. La maggior parte delle origini dati in genere non includono descrizioni significative o la documentazione e quelli che riguardano spesso i destinatari di sviluppatore tecnici DBA o database. Attraverso l'ampliamento delle origini dati nel **Catalogo dati di Azure** con tag e descrizioni appropriate al gruppo di destinatari, gli utenti garantiscono che chi individua i dati possa comprenderne i dettagli e l'uso previsto.
-  Ogni origine dati registrata può includere informazioni di accesso richieste, in modo che gli utenti possano facilmente comprendere e seguire processi esistenti per richiedere l'accesso all'origine dati e ai relativi dati.

> [AZURE.NOTE] Ogni utente del **Catalogo dati di Azure** può aggiungere tag e descrizioni per gli asset di dati e i relativi attributi. Il **Catalogo dati di Azure** rileverà il valore e l'origine di ogni annotazione e mostrerà l'utente che l’ha aggiunta. Questo approccio crowdsourcing ai metadati garantisce che tutti gli utenti con un punto di vista sui dati e il relativo utilizzo possono condividere le risorse e i pareri con la community di utenti nel suo complesso.

## Esplorare, individuare e comprendere

L'obiettivo della registrazione e dell'arricchimento delle origini dati nel **Catalogo dati di Azure** è quello di permettere che vengano individuati, capiti e usati dagli utenti dell'organizzazione. Il portale del **Catalogo dati di Azure** è lo strumento principale per questo processo.

Il portale del **Catalogo dati di Azure** fornisce due meccanismi principali per l'individuazione e l'esplorazione dei dati: ricerca e filtro.

Per eseguire la ricerca di origini dati nel **Catalogo dati di Azure**, è sufficiente immettere un termine di ricerca nella casella di ricerca nel portale del **Catalogo dati di Azure**. Il portale verrà visualizzato un riquadro per ogni origine dati registrato che corrisponde al termine di ricerca; le sezioni conterrà il nome, descrizione e tag assegnati all'origine dati, insieme ad altre informazioni di alto livello.

Per filtrare il contenuto del **Catalogo dati Azure**semplicemente selezionare uno o più filtri presentati nel portale **Catalogo dati Azure**. Questo limita le sezioni verranno visualizzate nel portale solo a quelli corrispondenti ai criteri di filtro specificato. È possibile filtrare le origini dati senza eseguire ricerche oppure è possibile filtrare i risultati di una ricerca.

Per visualizzare informazioni più complete per un'origine dati e comprendere se è appropriato per l'attività in questione, semplicemente fare clic sull'icona dell'origine dati. il riquadro proprietà verrà visualizzato e conterrà tutti i relativi metadati.

Nella parte superiore del riquadro proprietà sarà pulsanti aggiuntivi:

1.	Anteprima: Selezionando questo pulsante verrà visualizzato un insieme statico di record dall'origine dati di anteprima se anteprima è stata selezionata durante la registrazione dell'origine dati.
2.	Schema: selezionando questo pulsante viene visualizzato lo schema per l'origine dati, inclusi i nomi delle colonne e i tipi di dati, e i metadati a livello di colonna nel **Catalogo dati di Azure**.

> [AZURE.NOTE] È importante ricordare che l’esperienza**Discover** può essere un punto d’accesso all’esperienza**Enrich**e non solo all’esperienza**Consume**. L'approccio del crowdsourcing del **Catalogo dati di Azure** consente a qualsiasi utente che individui un'origine dati registrata di condividere il proprio parere sui dati, e non solo di usarli.

## Rimuovere i metadati dell'origine dati

Dopo la registrazione di un'origine dati, talvolta può essere necessario rimuovere il riferimento all'origine dati dal**Catalogo dati di Azure**. Modifica dei requisiti aziendali o al sistema di origine verranno ritirati possono essere. Indipendentemente dal motivo, il **Catalogo dati Azure**consente di rimuovere le origini dati selezionando semplicemente per eliminare in modo che non può essere individuati e utilizzati.

> [AZURE.IMPORTANT] L'eliminazione di un'origine dati dal **Catalogo dati di Azure** elimina solo i metadati memorizzati nel servizio **Catalogo dati di Azure**. L'origine dati non viene alterata in alcun modo.

## Utilizzare origini dati

L'obiettivo principale dell'individuazione dati è trovare i dati necessari e usarli nello strumento dati desiderato. L'esperienza di utilizzo dei dati nel Catalogo dati di Azure consente di sfruttare i vantaggi di questa funzionalità in due modi.

1.	Per le applicazioni client che sono supportate direttamente dal **Catalogo dati di Azure**, gli utenti possono fare clic sul menu **Apri in** nel riquadro dell'origine dati disponibile nel portale. L'applicazione client verrà quindi avviata con una connessione all'origine dati selezionata.
2.	Per tutte le applicazioni client, gli utenti possono utilizzare le informazioni di connessione visualizzate nel riquadro proprietà per un'origine dati selezionata. Queste informazioni sono inclusi tutti i dettagli, quali nome del server, nome del database e nome dell'oggetto, necessari per connettersi ai dati e possono essere copiate in esperienza di connessione dello strumento client. Se sono stati forniti i dettagli di accesso richiesti per un'origine dati, verranno visualizzati accanto ai dettagli della connessione.

<!---HONumber=AcomDC_0224_2016-->