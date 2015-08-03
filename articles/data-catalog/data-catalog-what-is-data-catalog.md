<properties
   pageTitle="Il Catalogo dati di Azure è un catalogo dati."
   description="Il Catalogo di dati di Microsoft Azure è un servizio cloud completamente gestito che funge da un sistema di registrazione e sistema di individuazione per origini dati aziendali. Il Catalogo dati di Azure offre funzionalità che consentono a qualsiasi utente, analisti, ricercatori di dati per gli e sviluppatori, di registrare, individuare, comprendere e utilizzare le origini dati."
   services="data-catalog"
   documentationCenter=""
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/13/2015"
   ms.author="derrickv"/>

# Che cos'è il catalogo dei dati di Azure?

Microsoft**Catalogo dati Azure**è un servizio cloud completamente gestito che funge da un sistema di registrazione e sistema di individuazione per origini dati aziendali. **Catalogo dati azure**fornisce funzionalità che consentono a qualsiasi utente – agli analisti di ricercatori di dati per gli sviluppatori: per registrare, individuare, comprendere e utilizzare origini dati.

## Descrizione del problema - panoramica e motivazione

Tradizionalmente, individuazione di origini dati aziendali è stato un processo organico basato su conoscenze specifiche. Questo presenta numerose sfide per le aziende che desiderano ottenere il massimo dalle loro risorse di informazioni.

-	Gli utenti non sono consapevoli che origini dati esistano, a meno che essi entrare in contatto con come parte di un altro processo. non esiste alcuna posizione centrale in cui origini dati vengono registrate.
-	A meno che un utente conosca il percorso di un'origine dati, è Impossibile connettersi ai dati utilizzando un'applicazione client. esperienze di utilizzo di dati richiedono agli utenti di individuare la stringa di connessione o il percorso.
-	A meno che un utente conosca il percorso della documentazione dell'origine dati, egli non può comprendere che usi dei dati. documentazione e le origini dati live in posizioni diverse e vengono utilizzati tramite esperienze diverse.
-	Se un utente ha delle domande su una risorsa di informazioni, deve individuare l'esperto o il team responsabile dei dati e coinvolgere tali esperti fuori rete. non è disponibile alcuna connessione esplicita tra i dati e quelli con prospettive esperti sul relativo utilizzo.
 
Mentre si trovano ad affrontare queste sfide consumer di dati, gli utenti responsabili per la produzione e Gestione risorse di informazioni ad affrontare sfide di proprie.

-	L'annotazione di origini dati con metadati descrittivi è spesso un lavoro perso; le applicazioni client in genere ignorano descrizioni archiviate nell'origine dati. 
-	La creazione di documentazione per le origini dati è spesso un lavoro perso; sincronizzazione documentazione con l'origine dati è una responsabilità in corso e gli utenti non dispongono di attendibilità nella documentazione come viene spesso considerata viene aggiornato.

Creazione e la documentazione relativa a un'origine dati di gestione è lunga e complessa. La sfida di rendere disponibile tale documentazione a tutti coloro che utilizzano l'origine dati è spesso più così.

In combinazione, queste sfide presentano un ostacolo significativo per le aziende che desiderano incoraggiare e promuovere l'utilizzo e la comprensione dei dati aziendali.

## Descrizione del servizio

Il **Catalogo dati Azure**è progettato per risolvere questi problemi e per consentire alle aziende di ottenere il massimo dalle loro attività informazioni esistenti, rendendole facilmente individuabili e comprensibile agli utenti che sono necessari i dati che gestiscono.

**Catalogo dati Azure**fornisce un servizio basato su cloud in cui dati di origine può essere registrato. I dati rimangono nella posizione esistente, ma una copia dei metadati, un riferimento al percorso di origine dati, viene aggiunto al**Catalogo dati Azure**. Questi metadati sono indicizzati anche per rendere facilmente individuabili tramite ricerca ogni origine dati.

Una volta registrata un'origine dati, i metadati possono essere arricchiti, dall'utente che ha eseguito la registrazione o da altri utenti nell'organizzazione. Tutti gli utenti possono annotare un'origine dati, fornendo descrizioni, tag o altri metadati, ad esempio la documentazione. Questi metadati descrittivi integrano i metadati strutturali (ad esempio i nomi delle colonne e tipi di dati) registrato dall'origine dati, per semplificare l'individuazione e comprenderne.

Individuazione e informazioni sulle origini dati e il relativo utilizzo è lo scopo principale di registrazione delle origini. Quando gli utenti aziendali devonono utilizzare dati per i loro sforzi (che potrebbero essere business intelligence, lo sviluppo di applicazioni, scienza dei dati o qualsiasi altra attività di cui sono richiesto i dati corretti) possono utilizzare il**Catalogo dati Azure**come esperienza di individuazione per trovare rapidamente i dati che soddisfano le proprie esigenze, comprendere i dati alla relativa idoneità a scopo di valutare e utilizzare i dati, aprire l'origine dati in loro strumento ideale.

