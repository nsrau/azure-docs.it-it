---
title: Come usare l'archiviazione tabelle di Azure con Ruby | Microsoft Docs
description: Archiviare dati non strutturati nel cloud con il servizio di archiviazione tabelle di Azure, ovvero un archivio dati NoSQL.
services: cosmos-db
documentationcenter: ruby
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 047cd9ff-17d3-4c15-9284-1b5cc61a3224
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: b1ad9fc7b609209ca867e64226c3a0e393081408
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="how-to-use-azure-table-storage-with-ruby"></a>Come usare l'Archiviazione tabelle di Azure con Ruby
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>Panoramica
Questa guida illustra come eseguire scenari comuni con il servizio tabelle di Azure. Gli esempi sono scritti utilizzando l'API Ruby. Gli scenari presentati includono **creazione ed eliminazione di una tabella, inserimento di entità ed esecuzione di query sulle entità in una tabella**.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Creare un'applicazione Ruby
Per istruzioni su come creare un'applicazione Ruby, vedere [Ruby on Rails Web application on an Azure VM](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md) (Applicazione Web Ruby on Rails in una VM di Azure).

## <a name="configure-your-application-to-access-storage"></a>Configurare l'applicazione per l'accesso all'archiviazione
Per usare l'archiviazione di Azure, è necessario scaricare e usare il pacchetto Ruby Azure, che comprende un set di pratiche librerie che comunicano con i servizi di archiviazione REST.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizzare RubyGems per ottenere il pacchetto
1. Usare un'interfaccia della riga di comando, ad esempio **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).
2. Digitare **gem install azure** nella finestra di comando per installare la gemma e le dipendenze.

### <a name="import-the-package"></a>Importare il pacchetto
Usando l'editor di testo preferito aggiungere quanto segue alla parte superiore del file Ruby dove si intende usare l'archiviazione:

```ruby
require "azure"
```

## <a name="set-up-an-azure-storage-connection"></a>Configurare una connessione di archiviazione di Azure
Il modulo di Azure leggerà le variabili di ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS\_KEY** per ottenere le informazioni necessarie per la connessione all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative all'account prima di utilizzare **Azure::TableService** con il codice seguente:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your azure storage access key>"
```

Per ottenere questi valori da un account di archiviazione classico o di Resource Manager nel portale di Azure:

1. Accedere al [Portale di Azure](https://portal.azure.com).
2. Passare all'account di archiviazione che si desidera utilizzare.
3. Nel pannello Impostazioni a destra fare clic su **Chiavi di accesso**.
4. Nel pannello Chiavi di accesso visualizzato notare la chiave di accesso 1 e la chiave di accesso 2. È possibile usare una di queste indifferentemente.
5. Fare clic sull'icona Copia per copiare la chiave negli Appunti.

## <a name="create-a-table"></a>Creare una tabella
L'oggetto **Azure::TableService** consente di utilizzare tabelle ed entità. Per creare una tabella, usare il metodo **create\_table()**. Nell'esempio seguente viene creata una tabella o stampato l'eventuale errore.

```ruby
azure_table_service = Azure::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella
Per aggiungere un'entità, creare innanzitutto un oggetto hash che definisca le proprietà dell'entità. Si noti che per ogni entità è necessario specificare un oggetto **PartitionKey** e un oggetto **RowKey**. Si tratta di identificatori univoci dell'entità e sono valori che possono essere interrogati molto più velocemente di altre proprietà. Archiviazione Azure utilizza **PartitionKey** per distribuire automaticamente le entità della tabella su molti nodi di archiviazione. Le entità con lo stesso oggetto **PartitionKey** vengono archiviate nello stesso nodo. RowKey **è l'ID univoco dell'entità all'interno della partizione cui appartiene.**

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>Aggiornare un'entità
Esistono vari metodi per aggiornare un'entità esistente:

* **update\_entity():** aggiorna un'entità esistente sostituendola.
* **merge\_entity():** aggiorna un'entità esistente unendovi i nuovi valori delle proprietà.
* **insert\_or\_merge\_entity():** aggiorna un'entità esistente sostituendola. Se non esiste alcuna entità, ne verrà inserita una nuova:
* **insert\_or\_replace\_entity():** aggiorna un'entità esistente unendovi i nuovi valori delle proprietà. Se non esiste alcuna entità, ne verrà inserita una nuova.

Nell'esempio seguente viene dimostrato l'aggiornamento di un'entità mediante l'uso di **update\_entity()**:

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

Con **update\_entity()** e **merge\_entity()**, se l'entità che si sta aggiornando non esiste, l'operazione di aggiornamento avrà esito negativo. Se pertanto si desidera archiviare un'entità indipendentemente dal fatto che esista o meno, è necessario usare **insert\_or\_replace\_entity()** oppure **insert\_or\_merge\_entity()**.

## <a name="work-with-groups-of-entities"></a>Usare i gruppi di entità
È talvolta consigliabile inviare più operazioni in un batch per garantire l'elaborazione atomica da parte del server. A tale scopo, è necessario prima creare un oggetto **Batch** e quindi usare il metodo **execute\_batch()** su **TableService**. Nell'esempio seguente viene dimostrato l'invio di due entità con RowKey 2 e 3 in un batch: Si noti che funziona solo per le entità con lo stesso oggetto PartitionKey.

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>Eseguire una query su un'entità
Per eseguire una query su un'entità in una tabella, usare il metodo **get\_entity()** passando il nome tabella e i parametri **PartitionKey** e **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Eseguire query su un set di entità
Per eseguire query su un set di entità in una tabella, creare un oggetto hash di query e usare il metodo **query\_entities()**. Nell'esempio seguente viene dimostrato l'invio di tutte le entità con lo stesso oggetto **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Se il risultato impostato è troppo grande affinché sia restituito da un'unica query, verrà restituito un token di continuazione per recuperare le pagine successive.
>
>

## <a name="query-a-subset-of-entity-properties"></a>Eseguire query su un subset di proprietà di entità
Mediante una query su una tabella è possibile recuperare solo alcune proprietà da un'entità. Questa tecnica, denominata "proiezione", consente di ridurre la larghezza di banda e di migliorare le prestazioni della query, in particolare per entità di grandi dimensioni. Utilizzare la clausola select e passare i nomi delle proprietà da inoltrare al client.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Eliminare un'entità
Per eliminare un'entità, usare il metodo **delete\_entity()**. È necessario passare il nome della tabella contenente l'entità e i parametri PartitionKey e RowKey dell'entità.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Eliminare una tabella
Per eliminare una tabella, usare il metodo **delete\_table()** e passare il nome della tabella che si desidera eliminare.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>Passaggi successivi

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma gratuita di Microsoft che consente di rappresentare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.
* [Azure SDK per Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) su GitHub

