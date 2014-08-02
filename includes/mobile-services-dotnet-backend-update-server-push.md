<ol>

1.  In Esplora soluzioni di Visual Studio espandere la cartella **Controllers** nel progetto del servizio mobile. Aprire TodoItemController.cs e aggiornare la definizione del metodo `PostTodoItem` con il codice seguente:

         public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
         {
             TodoItem current = await InsertAsync(item);
             WindowsPushMessage message = new WindowsPushMessage();
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

    Questo codice invierà una notifica push (con il testo dell'elemento inserito) dopo l'inserimento di un elemento Todo. In caso di errore, il codice aggiungerà una voce di log errori visualizzabile nella scheda **Logs** del servizio mobile nel portale di gestione.


