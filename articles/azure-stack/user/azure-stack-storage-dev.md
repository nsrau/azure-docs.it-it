---
title: Introduzione a strumenti di sviluppo dello Stack di archiviazione di Azure
description: Linee guida per iniziare a utilizzare gli strumenti di sviluppo dello Stack di archiviazione di Azure
services: azure-stack
author: xiaofmao
ms.author: xiaofmao
ms.date: 9/25/2017
ms.topic: get-started-article
ms.service: azure-stack
ms.openlocfilehash: 5b2898c64c0f1b5d804e63fa4e4e1218fa7a672c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Introduzione a strumenti di sviluppo dello Stack di archiviazione di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*


Stack di Microsoft Azure fornisce un set di servizi di archiviazione, inclusa l'archiviazione Blob di Azure, tabelle e code.

In questo articolo vengono fornite indicazioni rapido su come iniziare a utilizzare gli strumenti di sviluppo dello Stack di archiviazione di Azure. È possibile trovare ulteriori informazioni e codice di esempio nelle esercitazioni di archiviazione di Azure corrispondente.

Vi sono note le differenze tra l'archiviazione di Azure e Azure Stack di archiviazione, inclusi alcuni requisiti specifici per ogni piattaforma. Ad esempio, esistono requisiti per il suffisso dell'endpoint specifico per lo Stack di Azure e librerie client specifico. Per ulteriori informazioni, vedere [dello Stack di archiviazione di Azure: considerazioni e le differenze](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Librerie client di Azure
La versione supportata di API REST dello Stack di archiviazione di Azure è 2015-04-05. Non dispone di parità completa con la versione più recente dell'API REST di archiviazione Azure. Pertanto, per le librerie client di archiviazione, è necessario essere a conoscenza della versione è compatibile con REST API 2015-04-05.


|Libreria client|Versione supportata di Azure Stack|Collegamento|Specifica dell'endpoint|
|---------|---------|---------|---------|
|.NET     |6.2.0|Pacchetto NuGet:<br>[https://www.NuGet.org/Packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>Versione di GitHub:<br>[https://github.com/Azure/Azure-Storage-NET/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|file app. config|
|Java|4.1.0|Pacchetto di Maven:<br>[http://mvnrepository.com/Artifact/com.microsoft.Azure/Azure-Storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>Versione di GitHub:<br> [https://github.com/Azure/Azure-Storage-Java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|Installazione di stringa di connessione|
|Node.js     |1.1.0|Collegamento NPM:<br>[https://www.npmjs.com/package/Azure-Storage](https://www.npmjs.com/package/azure-storage)<br>(esecuzione:`npm install azure-storage@1.1.0)`<br><br>Versione di Github:<br>[https://github.com/Azure/Azure-Storage-Node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Dichiarazione dell'istanza del servizio||C++|2.4.0|Pacchetto NuGet:<br>[https://www.NuGet.org/Packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Versione di GitHub:<br>[https://github.com/Azure/Azure-Storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Installazione di stringa di connessione|
|C++|2.4.0|Pacchetto NuGet:<br>[https://www.NuGet.org/Packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Versione di GitHub:<br>[https://github.com/Azure/Azure-Storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Installazione di stringa di connessione|
|PHP|0.15.0|Versione di GitHub:<br>[https://github.com/Azure/Azure-Storage-PHP/releases/tag/V0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Installazione tramite Composer (vedere i dettagli riportati di seguito)|Installazione di stringa di connessione|
|Python     |0.30.0|Pacchetto PIP:<br> [https://pypi.Python.org/pypi/Azure-Storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(Esecuzione:`pip install -v azure-storage==0.30.0)`<br><br>Versione di GitHub:<br> [https://github.com/Azure/Azure-Storage-Python/releases/tag/V0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Dichiarazione dell'istanza del servizio|
|Ruby|0.12.1<br>Preview|Pacchetto RubyGems:<br> [https://rubygems.org/GEMS/Azure-Storage/Versions/0.12.1.Preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>Versione di GitHub:<br> [https://github.com/Azure/Azure-Storage-Ruby/releases/tag/V0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|Installazione di stringa di connessione|

> [!NOTE]
> Dettagli PHP<br><br>
>Per installare tramite creazione:
>1. Creare un file denominato `composer.json` nella radice del progetto con codice riportato di seguito:<br>
>
>   ```
>   {
>       "require":{
>           "Microsoft/azure-storage":"0.15.0"
>        }
>    }
>   ```
>
>2. Scaricare [composer.phar](http://getcomposer.org/composer.phar) nella radice del progetto.
>3. Eseguire: `php composer.phar install`.
>


## <a name="endpoint-declaration"></a>Dichiarazione di endpoint
Un endpoint di Azure Stack comprende due parti: il nome di un'area e il dominio di Azure Stack.
Nel Kit di sviluppo dello Stack di Azure, l'endpoint predefinito è **local.azurestack.external**.
Contattare l'amministratore del cloud se non si è sicuri sull'endpoint.

## <a name="examples"></a>esempi


### <a name="net"></a>.NET

Per lo Stack di Azure, viene specificato il suffisso dell'endpoint nel file app. config:

```
<add key="StorageConnectionString" 
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```
### <a name="java"></a>Java

Per lo Stack di Azure, viene specificato il suffisso dell'endpoint nel programma di installazione di stringa di connessione:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Per lo Stack di Azure, il suffisso dell'endpoint viene specificato nell'istanza di dichiarazione:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```
### <a name="c"></a>C++

Per lo Stack di Azure, viene specificato il suffisso dell'endpoint nel programma di installazione di stringa di connessione:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Per lo Stack di Azure, viene specificato il suffisso dell'endpoint nel programma di installazione di stringa di connessione:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Per lo Stack di Azure, il suffisso dell'endpoint viene specificato nell'istanza di dichiarazione:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```
### <a name="ruby"></a>Ruby

Per lo Stack di Azure, viene specificato il suffisso dell'endpoint nel programma di installazione di stringa di connessione:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Archiviazione BLOB

Le esercitazioni di archiviazione Blob di Azure seguenti sono applicabili allo Stack di Azure. Si noti il requisito di suffisso endpoint specifico per lo Stack di Azure descritto nella precedente [esempi](#examples) sezione.

* [Introduzione all'archiviazione BLOB di Azure con .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Come usare l'archiviazione BLOB da Java](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [Come usare l'archiviazione di Blob da Node.js]... /.. / storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Come usare l'archiviazione BLOB da C++](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Come usare l'archiviazione BLOB da PHP](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [Come utilizzare l'archiviazione Blob di Azure da Python](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [Come usare l'archiviazione BLOB da Ruby](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>Archiviazione code

Le esercitazioni di archiviazione della coda di Azure seguenti sono applicabili allo Stack di Azure. Si noti il requisito di suffisso endpoint specifico per lo Stack di Azure descritto nella precedente [esempi](#examples) sezione.

* [Introduzione all'archiviazione code di Azure con .NET](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Come usare l'archiviazione di accodamento da Java](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [Come usare l'archiviazione di accodamento da Node.js](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [Come usare l'archiviazione delle code da C++](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [Come usare l'archiviazione di accodamento da PHP](../../storage/queues/storage-php-how-to-use-queues.md)
* [Come usare l'archiviazione di accodamento da Python](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [Come usare l'archiviazione di accodamento da Ruby](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)


## <a name="table-storage"></a>Archiviazione tabelle

Le esercitazioni di archiviazione tabelle di Azure seguenti sono applicabili allo Stack di Azure. Si noti il requisito di suffisso endpoint specifico per lo Stack di Azure descritto nella precedente [esempi](#examples) sezione.

* [Introduzione all'archiviazione tabelle di Azure con .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Come usare l'archiviazione tabelle da Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Come usare l'archiviazione tabelle di Azure da Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Come usare l'archiviazione di tabelle da C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Come usare l'archiviazione tabelle da PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Come usare l'archiviazione tabelle di Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Come usare l'archiviazione tabelle da Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Archiviazione di Microsoft Azure](../../storage/common/storage-introduction.md)