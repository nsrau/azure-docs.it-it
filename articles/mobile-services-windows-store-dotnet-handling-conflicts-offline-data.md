<properties 
	pageTitle="Gestire i conflitti relativi alla sincronizzazione offline dei dati in Servizi mobili (Windows Store) | Mobile Dev Center" 
	description="Informazioni su come usare Servizi mobili di Azure per gestire conflitti durante la sincronizzazione dei dati offline nell'applicazione per Windows Store" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="wesmc"/>


# Gestione dei conflitti relativi alla sincronizzazione dei dati offline in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-offline-conflicts](../includes/mobile-services-selector-offline-conflicts.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Questo argomento descrive come sincronizzare i dati e gestire i conflitti quando si usano le funzionalità offline di Servizi mobili di Azure.</p>
<p>Se si preferisce guardare un video, nel clip a destra vengono eseguiti gli stessi passaggi dell'esercitazione.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">guarda l'esercitazione</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a> <span class="time">14:36</span></div>
</div>

In questa esercitazione si scaricherà una soluzione Windows universale scritta in C# per un'app che supporta la gestione dei conflitti di sincronizzazione offline. Si integrerà un servizio mobile con l'app e quindi si eseguiranno i client Windows Store 8.1 e Windows Phone 8.1 per generare un conflitto di sincronizzazione e risolverlo.

Questa esercitazione è basata sulle procedure e sull'app di esempio dell'esercitazione precedente [Introduzione ai dati offline]. Prima di iniziare questa esercitazione è necessario completare [Introduzione ai dati offline].


Questa esercitazione spiega come eseguire le operazioni di base seguenti:

