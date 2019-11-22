---
title: 'Esercitazione per C#: Indicizzare i dati da database SQL di Azure'
titleSuffix: Azure Cognitive Search
description: Esempio di codice C# che mostra come eseguire la connessione al database SQL di Azure, estrarre i dati ricercabili e caricarli in un indice di Ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 4e8097eeb07420bee4ba30eb0fedbe5d4db2db9d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113314"
---
# <a name="c-tutorial-import-azure-sql-database-using-azure-cognitive-search-indexers"></a>Esercitazione per C#: Importare un database SQL di Azure con gli indicizzatori di Ricerca cognitiva di Azure

Informazioni su come configurare un indicizzatore per l'estrazione di dati ricercabili da un database SQL di Azure di esempio. Gli [indicizzatori](search-indexer-overview.md) sono un componente di Ricerca cognitiva di Azure che effettua la ricerca per indicizzazione di origini dati esterne, popolando un [indice di ricerca](search-what-is-an-index.md) con contenuti. L'indicizzatore del database SQL di Azure è il più usato tra gli indicizzatori disponibili. 

Una competenza specifica a livello di configurazione dell'indicizzatore risulta utile perché riduce la quantità di codice da scrivere e mantenere. Invece di preparare ed eseguire il push di un set di dati JSON conforme allo schema, è possibile associare un indicizzatore a un'origine dati, richiedere all'indicizzatore di estrarre i dati e inserirli in un indice e facoltativamente eseguire l'indicizzatore su una pianificazione ricorrente per rilevare le modifiche nell'origine sottostante.

