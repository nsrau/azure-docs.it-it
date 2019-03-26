---
title: Esercitazione per l'indicizzazione dei database SQL di Azure nel portale di Azure - Ricerca di Azure
description: In questa esercitazione si eseguirà la connessione al database SQL di Azure, si estrarranno i dati ricercabili e si caricheranno in un indice di Ricerca di Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 4e94f4c1b5de47e36dd9a5be6b9e7f43d264de82
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201399"
---
# <a name="tutorial-crawl-an-azure-sql-database-using-azure-search-indexers"></a>Esercitazione: effettuare una ricerca per indicizzazione in un database SQL di Azure con gli indicizzatori di Ricerca di Azure

Informazioni su come configurare un indicizzatore per l'estrazione di dati ricercabili da un database SQL di Azure di esempio. Gli [indicizzatori](search-indexer-overview.md) sono un componente di Ricerca di Azure che effettua la ricerca per indicizzazione di origini dati esterne, popolando un [indice di ricerca](search-what-is-an-index.md) con contenuti. L'indicizzatore del database SQL di Azure è il più usato tra gli indicizzatori disponibili. 

Una competenza specifica a livello di configurazione dell'indicizzatore risulta utile perché riduce la quantità di codice da scrivere e mantenere. Invece di preparare ed eseguire il push di un set di dati JSON conforme allo schema, è possibile associare un indicizzatore a un'origine dati, richiedere all'indicizzatore di estrarre i dati e inserirli in un indice e facoltativamente eseguire l'indicizzatore su una pianificazione ricorrente per rilevare le modifiche nell'origine sottostante.

