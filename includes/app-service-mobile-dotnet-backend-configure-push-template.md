1. In **Esplora soluzioni** di Visual Studio espandere la cartella **Controller** nel progetto di back-end del servizio mobile. Aprire **TodoItemController.cs**. Aggiungere le istruzioni `using` seguenti all'inizio del file:

        using Microsoft.Azure.Mobile.Server.Notifications;


2. Sostituire il metodo `PostTodoItem` con il codice seguente:
        
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            HttpConfiguration config = this.Configuration;

            TemplatePushMessage message = new TemplatePushMessage();
            message.Add("message", item.Text);

            try
            {
                var client = new PushClient(config);
                var result = await client.SendAsync(message);

                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                config.Services.GetTraceWriter().Error(ex.Message, null, "Push.SendAsync Error");
            }

            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

<!---HONumber=Oct15_HO3-->