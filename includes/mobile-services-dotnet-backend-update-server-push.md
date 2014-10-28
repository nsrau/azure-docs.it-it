1.  In Esplora soluzioni di Visual Studio espandere la cartella **Controllers** nel progetto del servizio mobile. Aprire TodoItemController.cs e aggiornare la definizione del metodo `PostTodoItem` con il codice seguente:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            // Create a WNS native toast.
            WindowsPushMessage message = new WindowsPushMessage();

            // Define the XML paylod for a WNS native toast notification 
            // that contains the text of the inserted item.
            message.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                                 @"<toast><visual><binding template=""ToastText01"">" +
                                 @"<text id=""1"">" + item.Text + @"</text>" +
                                 @"</binding></visual></toast>";
            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Questo codice invia una notifica push (con il testo dell'elemento inserito) dopo l'inserimento di un elemento Todo. In caso di errore, il codice aggiungerà una voce di log errori visualizzabile nella scheda **Logs** del servizio mobile nel portale di gestione.

    > [WACOM.NOTE] È possibile usare le notifiche modello per inviare una singola notifica push ai client di più piattaforme. Per altre informazioni, vedere la pagina relativa al [supporto di più piattaforme per dispositivi da un singolo servizio mobile][supporto di più piattaforme per dispositivi da un singolo servizio mobile].

2.  Ripubblicare il progetto di servizio mobile in Azure.

  [supporto di più piattaforme per dispositivi da un singolo servizio mobile]: /it-it/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service/#push
