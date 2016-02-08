<properties 
	pageTitle="Case study sugli sviluppatori di Ricerca di Azure: in che modo WhatToPedia ha creato un portale di informazioni multimediale in Microsoft Azure | Microsoft Azure | Servizio di ricerca cloud ospitato" 
	description="Informazioni su come creare un portale di informazioni e un meta motore di ricerca usando Ricerca di Azure, un servizio di ricerca ospitato sul cloud per sviluppatori." 
	services="search, sql-database,  storage, web-sites" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe"/>

<tags 
	ms.service="search" 
	ms.devlang="NA" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="search" 
	ms.date="11/04/2015" 
	ms.author="heidist"/>

# Case study per gli sviluppatori di Ricerca di Azure

## In che modo [WhatToPedia.com](http://whattopedia.com/) ha creato un portale di informazioni multimediale in Microsoft Azure

 ![][6] &nbsp;&nbsp;&nbsp; <font size="9">Un’idea geniale</font>


La nostra idea consiste nel creare un portale di informazioni che consenta di mettere in contatto rivenditori e acquirenti tramite un'esperienza di ricerca estremamente precisa e dettagliata, simile a quella offerta nei portali di viaggi che associano i turisti a hotel, ristoranti e intrattenimento in territori inesplorati.

Il portale che immaginiamo garantirà un'esperienza di ricerca di altissima qualità sui dati dei rivenditori in un determinato mercato, aiutando gli acquirenti a trovare i negozi in base alla posizione e ad altri servizi offerti dal rivenditore. Per il seeding del motore di ricerca verrà utilizzato un set di dati iniziale, ma nel tempo, con l'aiuto dei rivenditori abbonati che pubblicano informazioni sulla propria attività, le informazioni verranno dettagliate. Promozioni, nuova merce, marchi più diffusi, servizi specializzati in-house, sono tutti esempi di dati che aggiungono valore al nostro sito. Questi dati vengono segnalati e integrati in modo autonomo nel corpo della ricerca, una volta che il rivenditore diventa sottoscrittore. La pubblicità, unita al modello di sottoscrizione, fornisce il flusso dei profitti per la nostra nuova attività.

La ricerca sarà il modello di interazione utente predominante su una piattaforma cloud pura. Ai fini della scalabilità e della riduzione dei costi, quasi tutte le operazioni, dall'esperienza portale al controllo del codice sorgente, saranno effettuate tramite un servizio online. Mediante l’utilizzo di un motore di ricerca che fornisce le funzionalità necessarie, siamo in grado di creare rapidamente un'applicazione di ricerca, senza dover creare e gestire un motore di ricerca.

## La nostra creazione