In questa esercitazione usare le [librerie client .NET di Ricerca di Azure](https://aka.ms/search-sdk) e un'applicazione console .NET Core per eseguire le attività seguenti:

> [!div class="checklist"]
> * Aggiungere le informazioni sul servizio di ricerca alle impostazioni dell'applicazione
> * Preparare un set di dati esterno nel database SQL di Azure 
> * Esaminare l'indice e le definizioni dell'indicizzatore nel codice di esempio
> * Eseguire il codice dell'indicizzatore per importare i dati
> * Eseguire ricerche nell'indice
> * Visualizzare la configurazione dell'indicizzatore nel portale

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

[Creare un servizio Ricerca di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. È possibile usare un servizio gratuito per questa esercitazione.

* Un [database SQL di Azure](https://azure.microsoft.com/services/sql-database/) che fornisce l'origine dati esterna usata da un indicizzatore. La soluzione di esempio fornisce un file di dati SQL per creare la tabella.

* + [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), qualsiasi edizione. Il codice di esempio e le istruzioni sono state testati nell'edizione Community Edition gratuita.

> [!Note]
> Se si usa il servizio Ricerca di Azure gratuito, si è limitati a tre indici, tre indicizzatori e tre origini dati. Questa esercitazione crea un elemento per ogni tipo. Assicurarsi che lo spazio nel servizio sia sufficiente per accettare le nuove risorse.

### <a name="download-the-solution"></a>Scaricare la soluzione

La soluzione dell'indicizzatore usata in questa esercitazione proviene da una raccolta di esempi di Ricerca di Azure distribuiti in un download master. La soluzione usata per questa esercitazione è *DotNetHowToIndexers*.

1. Passare ad [**Azure-Samples/search-dotnet-getting-started**](https://github.com/Azure-Samples/search-dotnet-getting-started) nel repository GitHub di esempi di Azure.

2. Fare clic su **Clone or Download** (Clona o scarica)  > **Download ZIP** (Scarica ZIP). Per impostazione predefinita, il file viene salvato nella cartella Download.

3. In **Esplora file** > **Download** fare clic con il pulsante destro del mouse sul file e quindi scegliere **Estrai tutto**.

4. Disattivare le autorizzazioni di sola lettura. Fare clic con il pulsante destro del mouse sul nome della cartella, quindi scegliere **Proprietà** > **Generale** e deselezionare l'attributo **Sola lettura** per la cartella corrente, le sottocartelle e i file.

5. In **Visual Studio 2017** aprire la soluzione *DotNetHowToIndexers.sln*.

6. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla soluzione padre del nodo principale, quindi scegliere **Ripristina pacchetti NuGet**.

### <a name="set-up-connections"></a>Configurare le connessioni
Le informazioni sulla connessione per i servizi necessari vengono specificate nel file **appsettings.json** nella soluzione. 

In Esplora soluzioni aprire **appsettings.json**, in modo che sia possibile popolare ogni impostazione attenendosi alle istruzioni disponibili in questa esercitazione.  

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

### <a name="get-the-search-service-name-and-admin-api-key"></a>Ottenere il nome del servizio di ricerca e la chiave API dell'amministratore

È possibile trovare l'endpoint e la chiave del servizio di ricerca nel portale. Una chiave fornisce l'accesso alle operazioni del servizio. Le chiavi amministratore consentono l'accesso in scrittura, necessario per la creazione e l'eliminazione di oggetti, ad esempio indici e indicizzatori, nel servizio.

1. Accedere al [portale di Azure](https://portal.azure.com/) e trovare i [servizi di ricerca per la sottoscrizione](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

2. Aprire la pagina del servizio.

3. Trovare il nome del servizio nella parte superiore della pagina principale. Nello screenshot seguente è *azs-tutorial*.

   ![Nome del servizio](./media/search-indexer-tutorial/service-name.png)

4. Copiarlo e incollarlo come prima voce in **appsettings.json** in Visual Studio.

   > [!Note]
   > Un nome del servizio è parte dell'endpoint che include search.windows.net. Se interessati, è possibile visualizzare l'URL completo in **Informazioni di base** nella pagina Panoramica. L'URL è simile all'esempio seguente: https://your-service-name.search.windows.net

5. A sinistra in **Impostazioni** > **Chiavi** copiare una delle chiavi amministratore e incollarla come seconda voce in **appsettings.json**. Le chiavi sono stringhe alfanumeriche generate per il servizio durante il provisioning e sono necessarie per l'accesso autorizzato alle operazioni del servizio. 

   Dopo l'aggiunta di entrambe le impostazioni l'aspetto del file sarà analogo a questo esempio:

   ```json
   {
    "SearchServiceName": "azs-tutorial",
    "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
    . . .
   }
   ```

## <a name="prepare-sample-data"></a>Preparare i dati di esempio

In questo passaggio viene creata un'origine dati esterna che può essere sottoposta a ricerca per indicizzazione da un indicizzatore. Il file di dati per questa esercitazione è *hotels.sql*, disponibile nella cartella \DotNetHowToIndexers della soluzione. 

### <a name="azure-sql-database"></a>Database SQL di Azure

È possibile usare il portale di Azure e il file *hotels.sql* dall'esempio per creare il set di dati nel database SQL di Azure. Ricerca di Azure utilizza set di righe bidimensionali, come quello generato da una visualizzazione o una query. Il file SQL nella soluzione di esempio crea e popola una singola tabella.

L'esercizio seguente presuppone che non sia disponibile alcun server o database e richiede la creazione di entrambi nel Passaggio 2. Se è presente una risorsa esistente, è facoltativamente possibile aggiungere ad essa la tabella relativa agli hotel, a partire dal Passaggio 4.

1. Accedere al [portale di Azure](https://portal.azure.com/). 

2. Fare clic su **Crea una risorsa** > **Database SQL** per creare un database, un server e un gruppo di risorse. È possibile usare le impostazioni predefinite e il piano tariffario del livello più basso. Uno dei vantaggi della creazione di un server consiste nella possibilità di specificare un nome utente e una password dell'amministratore, necessari per la creazione e il caricamento di tabelle in un passaggio successivo.

   ![Pagina Nuovo database](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Fare clic su **Crea** per distribuire il nuovo server e il nuovo database. Attendere il completamento della distribuzione di server e database.

4. Aprire la pagina del database SQL per il nuovo database, se non è già aperta. Il nome della risorsa dovrebbe essere *Database SQL* non *SQL Server*.

   ![Pagina Database SQL](./media/search-indexer-tutorial/hotels-db.png)

4. Sulla barra dei comandi fare clic su **Strumenti** > **Editor di query**.

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
      "SearchServiceName": "azs-tutorial",
      "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-index-and-indexer-code"></a>Informazioni sull'indice e sul codice dell'indicizzatore

Il codice è ora pronto per la compilazione e l'esecuzione. Prima di eseguire queste operazioni, esaminare l'indice e le definizioni dell'indicizzatore per questo esempio. Il codice rilevante è disponibile in due file:

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

In questa esercitazione l'indicizzatore esegue il pull di dati da un'origine dati. In pratica, è possibile associare più indicizzatori allo stesso indice, creando un indice ricercabile consolidato da più origini dati e indicizzatori. È possibile usare la stessa coppia indice-indicizzatore, modificando solo le origini dati, oppure un indice con diverse combinazioni di indicizzatore e origine dati, in base al tipo di flessibilità necessario.

### <a name="in-programcs"></a>In Program.cs

Il programma principale include funzioni per tutte e tre le origini dati rappresentative. Se ci si concentra solo sul database SQL di Azure, vengono evidenziati gli oggetti seguenti:

  ```csharp
  private const string IndexName = "hotels";
  private const string AzureSqlHighWaterMarkColumnName = "RowVersion";
  private const string AzureSqlDataSourceName = "azure-sql";
  private const string AzureSqlIndexerName = "azure-sql-indexer";
  ```

In Ricerca di Azure gli oggetti che possono essere visualizzati, configurati o eliminati in modo indipendente includono indici, indicizzatori e origini dati (*hotels*, *azure-sql-indexer*, *azure-sql*, rispettivamente). 

La colonna *AzureSqlHighWaterMarkColumnName* merita un'attenzione specifica, perché fornisce informazioni sul rilevamento delle modifiche, usate dall'indicizzatore per determinare se una riga è stata modificata dopo il carico di lavoro di indicizzazione più recente. I [criteri di rilevamento delle modifiche](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) sono supportati solo negli indicizzatori e dipendono dall'origine dati. Per il database SQL di Azure è possibile scegliere tra due criteri, in base ai requisiti del database.

Il codice seguente mostra i metodi nel file Program.cs usato per la creazione di un'origine dati e un indicizzatore. Il codice cerca ed elimina le risorse esistenti con lo stesso nome, presupponendo che sia possibile che il programma venga eseguito più volte.

  ```csharp
  private static string SetupAzureSqlIndexer(SearchServiceClient serviceClient, IConfigurationRoot configuration)
  {
    Console.WriteLine("Deleting Azure SQL data source if it exists...");
    DeleteDataSourceIfExists(serviceClient, AzureSqlDataSourceName);

    Console.WriteLine("Creating Azure SQL data source...");
    DataSource azureSqlDataSource = CreateAzureSqlDataSource(serviceClient, configuration);

    Console.WriteLine("Deleting Azure SQL indexer if it exists...");
    DeleteIndexerIfExists(serviceClient, AzureSqlIndexerName);

    Console.WriteLine("Creating Azure SQL indexer...");
    Indexer azureSqlIndexer = CreateIndexer(serviceClient, AzureSqlDataSourceName, AzureSqlIndexerName);

    return azureSqlIndexer.Name;
  }
  ```

Si noti che le chiamate API dell'indicizzatore sono indipendenti dalla piattaforma, ad eccezione di [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet), che specifica il tipo di crawler da richiamare.

## <a name="run-the-indexer"></a>Eseguire l'indicizzatore

In questo passaggio viene compilato ed eseguito il programma. 

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su **DotNetHowToIndexers**, quindi scegliere **Compila**.
2. Fare di nuovo clic con il pulsante destro del mouse su **DotNetHowToIndexers**, quindi scegliere **Debug** > **Avvia nuova istanza**.

Il programma viene eseguito in modalità di debug. Una finestra della console indica lo stato di ogni operazione.

  ![Script SQL](./media/search-indexer-tutorial/console-output.png)

Il codice viene eseguito localmente in Visual Studio, connettendosi al servizio di ricerca in Azure, che a sua volta usa la stringa di connessione per connettersi al database SQL di Azure e recuperare il set di dati. Un numero così elevato di operazione comporta molti punti di errore potenziali, ma in caso di errore è consigliabile verificare prima di tutto le condizioni seguenti:

+ Le informazioni sulla connessione al servizio di ricerca fornite sono limitate al nome del servizio on questa esercitazione. Se è stato immesso l'URL completo, le operazioni si arrestano alla creazione dell'indice, con un errore relativo all'impossibilità di connettersi.

+ Informazioni sulla connessione al database in **appsettings.json**. Dovrebbe trattarsi della stringa di connessione ADO.NET ottenuta dal portale, modificata in modo da includere un nome utente e una password validi per il database. L'account utente deve avere l'autorizzazione necessaria per recuperare i dati.

+ Limiti delle risorse. Occorre ricordare che il servizio condiviso (gratuito) prevede limiti di 3 indici, indicizzatori e origini dati. Un servizio che ha raggiunto il limite massimo non può creare nuovi oggetti.

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

1. Aprire la pagina Panoramica del servizio Ricerca di Azure.
2. Scorrere verso il basso per trovare i riquadri per **Indicizzatori** e **Origini dati**.
3. Fare clic su un riquadro per aprire un elenco di ogni risorsa. È possibile selezionare singoli indicizzatori o singole origini dati per visualizzare o modificare le impostazioni di configurazione.

   ![Riquadri dell'indicizzatore e dell'origine dati](./media/search-indexer-tutorial/tiles-portal.png)


## <a name="clean-up-resources"></a>Pulire le risorse

Il modo più veloce per pulire le risorse dopo un'esercitazione consiste nell'eliminare il gruppo di risorse contenente il servizio Ricerca di Azure. È possibile eliminare ora il gruppo di risorse per eliminare definitivamente tutti gli elementi in esso contenuti. Nel portale, il nome del gruppo di risorse è indicato nella pagina Panoramica del servizio Ricerca di Azure.

## <a name="next-steps"></a>Passaggi successivi

È possibile collegare gli algoritmi basati su intelligenza artificiale alla pipeline di un indicizzatore. Come passaggio successivo, continuare con l'esercitazione seguente.

> [!div class="nextstepaction"]
> [Indicizzazione di documenti in Archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md)