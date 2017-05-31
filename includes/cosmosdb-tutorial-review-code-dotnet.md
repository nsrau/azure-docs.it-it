Ecco una breve analisi di ciò che accade nell'app. Aprire il file DocumentDBRepository.cs. Come si noterà, queste righe di codice creano le risorse di DocumentDB. 

* DocumentClient viene inizializzato.

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);`
    ```

* Viene creato un nuovo database.

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* Viene creata una nuova raccolta.

    ```csharp
    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(DatabaseId),
        new DocumentCollection { Id = CollectionId },
        new RequestOptions { OfferThroughput = 1000 });
    ```
