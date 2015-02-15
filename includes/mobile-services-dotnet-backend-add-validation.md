
È sempre consigliabile convalidare la lunghezza dei dati inviati dagli utenti. In questa sezione verrà aggiunto del codice al servizio mobile che convalida la lunghezza dei dati di stringa inviati al servizio mobile e rifiuta le stringhe troppo lunghe, in questo caso lunghe più di 10 caratteri.

1. Avviare Visual Studio con l'opzione **Esegui come amministratore** e aprire la soluzione contenente il progetto di servizio mobile utilizzato nell'esercitazione [Attività iniziali] o [Introduzione ai dati](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/).

2. Nella finestra di Esplora soluzioni espandere il progetto di servizio elenco attività, quindi espandere **Contollers**. Aprire il file TodoItemController.cs, che fa parte del progetto di servizio mobile.  

   	![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-open-todoitemcontroller.png)

3. Sostituire il metodo `PostTodoItem` con il metodo seguente per verificare che la stringa di testo non sia più lunga di 10 caratteri. Per gli elementi con un testo composto da più di 10 caratteri, il metodo restituisce un messaggio di richiesta errata con codice di stato HTTP 400 e un messaggio descrittivo incluso nel contenuto.


        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            if (item.Text.Length > 10)
            {
                return BadRequest("The Item's Text length must not be greater than 10.");
            }
            else
            {
                TodoItem current = await InsertAsync(item);
                return CreatedAtRoute("Tables", new { id = current.Id }, current);
            } 
        }



4. Fare clic con il pulsante destro del mouse sul progetto di servizio mobile, quindi scegliere **Compila** per compilarlo. Verificare che non si siano verificati errori.

   	![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-build-dotnet-service.png)

5. Fare di nuovo clic con il pulsante destro del mouse sul progetto di servizio e scegliere **Pubblica**. Pubblicare il servizio mobile nell'account Microsoft Azure utilizzando le impostazioni di pubblicazione illustrate nell'esercitazione [Attività iniziali] o [Introduzione ai dati](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/).
 
     >[AZURE.NOTE] In alternativa, è possibile eseguire il test con il servizio ospitato in locale in IIS Express. Per altre informazioni, vedere l'esercitazione[Introduzione ai dati](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/).

    ![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-publish-dotnet-service.png)





<!-- URLs. -->
[Attività iniziali]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/

<!--HONumber=42-->
