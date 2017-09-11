---
title: Come usare l'archiviazione code da Ruby | Microsoft Docs
description: Informazioni su come usare il servizio di accodamento di Azure per creare ed eliminare code e per inserire, visualizzare ed eliminare messaggi. Gli esempi sono scritti in Ruby.
services: storage
documentationcenter: ruby
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 59c2d81b-db9c-46ee-ade2-2f0caae6b1e6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: robinsh
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: b1a7dd36af6c45bf085342cdf9c1c926a5040792
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="how-to-use-queue-storage-from-ruby"></a>Come usare l'archiviazione di accodamento da Ruby
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Panoramica
In questa guida viene illustrato come eseguire scenari comuni del servizio di archiviazione di accodamento di Microsoft Azure. Gli esempi sono scritti usando l'API Ruby di Azure.
Gli scenari illustrati includono **inserimento**, **visualizzazione**, **recupero** ed **eliminazione** dei messaggi in coda, oltre alla **creazione ed eliminazione di code**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Creare un'applicazione Ruby
Creare un'applicazione Ruby. Per istruzioni, vedere l'articolo relativo all' [applicazione Web Ruby on Rails in una macchina virtuale di Azure](../../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-access-storage"></a>Configurare l'applicazione per l'accesso all'archiviazione
Per utilizzare l'archiviazione di Azure, è necessario scaricare e utilizzare il pacchetto Ruby Azure, che comprende un set di librerie che comunicano con i servizi di archiviazione REST.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizzare RubyGems per ottenere il pacchetto
1. Usare un'interfaccia della riga di comando, ad esempio **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).
2. Digitare "gem install azure" nella finestra di comando per installare la gemma e le dipendenze.

### <a name="import-the-package"></a>Importare il pacchetto
Usando l'editor di testo preferito aggiungere quanto segue alla parte superiore del file Ruby dove si intende usare l'archiviazione:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Configurare una connessione di archiviazione di Azure
Il modulo di Azure leggerà le variabili di ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS_KEY** per ottenere le informazioni necessarie per la connessione all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative all'account prima di utilizzare **Azure::QueueService** con il codice seguente:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Per ottenere questi valori da un account di archiviazione classico o di Resource Manager nel portale di Azure:

1. Accedere al [Portale di Azure](https://portal.azure.com).
2. Passare all'account di archiviazione che si desidera utilizzare.
3. Nel pannello Impostazioni a destra fare clic su **Chiavi di accesso**.
4. Nel pannello Chiavi di accesso visualizzato notare la chiave di accesso 1 e la chiave di accesso 2. È possibile usare una di queste indifferentemente. 
5. Fare clic sull'icona Copia per copiare la chiave negli Appunti. 

## <a name="how-to-create-a-queue"></a>Procedura: creare una coda
La coda seguente crea un oggetto **Azure::QueueService** che consente di usare le code.

```ruby
azure_queue_service = Azure::QueueService.new
```

Usare il metodo **create_queue()** per creare una coda con il nome specificato.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Procedura: inserire un messaggio in una coda
Per inserire un messaggio in una coda, usare il metodo **create_message()** per creare un nuovo messaggio e aggiungerlo alla coda.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Procedura: visualizzare il messaggio successivo
È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo dalla coda chiamando il metodo **peek\_messages()** . Per impostazione predefinita, **peek\_messages()** visualizza un singolo messaggio. È anche possibile specificare il numero di messaggi che si desidera visualizzare.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Procedura: rimuovere il messaggio successivo dalla coda
È possibile rimuovere un messaggio da una coda in due passaggi.

1. Per impostazione predefinita, chiamando **list\_messages()** si ottiene il messaggio successivo in una coda. È anche possibile specificare il numero di messaggi che si desidera ottenere. I messaggi restituiti da **list\_messages()** diventano invisibili a qualsiasi altro codice che legge i messaggi dalla stessa coda. Passare il timeout di visibilità in secondi come parametro.
2. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche **delete_message**.

Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio abbia esito negativo a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice chiama **delete\_message()** immediatamente dopo l'elaborazione del messaggio.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Procedura: cambiare il contenuto di un messaggio accodato
È possibile cambiare il contenuto di un messaggio inserito nella coda. Il codice seguente usa il metodo **update_message()** per aggiornare un messaggio. Il metodo restituirà una tupla contenente il Pop Receipt del messaggio in coda e un valore di data e ora UTC che rappresenta il momento in cui il messaggio sarà visibile nella coda.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Procedura: opzioni aggiuntive per rimuovere i messaggi dalla coda
È possibile personalizzare il recupero di messaggi da una coda in due modi.

1. È possibile recuperare un batch di messaggi.
2. È possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio.

Nell'esempio di codice seguente viene utilizzato il metodo **list\_messages()** per recuperare 15 messaggi con una sola chiamata. Quindi, ogni messaggio viene stampato ed eliminato. Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Procedura: recuperare la lunghezza delle code
È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il metodo **get\_queue\_metadata()** chiede al servizio di accodamento di restituire il conteggio approssimativo dei messaggi e i metadati relativi alla coda.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Procedura: eliminare una coda
Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo **delete\_queue()** sull'oggetto coda.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base dell'archiviazione di accodamento, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.

* [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/)
* Archivio [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) su GitHub

Per un confronto tra il Servizio di accodamento di Azure discusso in questo articolo e le code del bus di servizio di Azure discusse nell'articolo [Come usare le code del bus di servizio](/develop/ruby/how-to-guides/service-bus-queues/) vedere [Analogie e differenze tra le code di Azure e le code del bus di servizio](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
