---
title: Introduzione a strumenti di sviluppo dello Stack di archiviazione di Azure
description: Linee guida per iniziare a utilizzare gli strumenti di sviluppo dello Stack di archiviazione di Azure
services: azure-stack
author: mabriggs
ms.author: mabrigg
ms.date: 02/21/2018
ms.topic: get-started-article
ms.service: azure-stack
manager: femila
ms.reviewer: xiaofmao
ms.openlocfilehash: 81c62fc569e9f758d08bfca0bdfc5bcc9ed5860f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Introduzione a strumenti di sviluppo dello Stack di archiviazione di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Stack di Microsoft Azure fornisce un set di servizi di archiviazione, inclusa l'archiviazione Blob di Azure, tabelle e code.

In questo articolo vengono fornite indicazioni rapido su come iniziare a utilizzare gli strumenti di sviluppo dello Stack di archiviazione di Azure. È possibile trovare ulteriori informazioni e codice di esempio nelle esercitazioni di archiviazione di Azure corrispondente.

Vi sono note le differenze tra l'archiviazione di Azure e Azure Stack di archiviazione, inclusi alcuni requisiti specifici per ogni piattaforma. Ad esempio, esistono requisiti per il suffisso dell'endpoint specifico per lo Stack di Azure e librerie client specifico. Per ulteriori informazioni, vedere [dello Stack di archiviazione di Azure: considerazioni e le differenze](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Librerie client di Azure

Le versioni supportate di API REST dello Stack di archiviazione di Azure sono 2017-04-17, 2016-05-31, 11-12-2015, 2015-07-08, 2015-04-05 per l'aggiornamento 1802 o versioni successive e 2015-04-05 per le versioni precedenti. Gli endpoint di Azure Stack non dispone di parità completa con la versione più recente dell'API REST di archiviazione Azure. Per le librerie client di archiviazione, è necessario essere a conoscenza della versione è compatibile con l'API REST.

### <a name="1802-update-or-newer-versions"></a>1802 update o versioni successive

| Libreria client | Versione supportata di Azure Stack | Collegamento | Specifica dell'endpoint |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Pacchetto NuGet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0<br> <br>Versione di GitHub:<br>https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0 | file app. config |
| Java | 6.1.0 | Pacchetto di Maven:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0<br> <br>Versione di GitHub:<br>https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0 | Configurazione della stringa di connessione |
| Node.js | 2.7.0 | Collegamento NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Esecuzione: `npm install azure-storage@2.7.0`)<br> <br>Versione di Github:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0 | Dichiarazione dell'istanza del servizio |
| C++ | 3.1.0 | Pacchetto NuGet:<br>https://www.nuget.org/packages/wastorage.v140/3.1.0<br> <br>Versione di GitHub:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0 | Configurazione della stringa di connessione |
| PHP | 1.0.0 | Versione di GitHub:<br>Comuni: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common<br>Blob: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob<br>Coda:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue<br>Tabella: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table<br> <br>Installazione tramite Composer (per ulteriori informazioni, [vedere i dettagli di seguito](#install-php-client-via-composer---current).) | Configurazione della stringa di connessione |
| Python | 1.0.0 | Versione di GitHub:<br>Comuni:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common<br>BLOB:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob<br>Coda:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue | Dichiarazione dell'istanza del servizio |
| Ruby | 1.0.1 | Pacchetto RubyGems:<br>Comuni:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>Blob: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>Queue: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>Table: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>Versione di GitHub:<br>Comuni: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>Blob: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>Coda: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>Tabella: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | Configurazione della stringa di connessione |

#### <a name="install-php-client-via-composer---current"></a>Installare PHP client tramite creazione - corrente

Per installare tramite Composer: (take blob esempio).

1. Creare un file denominato **composer.json** nella radice del progetto con codice riportato di seguito:
  ```php
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
  ```
2. Scaricare [composer.phar](http://getcomposer.org/composer.phar) per la radice del progetto.
3. Eseguire: `php composer.phar install`.

### <a name="previous-versions"></a>Versioni precedenti

|Libreria client|Versione supportata di Azure Stack|Collegamento|Specifica dell'endpoint|
|---------|---------|---------|---------|
|.NET     |6.2.0|Pacchetto NuGet:<br>[https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>Versione di GitHub:<br>[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|file app. config|
|Java|4.1.0|Pacchetto di Maven:<br>[http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>Versione di GitHub:<br> [https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|Configurazione della stringa di connessione|
|Node.js     |1.1.0|Collegamento NPM:<br>[https://www.npmjs.com/package/azure-storage](https://www.npmjs.com/package/azure-storage)<br>(esecuzione: `npm install azure-storage@1.1.0)`<br><br>Versione di Github:<br>[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Dichiarazione dell'istanza del servizio||C++|2.4.0|Pacchetto NuGet:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Versione di GitHub:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Configurazione della stringa di connessione|
|C++|2.4.0|Pacchetto NuGet:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Versione di GitHub:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Configurazione della stringa di connessione|
|PHP|0.15.0|Versione di GitHub:<br>[https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Installazione tramite Composer (vedere i dettagli riportati di seguito)|Configurazione della stringa di connessione|
|Python     |0.30.0|Pacchetto PIP:<br> [https://pypi.python.org/pypi/azure-storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(Esecuzione: `pip install -v azure-storage==0.30.0)`<br><br>Versione di GitHub:<br> [https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Dichiarazione dell'istanza del servizio|
|Ruby|0.12.1<br>Preview|Pacchetto RubyGems:<br> [https://rubygems.org/gems/azure-storage/versions/0.12.1.preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>Versione di GitHub:<br> [https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|Configurazione della stringa di connessione|

#### <a name="install-php-client-via-composer---previous"></a>Installare PHP client tramite creazione - precedente

Per installare tramite creazione:

1. Creare un file denominato **composer.json** nella radice del progetto con codice riportato di seguito:
  ```php
    {
          "require":{
          "Microsoft/azure-storage":"0.15.0"
          }
    }
  ```
2. Scaricare [composer.phar](http://getcomposer.org/composer.phar) nella radice del progetto.
3. Eseguire: `php composer.phar install`.

## <a name="endpoint-declaration"></a>Dichiarazione di endpoint

Un endpoint di Azure Stack comprende due parti: il nome di un'area e il dominio di Azure Stack.
Nel Kit di sviluppo dello Stack di Azure, l'endpoint predefinito è **local.azurestack.external**.
Contattare l'amministratore del cloud se non si è sicuri sull'endpoint.

## <a name="examples"></a>Esempi


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
* [Come usare l'archiviazione BLOB da Node.js](../../storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
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