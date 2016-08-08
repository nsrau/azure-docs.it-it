<properties
	pageTitle="Gestire i conflitti relativi ai dati offline in app di Windows universali | Microsoft Azure"
	description="Informazioni su come usare Servizi mobili di Azure per gestire conflitti durante la sincronizzazione dei dati offline nell'applicazione di Windows universale"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="glenga"/>


# Gestione dei conflitti relativi alla sincronizzazione offline dei dati in Servizi mobili

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../../includes/mobile-services-selector-offline-conflicts.md)]

##Panoramica

Questo argomento illustra come sincronizzare e gestire i conflitti quando si usano le funzionalità offline di Servizi mobili di Azure.

Se si preferisce guardare un video, nel clip seguente vengono eseguiti gli stessi passaggi dell'esercitazione.

> [AZURE.VIDEO build-offline-apps-with-mobile-services]

In questa esercitazione si scaricherà una soluzione C# di Windows universale per un'app che supporta la gestione dei conflitti di sincronizzazione offline. Si integrerà un servizio mobile con l'app e quindi si eseguiranno i client Windows Store 8.1 e Windows Phone 8.1 per generare un conflitto di sincronizzazione e risolverlo.

Questa esercitazione è basata sulle procedure e sull'app di esempio dell'esercitazione precedente [Introduzione ai dati offline]. Prima di iniziare questa esercitazione è necessario completare [Introduzione ai dati offline].


##Prerequisiti

Per completare questa esercitazione, è necessario disporre di Visual Studio 2013 in esecuzione su Windows 8.1.


##Scaricare il progetto di esempio

![][0]

Questa esercitazione è una procedura dettagliata che illustra come usare l'[esempio di Servizi mobili offline Todo] per gestire i conflitti di sincronizzazione tra l'archivio offline locale e il database di Servizi mobili in Azure.

1. Scaricare il file con estensione zip per il [repository Github degli esempi di Servizi mobili] ed estrarlo in una directory di lavoro.

2. Se SQLite per Windows 8.1 e Windows Phone 8.1 non sono già installati, come indicato nell'esercitazione [Introduzione ai dati offline], installare entrambi i runtime.

3. In Visual Studio 2013 aprire il file della soluzione *mobile-services-samples\\TodoOffline\\WindowsUniversal\\TodoOffline-Universal.sln*. Premere **F5** per ricompilare ed eseguire il progetto. Verificare che i pacchetti NuGet vengano ripristinati e che i riferimenti siano impostati correttamente.

    >[AZURE.NOTE] Potrebbe essere necessario eliminare i vecchi riferimenti al runtime di SQLite e sostituirli con il riferimento aggiornato, come indicato nell'esercitazione [Introduzione ai dati offline].

4. Nell'app digitare un testo in **Insert a TodoItem** e quindi fare clic su **Save** per aggiungere alcuni elementi todo all'archivio locale. Chiudere l'app.

Si noti che l'app non è ancora connessa ad alcun servizio mobile, pertanto i pulsanti **Push** e **Pull** genereranno eccezioni.




##Testare l'app su Servizi mobili

Ora verrà eseguito il test dell'app su Servizi mobili.

1. Nel [portale di Azure classico] individuare l'applicazione del servizio mobile facendo clic su **Gestisci chiavi** sulla barra dei comandi della scheda **Dashboard**. Copiare la **chiave applicazione**.

2. In Esplora soluzioni di Visual Studio aprire il file App.xaml.cs nel progetto di esempio client. Modificare l'inizializzazione di **MobileServiceClient** per utilizzare l'URL del servizio mobile e la chiave applicazione:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3. In Visual Studio premere **F5** per compilare ed eseguire nuovamente l'app.

    ![][0]


##Aggiornare i dati nel back-end per creare un conflitto

In uno scenario reale un conflitto di sincronizzazione si verifica quando un'app effettua il push di aggiornamenti a un record del database, quindi un'altra app tenta di effettuare il push di un aggiornamento allo stesso record usando un campo di versione non aggiornato di tale record. Come indicato in [Introduzione ai dati offline], la proprietà di sistema version è obbligatoria per supportare le funzionalità di sincronizzazione offline. Queste informazioni sulla versione vengono esaminate con ogni aggiornamento del database. Se un'istanza dell'app prova ad aggiornare un record con una versione obsoleta, si verificherà un conflitto, che verrà rilevato come `MobileServicePreconditionFailedException` nell'app. Se l'app non rileva `MobileServicePreconditionFailedException`, verrà generata un'eccezione `MobileServicePushFailedException` che descrive il numero di errori di sincronizzazione rilevato.

>[AZURE.NOTE] Per supportare la sincronizzazione dei record eliminati con la sincronizzazione dei dati offline, è consigliabile abilitare l'[eliminazione temporanea](mobile-services-using-soft-delete.md). In alternativa, è necessario rimuovere manualmente i record nell'archivio locale oppure chiamare `IMobileServiceSyncTable::PurgeAsync()` per ripulire quest'ultimo.


