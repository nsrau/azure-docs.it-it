<properties 
	pageTitle="" 
	description="" 
	services="mobile-services" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/8/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Introduzione](vs-mobile-services-dotnet-getting-started.md)
> - [Risultati](vs-mobile-services-dotnet-what-happened.md)

## Introduzione a Servizi mobili (progetti .NET)

Il primo passaggio da eseguire per attenersi al codice riportato in questi esempi dipende dal tipo di servizio mobile a cui si è connessi.

Per un servizio mobile back-end JavaScript, creare una tabella denominata TodoItem.  Per creare una tabella, individuare il servizio mobile nel nodo Azure di Esplora server, fare clic con il pulsante destro del mouse sul nodo per aprire il menu di scelta rapida, quindi scegliere **Crea tabella**. Immettere "TodoItem" come nome della tabella.

Se invece si è connessi a un servizio mobile back-end .NET, nel modello di progetto predefinito creato da Visual Studio è già presente una tabella TodoItem, che deve però essere pubblicata in Azure. Per eseguire la pubblicazione, aprire il menu di scelta rapida per il progetto del servizio mobile in Esplora soluzioni e scegliere **Pubblica sito Web**. Accettare le impostazioni predefinite, quindi scegliere **Pubblica**.

#####Ottenere un riferimento a una tabella

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

Questo codice funziona se le autorizzazioni della tabella sono impostate su **Chiunque con la chiave applicazione**. Se si cambiano le autorizzazioni per proteggere il servizio mobile, sarà necessario aggiungere il supporto per l'autenticazione utente. Vedere [Introduzione all'autenticazione](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/).

#####Aggiungere una voce 

Inserire un nuovo elemento in una tabella dati.

	TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
	await todoTable.InsertAsync(todoItem);

#####Leggere una tabella o eseguirvi query 

Il codice seguente esegue in una tabella una query relativa a tutti gli elementi. Si noti che restituisce solo la prima pagina di dati, che per impostazione predefinita include 50 elementi. È possibile passare qualsiasi dimensione di pagina, poiché si tratta di un parametro facoltativo.

    List<TodoItem> items;
    try
    {
        // Query that returns all items.   
        items = await todoTable.ToListAsync();             
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // handle exception
    }


#####Aggiornare una voce

Aggiornare una riga in una tabella dati. L'elemento di parametro è l'oggetto TodoItem da aggiornare.

	await todoTable.UpdateAsync(item);

#####Eliminare una voce

Eliminare una riga nel database. L'elemento di parametro è l'oggetto TodoItem da eliminare.

	await todoTable.DeleteAsync(item);


[Altre informazioni sui servizi mobili](http://azure.microsoft.com/documentation/services/mobile-services/)


<!--HONumber=42-->
