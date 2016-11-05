In questo argomento verrà illustrato come creare un'app con un client Web e un client mobile. Si creeranno un'app mobile e un'app Web e per entrambe si utilizzerà lo stesso database di base.

Innanzitutto, sarà possibile creare un nuovo back-end dell'app per dispositivi mobili e una semplice app *To do list* per archiviarvi i dati. Il back-end dell'app mobile utilizza le lingue .NET supportate per la logica di business lato server. L'app client può utilizzare qualsiasi piattaforma client supportata dall'app mobile, tra cui iOS, Windows, Xamarin iOS e Xamarin Android.

Successivamente, si creerà un'app Web, utilizzando lo stesso database dell'app mobile. Alla fine dell'esercitazione, il client Web e il client mobile funzioneranno con gli stessi dati.

> [!NOTE]
> Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

## Creare un nuovo back-end dell'app mobile e client
* Attenersi alle istruzioni dell'esercitazione [Creare un'app per dispositivi mobili] per creare un back-end dell'app per dispositivi mobili e un client. È possibile usare qualsiasi piattaforma client supportata dalle app mobili, tra cui iOS, Windows, Xamarin iOS e Xamarin Android.
* Assicurarsi di aver distribuito il back-end dell'app mobile in Azure e di aver connesso l'applicazione del client mobile al back-end ospitato. Il progetto codice dell'app mobile usa Entity Framework Code First e inizializza il database dopo la prima richiesta REST da parte di un'app client mobile.

## Pubblicare un'API Web TodoList da Visual Studio
In questa sezione, si creerà una nuova app Web usando una soluzione applicazione Web di esempio. Si modificherà l'esempio per usare lo stesso nome schema del database e la stessa stringa di connessione dell'app mobile.

> [!NOTE]
> Prima di completare la procedura, assicurarsi di aver inizializzato il database di back-end dell'app mobile tramite la connessione a un client. In caso contrario, l'app Web non sarà in grado di connettersi allo stesso database.
> 
> 

1. Nel [Portale di Azure](https://portal.azure.com/) creare una nuova app Web usando lo stesso Gruppo di risorse e Piano di hosting dell'app per dispositivi mobili.
2. Scaricare la soluzione di esempio [MultiChannelToDo] e aprirla in Visual Studio. La soluzione contiene un progetto API Web e un Progetto Applicazione Web per l'interfaccia utente del client Web.
3. Nel progetto API Web, modificare MultiChannelToDoContext.cs. In `OnModelCreating` aggiornare il nome dello schema affinché corrisponda al nome dell'app per dispositivi mobili:
   
        modelBuilder.HasDefaultSchema("your_mobile_app"); // your service name, replacing dashes with underscore
4. Successivamente si otterrà la stringa di connessione dell'app mobile dal Portale di Azure:
   
   * Selezionare l'app per dispositivi mobili nel portale e fare clic sulla parte con etichetta **Codice utente**.
   * Nel pannello visualizzato selezionare **Tutte le impostazioni**, quindi **Impostazioni applicazione**.
   * In **Stringhe di connessione** fare clic su **Mostra stringhe di connessione**. Copiare il valore per l'impostazione **MS\_TableConnectionString**. Questa è la stringa di connessione usata dall'app mobile per connettersi al database SQL.
5. In Visual Studio, fare clic con il pulsante destro del mouse sul progetto API Web e selezionare **Pubblica**. Selezionare **App Web Azure** come destinazione di pubblicazione e selezionare l'app Web creata in precedenza. Fare clic su **Avanti** fino alla sezione **Impostazioni** della procedura guidata Pubblica sito Web.
6. Nella sezione **Database** incollare la stringa di connessione dell'app per dispositivi mobili come valore per **MultiChannelToDoContext**. Selezionare solo la casella di controllo **Usare questa stringa di connessione al runtime**.
7. Una volta pubblicata l'API Web API su Azure, si visualizzerà una pagina di conferma. Copiare l'URL per il servizio pubblicato.

## Pubblicare l'interfaccia utente del client Web TodoList da Visual Studio
In questa sezione, si userà un'applicazione client Web di esempio implementata con AngularJS. Si utilizzerà, quindi, Visual Studio per pubblicare il progetto in una nuova app Web del servizio app ospitato in Azure.

1. In Visual Studio, aprire il progetto **MultiChannelToDo.Web**. Modificare il file `js/service/ToDoService.js`, aggiungendo l'URL all'API Web appena pubblicata:
   
        var apiPath = "https://your-web-api-site-name.azurewebsites.net";
2. Fare clic con il pulsante destro del mouse sul progetto **MultiChannelToDo.Web** e selezionare **Pubblica**.
3. Nella procedura guidata **Pubblica sito Web**, selezionare **App Web Azure** come destinazione di pubblicazione e creare una nuova app Web senza database.
4. Una volta pubblicato il progetto, si visualizzerà l'interfaccia utente Web nel browser in uso.

## Testare le app Web e mobile
1. Nell'interfaccia utente Web, aggiungere o modificare alcuni elementi todo.
   
    ![Vista dell'applicazione Web nel browser](./media/app-service-mobile-dotnet-backend-web-and-mobile/web-app-in-browser.png)
2. Eseguire l'app per dispositivi mobili creata nell'esercitazione [Creare un'app per dispositivi mobili]. Si visualizzeranno gli stessi elementi todo dell'app Web.
   
    ![Vista dell'app per dispositivi mobili Xamarin](./media/app-service-mobile-dotnet-backend-web-and-mobile/xamarin-ios-quickstart-device.png)

## Modifiche apportate
* Per una Guida per la modifica di siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sui servizi di Azure esistente](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida relativa al passaggio dal portale precedente al nuovo portale, vedere [Informazioni di riferimento per l'esplorazione del portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- Links -->

[MultiChannelToDo]: https://github.com/Azure/mobile-services-samples/tree/web-mobile/MultiChannelToDo
[Creare un'app per dispositivi mobili]: ../article/app-service-mobile/app-service-mobile-xamarin-ios-get-started.md

<!---HONumber=AcomDC_1203_2015-->