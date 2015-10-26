1. In **Esplora soluzioni** di Visual Studio espandere la cartella **Controller** nel progetto di back-end del servizio mobile. Aprire **TodoItemController.cs**. Aggiungere le istruzioni `using` seguenti all'inizio del file:

        using System.Collections.Generic;        
        using Microsoft.Azure.Mobile.Server.Notifications;


2. Sostituire il metodo `PostTodoItem` con il codice seguente:
        
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };

            HttpConfiguration config = this.Configuration;

            GooglePushMessage message = new GooglePushMessage(data, System.TimeSpan.FromHours(1));

            try
            {
                var client = new PushClient(config);
                var result = await client.SendAsync(message);

                ServiceSettingsDictionary settings = config.GetServiceSettingsProvider().GetServiceSettings();
                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                config.Services.GetTraceWriter().Error(ex.Message, null, "Push.SendAsync Error");
            }

            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

<!---HONumber=Oct15_HO3-->