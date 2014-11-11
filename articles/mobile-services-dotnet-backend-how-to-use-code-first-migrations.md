<properties pageTitle="How to use Code First Migrations .NET backend (Mobile Services)" metaKeywords="" description="" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Considerations for supporting multiple clients from a single mobile service" authors="glenga" solutions="mobile" writer="glenga" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Come modificare un modello di dati in un servizio mobile back-end .NET

Questo argomento illustra come usare Migrazioni Code First di Entity Framework per apportare modifiche al modello di dati in un database SQL esistente di Azure ed evitare la perdita di dati esistenti. Per questa procedura si presuppone che il progetto di servizio mobile sia già stato pubblicato in Azure, che vi siano dati esistenti nel database e che i modelli di dati remoti e locali siano ancora sincronizzati. In questo argomento vengono inoltre descritti gli inizializzatori Code First predefiniti implementati da Servizi mobili di Azure che vengono usati durante la fase di sviluppo. Questi inizializzatori consentono di apportare modifiche allo schema con estrema facilità, senza dover usare Migrazioni di Code First quando non è necessario per mantenere i dati esistenti.

## Inizializzatori del modello di dati

Servizi mobili supporta due classi base di inizializzatori del modello di dati in un progetto di servizio mobile back-end .NET. Questi inizializzatori eliminano e ricreano tabelle nel database ogni volta che Entity Framework rileva una modifica del modello di dati in [DbContext][DbContext]. Gli inizializzatori sono progettati per funzionare sia quando il servizio mobile viene eseguito in un computer locale sia quando è ospitato in Azure. Entrambe le classi base di inizializzatori eliminano dal database tutte le tabelle, le viste, le funzioni e le procedure presenti nello schema usate dal servizio mobile.

-   **ClearDatabaseSchemaIfModelChanges**
     Gli oggetti dello schema vengono eliminati solo quando Code First rileva una modifica nel modello di dati. L'inizializzatore predefinito in un progetto back-end .NET scaricato dal [portale di gestione di Azure][portale di gestione di Azure] eredita da questa classe base.

-   **ClearDatabaseSchemaAlways**
     Gli oggetti dello schema vengono eliminati ad ogni accesso del modello di dati. Usare questa classe base per reimpostare il database senza dover apportare modifiche al modello di dati.

Nel progetto di guida introduttiva scaricato l'inizializzatore Code First è definito nel file WebApiConfig.cs. Eseguire l'override del metodo **Seed** per aggiungere righe iniziali di dati alle nuove tabelle. Per alcuni esempi di seeding dei dati, vedere [Seeding di dati nelle migrazioni][Seeding di dati nelle migrazioni]. È possibile usare altri inizializzatori del modello di dati Code First quando il servizio è in esecuzione in un computer locale. Tuttavia, gli inizializzatori che provano a eliminare il database non vengono eseguiti in Azure. L'utente, infatti, molto opportunamente non dispone delle autorizzazioni necessarie per eliminare il database.

Durante la fase di sviluppo locale del servizio mobile è possibile continuare a usare gli inizializzatori e nelle esercitazioni relative al back-end .NET si presuppone che vengano usati. Tuttavia, nelle situazioni in cui si intenda apportare modifiche al modello di dati e conservare i dati esistenti nel database, è necessario utilizzare Migrazioni Code First.

> [WACOM.NOTE]Quando si sviluppa e si testa un progetto di servizio mobile in servizi di Azure attivi, è necessario utilizzare sempre un'istanza del servizio mobile dedicata al test. È sempre sconsigliabile che i servizi mobili in fase di produzione o utilizzati da app client siano oggetto di sviluppo o test.

## <a name="migrations"></a>Abilitazione di Migrazioni Code First

Migrazioni Code First utilizza un metodo snapshot per generare il codice che, se eseguito, apporta modifiche allo schema del database. Con Migrazioni, è possibile apportare modifiche incrementali al modello di dati e conservare i dati esistenti nel database.

> [WACOM.NOTE]Se il progetto di servizio mobile back-end .NET è già stato pubblicato in Azure e lo schema delle tabelle del database SQL non corrisponde al modello di dati corrente del progetto, è necessario usare un inizializzatore, eliminare le tabelle manualmente o altrimenti sincronizzare lo schema e il modello di dati prima di provare a eseguire la pubblicazione con Migrazioni Code First.

Per abilitare Migrazioni e apportare modifiche al modello di dati nel progetto, nel database locale e in Azure, attenersi alla procedura seguente.

1.  In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto di servizio mobile e scegliere **Imposta come progetto di avvio**.

2.  Espandere Gestione pacchetti **Gestione pacchetti NuGet** dal menu **Strumenti**, quindi fare clic su **Console di Gestione pacchetti**.

    Verrà visualizzata la finestra Console di Gestione pacchetti, che verrà utilizzata per gestire Migrazioni Code First.

3.  Nella finestra Console di Gestione pacchetti eseguire il comando seguente:

        PM> Enable-Migrations

    Questo comando consente di avviare Migrazioni Code First per il progetto.

4.  Nella console, eseguire il comando seguente:

        PM> Add-Migration Initial

    Questo comando crea una nuova migrazione denominata *Iniziale*. Il codice di migrazione verrà archiviato nella cartella del progetto Migrazioni.

