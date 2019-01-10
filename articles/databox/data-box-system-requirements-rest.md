---
title: Requisiti di archiviazione BLOB di Microsoft Azure Data Box | Microsoft Docs
description: Informazioni sulle versioni supportate di API, SDK e librerie client per l'archiviazione BLOB di Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/11/2018
ms.author: alkohli
ms.openlocfilehash: e7c2cc0c0ffaae11bd7bf5113c942cdb98397201
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550930"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Requisiti di archiviazione BLOB di Azure Data Box

Questo articolo elenca le versioni di API, SDK e strumenti di Azure supportati dall'archiviazione BLOB di Azure Data Box. L'archiviazione BLOB di Azure Data Box fornisce funzionalità di gestione BLOB dotata di semantica coerente con Azure. Inoltre questo articolo riepiloga le differenze note di archiviazione BLOB di Azure Data Box rispetto ai servizi di Archiviazione di Azure.

È consigliabile esaminare attentamente queste informazioni prima di connettersi all'archivio BLOB di Azure Data Box e farvi riferimento ogni volta che sia necessario.


## <a name="storage-differences"></a>Differenze di archiviazione

|     Funzionalità                                             |     Archiviazione di Azure                                     |     Archiviazione BLOB di Azure Data Box |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Archiviazione file di Azure                                   |    Condivisioni di file SMB basate sul cloud supportate              |    Non supportate      |
|    Crittografia del servizio per dati inattivi                  |    Crittografia AES a 256 bit                             |    Crittografia AES a 256 bit |
|    Tipo di account di archiviazione                                 |    Account di archiviazione BLOB di Azure e per uso generico    |    Solo uso generico v1|
|    Nome del BLOB                                            |    1.024 caratteri (2.048 byte)                     |    880 caratteri (1.760 byte)|
|    Dimensioni massime di BLOB in blocchi                              |    4,75 TB (100 MB x 50.000 blocchi)                   |    4,75 TB (100 MB x 50.000 blocchi) per Azure Data Box v 1.7 e successive.|
|    Dimensioni massime di BLOB di pagine                               |    8 TB                                               |    1 TB                   |
|    Dimensioni pagina del BLOB di pagine                                  |    512 byte                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Versioni API supportate

Le versioni seguenti del servizio di Archiviazione di Azure sono supportate dall'archiviazione BLOB di Azure Data Box:

Versione di anteprima pubblica (Azure Data Box 1.7 e versioni successive)

- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)

## <a name="supported-sdk-versions"></a>Versioni di SDK supportate

|     Libreria client     |     Versione supportata di archiviazione BLOB di Azure Data Box     |     Collegamento             |     Specifica dell'endpoint         |
|------------------------|-------------------------------------------------|---------------------------------------------|------------------------------------|
|    .NET                |    Dalla versione 6.2.0 alla 8.7.0.                         |    Pacchetto NuGet:   https://www.nuget.org/packages/WindowsAzure.Storage/ <br>Versione di GitHub:   https://github.com/Azure/azure-storage-net/releases                                                                      |    file app.config                 |
|    Java                |    Dalla versione 4.1.0 alla 6.1.0                          |    Pacchetto Maven:   http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage   <br>Versione di GitHub:   https://github.com/Azure/azure-storage-java/releases                                                      |    Configurazione della stringa di connessione         |
|    Node.js             |    Dalla versione 1.1.0 alla 2.7.0                          |    Collegamento NPM:   https://www.npmjs.com/package/azure-storage   (Ad esempio: eseguire "npm install azure-storage@2.7.0")   <br>Versione di GitHub:   https://github.com/Azure/azure-storage-node/releases                            |    Dichiarazione istanza del servizio    |
|    C++                 |    Dalla versione 2.4.0 alla 3.1.0                          |    Pacchetto NuGet:   https://www.nuget.org/packages/wastorage.v140/   <br>Versione di GitHub:   https://github.com/Azure/azure-storage-cpp/releases                                                                            |    Configurazione della stringa di connessione         |
|    PHP                 |    Dalla versione 0.15.0 alla 1.0.0                         |    Versione di GitHub:   https://github.com/Azure/azure-storage-php/releases   <br>Installazione tramite Composer (vedere i dettagli sotto)                                                                                                   |    Configurazione della stringa di connessione         |
|    Python              |    Dalla versione 0.30.0 alla 1.0.0                         |    Versione di GitHub:   https://github.com/Azure/azure-storage-python/releases                                                                                                                                              |    Dichiarazione istanza del servizio    |
|    Ruby                |    Dalla versione 0.12.1 alla 1.0.1                         |    Pacchetto RubyGems:<br>Comuni:   https://rubygems.org/gems/azure-storage-common/   <br>BLOB: https://rubygems.org/gems/azure-storage-blob/      <br>Versione di GitHub:   https://github.com/Azure/azure-storage-ruby/releases    |                                   |

