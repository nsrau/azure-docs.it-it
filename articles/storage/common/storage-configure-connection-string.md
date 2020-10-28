---
title: Configurare una stringa di connessione
titleSuffix: Azure Storage
description: Configurare una stringa di connessione per un account di archiviazione di Azure. Una stringa di connessione contiene le informazioni necessarie per autorizzare l'accesso a un account di archiviazione dall'applicazione in fase di esecuzione tramite l'autorizzazione della chiave condivisa.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: d7ca1707c89f03683960822591065143d3f8aa4f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783879"
---
# <a name="configure-azure-storage-connection-strings"></a>Configurare le stringhe di connessione di Archiviazione di Azure

Una stringa di connessione include le informazioni di autorizzazione necessarie all'applicazione per accedere ai dati in un account di archiviazione di Azure in fase di esecuzione tramite l'autorizzazione della chiave condivisa. Le stringhe di connessione possono essere configurate per:

* Connettersi all'emulatore di archiviazione azzurrite.
* Accedere a un account di archiviazione in Azure.
* Accedere alle risorse specificate in Azure tramite una firma di accesso condiviso (SAS).

Per informazioni su come visualizzare le chiavi di accesso dell'account e copiare una stringa di connessione, vedere [gestire le chiavi di accesso dell'account di archiviazione](storage-account-keys-manage.md).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="store-a-connection-string"></a>Archiviare una stringa di connessione

L'applicazione deve accedere alla stringa di connessione in fase di runtime per autenticare le richieste inviate al servizio Archiviazione di Azure. Sono disponibili diverse opzioni per l'archiviazione della stringa di connessione:

* È possibile archiviare la stringa di connessione in una variabile di ambiente.
* Un'applicazione in esecuzione sul desktop o in un dispositivo può archiviare la stringa di connessione in un file **app.config** o in un file **web.config** . Aggiungere la stringa di connessione alla sezione **AppSettings** in tali file.
* Un'applicazione in esecuzione in un servizio cloud di Azure può archiviare la stringa di connessione nel [file dello schema di configurazione dei servizi di Azure (.cscfg)](/previous-versions/azure/reference/ee758710(v=azure.100)). Aggiungere la stringa di connessione alla sezione **ConfigurationSettings** del file di configurazione del servizio.

Archiviando la stringa di connessione in un file di configurazione è possibile aggiornare facilmente la stringa di connessione per passare dall' [emulatore di archiviazione di azzurrite](../common/storage-use-azurite.md) a un account di archiviazione di Azure nel cloud. È sufficiente modificare la stringa di connessione in modo che faccia riferimento all'ambiente di destinazione.

È possibile usare [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) per accedere alla stringa di connessione in fase di runtime indipendentemente dall'ambiente in cui viene eseguita l'applicazione.

## <a name="configure-a-connection-string-for-azurite"></a>Configurare una stringa di connessione per azzurrite

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Per altre informazioni su azzurrite, vedere [usare l'emulatore di azzurrite per lo sviluppo locale di archiviazione di Azure](../common/storage-use-azurite.md).

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Configurare una stringa di connessione per un account di archiviazione di Azure

Per creare una stringa di connessione per l'account di archiviazione di Azure, usare il seguente formato. Indicare se si vuole eseguire la connessione all'account di archiviazione tramite HTTPS (scelta consigliata) o HTTP, sostituire `myAccountName` con il nome dell'account di archiviazione e sostituire `myAccountKey` con la chiave di accesso dell'account:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Ad esempio, la stringa di connessione può essere simile alla seguente:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Anche se Archiviazione di Azure supporta sia HTTP che HTTPS in una stringa di connessione, *è consigliabile usare HTTPS* .

> [!TIP]
> Le stringhe di connessione dell'account di archiviazione sono disponibili nel [portale di Azure](https://portal.azure.com). Passare a **Impostazioni**  >  **chiavi di accesso** nel pannello del menu dell'account di archiviazione per visualizzare le stringhe di connessione per le chiavi di accesso primarie e secondarie.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Creare una stringa di connessione usando una firma di accesso condiviso

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Creare una stringa di connessione per un endpoint di archiviazione esplicito

Nella stringa di connessione è possibile specificare in modo esplicito endpoint di servizio anziché usare gli endpoint predefiniti. Per creare una stringa di connessione che specifica un endpoint esplicito, specificare l'endpoint di servizio completo per ogni servizio, inclusa la specifica del protocollo, ad esempio HTTPS (scelta consigliata) o HTTP, usando il formato seguente:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Se l'endpoint di archiviazione BLOB è stato mappato su un [dominio personalizzato](../blobs/storage-custom-domain-name.md) può risultare utile specificare un endpoint esplicito. In tal caso è possibile specificare l'endpoint personalizzato per l'archiviazione BLOB nella stringa di connessione. Facoltativamente è possibile specificare gli endpoint predefiniti per gli altri servizi, se l'applicazione li usa.

Ecco un esempio di stringa di connessione che specifica un endpoint esplicito per il servizio BLOB:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Questo esempio specifica endpoint espliciti per tutti i servizi, tra cui un dominio personalizzato per il servizio BLOB:

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

I valori degli endpoint in una stringa di connessione vengono usati per costruire gli URI di richiesta per i servizi di archiviazione e per definire la struttura degli eventuali URI restituiti al codice.

Se un endpoint di archiviazione è stato mappato su un dominio personalizzato e tale endpoint viene omesso da una stringa di connessione, non sarà possibile usare la stringa di connessione per accedere ai dati in quel servizio dal codice.

Per altre informazioni sulla configurazione di un dominio personalizzato per archiviazione di Azure, vedere [eseguire il mapping di un dominio personalizzato a un endpoint di archiviazione BLOB di Azure](../blobs/storage-custom-domain-name.md).

> [!IMPORTANT]
> I valori degli endpoint di servizio nelle stringhe di connessione devono essere URI formulati correttamente e includere `https://` (opzione consigliata) o `http://`.

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Creare una stringa di connessione con un suffisso dell'endpoint

Per creare una stringa di connessione per un servizio di archiviazione in aree o istanze con suffissi dell'endpoint diversi, ad esempio per Azure Cina 21Vianet o Azure per enti pubblici, usare il seguente formato della stringa di connessione. Indicare se si vuole eseguire la connessione all'account di archiviazione tramite HTTPS (opzione consigliata) o HTTP, quindi sostituire `myAccountName` con il nome dell'account di archiviazione, sostituire `myAccountKey` con la chiave di accesso del proprio account e sostituire `mySuffix` con il suffisso URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Ecco una stringa di connessione di esempio per i servizi di archiviazione in Azure Cina 21Vianet:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Analisi di una stringa di connessione

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Usare l'emulatore di azzurrite per lo sviluppo locale di archiviazione di Azure](../common/storage-use-azurite.md)
* [Strumenti di esplorazione di Archiviazione di Azure](storage-explorers.md)
* [Uso delle firme di accesso condiviso](storage-sas-overview.md)