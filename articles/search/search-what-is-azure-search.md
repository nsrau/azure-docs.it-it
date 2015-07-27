<properties
	pageTitle="Che cos&#39;è la Ricerca di Azure?"
	description="Panoramica tecnica di Ricerca di Azure e riepilogo funzionalità"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="get-started-article" 
	ms.tgt_pltfrm="na"
	ms.date="07/08/2015"
	ms.author="heidist"/>

# Che cos'è la Ricerca di Azure?

Il servizio di Ricerca di Azure è un servizio cloud completamente gestito che consente agli sviluppatori di compilare applicazioni di ricerca avanzate utilizzando un SDK .NET o API REST. Include la ricerca full-text concentrata sul contenuto, su comportamenti di ricerca avanzati simili a quelli disponibili nei motori di ricerca web commerciali, ad esempio suggerimenti digitazione query in base a un termine parziale di input, evidenziazione e navigazione sfaccettata. É integrato un supporto per linguaggio naturale, utilizzando le regole linguistiche appropriate per la lingua specificata.

È possibile scalare il servizio in base alla necessità di capacità di archiviazione o maggiore di ricerca. Ad esempio i rivenditori possono aumentare capacità per soddisfare volumi aggiuntivi associati agli eventi di acquisti o promozionale festività.

La Ricerca di Azure è un servizio basato su API per gli sviluppatori e integratori di sistemi che sanno come utilizzare i servizi web e HTTP. La Ricerca di Azure semplifica l'utilizzo di gestione di un servizio di ricerca del cloud e semplifica la creazione di applicazioni per dispositivi mobili e basato sulla ricerca web.

##Funzionamento