WhatToPedia è una start-up di informazioni multimediale. Abbiamo creato [WhatToPedia.com](http://whattopedia.com/), al momento in un mercato di test in Nord Europa, con la data go-live del 2 febbraio 2015. La nostra base clienti è costituita principalmente da negozi tradizionali che necessitano di una presenza online conveniente dal punto di vista economico che sia facile da gestire e mantenere.

La nostra attività consiste nell’attrarre acquirenti tramite un'esperienza di ricerca online eccezionale, che elevi la priorità dei risultati in base alla città o alle località nelle vicinanze, ai marchi, ai nomi dei negozi o alle tipologie di negozio. Attirare gli acquirenti genera un effetto a catena, motivando i rivenditori a effettuare la sottoscrizione al sito del nostro portale. Le sottoscrizioni sono a pagamento, a un prezzo conveniente.

 ![][7]

Dopo essersi registrati per una sottoscrizione, il rivenditore prende in carico il proprio profilo esistente (creato inizialmente da noi dai dati acquistati) e lo aggiorna con altri dati sulle promozioni, i marchi disponibili o con degli annunci. È possibile segnalare in maniera autonoma le capacità in-house, le lingue parlate, le valute accettate, gli acquisti senza tasse, in modo da attirare meglio gli acquirenti alla ricerca di questi servizi.

## Chi siamo

Mi chiamo Thomas Segato (Microsoft Consulting) e ho collaborato con Jesper Boelling, Lead Developer presso WhatToPedia per progettare la soluzione.

WhatToPedia è una start-up, che esegue il test del marketing del nuovo portale aziendale in Svezia, dove la maggior parte dei 60.000 rivenditori è rappresentato da PMI (piccole e medie imprese) di tipo tradizionale. Poiché è noto che una persona che acquista in Europa parla più lingue e utilizza più valute, creiamo soluzioni adatte a un acquirente multilingua. Avevamo bisogno (e lo abbiamo trovato) di un motore di ricerca in grado di supportare i nostri requisiti multilingua in Ricerca di Azure.

Ricerca di Azure ha rappresentato un cambiamento radicale per il nostro progetto. Prima che fosse disponibile di Ricerca di Azure, abbiamo investito una notevole quantità di energia nel risolvere i problemi legati alla creazione di un nostro motore di ricerca. La possibilità di utilizzare Ricerca di Azure come servizio online ha eliminato il principale ostacolo tecnico e amministrativo dalla nostra soluzione, comportando un’immissione sul mercato più rapida e con un'esperienza di ricerca più affidabile.

## Il processo di creazione

Il nostro obiettivo era quello di creare un’infrastruttura completa basata unicamente su servizi cloud. Microsoft è stata scelta come piattaforma strategica perché era il provider che offriva i servizi necessari (sia per la collaborazione che per lo sviluppo), la scalabilità su richiesta e prezzi accessibili.
 
### Componenti di alto livello

Abbiamo creato un'azienda, non soltanto un sito. Per supportare tutto il lavoro, è stato necessario ricorrere a una vasta gamma di strumenti e applicazioni. Abbiamo adottato Visual Studio e Visual Studio Team Services per lo sviluppo, Team Foundation Service (TFS) Online per il controllo del codice sorgente e la gestione di scrum, Office 365 per la comunicazione e la collaborazione e naturalmente Microsoft Azure per tutte le operazioni relative al sito e all'archiviazione. Con Visual Studio, l’IDE ha fornito il provisioning diretto in Azure, mentre l'integrazione di TFS Online ha garantito un aumento ulteriore della produttività.

Nel diagramma seguente sono illustrati i componenti di alto livello utilizzati nell'infrastruttura di WhatToPedia.

   ![][8]

### In che modo utilizziamo Microsoft Azure

Guardando le caselle verdi del diagramma precedente, si noterà che la soluzione WhatToPedia è basata su questi servizi:

- [Ricerca di Azure](https://azure.microsoft.com/services/search/)
- [Siti Web di Azure con MVC 4](https://azure.microsoft.com/services/websites/)
- [Azure WebJobs per le attività pianificate](../websites-webjobs-resources.md)
- [Database SQL di Azure](https://azure.microsoft.com/services/sql-database/)
- [Archiviazione BLOB di Azure](https://azure.microsoft.com/services/storage/)
- [Recapito della posta elettronica SendGrid](https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/)

Il fulcro della soluzione è rappresentato dai dati e dalla ricerca. Di seguito è illustrato il flusso di dati dal provider del rivenditore al cliente finale:

  ![][9]

L’archivio dati primario è rappresentato dai dati dei rivenditori e dei clienti nel database SQL di Azure. Il database è costituito dal set di dati iniziale e dai dati specifici del rivenditore aggiunti nel tempo. Utilizziamo un processo Web di Azure WebJobs per pubblicare gli aggiornamenti dal database SQL al corpo ricerche di Ricerca di Azure.

### Livello di presentazione

Il portale è un sito Web di Azure, implementato in MVC 4 e [Twitter Bootstrap](http://en.wikipedia.org/wiki/Bootstrap_%28front-end_framework%29). Abbiamo scelto MVC in quanto offre un approccio molto più chiaro ad HTML rispetto allo sviluppo basato sui form di ASP.NET. Per evitare di creare applicazioni per più dispositivi e gestire più piattaforme mobili, è stato scelto Twitter Bootstrap, in modo da supportare tutti i dispositivi e le piattaforme.

### Autenticazione, autorizzazioni e dati sensibili

Gli acquirenti esplorano il sito in modo anonimo. Di conseguenza, non sono previsti requisiti di accesso per gli acquirenti, né archiviamo i dati dei consumatori.

Per i rivenditori, la faccenda è diversa. In questo caso, archiviamo le informazioni del profilo pubblico, le informazioni di fatturazione e i contenuti multimediali che desiderano esporre sul sito. Ogni rivenditore che effettua la sottoscrizione al sito ottiene un account di accesso utente, utilizzato per autenticare l'utente prima di apportare aggiornamenti al profilo del sottoscrittore. Tutti i dati dei sottoscrittori vengono archiviati in modo sicuro nel database SQL di Azure e nell’archivio BLOB di Azure. Abbiamo optato per un modello di autenticazione basato sull'autenticazione basata su form .NET. La scelta di questo approccio è legata alla semplicità. Non era necessario utilizzare ruoli, supporto di interfaccia utente e altre funzionalità estranee associate ad altri approcci.

Per essere certi che i rivenditori visualizzino solo i propri dati, è stato creato un ID rivenditore per ogni rivenditore, che viene utilizzato successivamente in tutte le operazioni di lettura e scrittura che includono dati specifici del rivenditore. Grazie a questo approccio, abbiamo riscontrato che non era necessario concedere autorizzazioni di database ai singoli rivenditori. Tutti i rivenditori interagiscono con il sistema in un unico ruolo di database, con l'ID del rivenditore come tecnica di isolamento dei dati.

Poiché la nostra azienda è incentrata completamente sugli effetti a valle (aumento del business per i rivenditori, creazione di incentivi per la pubblicità e le sottoscrizioni), possiamo prefiggerci di gestire gli acquisti sul Web. Pertanto, sul nostro sito non sarà presente un carrello della spesa, e questo semplifica le nostre esigenze di protezione.

Un’altra semplificazione che abbiamo impiegato è stato l'outsourcing delle operazioni di fatturazione e di contabilità dei fornitori. Indirizzando le informazioni di pagamento dei clienti direttamente a una terza parte ([SveaWebPay](http://www.sveawebpay.se/)), riduciamo i rischi associati all’archiviazione e alla protezione dei dati sensibili nei nostri archivi dati.

### Motore di ricerca

Il nucleo della nostra soluzione è il motore di ricerca basato sul servizio Ricerca di Azure. Inizialmente è stato creato un motore di ricerca personalizzato, ma durante questo processo ci siamo resi conto della complessità e l’impegno è stato davvero notevole. Tutto questo ci ha spinto a valutare altre alternative.

Di seguito sono elencate le funzionalità di base che per noi era più importante includere:

- Filtri
- Esplorazione in base a facet
- Miglioramento dei risultati
- Paging tramite AJAX

A seguito di una ricerca Internet siamo approdati al seguente video, che ci ha fornito l’ispirazione per offrire una possibilità a Ricerca di Azure: [Approfondimento di TechEd Europe](http://channel9.msdn.com/events/TechEd/Europe/2014/DBI-B410)

Dopo aver guardato il video, eravamo pronti a creare un prototipo basato su quello che avevamo visto. Poiché disponevamo già di un modello di dati in MVC, la creazione del prototipo è stata semplice, in quanto i dati contenevano termini nei quali era possibile eseguire delle ricerche e avevamo già messo a punto i requisiti in base ai quali desideravamo ordinare, diversificare e filtrare i dati.

Di seguito viene descritto come abbiamo creato il prototipo.

**Configurazione del servizio Ricerca di Azure**

1. Abbiamo effettuato l’accesso al portale di Azure classico e aggiunto il servizio di ricerca alla sottoscrizione. Abbiamo utilizzato la versione condivisa (gratuita con la sottoscrizione).
2. Creazione di un indice. Per il prototipo, abbiamo utilizzato l'interfaccia utente del portale per definire i campi di ricerca e creare i profili di assegnazione dei punteggi. Il nostro profilo di assegnazione dei punteggi si basa sui dati di posizione: paese | città |indirizzo (vedere: Aggiunta di profili di assegnazione dei punteggi).
3. Copia dell’URL del servizio e della chiave API di amministrazione nei file di configurazione. Questa chiave si trova nella pagina del servizio di ricerca del portale e viene utilizzata per l'autenticazione al servizio.
	
**Sviluppo di un processo di indicizzatore di ricerca: console di Windows**

1. Lettura di tutti i rivenditori del database.
2. Chiamata all'API del servizio Ricerca di Azure per caricare i rivenditori uno alla volta (vedere: http://msdn.microsoft.com/library/azure/dn798930.aspx).
3. Impostazione di una proprietà nel database in cui il rivenditore è indicizzato per l’indicizzazione incrementale. Tale operazione è stata eseguita aggiungendo un campo relativo all’indicizzatore nel quale viene archiviato lo stato dell’indice di ciascun profilo (indicizzato oppure no). 

Vedere l'appendice per il frammento di codice che consente di compilare il processo dell'indicizzatore.

**Sviluppo di un portale Web di ricerca – MVC**

1. Chiamata del servizio di Ricerca di Azure per ottenere tutti i documenti dalla ricerca (vedere: http://msdn.microsoft.com/library/azure/dn798927.aspx)
2. Estrazione degli elementi indicati di seguito dalla risposta del servizio di ricerca (tramite json.net http://james.newtonking.com/json)
   - Risultati
   - Facet
   - Conteggio dei risultati
   - Sviluppo di un'interfaccia utente per la visualizzazione di risultati della ricerca, facet e conteggi (già disponibile).

Questo è il codice che è stato utilizzato per ottenere i risultati di Ricerca di Azure:

    string requestUrl = 
    string.Format("https://{0}.search.windows.net/indexes/profiles/docs?searchMode=all&$count=true&search={1}&facet=city,count:20&facet=category&$top=10&$skip={2}&api-version=2014-07-31-Preview{3}", Config.SearchServiceName, EscapeODataString(q), skip, filter);
      var response = client.GetAsync(requestUrl).Result; //  + Uri.EscapeDataString("hennes")
      response.EnsureSuccessStatusCode();
     dynamic json = JsonConvert.DeserializeObject(response.Content.ReadAsStringAsync().Result);

**Elevazione della priorità in base alla posizione**

Probabilmente il requisito più importante da verificare nel prototipo includeva l’aggiunta alla query di una parola chiave di ricerca della posizione. È fondamentale per il nostro portale che se un utente immette un nome di città nella ricerca di query, i rivenditori in quella determinata città si collochino più in alto rispetto ai rivenditori che hanno come parola chiave quella città nella descrizione. Per questo requisito, abbiamo utilizzato un profilo di assegnazione dei punteggi in modo da classificare il campo relativo alla città più in alto rispetto ad altri campi.

**Supporto di più lingue**

Avevamo bisogno di visualizzare risultati della ricerca corretti nelle lingue corrette e fornire un'opzione per trovare gli stessi risultati in lingue diverse. Le due facce di questo problema erano:

- Cercare le parole in più lingue
- Visualizzare i risultati della ricerca nella lingua corretta

La parte della presentazione è stata risolta mediante l'aggiunta di un documento per ogni lingua con il testo localizzato e di una proprietà con la lingua. Quando un utente immette un termine di ricerca, per filtrare la lingua scelta dall’utente vengono utilizzate le espressioni `$filter`.

Ogni documento ha una proprietà nascosta denominata "cities", basata sul tipo di raccolta. Questa proprietà archivia i nomi di città in tutte le lingue, consentendo all'utente di eseguire la ricerca in più lingue.

###Archiviazione dei dati

Tutti i dati (profilo, sottoscrizione e contabilità) vengono archiviati nel database SQL. Tutti i file multimediali vengono archiviati nell'archiviazione BLOB di Azure, inclusi immagini e video forniti dal rivenditore. L’uso dell'archiviazione BLOB separata isola gli effetti del caricamento dei file. I file non vengono mai confusi con il sito Web, pertanto non è necessario ricompilare il sito quando si aggiungono file.

Un vantaggio importante della progettazione della nostra archiviazione consiste nel fatto che più sviluppatori possono condividere un singolo archivio di sviluppo. Uno dei requisiti per il progetto WhatToPedia consisteva nell’essere in grado di creare un ambiente di sviluppo entro 15 minuti, includendo i dati, le immagini e i video del rivenditore. Grazie alla possibilità di ottenere i dati più aggiornati da TFS Online, di eseguire uno script SQL e il processo di importazione, è possibile mettere in piedi un ambiente completo in brevissimo tempo. Questa pratica migliora anche il processo di gestione temporanea.

###WebJobs

Per aggiornare i dati nell'indice viene utilizzato Azure WebJobs. La creazione di un processo indicizzatore della ricerca, ha reso molto semplice l’integrazione dell’indicizzazione nella nostra soluzione. L'unica modifica apportata al codice è stata eseguita al fine di inserire il processo indicizzatore ed è consistita nell’aggiunta di un campo `Indexed` al nostro modello di dati per indicare lo stato dell'indice. Ogni volta che viene aggiornato o aggiunto un nuovo profilo, il campo `Indexed` viene impostato su false. Lo stesso vale se il rivenditore modifica i dati del proprio profilo tramite il portale.

Il processo cerca tutte le righe in cui `Indexed` è impostato su false. Quando viene trovata la riga, il documento viene pubblicato in Ricerca di Azure, quindi il campo `Indexed` viene impostato su true. Non è stato necessario pianificare l'aggiunta piuttosto che l'aggiornamento dei dati, in quanto è il servizio Ricerca di Azure a occuparsi di questa operazione. Se si aggiunge un documento che è già presente, il servizio eseguirà automaticamente un aggiornamento.

Tutti i processi Web sono stati sviluppati come applicazioni console che possono essere caricate in siti Web di Azure come file ZIP, decompressi e quindi pianificate.

Il processo è pianificato per essere eseguito ogni 5 minuti come attività Web pianificata. Abbiamo calcolato che il servizio richiede circa 3 minuti per caricare 3.000 documenti, il che rientrava nei nostri requisiti.

> [AZURE.NOTE] Una funzionalità di indicizzatore prototipo è stata introdotta di recente in Ricerca di Azure. Tale funzionalità è stata introdotta troppo tardi perché potessimo utilizzarla nella prima versione, ma sembra risolvere lo stesso problema per il quale è stato utilizzato il processo indicizzatore, ovvero per automatizzare le operazioni di caricamento dei dati.


###Strategia di backup

Abbiamo progettato una strategia di backup a più livelli per il ripristino da una gamma di scenari, dall’errore irreversibile, fino al ripristino di una singola transazione. Le risorse per la protezione includono tre tipi di dati (sito Web, dati del sottoscrittore e file multimediali).

In primo luogo, mantenendo il codice sorgente del sito Web in TFS Online, sappiamo che se il sito diventa inattivo, è possibile ricompilarlo mediante la ripubblicazione da TFS.

I dati del sottoscrittore nel database SQL di Azure rappresentano la risorsa più sensibile. Il backup di questi dati viene eseguito utilizzando la funzionalità integrata (vedere [Backup e ripristino del database SQL di Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)). La pianificazione di backup consiste nel backup completo del database una volta alla settimana, dei backup differenziali del database una volta al giorno e dei backup del log delle transazioni ogni 5 minuti. Date le dimensioni dei dati, questa soluzione è più che sufficiente per i volumi di dati immediati e proiettati.

In terzo luogo, i file di immagini e video vengono archiviati nell'archiviazione BLOB di Azure. Stiamo ancora valutando il piano di backup finale per questi dati, considerando Cloudberry Explorer for Azure come potenziale soluzione. Per ora, viene utilizzato un processo Web di Azure WebJobs per copiare immagini e video in un'altra posizione.

##Cosa abbiamo imparato

Poiché i dati erano già in nostro possesso, è stato semplice definire il modello di prova. In poche ore, avevamo un prototipo con facet, contatori, paging, profili classificati e risultati di ricerca. I risultati della ricerca erano così precisi, che abbiamo deciso di rimuovere alcuni dei filtri presentati al cliente finale.

La sorpresa più grande per noi è stata la velocità di apprendimento di Ricerca di Azure e i risultati che abbiamo ottenuto. Abbiamo definito il modello di prova realmente in poche ore (vedere la nota seguente), sostituendo 500 righe di codice con tre righe di codice nell'applicazione front-end (più un nuovo processo Web) e ottenendo risultati migliori.

In precedenza, il nostro codice implementava paging, conteggi, e altri comportamenti standard per la ricerca. Grazie all’utilizzo di Ricerca di Azure, i risultati che vengono restituiti includono risultati della ricerca, facet, dati di paging e conteggi, tutti ciò di cui avevamo bisogno e a cui dovevamo provvedere da soli. Erano inoltre inclusi l’elevazione della priorità e l’esplorazione in base ai facet integrata, che non erano presenti nella soluzione originale.

La sfida più grande durante l'implementazione è stata che si trattava di una versione di anteprima ed era difficile trovare informazioni ed esperienze condivise. Dopo aver acquisito una visione d’insieme, abbiamo scoperto che utilizzare il servizio Ricerca di Azure era molto semplice grazie all’API REST e al formato di dati JSON. Potevamo chiamare il framework direttamente dalla maggior parte dei plugin open source, quali JQuery JSON.Net e potevamo utilizzare strumenti come Fiddler per la sperimentazione e il debug rapidi.

> [AZURE.NOTE] Oltre a disporre dei dati preparati, è stato utile che i membri del team che compilavano il prototipo conoscessero già di funzionamento della tecnologia di ricerca, rendendoci più produttivi e maggiormente in grado di apprezzare le funzionalità integrate. Se è necessario partire da zero sulla costruzione della query di ricerca, sull’esplorazione in base a facet, sui filtri e così via, è necessario aspettarsi che la realizzazione del prototipo richieda tempi più lunghi.

###Facet di controllo nella pagina di presentazione della ricerca

Una delle cose che abbiamo appreso durante il modello di prova è stata quella di pianificare con attenzione e in anticipo i facet. Dopo aver caricato una grande quantità di dati nella soluzione, abbiamo visto che il volume completo dei facet era troppo elevato per presentarlo agli utenti.

Abbiamo risolto questo problema limitando il parametro count dei facet. Il parametro count impone un limite rigido al numero di facet restituiti all'utente. Un collegamento che include una discussione sul parametro count è disponibile [qui](search-faceted-navigation.md).

###Processi Web per la pianificazione di attività

Ricerca di Azure non è stata l’unica sorpresa piacevole per noi. Abbiamo scoperto che l’utilizzo dei processi Web per automatizzare le operazioni di caricamento dei dati in Ricerca di Azure era notevolmente superiore al nostro approccio precedente, che implicava l’uso di una macchina virtuale dedicata con Utilità di pianificazione di Windows, con le attività pianificate per l'aggiornamento dell'indice di ricerca. Configurare WebJobs ed eseguirne il debug è stato più semplice e ovviamente molto più economico rispetto ai costi da sostenere per una macchina virtuale dedicata.

###Esplora archivi BLOB di Azure per l'aggiornamento delle immagini

Abbiamo trovato molto utile l’utilizzo di [Esplora archivi BLOB di Azure](https://azurestorageexplorer.codeplex.com/) (disponibile su CodePlex) nella gestione degli aggiornamenti di immagini e video per il sito. Questa funzionalità viene utilizzata come strumento di sviluppo per aggiornare manualmente immagini e video che fanno parte del nostro sito principale. Lo abbiamo trovato più flessibile rispetto alla distribuzione delle modifiche al portale. Inoltre, consente di eliminare un'iterazione di test completa ogni volta che è necessario aggiornare un'immagine.

##Conclusione

Grazie al popolo eccezionale di WhatToPedia che ci ha consentito di condividere la sua storia.

Ci auguriamo che questo case study risulti utile. Se si continua a utilizzare Ricerca di Azure, di seguito sono consigliate alcune risorse per accelerare il processo:

- [Forum MSDN dedicato a Ricerca di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)
- [Anche StackOverflow dispone di un tag](http://stackoverflow.com/questions/tagged/azure-search)
- [Pagina della documentazione su Azure.com](https://azure.microsoft.com/documentation/services/search/)
- [Documentazione relativa a Ricerca di Azure Search su MSDN](http://msdn.microsoft.com/library/azure/dn798933.aspx)


##Appendice: Processo Web indicizzatore di ricerca

Il codice seguente compila l'indicizzatore menzionato nella sezione sulla compilazione del prototipo.

        static void Main(string[] args)
        {
            int success = 0;
            int errors = 0;

            Log.Write("Starting job","", System.Diagnostics.TraceLevel.Info);

            var serviceName = ConfigurationManager.AppSettings["SearchServiceName"];
            var serviceKey = ConfigurationManager.AppSettings["SearchServiceKey"];

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("api-key", serviceKey);

            var db = new DB(Config.ConectionString);

            var recreateIndex = false;
            Boolean.TryParse(ConfigurationManager.AppSettings["RecreateIndex"], out recreateIndex);

            if(recreateIndex)
            {
                Log.Write("Recreating index and set all to no index", "", System.Diagnostics.TraceLevel.Info);
                db.SetAllToNotIndexed();
                RecreateIndex(serviceName, client);
            }            
            
            var profiles = db.Profiles.Where(p=>!p.Indexed).ToList();

            Log.Write(string.Format("Indexing {0} profiles",profiles.Count),"", System.Diagnostics.TraceLevel.Info);

            var cities = db.Cities.ToList();
            var categories = db.Tags.Where(p=>p.ParentId==null).ToList();            

            foreach (var profile in profiles)
            {
                Log.Write(string.Format("Indexing profile {0}", profile.Name),"",profile.ProfileId,0,System.Diagnostics.TraceLevel.Verbose);

                try
                {
                    var city = cities.Where(p => p.CityId == profile.CityId);
                    var category = categories.Where(p => p.TagId == profile.CategoryId);

                    var cityse = city.Where(p => p.Lang == "se").FirstOrDefault();
                    var cityen = city.Where(p => p.Lang == "en").FirstOrDefault();
                    var categoryse = category.Where(p => p.Lang == "se").FirstOrDefault();
                    var categoryen = category.Where(p => p.Lang == "en").FirstOrDefault();

                    var citysename = cityse == null ? "" : cityse.Name;
                    var cityenname = cityen == null ? "" : cityen.Name;
                    var categorysename = categoryse == null ? "" : categoryse.Name;
                    var categoryenname = categoryen == null ? "" : categoryen.Name;

                    var tags = db.GetTagsFromProfile(profile.ProfileId);

                    var batch = new
                    {
                        value = new[] 
                    { 
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_en",
                            profileid = profile.ProfileId.ToString(),
                            city = cityenname,
                            category = categoryenname,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "en",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        },
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_se",
                            profileid = profile.ProfileId.ToString(),
                            city = citysename,
                            category = categorysename,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "se",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        }
                    },
                    };

                    var response = client.PostAsync("https://" + serviceName + ".search.windows.net/indexes/profiles/docs/index?api-version=2014-10-20-Preview", new StringContent(JsonConvert.SerializeObject(batch), Encoding.UTF8, "application/json")).Result;
                    response.EnsureSuccessStatusCode();

                    db.Entry(profile).State = System.Data.Entity.EntityState.Modified;
                    profile.Indexed = true;
                    db.SaveChanges();
                    success++;
                }
                catch(Exception ex)
                {
                    Log.Write("Error indexing profile", ex.Message, profile.ProfileId, 0, System.Diagnostics.TraceLevel.Verbose);
                    errors++;
                }
            }
            if(errors > 0)
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Error);
            }
            else
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Info);
            }
            
        }

        static void RecreateIndex( string ServiceName, HttpClient client)
        {
            var index = new
            {
                name = "profiles",
                fields = new[] 
                { 
                    new { name = "id",              type = "Edm.String",         key = true,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "profileid",       type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "cityid",          type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "city",            type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = true,  facetable = true, retrievable = true,  suggestions = true  },
                    new { name = "category",        type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = false, facetable = true, retrievable = true,  suggestions = false  },
                    new { name = "address",         type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "email",           type = "Edm.String",         key = false, searchable = true,  filterable = false, sortable = true, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "name",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true, suggestions = true },
                    new { name = "lang",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = false,  facetable = false,  retrievable = true, suggestions = false },
                    new { name = "brands",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descen",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descse",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "orgnumber",       type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "phone",           type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "zip",             type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "cities",          type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                   new { name = "categories",      type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                    new { name = "tags",            type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false }
                    
                }
            };

            var url = "https://" + ServiceName + ".search.windows.net/indexes/?api-version=2014-10-20-Preview";

            var deleteUrl = "https://" + ServiceName + ".search.windows.net/indexes/profiles?api-version=2014-10-20-Preview";

            try
            {
                var deleteResponseIndex = client.DeleteAsync(deleteUrl).Result;
                deleteResponseIndex.EnsureSuccessStatusCode();
            }
            catch (Exception ex)
            {

            }

            var responseIndex = client.PostAsync(url, new StringContent(JsonConvert.SerializeObject(index), Encoding.UTF8, "application/json")).Result;
            responseIndex.EnsureSuccessStatusCode();            
          


<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Subheading 4]: #subheading-4
[Subheading 5]: #subheading-5
[Next steps]: #next-steps

<!--Image references-->
[6]: ./media/search-dev-case-study-whattopedia/lightbulb.png
[7]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Search-Bageri.png
[8]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Stack.png
[9]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Archicture.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
 

<!---HONumber=AcomDC_0128_2016-->