## Registrare le origini dati

La registrazione dell'origine dati viene eseguita utilizzando il**Catalogo dati Azure**strumento di registrazione di origine dati. Questa applicazione può essere scaricata dal portale **Catalogo dati Azure**.

Il processo di registrazione prevede tre passaggi di base:
 
1.	Connettersi a un'origine dati - l'utente specifica il percorso di origine dati e le credenziali per connettersi all'origine dati, ad esempio un'istanza di SQL Server.
2.	Selezionare gli oggetti da registro - l'utente seleziona gli oggetti nel percorso specificato deve essere registrato con**Catalogo dati Azure**. Potrebbe trattarsi di un set completo di tabelle in tutti i database nel server o un sottoinsieme di tabelle e viste specificamente selezionato.
3.	Completare la registrazione - l'utente completa il processo e lo strumento di registrazione di origine dati consente di estrarre i metadati strutturali dall'origine dati e invia tali metadati per il**Catalogo dati Azure**servizio cloud.
	
> [AZURE.NOTE]Per l'anteprima, il **Catalogo dati Azure**supporta attualmente i seguenti tipi di origine e di asset dati:
 
- Tabella di SQL Server
- Visualizzazione SQL Server
- Tabella di Database Oracle
- Vista di Database Oracle
- Dimensione multidimensionali di SQL Server Analysis Services
- Misura multidimensionale di SQL Server Analysis Services
- KPI multidimensionali di SQL Server Analysis Services 
- Tabella tabulare di SQL Server Analysis Services
- Report di SQL Server Reporting Services

Altre origini dati e i tipi di asset verranno aggiunti durante il**Catalogo dati Azure**anteprima.

> [AZURE.IMPORTANT]Registrazione di un'origine dati in**Catalogo dati Azure**non copia i dati dall'origine dati, a meno che non si seleziona "Anteprima includono" nello strumento di registrazione di origine dati. Registrazione copia i metadati dell'origine dati, non i dati. Esempi di metadati includono i nomi delle tabelle e altri oggetti origine dati, insieme ai tipi di dati e i nomi delle colonne e altri attributi di origine dati. I metadati includono inoltre il percorso di origine dati, in modo che gli utenti che consentono di individuare i dati di origine utilizzando**Catalogo dati Azure**potrà quindi connettersi all'origine dati. Se si seleziona "Anteprima includono", lo strumento di registrazione di origine dati anche copierà il **catalogo dei dati di Azure**un piccolo set di record che verrà visualizzato agli utenti di individuare l'origine dati nella**catalogo dei dati di Azure**portale.

## Arricchire i metadati dell'origine dati

Una volta completata la registrazione, le origini dati possono essere individuate e utilizzate, ma il valore reale del**Catalogo dati Azure**provengono da metadati descrittivi business con la stessa esperienza strutturale metadati estratti dall'origine dati. Questi metadati aggiuntivi offre due vantaggi significativi:
 
-	Le origini dei dati registrati sono più facilmente individuabili. Vengono aggiunti i metadati forniti dall'utente per il**Catalogo dati Azure**indice di ricerca. Ciò consente agli utenti di individuare i dati utilizzando i termini ed i concetti che non possono essere presenti nell'origine dati. Ad esempio, se una tabella di database che contiene i dati dei clienti è denominata "tbl_c45", fornire un nome descrittivo di "Customer" renderà più facilmente individuabili dagli utenti alla ricerca di dati dei clienti. Analogamente, fornendo una descrizione che include i nomi dei processi che utilizzano i dati, dashboard o rapporti semplificherà l'origine dati per gli utenti che utilizzano tali elementi a valle come i termini di ricerca per trovare.
-	Le origini dei dati registrati sono più facilmente comprensibile una volta individuati. I metadati forniti dall'utente viene presentato a qualsiasi**Catalogo dati Azure**utente che visualizza l'origine dati con annotazioni, che fornisce ulteriore informazioni sul contesto e. La maggior parte delle origini dati in genere non includono descrizioni significative o la documentazione e quelli che riguardano spesso i destinatari di sviluppatore tecnici DBA o database. Dall'ampliamento di origini dati in**Catalogo dati Azure**con tag e descrizioni appropriato al gruppo di destinatari, gli utenti consente di garantire che gli utenti che individua i dati possono comprendere i dettagli e l'utilizzo previsto.
  
