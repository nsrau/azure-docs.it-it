<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Operazione eseguita

###### Aggiunta di riferimenti

Il pacchetto NuGet per Servizi mobili di Azure è stato aggiunto al progetto. Sono stati quindi aggiunti al progetto i riferimenti a .NET seguenti: Microsoft.WindowsAzure.Mobile, Microsoft.WindowsAzure.Mobile.Ext, Newtonsoft.Json, System.Net.Http.Extensions, System.Net.Http.Primitives

###### Valori della stringa di connessione per Servizi mobili

Nel file App.xaml.cs è stato creato un oggetto MobileServiceClient con l'URL applicazione e la chiave applicazione del servizio mobile selezionato.

### Introduzione a Servizi mobili

###### Ottenere un riferimento a una tabella

Il codice seguente ottiene un riferimento a una tabella contenente i dati per TodoItem, che può essere usato in operazioni successive per leggere e aggiornare la tabella dati. La classe TodoItem con gli attributi impostati sarà necessaria per interpretare i dati JSON inviati dal servizio mobile in risposta alle query.

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

    IMobileServiceTable<TodoItem> todoTable = App.<yourClient>.GetTable<TodoItem>();

Questo codice funziona se le autorizzazioni della tabella sono impostate su **Chiunque con la chiave applicazione**. Se si cambiano le autorizzazioni per proteggere il servizio mobile, sarà necessario aggiungere il supporto per l'autenticazione utente. Vedere [Introduzione all'autenticazione][Introduzione all'autenticazione].

###### Aggiungere una voce

Inserire un nuovo elemento in una tabella dati.

    TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
    await todoTable.InsertAsync(todoItem);

###### Leggere una tabella o eseguirvi query

Il codice seguente esegue in una tabella una query relativa a tutti gli elementi. Si noti che restituisce solo la prima pagina di dati, che per impostazione predefinita include 50 elementi. È possibile passare qualsiasi dimensione di pagina, poiché si tratta di un parametro facoltativo.

    List<TodoItem> items = await todoTable.ToListAsync();
    try
    {
        // Query that returns all items.   
        items = await todoTable.ToCollectionAsync();             
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // handle exception
    }

###### Aggiornare una voce

Aggiornare una riga in una tabella dati. L'elemento di parametro è l'oggetto TodoItem da aggiornare.

    await todoTable.UpdateAsync(item);

###### Eliminare una voce

Eliminare una riga nel database. L'elemento di parametro è l'oggetto TodoItem da eliminare.

    await todoTable.DeleteAsync(item);

  [Introduzione all'autenticazione]: http://azure.microsoft.com/it-it/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/