In questa esercitazione verranno usate le [librerie client .NET di Ricerca cognitiva di Azure](https://aka.ms/search-sdk) e un'applicazione console .NET Core per eseguire le attività seguenti:

> [!div class="checklist"]
> * Aggiungere le informazioni sul servizio di ricerca alle impostazioni dell'applicazione
> * Preparare un set di dati esterno nel database SQL di Azure 
> * Esaminare l'indice e le definizioni dell'indicizzatore nel codice di esempio
> * Eseguire il codice dell'indicizzatore per importare i dati
> * Eseguire ricerche nell'indice
> * Visualizzare la configurazione dell'indicizzatore nel portale

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

In questa guida di avvio rapido vengono usati i servizi, gli strumenti e i dati seguenti. 

[Creare un servizio di ricerca cognitiva di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. È possibile usare un servizio gratuito per questa esercitazione.

[Database SQL di Azure](https://azure.microsoft.com/services/sql-database/) archivia l'origine dati esterna usata da un indicizzatore. La soluzione di esempio fornisce un file di dati SQL per creare la tabella. I passaggi per la creazione del servizio e del database sono forniti in questa esercitazione.

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), in qualsiasi edizione, può essere usato per eseguire la soluzione di esempio. Il codice di esempio e le istruzioni sono stati testati nell'edizione Community Edition gratuita.

[Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) fornisce la soluzione di esempio che si trova nel repository GitHub di esempi di Azure. Scaricare ed estrarre la soluzione. Per impostazione predefinita, le soluzioni sono di sola lettura. Fare clic con il pulsante destro del mouse sulla soluzione e deselezionare l'attributo di sola lettura in modo che sia possibile modificare i file.

> [!Note]
> Se si usa il servizio Ricerca cognitiva di Azure gratuito, è previsto un limite di tre indici, tre indicizzatori e tre origini dati. Questa esercitazione crea un elemento per ogni tipo. Assicurarsi che lo spazio nel servizio sia sufficiente per accettare le nuove risorse.

## <a name="get-a-key-and-url"></a>Ottenere una chiave e un URL

Le chiamate REST richiedono l'URL del servizio e una chiave di accesso per ogni richiesta. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se il servizio Ricerca cognitiva di Azure è stato aggiunto alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

1. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

![Ottenere un endpoint HTTP e una chiave di accesso](media/search-get-started-postman/get-url-key.png "Ottenere un endpoint HTTP e una chiave di accesso")

Per ogni richiesta inviata al servizio è necessario specificare una chiave API. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

## <a name="set-up-connections"></a>Configurare le connessioni
Le informazioni sulla connessione per i servizi necessari vengono specificate nel file **appsettings.json** nella soluzione. 

1. In Visual Studio aprire il file **DotNetHowToIndexers.sln**.

1. In Esplora soluzioni aprire **appsettings.json**, in modo che sia possibile popolare ogni impostazione.  

Le prime due voci possono essere compilate subito, usando l'URL e le chiavi amministratore per il servizio Ricerca cognitiva di Azure. Con `https://mydemo.search.windows.net` come endpoint, il nome del servizio da fornire sarà `mydemo`.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

L'ultima voce richiede un database esistente che verrà creato nel passaggio successivo.

## <a name="prepare-sample-data"></a>Preparare i dati di esempio

In questo passaggio viene creata un'origine dati esterna che può essere sottoposta a ricerca per indicizzazione da un indicizzatore. È possibile usare il portale di Azure e il file *hotels.sql* dall'esempio per creare il set di dati nel database SQL di Azure. Ricerca cognitiva di Azure utilizza set di righe bidimensionali, come quello generato da una visualizzazione o una query. Il file SQL nella soluzione di esempio crea e popola una singola tabella.

L'esercizio seguente presuppone che non sia disponibile alcun server o database e richiede la creazione di entrambi nel Passaggio 2. Se è presente una risorsa esistente, è facoltativamente possibile aggiungere ad essa la tabella relativa agli hotel, a partire dal Passaggio 4.

1. [Accedere al portale di Azure](https://portal.azure.com/). 

2. Cercare o creare un **database SQL di Azure** per creare un database, un server e un gruppo di risorse. È possibile usare le impostazioni predefinite e il piano tariffario del livello più basso. Uno dei vantaggi della creazione di un server consiste nella possibilità di specificare un nome utente e una password dell'amministratore, necessari per la creazione e il caricamento di tabelle in un passaggio successivo.

   ![Pagina Nuovo database](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Fare clic su **Crea** per distribuire il nuovo server e il nuovo database. Attendere il completamento della distribuzione di server e database.

4. Aprire la pagina del database SQL per il nuovo database, se non è già aperta. Il nome della risorsa dovrebbe essere *Database SQL* non *SQL Server*.

   ![Pagina Database SQL](./media/search-indexer-tutorial/hotels-db.png)

4. Nel riquadro di spostamento fare clic su **Editor di query (anteprima)** .

5. Fare clic su **Accedi** e immettere nome utente e password dell'amministratore del server.

6. Fare clic su **Apri query** e passare alla posizione del file *hotels.sql*. 

7. Selezionare il file e fare clic su **Apri**. Lo script dovrebbe essere simile allo screenshot seguente:

   ![Script SQL](./media/search-indexer-tutorial/sql-script.png)

8. Fare clic su **Esegui** per eseguire la query. Nel riquadro Risultati dovrebbe essere visualizzato un messaggio di esito positivo della query, per 3 righe.

9. Per restituire un set di righe da questa tabella, è possibile eseguire la query seguente come passaggio di verifica:

    ```sql
    SELECT HotelId, HotelName, Tags FROM Hotels
    ```
    La query prototipo, `SELECT * FROM Hotels`, non funziona nell'Editor di query. I dati di esempio includono coordinate geografiche nel campo Location, che non viene gestito nell'editor in questo momento. Per un elenco di altre colonne da sottoporre a query, è possibile eseguire questa istruzione: `SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')`

10. È ora disponibile un set di dati esterno. Copiare la stringa di connessione ADO.NET per il database. Nella pagina del database SQL passare a **Impostazioni** > **Stringhe di connessione** e copiare la stringa di connessione ADO.NET.
 
    Una stringa di connessione ADO.NET ha un aspetto simile all'esempio seguente, modificato per usare un nome database, un nome utente e una password validi.

    ```sql
    Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```
11. Incollare la stringa di connessione in "AzureSqlConnectionString" come terza voce nel file **appsettings.json** in Visual Studio.

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-the-code"></a>Informazioni sul codice

Dopo aver definito dati e impostazioni di configurazione, il programma di esempio in **DotNetHowToIndexers.sln** è pronto per la compilazione e l'esecuzione. Prima di eseguire queste operazioni, esaminare l'indice e le definizioni dell'indicizzatore per questo esempio. Il codice rilevante è disponibile in due file:

  + **hotel.cs**, contenente lo schema che definisce l'indice
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

In questa esercitazione l'indicizzatore esegue il pull di dati da un'origine dati. In pratica, è possibile associare più indicizzatori allo stesso indice, creando un indice ricercabile consolidato da più origini dati. È possibile usare la stessa coppia indice-indicizzatore, modificando solo le origini dati, oppure un indice con diverse combinazioni di indicizzatore e origine dati, in base al tipo di flessibilità necessario.

### <a name="in-programcs"></a>In Program.cs

Il programma principale include la logica per la creazione di un client, un indice, un'origine dati e un indicizzatore. Il codice cerca ed elimina le risorse esistenti con lo stesso nome, presupponendo che sia possibile che il programma venga eseguito più volte.

L'oggetto origine dati è configurato con le impostazioni specifiche nelle risorse del database SQL di Azure, tra cui l'[indicizzazione incrementale](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) per sfruttare le [funzionalità di rilevamento della modifica](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) predefinite di Azure SQL. Il database degli hotel demo in SQL di Azure include una colonna di "eliminazione temporanea" denominata **IsDeleted**. Quando questa colonna è impostata su true nel database, l'indicizzatore rimuove il documento corrispondente dall'indice di Ricerca cognitiva di Azure.

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



## <a name="run-the-indexer"></a>Eseguire l'indicizzatore

In questo passaggio viene compilato ed eseguito il programma. 

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su **DotNetHowToIndexers**, quindi scegliere **Compila**.
2. Fare di nuovo clic con il pulsante destro del mouse su **DotNetHowToIndexers**, quindi scegliere **Debug** > **Avvia nuova istanza**.

Il programma viene eseguito in modalità di debug. Una finestra della console indica lo stato di ogni operazione.

  ![Script SQL](./media/search-indexer-tutorial/console-output.png)

Il codice viene eseguito localmente in Visual Studio, connettendosi al servizio di ricerca in Azure, che a sua volta usa la stringa di connessione per connettersi al database SQL di Azure e recuperare il set di dati. Un numero così elevato di operazione comporta molti punti di errore potenziali, ma in caso di errore è consigliabile verificare prima di tutto le condizioni seguenti:

+ Le informazioni sulla connessione al servizio di ricerca fornite sono limitate al nome del servizio on questa esercitazione. Se è stato immesso l'URL completo, le operazioni si arrestano alla creazione dell'indice, con un errore relativo all'impossibilità di connettersi.

+ Informazioni sulla connessione al database in **appsettings.json**. Dovrebbe trattarsi della stringa di connessione ADO.NET ottenuta dal portale, modificata in modo da includere un nome utente e una password validi per il database. L'account utente deve avere l'autorizzazione necessaria per recuperare i dati.

+ Limiti delle risorse. Tenere presente che il livello gratuito prevede limiti di 3 indici, indicizzatori e origini dati. Un servizio che ha raggiunto il limite massimo non può creare nuovi oggetti.

## <a name="search-the-index"></a>Eseguire ricerche nell'indice 

Nella pagina Panoramica del servizio di ricerca nel portale di Azure fare clic su **Esplora ricerche** in alto per inviare alcune query sul nuovo indice.

1. Fare clic su **Cambia indice** in alto per selezionare l'indice *hotels*.

2. Fare clic sul pulsante **Ricerca** per eseguire una ricerca vuota. 

   Le tre voci nell'indice vengono restituite come documenti JSON. Esplora ricerche restituisce documenti in JSON, per consentire di visualizzare l'intera struttura.

3. Immettere quindi una stringa di ricerca: `search=river&$count=true`. 

   Questa query richiama una ricerca full-text sul termine `river` e il risultato include un conteggio dei documenti corrispondenti. La restituzione del numero di documenti corrispondenti risulta utile negli scenari di test quando è presente un indice di grandi dimensioni con migliaia o milioni di documenti. In questo caso, solo un documento corrisponde alla query.

4. Immettere infine una stringa di ricerca che limita l'output JSON ai campi rilevanti: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   La risposta della query viene ridotta ai campi selezionati e si otterrà quindi un output più conciso.

## <a name="view-indexer-configuration"></a>Visualizzare la configurazione dell'indicizzatore

Tutti gli indicizzatori, incluso quello appena creato a livello di codice, vengono elencati nel portale. È possibile aprire una definizione dell'indicizzatore e visualizzare la rispettiva origine dati oppure configurare una pianificazione di aggiornamento per rilevare le righe nuove e modificate.

1. [Accedere al portale di Azure](https://portal.azure.com/) e, nella pagina **Panoramica** del servizio di ricerca, fare clic sui collegamenti per **Indici**, **Indicizzatori** e **Origini dati**.
3. Selezionare i singoli oggetti per visualizzare o modificare le impostazioni di configurazione.

   ![Riquadri dell'indicizzatore e dell'origine dati](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Il modo più veloce per pulire le risorse dopo un'esercitazione consiste nell'eliminare il gruppo di risorse contenente il servizio Ricerca cognitiva di Azure. È possibile eliminare ora il gruppo di risorse per eliminare definitivamente tutti gli elementi in esso contenuti. Nel portale il nome del gruppo di risorse è indicato nella pagina Panoramica del servizio Ricerca cognitiva di Azure.

## <a name="next-steps"></a>Passaggi successivi

È possibile collegare gli algoritmi di arricchimento con intelligenza artificiale alla pipeline di un indicizzatore. Come passaggio successivo, continuare con l'esercitazione seguente.

> [!div class="nextstepaction"]
> [Indicizzazione di documenti in Archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md)