I passaggi seguenti mostrano come i client Windows Phone 8.1 e Windows Store 8.1 eseguiti simultaneamente provochino e risolvano un conflitto usando l'esempio.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto Windows Phone 8.1 e quindi scegliere **Imposta come progetto di avvio**. Premere quindi **CTRL+F5** per eseguire il client Windows Phone 8.1 senza il debug. Una volta che il client Windows Phone 8.1 è operativo nell'emulatore, fare clic sul pulsante **Pull** per sincronizzare l'archivio locale con lo stato corrente del database.

    ![][3]


2. In Visual Studio fare clic con il pulsante destro del mouse sul progetto di Windows 8.1 Runtime e quindi scegliere **Imposta come progetto di avvio** per impostarlo nuovamente sul progetto di avvio. Premere quindi **F5** per eseguirlo. Una volta che il client Windows Store 8.1 è operativo, fare clic sul pulsante **Pull** per sincronizzare l'archivio locale con lo stato corrente del database.

    ![][4]

3. A questo punto entrambi i client sono sincronizzati con il database. Il codice di entrambi i client sta anche usando la sincronizzazione incrementale in modo che vengano sincronizzati solo elementi todo incompleti. Gli elementi todo completati verranno ignorati. Scegliere uno degli elementi e modificare il testo dello stesso elemento in entrambi i client sostituendolo con un valore diverso. Fare clic sul pulsante **Push** per sincronizzare entrambe le modifiche con il database sul server.

    ![][5]

    ![][6]


4. Il client il cui push è stato eseguito per ultimo rileva il conflitto e consente all'utente di decidere quale valore inviare al database. L'eccezione fornisce il valore corretto della versione, che viene usato per risolvere il conflitto.

    ![][7]



##Revisione del codice per la gestione dei conflitti di sincronizzazione

Per poter usare la funzionalità offline per Servizi mobili, è necessario includere la colonna relativa alla versione sia nel database locale che nell'oggetto di trasferimento dei dati. A tale scopo viene aggiornato il membro seguente della classe `TodoItem`:

        [Version]
        public string Version { get; set; }

La colonna `__version` viene inclusa nel database locale nel metodo `OnNavigatedTo()` quando la classe `TodoItem` viene usata per definire l'archivio locale.

Per gestire i conflitti di sincronizzazione offline nel codice, creare una classe che implementi `IMobileServiceSyncHandler`. Passare un oggetto di questo tipo nella chiamata a `MobileServiceClient.SyncContext.InitializeAsync()`. Ciò si verifica anche nel metodo `OnNavigatedTo()` dell'esempio.

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

La classe `SyncHandler` in **SyncHandler.cs** implementa `IMobileServiceSyncHandler`. Il metodo `ExecuteTableOperationAsync` viene chiamato quando ciascuna operazione push viene inviata al server. Se viene generata un'eccezione del tipo `MobileServicePreconditionFailedException`, significa che è presente un conflitto tra la versione locale e quella remota di un elemento.

Per risolvere i conflitti a favore dell'elemento locale, è sufficiente riprovare a eseguire l'operazione. Dopo che un conflitto si è verificato, la versione dell'elemento locale verrà aggiornata in base alla versione del server, quindi la ripetizione dell'operazione sovrascriverà le modifiche del server con le modifiche locali:

    await operation.ExecuteAsync();

Per risolvere i conflitti a favore dell'elemento del server, è sufficiente eseguire una restituzione da `ExecuteTableOperationAsync`. La versione locale dell'oggetto verrà eliminata e sostituita con il valore del server.

Per interrompere l'operazione push (conservando però le modifiche in coda), usare il metodo `AbortPush()`:

    operation.AbortPush();

L'operazione push corrente verrà interrotta, ma verranno mantenute tutte le modifiche in sospeso, tra cui l'operazione corrente se `AbortPush` è chiamato da `ExecuteTableOperationAsync`. Alla prossima chiamata di `PushAsync()` queste modifiche verranno inviate al server.

Quando un push viene annullato, `PushAsync` genererà un'eccezione `MobileServicePushFailedException` e la proprietà dell'eccezione `PushResult.Status` avrà il valore `MobileServicePushStatus.CancelledByOperation`.



<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/javascript-backend-database.png
[2]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/dotnet-backend-database.png
[3]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/wp81-view.png
[4]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/win81-view.png
[5]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/wp81-edit-text.png
[6]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/win81-edit-text.png
[7]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/conflict.png




<!-- URLs -->
[Handling conflicts code sample]: http://go.microsoft.com/fwlink/?LinkId=394787
[Get started with Mobile Services]: ../mobile-services-windows-store-get-started.md
[Introduzione ai dati offline]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[portale di Azure classico]: https://manage.windowsazure.com/
[Handling Database Conflicts]: mobile-services-windows-store-dotnet-handle-database-conflicts.md#test-app
[repository Github degli esempi di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=512865
[esempio di Servizi mobili offline Todo]: http://go.microsoft.com/fwlink/?LinkId=512866

<!---HONumber=AcomDC_0727_2016-->