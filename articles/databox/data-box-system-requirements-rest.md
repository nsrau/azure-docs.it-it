---
title: Requisiti di archiviazione BLOB di Microsoft Azure Data Box | Microsoft Docs
description: Informazioni sulle versioni supportate di API, SDK e librerie client per l'archiviazione BLOB di Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/05/2020
ms.author: alkohli
ms.openlocfilehash: ac5f2de383066d6ee399dac3b0ad8c365b2e72bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91744101"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Requisiti di archiviazione BLOB di Azure Data Box

Questo articolo elenca le versioni delle API di Azure, le librerie client di Azure e gli strumenti supportati con l'archivio BLOB Data Box. L'archiviazione BLOB di Azure Data Box fornisce funzionalità di gestione BLOB dotata di semantica coerente con Azure. Inoltre questo articolo riepiloga le differenze note di archiviazione BLOB di Azure Data Box rispetto ai servizi di Archiviazione di Azure.

È consigliabile esaminare attentamente queste informazioni prima di connettersi all'archivio BLOB di Azure Data Box e farvi riferimento ogni volta che sia necessario.


## <a name="storage-differences"></a>Differenze di archiviazione

|     Funzionalità                                             |     Archiviazione di Azure                                     |     Archiviazione BLOB di Azure Data Box |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Archiviazione file di Azure                                   |    Condivisioni di file SMB basate sul cloud supportate              |    Non supportato      |
|    Crittografia del servizio per dati inattivi                  |    Crittografia AES a 256 bit                             |    Crittografia AES a 256 bit |
|    Tipo di account di archiviazione                                 |    Account di archiviazione BLOB di Azure e per uso generico    |    Solo uso generico v1|
|    Nome del BLOB                                            |    1.024 caratteri (2.048 byte)                     |    880 caratteri (1.760 byte)|
|    Dimensioni massime di BLOB in blocchi                              |    4,75 TB (100 MB x 50.000 blocchi)                   |    4,75 TB (100 MB x 50.000 blocchi) per Azure Data Box v 3,0 e versioni successive.|
|    Dimensioni massime di BLOB di pagine                               |    8 TB                                               |    1 TB                   |
|    Dimensioni pagina del BLOB di pagine                                  |    512 byte                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Versioni API supportate

Con Data Box archiviazione BLOB sono supportate le seguenti versioni delle API del servizio di archiviazione di Azure.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box 3,0 e versioni successive

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Librerie client di Azure supportate

Per l'archiviazione BLOB di Azure Data Box sono disponibili librerie client specifiche e requisiti specifici per il suffisso dell'endpoint. Gli endpoint di archiviazione BLOB Data Box non hanno una parità completa con la versione più recente dell'API REST di archiviazione BLOB di Azure. vedere le [versioni supportate per Azure Data Box 3,0 e versioni](#supported-api-versions)successive. Per le librerie client di archiviazione è necessario essere a conoscenza della versione compatibile con l'API REST.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box 3,0 e versioni successive

Per Data Box archiviazione BLOB sono supportate le seguenti versioni della libreria client di Azure.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-php-client-via-composer---current"></a>Installare il client PHP tramite Composer - corrente

Per l'installazione tramite Composer: (prendere BLOB come esempio).
1. Creare un file denominato composer.json nella radice del progetto con il codice seguente:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Scaricare `composer.phar` nella radice del progetto.

3. Eseguire: php composer.phar install.

### <a name="endpoint-declaration"></a>Dichiarazione dell'endpoint

In Data Box BLOB Storage SDK, il suffisso dell'endpoint, che `<device serial number>.microsoftdatabox.com` identifica il dominio di data box. Per altre informazioni sull'endpoint del servizio BLOB, vedere [connettersi tramite Data Box archiviazione BLOB](data-box-deploy-copy-data-via-rest.md).
 
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
