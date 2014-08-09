<properties linkid="develop-mobile-tutorials-get-started-with-data-xamarin-android" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Xamarin.Android) - Azure Mobile Services" metaKeywords="Azure Xamarin.Android data, Azure mobile services data" description="Learn how to store and access data from your Azure Mobile Services Xamarin.Android app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services" documentationCenter="Mobile" authors="" />

Introduzione ai dati in Servizi mobili
======================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per sfruttare i dati in un'app per Xamarin.Android. In questa esercitazione si scaricherà un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

**Nota**

In questa esercitazione viene descritto come è possibile utilizzare Servizi mobili di Azure per archiviare e recuperare i dati da un'app per Xamarin.Android e vengono pertanto riproposte molte delle procedure già completate nella guida introduttiva a Servizi mobili. Se si tratta della prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started-xamarin-android).

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Download del progetto dell'app per Xamarin.Android](http://go.microsoft.com/fwlink/p/?LinkId=331302)
2.  [Creazione del servizio mobile](#create-service)
3.  [Aggiunta di una tabella dati per l'archiviazione](#add-table)
4.  [Aggiornamento dell'app per l'utilizzo di Servizi mobili](#update-app)
5.  [Test dell'app in Servizi mobili](#test-app)

**Nota**

Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A9C9624B5).

Per completare questa esercitazione, è necessario disporre del [componente Servizi mobili di Azure](http://components.xamarin.com/view/azure-mobile-services/) [Xamarin.Android] e di Android SDK 4.2 o versione successiva.

**Nota**

Il progetto GetStartedWithData scaricato richiede Android 4.2 o versione successiva invece con Mobile Services SDK è richiesto solo Android 2.2 o versione successiva.

Download del progettoDownload del progetto GetStartedWithData
-------------------------------------------------------------

Questa esercitazione è basata sull'[app GetStartedWithData](http://go.microsoft.com/fwlink/p/?LinkId=331302) che è un'app per Xamarin.Android. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili per Android, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1.  Scaricare l'app di esempio `GetStartedWithData` ed estrarre i file nel computer.

2.  In Xamarin Studio fare clic su **File** e scegliere **Open**, passare alla posizione in cui è stato estratto il progetto di esempio GetStartedWithData, quindi selezionare e aprire **XamarinTodoQuickStart.Android.sln**.

3.  Individuare e aprire la classe **TodoActivity**.

    Si noti che sono presenti commenti `// TODO::` in cui vengono specificate le procedure da eseguire per il funzionamento dell'app con il servizio mobile.

4.  Nel menu **Run** fare clic su **Start Without Debugging**. Verrà chiesto di scegliere un emulatore o un dispositivo USB Android collegato.

    **Nota**

    È possibile eseguire il progetto utilizzando un telefono Android o l'emulatore di Android. L'esecuzione in un telefono Android richiede il download di un driver USB specifico del telefono.

    Per eseguire il progetto nell'emulatore di Android, è necessario definire almeno un Android Virtual Device (AVD). Utilizzare AVD Manager per creare e gestire questi dispositivi.

5.  Nell'app digitare un testo significativo, ad esempio *Complete the tutorial* e quindi fare clic su **Add**.

    ![][13]

   Si noti che il testo salvato è archiviato in una raccolta in memoria e visualizzato nell'elenco riportato di seguito.

Creazione del servizio mobileCreazione di un nuovo servizio mobile nel portale di gestione
------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

Aggiunta di una nuova tabellaAggiunta di una nuova tabella al servizio mobile
-----------------------------------------------------------------------------

Prima di poter archiviare i dati dell'app nel nuovo servizio mobile, è necessario creare una nuova tabella.

1.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

2.  Fare clic sulla scheda **Data** e quindi su **+Create**.

    ![][5]

    Verrà visualizzata la finestra di dialogo **Create new table**.

3.  In **Table name** digitare *TodoItem*, quindi fare clic sul segno di spunta.

	![](./media/partner-xamarin-mobile-services-android-get-started-data/mobile-create-todoitem-table.png) 
	Verrà creata una nuova tabella di archiviazione **TodoItem** con le autorizzazioni predefinite impostate. Questo significa che qualsiasi utente dell'app potrà accedere alla tabella e modificare i dati.

    <div class="dev-callout"> 
    <b>Nota</b> 
    <p>Nella Guida introduttiva di Servizi mobili viene utilizzato lo stesso nome di tabella. Tuttavia, ogni tabella viene creata in uno schema specifico per un determinato servizio mobile. In questo modo è possibile evitare conflitti di dati quando più servizi mobili utilizzano lo stesso database.</p> 
    </div>

1.  Fare clic sulla nuova tabella **TodoItem** e verificare che non siano presenti righe di dati.

2.  Fare clic sulla scheda **Columns** e verificare che sia presente una sola colonna **id**, che viene creata automaticamente.

	Questo è il requisito minimo per le tabelle in Servizi mobili.

    <div class="dev-callout"><b>Nota</b>
    <p>Se nel servizio mobile è abilitato lo schema dinamico, vengono automaticamente create nuove colonne quando al servizio mobile vengono inviati oggetti JSON mediante un'operazione di inserimento o aggiornamento.</p>
    </div>

È ora possibile utilizzare il nuovo servizio mobile come archivio dati per l'app.

Aggiornamento dell'appAggiornamento dell'app per l'utilizzo del servizio mobile per l'accesso ai dati
-----------------------------------------------------------------------------------------------------

Ora che il servizio mobile è pronto, è possibile aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili anziché nella raccolta locale.

1.  Se **Servizi mobili di Azure** non è già elencato nella cartella dei componenti, per accedervi fare clic con il pulsante destro del mouse su **Components**, scegliere **Get More Components** e quindi cercare **Servizi mobili di Azure**.

	Il componente Mobile Services SDK verrà aggiunto al progetto.

1.  Aprire il file **AndroidManifest.xml** e verificare la presenza della riga di autorizzazione seguente:

        <uses-permission android:name="android.permission.INTERNET" />

	Questo consente all'app di accedere a Servizi mobili in Azure.

1.  Nella finestra **Solution** aprire la classe **TodoActivity** e rimuovere i simboli di commento dalla riga di codice seguente:

         using Microsoft.WindowsAzure.MobileServices;

2.  Si procederà quindi alla rimozione dell'elenco in memoria attualmente utilizzato dall'app in modo da poterlo sostituire con un servizio mobile. Nella classe **TodoActivity** impostare come commento la riga di codice seguente che definisce l'elenco **todoItemList** esistente.

         public List<TodoItem> todoItemList = new ArrayList<TodoItem>();

3.  Dopo avere completato il passaggio precedente, il progetto indicherà la presenza di errori di compilazione. Cercare le altre tre posizioni in cui viene utilizzata la variabile `todoItemList` e impostare come commento le sezioni indicate.

4.  Ora si procederà all'aggiunta del servizio mobile. Rimuovere quindi i simboli di commento dalle righe di codice seguenti:

         private MobileServiceClient client; // Mobile Service Client references
         private IMobileServiceTable<TodoItem> todoTable; // Mobile Service Table used to access data   

5.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

6.  Fare clic sulla scheda **Dashboard** e prendere nota del valore di **Site URL**, quindi fare clic su **Manage keys** e prendere nota del valore di **Application key**.

    ![][8]

	Questi valori sono necessari per accedere al servizio mobile dal codice dell'app.

1.  Nella classe **Constants** rimuovere i simboli di commento dalle variabili membro seguenti:

         public const string ApplicationURL = @"AppUrl";
         public const string ApplicationKey = @"AppKey";

2.  Sostituire **AppUrl** e **AppKey** nelle variabili precedenti con i valori recuperati dal portale di gestione.

3.  Nel metodo **OnCreate** rimuovere i simboli di commento dalle righe di codice seguenti che definiscono la variabile **MobileServiceClient**:

        // Create the Mobile Service Client instance, using the provided
        // Mobile Service URL and key
        client = new MobileServiceClient(
            Constants.ApplicationURL,
            Constants.ApplicationKey).WithFilter(filter);

        // Get the Mobile Service Table instance to use
        todoTable = client.GetTable<TodoItem>();    

	Verrà creata una nuova istanza di MobileServiceClient utilizzata per accedere al servizio mobile. Verrà inoltre creata l'istanza di MobileServiceTable utilizzata per comunicare con l'archiviazione dati nel servizio mobile.

1.  Individuare la classe ProgressFilter verso la fine del file e rimuovere i simboli di commento. Questa classe visualizza un indicatore 'loading' mentre MobileServiceClient esegue le operazioni di rete.

2.  Rimuovere i simboli di commento dalle righe seguenti del metodo **CheckItem**:

        try {
            await todoTable.UpdateAsync(item);
            if (item.Complete)
                adapter.Remove(item);
        } catch (Exception e) {
            CreateAndShowDialog(e, "Error");
            }

	Verrà quindi inviato un aggiornamento dell'elemento al servizio mobile e verranno rimossi gli elementi dall'adattatore.

1.  Rimuovere i simboli di commento dalle righe seguenti del metodo **AddItem**:

        try 
            {
                // Insert the new item
            await todoTable.InsertAsync(item);

            if (!item.Complete) 
                adapter.Add(item);          
            } 
            catch (Exception e) 
            {
                CreateAndShowDialog(e, "Error");
            }           

	Verrà creato e inserito un nuovo elemento nella tabella nel servizio mobile remoto.

1.  Rimuovere i simboli di commento dalle righe seguenti del metodo **RefreshItemsFromTableAsync**:

        try {
            // Get the items that weren't marked as completed and add them in the adapter
            var list = await todoTable.Where(item => item.Complete == false).ToListAsync ();

            adapter.Clear();

            foreach (TodoItem current in list)
                adapter.Add(current);
            } 
            catch (Exception e) 
            {
                CreateAndShowDialog(e, "Error");
            }

    Verrà interrogato il servizio mobile e verranno restituiti gli oggetti non contrassegnati come completati. Gli elementi verranno aggiunti all'adattatore per l'associazione.

Una volta aggiornata l'app per consentire l'utilizzo di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.

Test dell'appTest dell'app nel nuovo servizio mobile
----------------------------------------------------

1.  Nel menu **Run** fare clic su **Start Without Debugging** per avviare il progetto. Verrà chiesto di selezionare un'immagine di emulatore o un dispositivo Android USB collegato.

    L'app, compilata tramite Xamarin.Android, verrà eseguita. Quest'app utilizza la libreria client per inviare una query che restituisce gli elementi per il servizio mobile.

2.  Come in precedenza, digitare un testo significativo e quindi fare clic su **Add**.

    Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3.  Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Mobile Services** e quindi sul servizio mobile.

4.  Fare clic sulla scheda **Data** e quindi su **Browse**.

    ![][9]
          
    Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

L'esercitazione **Introduzione ai dati** per Xamarin.Android è terminata.

Download dell'esempio completato
--------------------------------

Scaricare il [progetto di esempio completato](http://go.microsoft.com/fwlink/p/?LinkId=331302). Assicurarsi di aggiornare le variabili **applicationURL** e **applicationKey** con le proprie impostazioni di Azure.

Passaggi successivi
-------------------

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app per Xamarin.Android di utilizzare dati in Servizi mobili.

In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

-   [Utilizzo di script per la convalida e la modifica di dati](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android) <br/>
    Ulteriori informazioni sull'utilizzo di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

-   [Utilizzo del paging per ridefinire le query](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android) <br/>
    Ulteriori informazioni su come utilizzare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire le esercitazioni per Xamarin.Android seguenti:

-   [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android) <br/>
    Informazioni sull'autenticazione degli utenti dell'app.

-   [Introduzione alle notifiche push](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android) <br/>
    Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.

[5]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