5.  Espandere la cartella App\_Start, aprire il file WebApiConfig.cs e aggiungere l'istruzione **using** seguente:

        using System.Data.Entity.Migrations;
        using todolistService.Migrations;

    Nel codice precedente, è necessario sostituire la stringa *todolistService* con lo spazio dei nomi del progetto che, per il progetto di guida introduttiva scaricato, è Servizio *mobile\_service\_name*.

6.  Nello stesso file di codice, impostare come commento la chiamata al metodo **Database.SetInitializer** e aggiungere di seguito il codice seguente:

        var migrator = new DbMigrator(new Configuration());
        migrator.Update();

    Verrà così disabilitato l'inizializzatore predefinito di database Code First che elimina e ricrea il database, e verrà sostituito con una richiesta esplicita di applicazione dell'ultima migrazione. A questo punto, ogni modifica al modello di dati genererà un'eccezione InvalidOperationException al momento dell'accesso ai dati, a meno che non sia stata creata una migrazione. Per il futuro, per il servizio utilizzare Migrazioni Code First per la migrazione di modifiche al modello di dati al database.

7.  Premere F5 per avviare il progetto di servizio mobile sul computer locale.

    A questo punto, il database è sincronizzato con il modello di dati. Se sono stati forniti dati di seeding, è possibile effettuare una verifica facendo clic su **Try it out**, **GET tables/todoitem** e quindi su **Try this out** e **Send**. Per ulteriori informazioni, vedere [Seeding di dati nelle migrazioni][Seeding di dati nelle migrazioni].

8.  Apportare una modifica al modello di dati, ad esempio aggiungere una nuova proprietà al tipo TodoItem, ricreare il progetto e quindi eseguire il comando seguente nella Gestione pacchetti:

        PM> Add-Migration NewUserId

    Questo comando crea una nuova migrazione denominata *NewUserId*. Verrà aggiunto nella cartella Migrazioni un nuovo file del codice che implementa questa modifica.

9.  Premere nuovamente F5 per riavviare il progetto di servizio mobile sul computer locale.

    La migrazione verrà applicata al database e il database sarà nuovamente sincronizzato con il modello di dati. Se sono stati forniti dati di seeding, è possibile effettuare una verifica facendo clic su **Try it out**, **GET tables/todoitem** e quindi su **Try this out** e **Send**. Per ulteriori informazioni, vedere [Seeding di dati nelle migrazioni][Seeding di dati nelle migrazioni].

10. Ripubblicare il servizio mobile in Azure e quindi eseguire l'app client per accedere ai dati, verificare che siano caricati e che non si verifichino errori.

11. (Facoltativo) Nel [portale di gestione di Azure][portale di gestione di Azure] selezionare il servizio mobile, fare clic sulla scheda **Configura**, quindi fare clic sul collegamento **Database SQL**.

    ![][0]

    Verrà visualizzata la pagina del database SQL per il database del servizio mobile.

12. (Facoltativo) Fare clic su **Manage**, accedere al server di database SQL e quindi fare clic su **Design** e verificare che le modifiche allo schema siano state riportate in Azure.

    ![][1]

## <a name="seeding"></a>Seeding di dati nelle migrazioni.

Quando si esegue una migrazione, è possibile fare in modo che Migrazioni effettui il seeding di dati nel database. La classe **Configuration** dispone di un metodo **Seed** di cui è possibile eseguire l'override per inserire o aggiornare dati. Quando Migrazioni viene abilitato, il file di codice Configuration.cs viene aggiunto alla cartella Migrazioni. Questi esempi illustrano come eseguire l'override del metodo [Seed][Seed] per effettuare il seeding dei dati nella tabella **TodoItems**. Il metodo [Seed][Seed] viene chiamato dopo la migrazione alla versione più recente.

### Seeding di una nuova tabella

Il codice seguente esegue il seeding della tabella **TodoItems** con nuove righe di dati:

        List<TodoItem> todoItems = new List<TodoItem>
        {
            new TodoItem { Id = "1", Text = "First item", Complete = false },
            new TodoItem { Id = "2", Text = "Second item", Complete = false },
        };

        foreach (TodoItem todoItem in todoItems)
        {
            context.Set<TodoItem>().Add(todoItem);
        }
        base.Seed(context);

### Seeding di una nuova colonna in una tabella

Con il codice seguente è possibile eseguire il seeding nella sola colonna UserId:

        context.TodoItems.AddOrUpdate(
            t => t.UserId,
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 2 }
            );
        base.Seed(context);

Questo codice chiama il metodo [AddOrUpdate][AddOrUpdate] con estensione helper per aggiungere dati di seeding alla nuova colonna UserId. Con [AddOrUpdate][AddOrUpdate] non vengono create righe duplicate.

<!-- Anchors --> <!-- Images --> <!-- URLs -->

  [DbContext]: http://msdn.microsoft.com/it-it/library/system.data.entity.dbcontext(v=vs.113).aspx
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [Seeding di dati nelle migrazioni]: #seeding
  [0]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png
  [1]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png
  [Seed]: http://msdn.microsoft.com/it-it/library/hh829453(v=vs.113).aspx
  [AddOrUpdate]: http://msdn.microsoft.com/it-it/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx
