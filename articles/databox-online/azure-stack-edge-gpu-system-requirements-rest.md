---
title: Requisiti per l'archiviazione BLOB Microsoft Azure Stack Edge | Microsoft Docs
description: Informazioni sulle versioni supportate per le API, gli SDK e le librerie client per l'archiviazione BLOB Azure Stack Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: 5e3b9b841c8e6ff17a29ac9c6a5e746ed6b687b9
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92128488"
---
# <a name="azure-stack-edge-blob-storage-requirements"></a>Requisiti per l'archiviazione BLOB Azure Stack Edge

Questo articolo elenca le versioni delle API di Azure, le librerie client di Azure e gli strumenti supportati con l'archiviazione BLOB di Azure Stack Edge. Azure Stack archiviazione BLOB perimetrale fornisce funzionalità di gestione dei BLOB con semantica coerente con Azure. Questo articolo riepiloga anche le differenze di archiviazione BLOB Azure Stack Edge note rispetto ai servizi di archiviazione di Azure.

Si consiglia di esaminare attentamente le informazioni prima di connettersi all'archiviazione BLOB di Azure Stack Edge, quindi rimandarla a se necessario.


## <a name="storage-differences"></a>Differenze di archiviazione

|     Funzionalità                                             |     Archiviazione di Azure                                     |     Archiviazione BLOB Azure Stack Edge |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Archiviazione file di Azure                                   |    Condivisioni di file SMB basate sul cloud supportate              |    Non supportato      |
|    Crittografia del servizio per dati inattivi                  |    Crittografia AES a 256 bit                             |    Crittografia AES a 256 bit |
|    Tipo di account di archiviazione                                 |    Account di archiviazione BLOB di Azure e per uso generico    |    Solo uso generico v1|
|    Nome del BLOB                                            |    1.024 caratteri (2.048 byte)                     |    880 caratteri (1.760 byte)|
|    Dimensioni massime di BLOB in blocchi                              |    4,75 TB (100 MB x 50.000 blocchi)                   |    4,75 TB (100 MB x 50.000 blocchi) per Azure Stack Edge v 2.1.1377.2170 e versioni successive|
|    Dimensioni massime di BLOB di pagine                               |    8 TB                                               |    1 TB                   |
|    Dimensioni pagina del BLOB di pagine                                  |    512 byte                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Versioni API supportate

Con l'archiviazione BLOB Azure Stack Edge sono supportate le seguenti versioni delle API del servizio di archiviazione di Azure.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack 2.1.1377.2170 Edge in avanti

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Librerie client di Azure supportate

Per l'archiviazione BLOB Azure Stack Edge sono disponibili librerie client specifiche e requisiti specifici per il suffisso dell'endpoint. Gli endpoint di archiviazione BLOB Azure Stack Edge non hanno una parità completa con la versione più recente dell'API REST di archiviazione BLOB di Azure. vedere le [versioni API supportate per Azure stack 2.1.1377.2170 Edge](#supported-api-versions). Per le librerie client di archiviazione è necessario essere a conoscenza della versione compatibile con l'API REST.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack 2.1.1377.2170 Edge in avanti

Le seguenti versioni della libreria client di Azure sono supportate per l'archiviazione BLOB Azure Stack Edge.

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

In Azure Stack Edge BLOB Storage SDK, il suffisso dell'endpoint, `<device serial number>.microsoftdatabox.com` identifica il dominio Azure stack Edge. Per altre informazioni sull'endpoint del servizio BLOB, vedere [trasferire i dati tramite gli account di archiviazione con la GPU Pro Azure stack Edge](azure-stack-edge-j-series-deploy-add-storage-accounts.md).
 
## <a name="examples"></a>Esempi

### <a name="net"></a>.NET

Per l'archiviazione BLOB Azure Stack Edge, il suffisso dell'endpoint viene specificato nel `app.config` file:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Per l'archiviazione BLOB Azure Stack Edge, il suffisso dell'endpoint viene specificato nella stringa di connessione del programma di installazione:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Per l'archiviazione BLOB Azure Stack Edge, il suffisso dell'endpoint viene specificato nell'istanza della dichiarazione:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Per l'archiviazione BLOB Azure Stack Edge, il suffisso dell'endpoint viene specificato nella stringa di connessione del programma di installazione:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Per l'archiviazione BLOB Azure Stack Edge, il suffisso dell'endpoint viene specificato nella stringa di connessione del programma di installazione:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Per l'archiviazione BLOB Azure Stack Edge, il suffisso dell'endpoint viene specificato nell'istanza della dichiarazione:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Per l'archiviazione BLOB Azure Stack Edge, il suffisso dell'endpoint viene specificato nella stringa di connessione del programma di installazione:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Passaggi successivi

* [Preparare la distribuzione di Azure Stack Edge Pro con GPU](azure-stack-edge-gpu-deploy-prep.md)