> [AZURE.NOTE]Ogni**Catalogo dati Azure**utente può aggiungere la propria tag e le descrizioni per le colonne e le risorse di dati. **Catalogo dati azure**rileverà il valore e l'origine di ogni annotazione e verrà visualizzato l'utente e la data in cui è stato aggiunto l'annotazione. Questo approccio crowdsourcing ai metadati garantisce che tutti gli utenti con un punto di vista sui dati e il relativo utilizzo possono condividere le risorse e i pareri con la community di utenti nel suo complesso.

## Esplorare, individuare e comprendere

L'obiettivo di registrazione e l'arricchimento dei dati di origini**Catalogo dati Azure**individuata e in modo che possono essere individuati, utilizzati dagli utenti dell'organizzazione. Il portale **Catalogo dati Azure** è lo strumento principale per questo processo.

Il portale **Catalogo dati Azure**fornisce due meccanismi principali per l'individuazione ed esplorazione dei dati: ricerca e filtro.

Per eseguire la ricerca**Catalogo dati Azure**per origini dati, è sufficiente immettere un termine di ricerca nella casella di ricerca nel portale**Catalogo dati Azure**. Il portale verrà visualizzato un riquadro per ogni origine dati registrato che corrisponde al termine di ricerca; le sezioni conterrà il nome, descrizione e tag assegnati all'origine dati, insieme ad altre informazioni di alto livello.

Per filtrare il contenuto del **Catalogo dati Azure**semplicemente selezionare uno o più filtri presentati nel portale **Catalogo dati Azure**. Questo limita le sezioni verranno visualizzate nel portale solo a quelli corrispondenti ai criteri di filtro specificato. È possibile filtrare le origini dati senza eseguire ricerche oppure è possibile filtrare i risultati di una ricerca.

Per visualizzare informazioni più complete per un'origine dati e comprendere se è appropriato per l'attività in questione, semplicemente fare clic sull'icona dell'origine dati. il riquadro proprietà verrà visualizzato e conterrà tutti i relativi metadati.

Nella parte superiore del riquadro proprietà sarà pulsanti aggiuntivi:
 
1.	Anteprima: Selezionando questo pulsante verrà visualizzato un insieme statico di record dall'origine dati di anteprima se anteprima è stata selezionata durante la registrazione dell'origine dati.
2.	Schema: Selezionando questo pulsante viene visualizzato lo schema per l'origine dati, inclusi i nomi delle colonne e tipi di dati e i metadati a livello di colonna nel **Catalogo dati Azure**.

> [AZURE.NOTE]È importante ricordare che l’esperienza**Discover** può essere un punto di ingresso nell’esperienza**Enrich**verificano e non solo nell’esperienza**consumare**. L’approccio del crowdsourcing**Catalogo dati Azure**visualizzata indica che qualsiasi utente che consente di individuare un'origine dei dati registrati può condividere il suo parere sui dati, oltre a utilizzare che i dati ha scoperto.

## Rimuovere i metadati dell'origine dati

Dopo la registrazione di un'origine dati, talvolta può essere necessario rimuovere il riferimento all'origine dati da**Catalogo dati Azure**. Modifica dei requisiti aziendali o al sistema di origine verranno ritirati possono essere. Indipendentemente dal motivo, il **Catalogo dati Azure**consente di rimuovere le origini dati selezionando semplicemente per eliminare in modo che non può essere individuati e utilizzati.

> [AZURE.IMPORTANT]L'eliminazione di un'origine dati da**Catalogo dati Azure**elimina solo i metadati memorizzati nel**Catalogo dati Azure**servizio. L'origine dati non viene alterato in alcun modo.

## Utilizzare origini dati

L'obiettivo principale di individuazione dati è trovare i dati necessari e utilizzare nello strumento dati desiderato. L'esperienza di utilizzo di dati nel catalogo di dati di Azure consente questa funzionalità in due modi.
 
1.	Per le applicazioni client sono supportate direttamente dal**Catalogo dati Azure**gli utenti possono fare clic sui**Connect**menu nella sezione origine dati disponibile nel portale di. L'applicazione client verrà quindi avviata con una connessione all'origine dati selezionata.
2.	Per tutte le applicazioni client, gli utenti possono utilizzare le informazioni di connessione visualizzate nel riquadro proprietà per un'origine dati selezionata. Queste informazioni sono inclusi tutti i dettagli, quali nome del server, nome del database e nome dell'oggetto, necessari per connettersi ai dati e possono essere copiate in esperienza di connessione dello strumento client.
 
> [AZURE.NOTE]Per l'anteprima privata del catalogo dei dati di Azure, solo Microsoft Excel sarà direttamente supportato e disponibile nel menu**Connect**.

<!---HONumber=July15_HO4-->