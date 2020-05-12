---
title: Esercitazione per C# sull'indicizzazione di dati di Azure SQL
titleSuffix: Azure Cognitive Search
description: In questa esercitazione per C# si eseguirà la connessione al database SQL di Azure, si estrarranno i dati ricercabili e si caricheranno in un indice di Ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: cab996eb7c0bfccf31ed49294c6aa4b3e8cefc8f
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780760"
---
# <a name="tutorial-index-azure-sql-data-using-the-net-sdk"></a>Esercitazione: Indicizzare i dati di Azure SQL con .NET SDK

Configurare un [indicizzatore](search-indexer-overview.md) per estrarre i dati ricercabili dal database SQL di Azure e inviarli a un indice di ricerca in Ricerca cognitiva di Azure. 

In questa esercitazione si usa C# e [.NET SDK](https://aka.ms/search-sdk) per le attività seguenti:

> [!div class="checklist"]
> * Creare un'origine dati che si connette al database SQL di Azure
> * Creare un indicizzatore
> * Eseguire un indicizzatore per caricare i dati in un indice
> * Eseguire query su un indice come passaggio di verifica

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

+ [Database SQL di Azure](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Creare](search-create-service-portal.md) o [trovare un servizio di ricerca esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> È possibile usare il servizio gratuito per questa esercitazione. Un servizio di ricerca gratuito consente di usare solo tre indici, tre indicizzatori e tre origini dati. Questa esercitazione crea un elemento per ogni tipo. Prima di iniziare, assicurarsi che lo spazio nel servizio sia sufficiente per accettare le nuove risorse.

## <a name="download-files"></a>Scaricare i file

Il codice sorgente per questa esercitazione si trova nella cartella [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) nel repository [Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) GitHub.

## <a name="1---create-services"></a>1 - Creare i servizi

Questa esercitazione usa Ricerca cognitiva di Azure per l'indicizzazione e le query e un database SQL di Azure come origine dati esterna. Se possibile, creare entrambi i servizi nella stessa area e nello stesso gruppo di risorse per motivi di prossimità e gestibilità. In pratica, il database SQL di Azure può trovarsi in qualsiasi area.

### <a name="start-with-azure-sql-database"></a>Iniziare con il database SQL di Azure

In questo passaggio viene creata un'origine dati esterna nel database SQL di Azure che può essere sottoposta a ricerca per indicizzazione da un indicizzatore. È possibile usare il portale di Azure e il file *hotels.sql* dall'esempio scaricato per creare il set di dati nel database SQL di Azure. Ricerca cognitiva di Azure utilizza set di righe bidimensionali, come quello generato da una visualizzazione o una query. Il file SQL nella soluzione di esempio crea e popola una singola tabella.

Se è presente una risorsa di database SQL di Azure esistente, è possibile aggiungere ad essa la tabella relativa agli hotel, a partire dal passaggio 4.

1. [Accedere al portale di Azure](https://portal.azure.com/).

1. Trovare o creare un **database SQL**. È possibile usare le impostazioni predefinite e il piano tariffario del livello più basso. Uno dei vantaggi della creazione di un server consiste nella possibilità di specificare un nome utente e una password dell'amministratore, necessari per la creazione e il caricamento di tabelle in un passaggio successivo.

   ![Pagina Nuovo database](./media/search-indexer-tutorial/indexer-new-sqldb.png "Pagina Nuovo database")

1. Fare clic su **Rivedi e crea** per distribuire il nuovo server e il nuovo database. Attendere il completamento della distribuzione di server e database.

1. Nel riquadro di spostamento fare clic su **Editor di query (anteprima)** e immettere il nome utente e la password dell'amministratore del server. 

   Se l'accesso viene negato, copiare l'indirizzo IP del client dal messaggio di errore, quindi fare clic sul collegamento **Imposta firewall server** per aggiungere una regola che consenta l'accesso dal computer client, usando l'indirizzo IP del client per l'intervallo. L'applicazione della regola potrebbe richiedere alcuni minuti.

1. Nell'editor di query fare clic su **Apri query** e passare al percorso del file *hotels.sql* nel computer locale. 

1. Selezionare il file e fare clic su **Apri**. Lo script dovrebbe essere simile allo screenshot seguente:

   ![Script SQL](./media/search-indexer-tutorial/sql-script.png "Script SQL")

1. Fare clic su **Esegui** per eseguire la query. Nel riquadro Risultati dovrebbe essere visualizzato un messaggio di esito positivo della query, per 3 righe.

1. Per restituire un set di righe da questa tabella, è possibile eseguire la query seguente come passaggio di verifica:

    ```sql
    SELECT * FROM Hotels
    ```

1. Copiare la stringa di connessione ADO.NET per il database. In **Impostazioni** > **Stringhe di connessione** copiare la stringa di connessione ADO.NET simile all'esempio seguente.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

Questa stringa di connessione sarà necessaria nell'esercizio successivo per la configurazione dell'ambiente.

### <a name="azure-cognitive-search"></a>Ricerca cognitiva di Azure

Il componente successivo è Ricerca cognitiva di Azure, che è possibile [creare nel portale](search-create-service-portal.md). Per completare questa procedura dettagliata, è possibile usare il livello gratuito. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Ottenere un URL e una chiave API di amministrazione per Ricerca cognitiva di Azure

Le chiamate API richiedono l'URL del servizio e una chiave di accesso. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se il servizio Ricerca cognitiva di Azure è stato aggiunto alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

1. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

   ![Ottenere un endpoint HTTP e una chiave di accesso](media/search-get-started-postman/get-url-key.png "Ottenere un endpoint HTTP e una chiave di accesso")

## <a name="2---set-up-your-environment"></a>2 - Configurare l'ambiente

1. Avviare Visual Studio e aprire il file **DotNetHowToIndexers.sln**.

1. In Esplora soluzioni aprire il file **appsettings.json** per aggiungervi le informazioni sulla connessione.

1. Per `searchServiceName`, se l'URL completo è "https://my-demo-service.search.windows.net", il nome del servizio da specificare sarà "my-demo-service".

1. Per `AzureSqlConnectionString`, il formato della stringa è simile al seguente: `"Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"`

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "<placeholder-ADO.NET-connection-string",
    }
    ```

1. Verificare che la stringa di connessione contenga una password valida. Il nome del database e il nome utente verranno copiati automaticamente, ma la password deve essere immessa manualmente.

## <a name="3---create-the-pipeline"></a>3 - Creare la pipeline

Gli indicizzatori richiedono un oggetto origine dati e un indice. Il codice rilevante è disponibile in due file:

  + **hotel.cs**, contenente uno schema che definisce l'indice
  + **Program.cs**, contenente le funzioni per la creazione e la gestione delle strutture nel servizio

### <a name="in-hotelcs"></a>In hotel.cs

Lo schema dell'indice definisce la raccolta di campi, inclusi gli attributi che specificano le operazioni consentite, ad esempio se un campo è compatibile con la ricerca full-text, filtrabile oppure ordinabile, come mostrato nella definizione di campo seguente per HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Uno schema può includere anche altri elementi, tra cui l'assegnazione di punteggi ai profili per il boosting del punteggio di una ricerca, analizzatori personalizzati e altri costrutti. Per le finalità specifiche di questa esercitazione, tuttavia, lo schema è scarsamente definito ed è costituito solo dai campi disponibili nel set di dati di esempio.

### <a name="in-programcs"></a>In Program.cs

Il programma principale include la logica per la creazione di un client, un indice, un'origine dati e un indicizzatore. Il codice cerca ed elimina le risorse esistenti con lo stesso nome, presupponendo che sia possibile che il programma venga eseguito più volte.

L'oggetto origine dati è configurato con le impostazioni specifiche per le risorse del database SQL di Azure, tra cui l'[indicizzazione parziale o incrementale](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) per sfruttare le [funzionalità di rilevamento delle modifiche](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) predefinite di Azure SQL. Il database degli hotel demo in SQL di Azure include una colonna di "eliminazione temporanea" denominata **IsDeleted**. Quando questa colonna è impostata su true nel database, l'indicizzatore rimuove il documento corrispondente dall'indice di Ricerca cognitiva di Azure.

  ```csharp
  Console.WriteLine("Creating data source...");

  DataSource dataSource = DataSource.AzureSql(
      name: "azure-sql",
      sqlConnectionString: configuration["AzureSQLConnectionString"],
      tableOrViewName: "hotels",
      deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
          softDeleteColumnName: "IsDeleted",
          softDeleteMarkerValue: "true"));
  dataSource.DataChangeDetectionPolicy = new SqlIntegratedChangeTrackingPolicy();

  searchService.DataSources.CreateOrUpdateAsync(dataSource).Wait();
  ```

Un oggetto indicizzatore non dipende dalla piattaforma, ovvero la configurazione, la pianificazione e la chiamata sono uguali, indipendentemente dall'origine. Questo esempio di indicizzatore include una pianificazione, un'opzione di ripristino che cancella la cronologia dell'indicizzatore e chiama un metodo per creare ed eseguire immediatamente l'indicizzatore.

  ```csharp
  Console.WriteLine("Creating Azure SQL indexer...");
  Indexer indexer = new Indexer(
      name: "azure-sql-indexer",
      dataSourceName: dataSource.Name,
      targetIndexName: index.Name,
      schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
  // Indexers contain metadata about how much they have already indexed
  // If we already ran the sample, the indexer will remember that it already
  // indexed the sample data and not run again
  // To avoid this, reset the indexer if it exists
  exists = await searchService.Indexers.ExistsAsync(indexer.Name);
  if (exists)
  {
      await searchService.Indexers.ResetAsync(indexer.Name);
  }

  await searchService.Indexers.CreateOrUpdateAsync(indexer);

  // We created the indexer with a schedule, but we also
  // want to run it immediately
  Console.WriteLine("Running Azure SQL indexer...");

  try
  {
      await searchService.Indexers.RunAsync(indexer.Name);
  }
  catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
  {
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
  }
  ```

## <a name="4---build-the-solution"></a>4 - Compilare la soluzione

Premere F5 per compilare ed eseguire la soluzione. Il programma viene eseguito in modalità di debug. Una finestra della console indica lo stato di ogni operazione.

   ![Output della console](./media/search-indexer-tutorial/console-output.png "Output console")

Il codice viene eseguito localmente in Visual Studio, connettendosi al servizio di ricerca in Azure, che a sua volta si connette al database SQL di Azure e recupera il set di dati. Un numero così elevato di operazione comporta molti punti di errore potenziali. Se si verifica un errore, verificare prima di tutto le condizioni seguenti:

+ Le informazioni sulla connessione al servizio di ricerca fornite sono limitate al nome del servizio on questa esercitazione. Se è stato immesso l'URL completo, le operazioni si arrestano alla creazione dell'indice, con un errore relativo all'impossibilità di connettersi.

+ Informazioni sulla connessione al database in **appsettings.json**. Dovrebbe trattarsi della stringa di connessione ADO.NET ottenuta dal portale, modificata in modo da includere un nome utente e una password validi per il database. L'account utente deve avere l'autorizzazione necessaria per recuperare i dati. È necessario consentire l'accesso all'indirizzo IP del client locale.

+ Limiti delle risorse. Tenere presente che il livello gratuito prevede limiti di 3 indici, indicizzatori e origini dati. Un servizio che ha raggiunto il limite massimo non può creare nuovi oggetti.

## <a name="5---search"></a>5 - Eseguire ricerche

Usare il portale di Azure per verificare la creazione dell'oggetto e quindi usare **Esplora ricerche** per eseguire una query sull'indice.

1. [Accedere al di portale di Azure](https://portal.azure.com/) e nella pagina **Panoramica** del servizio di ricerca aprire ogni elenco a turno per verificare che l'oggetto sia stato creato. Gli **indici**, gli **indicizzatori** e le **origini dati** avranno rispettivamente "hotels", "azure-sql-indexer" e "azure-sql".

   ![Riquadri dell'indicizzatore e dell'origine dati](./media/search-indexer-tutorial/tiles-portal.png)

1. Selezionare l'indice hotels. Nella pagina hotels **Esplora ricerche** è la prima scheda. 

1. Fare clic su **Cerca** per eseguire una query vuota. 

   Le tre voci nell'indice vengono restituite come documenti JSON. Esplora ricerche restituisce documenti in JSON, per consentire di visualizzare l'intera struttura.

   ![Eseguire una query su un indice](./media/search-indexer-tutorial/portal-search.png "Eseguire una query su un indice")
   
1. Immettere quindi una stringa di ricerca: `search=river&$count=true`. 

   Questa query richiama una ricerca full-text sul termine `river` e il risultato include un conteggio dei documenti corrispondenti. La restituzione del numero di documenti corrispondenti risulta utile negli scenari di test quando è presente un indice di grandi dimensioni con migliaia o milioni di documenti. In questo caso, solo un documento corrisponde alla query.

1. Immettere infine una stringa di ricerca che limita l'output JSON ai campi rilevanti: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   La risposta della query viene ridotta ai campi selezionati e si otterrà quindi un output più conciso.

## <a name="reset-and-rerun"></a>Reimpostare ed eseguire di nuovo

Nelle prime fasi sperimentali di sviluppo l'approccio più pratico per le iterazioni di progettazione consiste nell'eliminare gli oggetti da Ricerca cognitiva di Azure e consentire al codice di ricompilarli. I nomi di risorsa sono univoci. L'eliminazione di un oggetto consente di ricrearlo usando lo stesso nome.

Il codice di esempio per questa esercitazione verifica la presenza di oggetti esistenti e li elimina in modo da poter eseguire nuovamente il codice.

È anche possibile usare il portale per eliminare indici, indicizzatori e origini dati.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno rimuovere le risorse che non sono più necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento Tutte le risorse o Gruppi di risorse nel riquadro di spostamento a sinistra.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver acquisito familiarità con i concetti di base dell'indicizzazione del database SQL, si esaminerà in dettaglio la configurazione dell'indicizzatore.

> [!div class="nextstepaction"]
> [Configurare un indicizzatore per il database SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)