1. [Download del progetto dell'app di Windows Store] 
2. [Testare l'app su Servizi mobili]
3. [Aggiornare manualmente i dati nel back-end per creare un conflitto]

Per completare questa esercitazione, è necessario disporre di Visual Studio 2013 in esecuzione su Windows 8.1.


## <a name="download-app"></a>Scaricare il progetto di esempio

![][0]

Questa esercitazione è una procedura dettagliata che illustra come usare l'[esempio di Servizi mobili offline Todo] per gestire i conflitti di sincronizzazione tra l'archivio offline locale e il database di Servizi mobili in Azure.

1. Scaricare il file ZIP per il [repository Github degli esempi di Servizi mobili] ed estrarlo in una directory di lavoro. 

2. Se SQLite per Windows 8.1 e Windows Phone 8.1 non sono già installati, come indicato nell'esercitazione [Introduzione ai dati offline] installare entrambi i runtime.

3. In Visual Studio 2013 aprire il file della soluzione  *mobile-services-samples\TodoOffline\WindowsUniversal\TodoOffline-Universal.sln*. Premere **F5** per ricompilare ed eseguire il progetto. Verificare che i pacchetti NuGet vengano ripristinati e che i riferimenti siano impostati correttamente.

    >[AZURE.NOTE] Potrebbe essere necessario eliminare i vecchi riferimenti al runtime di SQLite e sostituirli con il riferimento aggiornato, come indicato nell'esercitazione [Introduzione ai dati offline].

4. Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save** per aggiungere alcuni elementi todo all'archivio locale. Chiudere l'app.

Si noti che l'app non è ancora connessa ad alcun servizio mobile, pertanto i pulsanti **Push** e **Pull** genereranno eccezioni.




## <a name="test-app"></a>Testare l'app nel nuovo servizio mobile

Ora verrà eseguito il test dell'app su Servizi mobili.

1. Nel portale di gestione di Azure individuare l'applicazione del servizio mobile facendo clic su **Gestisci chiavi** nella barra dei comandi della scheda **Dashboard**. Copiare la **chiave applicazione**.

2. In Esplora soluzioni di Visual Studio aprire il file App.xaml.cs nel progetto di esempio client. Modificare l'inizializzazione di **MobileServiceClient** per usare l'URL del servizio mobile e la chiave applicazione:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3. In Visual Studio premere **F5** per compilare ed eseguire nuovamente l'app.

    ![][0]


## <a name="handle-conflict"></a>Aggiornare i dati nel back-end per creare un conflitto

In uno scenario reale un conflitto di sincronizzazione si verifica quando un'app effettua il push di aggiornamenti a un record del database, quindi un'altra app tenta di effettuare il push di un aggiornamento allo stesso record usando un campo di versione non aggiornato di tale record. Come indicato in [Introduzione ai dati offline], la proprietà di sistema version è obbligatoria per supportare le funzionalità di sincronizzazione offline. Queste informazioni sulla versione vengono esaminate con ogni aggiornamento del database. Se un'istanza dell'app tenta di aggiornare un record con una versione obsoleta, si verificherà un conflitto, che verrà rilevato come  `MobileServicePreconditionFailedException` nell'app. Se l'app non rileva  `MobileServicePreconditionFailedException` verrà generata un'eccezione  `MobileServicePushFailedException` che descrive il numero di errori di sincronizzazione rilevato.

>[AZURE.NOTE] Per supportare la sincronizzazione dei record eliminati con la sincronizzazione dei dati offline, è consigliabile abilitare [Soft Delete](/it-it/documentation/articles/mobile-services-using-soft-delete/). In alternativa, è necessario rimuovere manualmente i record nell'archivio locale oppure chiamare  `IMobileServiceSyncTable::PurgeAsync()` per ripulire quest'ultimo.


I passaggi seguenti mostrano come i client Windows Phone 8.1 e Windows Store 8.1 eseguiti simultaneamente provochino e risolvano un conflitto usando l'esempio.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto Windows Phone 8.1, quindi selezionare **Imposta come progetto di avvio**. Premere quindi **CTRL+F5** per eseguire il client Windows Phone 8.1 senza il debug. Una volta che il client Windows Phone 8.1 è operativo nell'emulatore, fare clic sul pulsante **Pull** per sincronizzare l'archivio locale con lo stato corrente del database.
 
    ![][3]
 
   
2. In Visual Studio fare clic con il pulsante destro del mouse sul progetto del runtime di Windows 8.1, quindi selezionare **Imposta come progetto di avvio** per impostarlo nuovamente come progetto di avvio. Premere quindi **F5** per eseguirlo. Una volta che il client Windows Store 8.1 è operativo, fare clic sul pulsante **Pull** per sincronizzare l'archivio locale con lo stato corrente del database.

    ![][4]
 
3. A questo punto entrambi i client sono sincronizzati con il database. Il codice di entrambi i client sta anche usando la sincronizzazione incrementale in modo che vengano sincronizzati solo elementi todo incompleti. Gli elementi todo completati verranno ignorati. Scegliere uno degli elementi e modificare il testo dello stesso elemento in entrambi i client sostituendolo con un valore diverso. Fare clic sul pulsante **Push** per sincronizzare entrambe le modifiche con il database sul server.

    ![][5]

    ![][6]


4. Il client il cui push è stato eseguito per ultimo rileva il conflitto e consente all'utente di decidere quale valore inviare al database. L'eccezione fornisce il valore corretto della versione, che viene usato per risolvere il conflitto.

    ![][7]



## Revisione del codice per la gestione dei conflitti di sincronizzazione

Per poter usare la funzionalità offline per Servizi mobili, è necessario includere la colonna relativa alla versione sia nel database locale che nell'oggetto di trasferimento dei dati. A tale scopo viene aggiornato il membro seguente della classe  `TodoItem`:

        [Version]
        public string Version { get; set; }

La colonna `__version` viene inclusa nel database locale nel metodo  `OnNavigatedTo()` quando la classe  `TodoItem` viene usata per definire l'archivio locale.

Per gestire i conflitti di sincronizzazione offline nel codice, creare una classe che implementi  `IMobileServiceSyncHandler`. Passare un oggetto di questo tipo nella chiamata a  `MobileServiceClient.SyncContext.InitializeAsync()`. Ciò si verifica anche nel metodo  `OnNavigatedTo()` dell'esempio.

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

La classe  `SyncHandler` in **SyncHandler.cs** implementa  `IMobileServiceSyncHandler`. Il metodo  `ExecuteTableOperationAsync` viene chiamato quando ciascuna operazione push viene inviata al server. Se viene generata un'eccezione del tipo  `MobileServicePreconditionFailedException` è presente un conflitto tra la versione locale e la versione remota di un elemento.

Per risolvere i conflitti a favore dell'elemento locale, è sufficiente riprovare a eseguire l'operazione. Dopo che un conflitto si è verificato, la versione dell'elemento locale verrà aggiornata in base alla versione del server, quindi la ripetizione dell'operazione sovrascriverà le modifiche del server con le modifiche locali:

    await operation.ExecuteAsync(); 

Per risolvere i conflitti a favore dell'elemento del server, è sufficiente tornare da  `ExecuteTableOperationAsync`. La versione locale dell'oggetto verrà eliminata e sostituita con il valore del server.

Per interrompere l'operazione push (conservando però le modifiche in coda), usare il metodo  `AbortPush()`:

    operation.AbortPush();

L'operazione push corrente verrà interrotta, ma verranno mantenute tutte le modifiche in sospeso, tra cui l'operazione corrente se  `AbortPush` è chiamato da  `ExecuteTableOperationAsync`. Alla prossima chiamata di  `PushAsync()` queste modifiche verranno inviate al server. 

Quando un push viene annullato,  `PushAsync` genererà un'eccezione  `MobileServicePushFailedException` e la proprietà dell'eccezione  `PushResult.Status` avrà il valore  `MobileServicePushStatus.CancelledByOperation`. 


<!-- Anchors. -->
[Scaricare il progetto dell'app di Windows Store]: #download-app
[Creazione del servizio mobile]: #create-service
[Aggiungere una colonna di scadenza per il database]: #add-column
[Aggiornamento del database per i servizi mobili back-end .NET]: #dotnet-backend  
[Aggiornamento del database per i servizi mobili JavaScript]: #javascript-backend
[Testare l'app su Servizi mobili]: #test-app
[Aggiornare manualmente i dati nel back-end per creare un conflitto]: #handle-conflict
[Passaggi successivi]:#next-steps

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
[Esempio di codice per la gestione dei conflitti]: http://go.microsoft.com/fwlink/?LinkId=394787
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-store-get-started/
[Introduzione ai dati offline]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
[SQLite per Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Gestione dei conflitti nel database]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/#test-app
[Repository Github degli esempi di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=512865
[Esempio di Servizi mobili offline Todo]: http://go.microsoft.com/fwlink/?LinkId=512866


<!--HONumber=42-->
