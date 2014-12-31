<properties urlDisplayName="Queue Service" pageTitle="Come usare il servizio di accodamento (Ruby) | Microsoft Azure" metaKeywords="Azure Queue Service get messages Ruby" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Ruby." metaCanonical="" services="storage" documentationCenter="Ruby" title="How to Use the Queue Storage Service from Ruby" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="11/24/2014" ms.author="tomfitz" />





# Come usare il servizio di archiviazione di accodamento di Ruby

In questa guida viene illustrato come eseguire scenari comuni del servizio di archiviazione di accodamento di Azure. Gli esempi sono scritti usando l'API Ruby di Azure.
Gli scenari presentati includono l'**inserimento**, la **visualizzazione**, il **recupero** e l'**eliminazione** dei messaggi in coda, oltre alle procedure di **creazione ed eliminazione di code**. Per altre informazioni sulle code, fare riferimento alla sezione [Passaggi successivi](#next-steps).

## Sommario

* [Informazioni sull'archiviazione di accodamento](#what-is)
* [Concetti](#concepts)
* [Creare un account di archiviazione di Azure](#CreateAccount)
* [Creare un'applicazione Ruby](#create-a-ruby-application)
* [Configurare l'applicazione per l'accesso all'archiviazione](#configure-your-application-to-access-storage)
* [Configurare una connessione di archiviazione di Azure](#setup-a-windows-azure-storage-connection)
* [Procedura: Creare una coda](#how-to-create-a-queue)
* [Procedura: Inserire un messaggio in una coda](#how-to-insert-a-message-into-a-queue)
* [Procedura: Visualizzare il messaggio successivo](#how-to-peek-at-the-next-message)
* [Procedura: Rimuovere il messaggio successivo dalla coda](#how-to-dequeue-the-next-message)
* [Procedura: Cambiare il contenuto di un messaggio in coda](#how-to-change-the-contents-of-a-queued-message)
* [Procedura: Opzioni aggiuntive per rimuovere i messaggi dalla coda](#how-to-additional-options-for-dequeuing-messages)
* [Procedura: Recuperare la lunghezza della coda](#how-to-get-the-queue-length)
* [Procedura: Eliminare una coda](#how-to-delete-a-queue)
* [Passaggi successivi](#next-steps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <a id="CreateAccount"></a>Creare un account di archiviazione di Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a id="create-a-ruby-application"></a>Creare un'applicazione Ruby

Creare un'applicazione Ruby. Per istruzioni,vedere [Creazione di un'applicazione Ruby in Azure](/it-it/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a id="configure-your-application-to-access-storage"></a>Configurare l'applicazione per l'accesso all'archiviazione

Per usare l'archiviazione di Azure, è necessario scaricare e usare il pacchetto Ruby Azure, che comprende un set di pratiche librerie che comunicano con i servizi di archiviazione REST.

### Usare RubyGems per ottenere il pacchetto

1. Usare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminale** (Mac) o **Bash** (Unix).

2. Digitare "gem install azure" nella finestra di comando per installare la gemma e le dipendenze.

### Importare il pacchetto

Usando l'editor di testo preferito aggiungere quanto segue alla parte superiore del file Ruby dove si intende usare l'archiviazione:

	require "azure"

## <a id="setup-a-windows-azure-storage-connection"></a>Configurare una connessione di archiviazione di Azure

Il modulo azure leggerà le variabili di ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS_KEY** 
per ottenere le informazioni necessarie per connettersi all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, 
sarà necessario specificare le informazioni relative all'account prima di usare **Azure::TableService** con il codice seguente:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your Azure storage access key>"

Per ottenere questi valori:

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/).
2. Passare all'account di archiviazione che si desidera usare
3. Fare clic su **GESTISCI CHIAVI** nella parte inferiore del pannello di navigazione.
4. Nella finestra di dialogo popup saranno visualizzati il nome dell'account di archiviazione, la chiave di accesso primaria e la chiave di accesso secondaria. Per la chiave di accesso è possibile selezionare la primaria o la secondaria.

## <a id="how-to-create-a-queue"></a>Procedura: Creare una coda

La coda seguente crea un oggetto **Azure::QueueService** che consente di usare le code.

	azure_queue_service = Azure::QueueService.new

Usare il metodo **create_queue()** per creare una coda con il nome specificato.

	begin
	  azure_queue_service.create_queue("test-queue")
	rescue
	  puts $!
	end

## <a id="how-to-insert-a-message-into-a-queue"></a>Procedura: Inserire un messaggio in una coda

Per inserire un messaggio in una coda, usare il metodo **create_message()** per creare un nuovo messaggio e aggiungerlo alla coda.

	azure_queue_service.create_message("test-queue", "test message")

## <a id="how-to-peek-at-the-next-message"></a>Procedura: Visualizzare il messaggio successivo

È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo dalla coda chiamando il metodo **peek\_messages()**. Per impostazione predefinita, **peek\_messages()**visualizza un singolo messaggio. È anche possibile specificare il numero di messaggi che si desidera visualizzare.

	result = azure_queue_service.peek_messages("test-queue",
	  {:number_of_messages => 10})

## <a id="how-to-dequeue-the-next-message"></a>Procedura: Rimuovere il messaggio successivo dalla coda

È possibile rimuovere un messaggio da una coda in due passaggi.

1. Per impostazione predefinita, **list\_messages()**si ottiene il messaggio successivo in una coda. È anche possibile specificare il numero di messaggi che si desidera ottenere. Un messaggio restituito da **list\_messages()** diventa invisibile a qualsiasi altro codice che legge i messaggi dalla stessa coda. Passare il timeout di visibilità in secondi come parametro.

2. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche **delete_message()**.

Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio abbia esito negativo a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice chiama **delete\_message()** immediatamente dopo l'elaborazione del messaggio.

	messages = azure_queue_service.list_messages("test-queue", 30)
	azure_queue_service.delete_message("test-queue", 
	  messages[0].id, messages[0].pop_receipt)

## <a id="how-to-change-the-contents-of-a-queued-message"></a>Procedura: Cambiare il contenuto di un messaggio in coda

È possibile cambiare il contenuto di un messaggio inserito nella coda. La coda seguente usa il metodo **update_message()** per aggiornare un messaggio. Il metodo restituirà una tupla contenente il Pop Receipt del messaggio in coda e un valore di data e ora UTC che rappresenta il momento in cui il messaggio sarà visibile nella coda.

	message = azure_queue_service.list_messages("test-queue", 30)
	pop_receipt, time_next_visible = azure_queue_service.update_message(
	  "test-queue", message.id, message.pop_receipt, "updated test message", 
	  30)

## <a id="how-to-additional-options-for-dequeuing-messages"></a>Procedura: Opzioni aggiuntive per rimuovere i messaggi dalla coda

È possibile personalizzare il recupero di messaggi da una coda in due modi.

1. È possibile recuperare un batch di messaggi.

2. È possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio.

Nell'esempio di codice seguente viene usato il metodo **list\_messages()** per recuperare 15 messaggi con una sola chiamata. Quindi, ogni messaggio viene stampato ed eliminato. Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

	azure_queue_service.list_messages("test-queue", 300
	  {:number_of_messages => 15}).each do |m|
	  puts m.message_text
	  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
	end

## <a id="how-to-get-the-queue-length"></a>Procedura: Recuperare la lunghezza della coda

È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il metodo **get\_queue\_metadata()** chiede al servizio di accodamento di restituire il conteggio approssimativo dei messaggi e i metadati relativi alla coda.

	message_count, metadata = azure_queue_service.get_queue_metadata(
	  "test-queue")

## <a id="how-to-delete-a-queue"></a>Procedura: Eliminare una coda

Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo **delete\_queue()** sull'oggetto coda.

	azure_queue_service.delete_queue("test-queue")

## <a id="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione di accodamento, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.

- Vedere le informazioni di riferimento in MSDN: [Archiviazione e accesso ai dati in Azure](http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx)
- [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Repository [Azure SDK per Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) su GitHub

Per un confronto tra il Servizio di accodamento di Azure trattato in questo articolo e le code del bus di servizio di Azure trattate nell'articolo [Come usare le code del bus di servizio](/it-it/develop/ruby/how-to-guides/service-bus-queues/) vedere [Analogie e differenze tra le code di Azure e le code di Service Bus](http://msdn.microsoft.com/it-it/library/windowsazure/hh767287.aspx)

<!--HONumber=35_1-->