## <a name="supported-azure-client-libraries"></a>Librerie client di Azure supportate

Per l'archiviazione BLOB di Azure Data Box sono disponibili librerie client specifiche e requisiti specifici per il suffisso dell'endpoint.

Le versioni supportate di API REST per l'archiviazione BLOB di Azure Data Box sono 2017-04-17, 2016-05-31, 2015-12-11, 2015-07-08 e 2015-04-05 per la versione 1.7 di Azure Data Box e successive. Gli endpoint di archiviazione BLOB di Azure Data Box non sono del tutto equivalenti alla versione più recente dell'API REST di Archiviazione BLOB di Azure. Per le librerie client di archiviazione è necessario essere a conoscenza della versione compatibile con l'API REST.

### <a name="azure-data-box-17-onwards"></a>Azure Data Box 1.7 e versioni successive

| Libreria client     |Versione supportata di archiviazione BLOB di Azure Data Box     | Collegamento   |     Specifica dell'endpoint      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    8.7.0                                           |    Pacchetto NuGet:   https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0    <br>Versione di GitHub:   https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0                                                                                                                                                                                               |    file app.config                 |
|    Java                |    6.1.0                                           |    Pacchetto Maven:   http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>Versione di GitHub:   https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0                                                                                                                                                                              |    Configurazione della stringa di connessione         |
|    Node.js             |    2.7.0                                           |    Collegamento NPM:   https://www.npmjs.com/package/azure-storage   (Eseguire: npm installazure-storage@2.7.0)   <br>Versione di GitHub:   https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0                                                                                                                                                                        |    Dichiarazione istanza del servizio    |
|    C++                 |    3.1.0                                           |    Pacchetto NuGet:   https://www.nuget.org/packages/wastorage.v140/3.1.0   <br>Versione di GitHub:   https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0                                                                                                                                                                                                     |    Configurazione della stringa di connessione         |
|    PHP                 |    1.0.0                                           |    Versione di GitHub:<br>Comuni: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common   <br>BLOB: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob      <br>Installazione tramite Composer (per altre informazioni, vedere i dettagli sotto).                                                                                                             |    Configurazione della stringa di connessione         |
|    Python              |    1.0.0                                           |    Versione di GitHub:<br>Comuni:   https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>BLOB:   https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob                                                                                                                                                                          |    Dichiarazione istanza del servizio    |
|    Ruby                |    1.0.1                                           |    Pacchetto RubyGems:<br>Comuni:   https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>BLOB: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>Versione di GitHub:<br>Comuni: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>BLOB: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Configurazione della stringa di connessione         |



### <a name="install-php-client-via-composer---current"></a>Installare il client PHP tramite Composer - corrente

Per l'installazione tramite Composer: (prendere BLOB come esempio).
Creare un file denominato composer.json nella radice del progetto con il codice seguente:

```
 {
   "require": {
   "Microsoft/azure-storage-blob":"1.0.0"
   }
```

Scaricare `composer.phar` nella radice del progetto.

Eseguire: php composer.phar install.

### <a name="endpoint-declaration"></a>Dichiarazione dell'endpoint

Un endpoint di archiviazione BLOB di Azure Data Box include due parti: il nome di un'area e il dominio di Data Box. Nell'SDK di archiviazione BLOB di Azure Data Box l'endpoint predefinito è <serial no. of the device>.microsoftdatabox.com.  Per altre informazioni sull'endpoint di servizio BLOB, consultare [Connect via Data Box Blob storage](data-box-deploy-copy-data-via-rest.md) (Connettersi tramite l'archiviazione BLOB di Azure Data Box).
 
## <a name="examples"></a>Esempi

### <a name="net"></a>.NET

Per l'archiviazione BLOB di Azure Data Box, il suffisso dell'endpoint viene specificato nel file `app.config`:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Per l'archiviazione BLOB di Azure Data Box, il suffisso dell'endpoint viene specificato nella configurazione della stringa di connessione:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Per l'archiviazione BLOB di Azure Data Box, il suffisso dell'endpoint viene specificato nell'istanza di dichiarazione:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Per l'archiviazione BLOB di Azure Data Box, il suffisso dell'endpoint viene specificato nella configurazione della stringa di connessione:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Per l'archiviazione BLOB di Azure Data Box, il suffisso dell'endpoint viene specificato nella configurazione della stringa di connessione:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Per l'archiviazione BLOB di Azure Data Box, il suffisso dell'endpoint viene specificato nell'istanza di dichiarazione:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Per l'archiviazione BLOB di Azure Data Box, il suffisso dell'endpoint viene specificato nella configurazione della stringa di connessione:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire Azure Data Box](data-box-deploy-ordered.md)