La Ricerca di Azure è un [servizio PaaS](https://wikipedia.org/wiki/Platform_as_a_service) che delega la gestione di server e dell'infrastruttura a Microsoft, offrendo un servizio pronto per l'uso che consente di inserire i dati di ricerca e quindi accedere dall'applicazione. A seconda della configurazione del servizio, si utilizzerà il servizio gratuito condiviso con altri sottoscrittori Ricerca di Azure o il livello del prezzo Standard che offre risorse dedicate utilizzate solo dal servizio. La Ricerca standard è scalabile, con le opzioni per soddisfare le esigenze di maggiore per l'archiviazione o query carica.

La Ricerca di Azure permette di archiviare dati in un indice e può essere effettuata la ricerca le query full-text. Lo schema di questi indici può essere creato nel portale di Azure o a livello di codice utilizzando la libreria client o l'API REST. Una volta definito lo schema, è quindi possibile caricare i dati al servizio di Ricerca di Azure dove sono successivamente indicizzati.

È possibile utilizzare push o pull modelli per caricare i dati dell'indice. Il modello pull è fornito tramite gli indicizzatori che possono essere configurati su richiesta o con aggiornamenti pianificati (vedere [operazioni indicizzatore (ricerca API REST di Azure)](https://msdn.microsoft.com/library/azure/dn946891.aspx)), permettendo di acquisire facilmente dati ed i cambiamenti dei dati da un DocumentDB di Azure, Database SQL di Azure o SQL Server ospitato in una macchina virtuale di Azure VM. Il modello push viene fornito tramite il SDK o l'API REST per l'invio di documenti aggiornati a un indice. È possibile inserire dati da qualsiasi set di dati, purché in formato JSON. Vedere [Aggiungi, aggiorna o cancella documenti](https://msdn.microsoft.com/library/azure/dn798930.aspx) o [come utilizzare il SDK di .NET)](search-howto-dotnet-sdk.md) per informazioni sul caricamento dei dati.

Alcuni sviluppatori sceglieranno un indicizzatore per la sua praticità. Per gli altri sviluppatori, il modello push merita uno pena sforzo aggiuntivo. I motivi per la scelta di un modello push sono duplici. In primo luogo, è possibile evitare di caricare altri meccanismi crawler stile sui server dati. In secondo luogo, è possibile evitare la latenza implicita prevista con l'indicizzazione pianificata. Nel caso di lunedì o venerdì nero elettronico, la ricerca dovrà riflettere lo l’inventario quasi al secondo. Un modello push consente di visualizzare tale grado di precisione.

##Cosa si crea e stocca

Il flusso di lavoro tipico consiste nel compilare lo schema e i documenti in un ambiente di sviluppo locale e quindi utilizzare .NET SDK o API REST per caricare, processare e infine eseguire query sui dati. Tutti i dati indicizzati rimangono all'interno della Ricerca di Azure per migliorare le prestazioni e per garantire la coerenza tra le operazioni di ricerca.

È possibile utilizzare l’editor integrato del portale per creare lo schema di indice e i punteggi di profili, utili per la creazione di prototipi. Per gli sviluppatori che necessitano di un approccio automatizzato e ripetibile, è consigliabile creare l'indice nel codice. Prima di tutto vengono aggiunte funzionalità più recenti all'API, per cui a seconda dei requisiti dell'applicazione, un approccio programmatico potrebbe essere l'unica opzione disponibile.

Quando si genera lo schema, si definiscono i campi e i relativi attributi supportati nell'applicazione di ricerca. I campi contengono dati con cui è possibile eseguire ricerche, ad esempio nomi dei prodotti, descrizioni, commenti dei clienti, marchi, prezzi, notifich promozionali e così via. Gli attributi indicano i tipi di operazioni che possono essere eseguite. Gli esempio degli attributi più comunemente utilizzati includono un campo che supporta la ricerca full-text (ricerca = true), filtri (filtrabile = true), oi facet (facetable = true).

I Campi contengono inoltre i dati con non è possibile eseguire ricerche rilevanti per l'applicazione di ricerca, ad esempio valori internamente utilizzati nei filtri e assegnazione dei punteggi profili o magari URL al contenuto archiviato in altre piattaforme di archiviazione (per esempio, video o immagine file inseriti in archiviazione BLOB). Un esempio citato spesso di un campo solo interno è un margine di profitto o un altro valore che indica i ricavi potenziali. Ad esempio l'applicazione di ricerca deve promuovere elementi specifici che forniscono il maggiore vantaggio finanziario all’azienda Lo schema può includere gli attributi di campo che consentono questi tipi di comportamenti di ricerca.

I documenti sono i dati dettagliati restituiti dal motore di ricerca nei risultati della ricerca. Ad esempio, se l'applicazione di ricerca è un catalogo in linea, è necessario un solo documento per ogni SKU e la pagina dei risultati viene poi creata utilizzando i valori restituiti dai documenti che corrispondono ai criteri di ricerca.

##Vedere in azione

Guarda il video per ulteriori informazioni sugli scenari e le funzionalità. Visitare [Ricerca di Azure: esempi di esercitazioni e demo video](https://msdn.microsoft.com/library/azure/dn818681.aspx) per collegamenti a contenuti video.

##Funzionalità di drill-down

La Ricerca di Azure fornisce il valore in più categorie, tra cui provisioning e scala, programmabilità, accesso e controllo e le funzionalità che si decide di implementare in un'applicazione di ricerca personalizzate.

Il seguente elenco di funzionalità consente di valutare la Ricerca di Azure per esigenze dell'applicazione di ricerca. Gli annunci di nuove funzionalità sono disponibili negli aggiornamenti più recenti per la Ricerca di Azure. È inoltre possibile esaminare la pagina di richiesta funzionalità di Ricerca di Azure per controllare lo stato delle funzionalità non ancora implementato.

Domande sulle funzionalità specifiche? Provare il [forum di Ricerca di Azure su MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch). È inoltre possibile rivedere il [pagina richiesta della funzionalità Ricerca di Azure](http://feedback.azure.com/forums/263029-azure-search) per controllare lo stato delle funzionalità non ancora implementato.

###Caratteristiche di capacità e la scala e restrizioni

Il servizio può essere eseguito come Standard o con distribuzione di servizi condivisi. La Ricerca standard supporta risorse dedicate che possono essere ridimensionate in base ai carichi di lavoro. I servizi condivisi sono gratuiti e in genere sono utilizzati per testare la funzionalità dei servizi visto che non ci sono garanzie di prestazioni.

**Scalabile** in incrementi di conteggi di archiviazione e di documenti (partizioni) o nel carico di query (repliche). Per ogni replica viene eseguita una copia di un indice. La disponibilità elevata richiede 2 repliche per i carichi di lavoro di query e 3 per carichi di lavoro di lettura / scrittura (Progettazione query e indicizzazione). Per ulteriori informazioni sulla pianificazione della capacità, vedere limiti e i vincoli (Ricerca di Azure).

La Ricerca di Azure si espande automaticamente a documenti ed indici in tutte le partizioni che sono state allocate per questo servizio. Ciò significa che è impossibile procedere al peg di indici e documenti in un set di partizioni e repliche.

Partizioni e repliche sono risorse a livello di servizio, con tutti gli indici in esecuzione su tutte le repliche. Se fosse necessario indicizzare un isolamento o si dispone di requisiti per dispersione geografica dei servizi e risorse in data center diversi, è possibile creare un secondo servizio.

Vi sono limiti sull'archiviazione e il numero di indici e documenti caricati nel servizio. Il limite effettivo sarà rispettivamente: esaurimento di memoria fisica o soddisfazione del limite superiore sugli indici e conteggi dei documenti. Vedere [limiti e i vincoli (Ricerca di Azure)](https://msdn.microsoft.com/library/azure/dn798934.aspx) per informazioni dettagliate.

###Programmabilità

API REST è costituito da richieste e risposte HTTP con il contenuto in formato JSON. Esiste un'API per accedere al servizio e un'API per la gestione del servizio. Vedere [API REST dei servizi di API REST di gestione di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) e [API REST per la gestione di API REST di gestione di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn832684.aspx) per informazioni dettagliate.

.NET SDK include classi che consentono di utilizzare il servizio di Ricerca di Azure senza la necessità di lavorare direttamente con HTTP e JSON. Vedere [come utilizzare la Ricerca di Azure .NET SDK](search-howto-dotnet-sdk.md) per ulteriori informazioni.

###Accesso e controllo

La ricerca è accessibile solo tramite HTTPS.

L’autenticazione dall'applicazione host per la Ricerca di Azure avviene tramite una chiave api di amministrazione in un'intestazione HTTP. Non è disponibile alcun modello di autenticazione o autorizzazione per utente. Tuttavia, è possibile utilizzare $filter per limitare l'accesso dall'identità dell'utente. È inoltre possibile utilizzare più query api tasti che è possibile assegnare a diverse applicazioni client. Per ulteriori informazioni sulla gestione delle chiavi, vedere [gestire il servizio di ricerca in Microsoft Azure](search-manage.md). Vedere [documenti Search (ricerca API REST di Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) per informazioni dettagliate su $filter.

###Gli indici e documenti

È possibile avere più indici (vedere [limiti e i vincoli (Ricerca di Azure)](https://msdn.microsoft.com/library/azure/dn798934.aspx) per limiti in base a livelli di prezzo). Si noti che non esiste attualmente supporto per l'unione degli indici. Una richiesta di ricerca può precisare un indice.

I**Documenti** contengono campi e attributi associati. I campi includono testo ricercabile, valori utilizzati prevalentemente (o persino esclusivamente) nei filtri e assegnazione dei punteggi profili, e molto probabilmente URL o puntatori al contenuto, ad esempio immagini, in altri dati. Molte applicazioni di ricerca utilizzano più tipi di archiviazione. Immagini o video possono essere archiviati un prezzo molto più conveniente in altri supporti di archiviazione, ad esempio l'archiviazione Blob di Azure.

Gli **Indicizzatori** possono essere utilizzati per pianificare gli aggiornamenti dell'indice da dati modificati nel Database di SQL Azure, SQL Server su macchine virtuali di Azure o Azure DocumentDB. Vedere [indicizzatore operazioni (ricerca API REST di Azure)]((https://msdn.microsoft.com/library/azure/dn946891.aspx) per informazioni dettagliate.

La **Query** può essere formulata utilizzando la sintassi di OData per filtri booleani e una sintassi di query semplici per la ricerca full-text. Vedere [sintassi delle espressioni di OData per la Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798921.aspx) e [sintassi di query semplice nella Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798920.aspx) per informazioni dettagliate.

È possibile cercare documenti specifici in base al relativo ID per recuperare rapidamente elementi specifici. Ciò è utile quando un utente fa clic su un risultato di ricerca specifico e si desidera eseguire una ricerca dettagli specifici su tale documento. Vedere [documento di ricerca (ricerca API REST di Azure)](https://msdn.microsoft.com/library/azure/dn798929.aspx) per informazioni dettagliate.

##Funzionalità di applicazione di ricerca

La **Ricerca full-text** (in base all'impostazione predefinita per i campi di testo) è abilitata per tutti i campi con l'attributo è possibile eseguire ricerche. La Ricerca full-text si fonda sul prefisso di ricerca, che significa che le corrispondenze sono basate sulla prima parte di un termine di ricerca e non al centro o alla fine della parola. Vedere [Create Index (ricerca API REST di Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx) o [creare un indice nel portale di](search-create-index-portal.md) per istruzioni su come definire un indice.

I**Profili di punteggio** vengono utilizzati per creare modelli di classificazione che consentono di ottimizzare la ricerca in base agli obiettivi aziendali. Ad esempio, si potrebbe decidere che prodotti più recenti o prodotti scontati compaiano prima tra i risultati della ricerca. È inoltre possibile compilare profili di puntaggio utilizzando tag per il punteggio personalizzati in base alle preferenze di ricerca cliente che sono stati rilevati e archiviati separatamente. Per ulteriori dettagli, vedere [Aggiungere profili di punteggio a un indice di ricerca (API REST di Ricerca di Azure)](https://msdn.microsoft.com/library/azure/dn798928.aspx).

Il **Supporto linguistico** è incorporato per cinquanta linguaggi diversi, utilizzando più opzioni di stack di elaborazione del linguaggio naturale, inclusi analizzatori Lucene ben noti e analizzatori di Microsoft che utilizzano Microsoft Office e Bing (solo anteprima). Vedere [supporto linguistico (ricerca API REST di Azure)](https://msdn.microsoft.com/library/azure/dn879793.aspx) per Lucene, e [ricerca servizio REST versione API 2015-02-28-Anteprima di Azure](search-api-2015-02-28-Preview.md) per i processori di linguaggio naturale.

La **Navigazione sfaccettata** fa riferimento a una struttura ad albero di categorizzazione utilizzata per la ricerca mirata, spesso fondata su marchi, modelli, dimensioni o su tutte le categorie che sono utili i dati. La navigazione sfaccettata viene implementata tramite gli attributi nell'indice e di un campo di facet che è disponibile in una query. Vedere [navigazione sfaccettata](search-faceted-navigation.md) per informazioni dettagliate.

**Suggerimenti** di digitazione o di completamento automatico query sono supportati attraverso attributi nell’indice. Ricerca di Azure supporta corrispondenze fuzzy o per infissi (corrispondenza con qualsiasi parte del contenuto del campo) È possibile eseguire entrambe le cose; non si escludono a vicenda. Vedere [Create Index (ricerca API REST di Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx) per informazioni sull'abilitazione dei suggerimenti, e [suggerimenti (ricerca API REST di Azure)](https://msdn.microsoft.com/library/azure/dn798936.aspx) per il loro utilizzo.

I**Filtri** possono essere utilizzati per implementare la navigazione sfaccettata, utilizzata nella costruzione di query o a livello globale per vincolare le operazioni di ricerca e filtrare i criteri stabiliti nel codice. I filtri sono abilitati per i campi specifici tramite lo schema di indice e tramite il parametro di ricerca $Filter. Vedere [Create Index (ricerca API REST di Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx) e [documenti Search (ricerca API REST di Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) per informazioni dettagliate.

La funzione**Ordinamento** è attivata anche per i campi specifici tramite lo schema di indice e quindi implementata come parametro di ricerca tramite il parametro $orderBy. Ancora una volta [Creare Index (ricerca API REST di Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx) e [documenti di ricerca (ricerca API REST di Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) precisano gli specifici dettagli.

Il **Conteggio** dell'ambito di ricerca restituito per una query e la possibilità di limitare il numero di visite insieme vengono implementate tramite $top e $skip. Vedere [documenti di ricerca (ricerca API REST di Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) per informazioni dettagliate su $filter.

I **Riscontri evidenziati** sono precisati tramite il parametro di query evidenziato e consentono di visualizzare da parte degli utenti un frammento di testo, che evidenzia le parole chiave disponibili rispetto alle condizioni di ricerca inserite dall'utente. Vedere [documenti di ricerca (ricerca API REST di Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) per ulteriori informazioni sui parametri di query.

##Reporting ed analisi

L'utilizzo delle risorse viene visualizzato nel dashboard del servizio in modo che sia possibile ottenere rapidamente un'idea dell'utilizzo dell'archiviazione.

Il consumo dell'archiviazione, il numero di documento e il numero di indice sono disponibili nel portale. È inoltre possibile utilizzare l'API. Vedere [ottenere statistiche dell'indice (ricerca API REST di Azure)](https://msdn.microsoft.com/library/azure/dn798942.aspx) per informazioni dettagliate.

Non esiste alcun meccanismo incorporato per misurare la velocità effettiva della query o altre operazioni di servizio. Inoltre, non è attualmente presente alcun supporto per la registrazione o l'analisi dei risultati di ricerca (ad esempio, il recupero un elenco di termini di ricerca che non ha permesso di ottenere alcun risultato o la segnalazione dell'origine delle richieste di ricerca).

##Provare il servizio

Visitare [creare un servizio di ricerca di Azure](search-create-service-portal.md) per impostare il servizio oppure utilizzare [provare Azure App Service](http://go.microsoft.com/fwlink/p/?LinkId=618214) per una sessione di un'ora disponibile senza alcuna installazione o sottoscrizione obbligatoria.

È inoltre possibile provare queste esercitazioni:

[Come utilizzare la Ricerca di Azure in .NET](search-howto-dotnet-sdk.md) [iniziare a utilizzare Ricerca di Azure .NET](search-get-started-dotnet.md) [Ricerca di Azure: esempi di esercitazioni e demo video](https://msdn.microsoft.com/library/azure/dn818681.aspx)

<!---HONumber=July15_HO2-->