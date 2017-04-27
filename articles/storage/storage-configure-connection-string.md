---
title: Configurare una stringa di connessione per Archiviazione di Azure | Microsoft Docs
description: Configurare una stringa di connessione per un account di archiviazione di Azure. Una stringa di connessione contiene le informazioni necessarie per autenticare l&quot;accesso a un account di archiviazione dall&quot;applicazione in fase di runtime.
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: ecb0acb5-90a9-4eb2-93e6-e9860eda5e53
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 01aa506e2b47fc29a70592e670a206a2b74248a4
ms.lasthandoff: 04/12/2017


---
# <a name="configure-azure-storage-connection-strings"></a>Configurare le stringhe di connessione di Archiviazione di Azure

Una stringa di connessione include le informazioni necessarie all'applicazione per l'accesso ai dati di un account di Archiviazione di Azure in fase di runtime. Le stringhe di connessione possono essere configurate per:

* Connettersi all'emulatore di archiviazione di Azure.
* Accedere a un account di archiviazione in Azure.
* Accedere alle risorse specificate in Azure tramite una firma di accesso condiviso (SAS).

[!INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Recupero della stringa di connessione
L'applicazione deve accedere alla stringa di connessione in fase di runtime per autenticare le richieste inviate al servizio Archiviazione di Azure. Sono disponibili diverse opzioni per l'archiviazione della stringa di connessione:

* Un'applicazione in esecuzione sul desktop o in un dispositivo può archiviare la stringa di connessione in un file **app.config** o in un file **web.config**. Aggiungere la stringa di connessione alla sezione **AppSettings** in tali file.
* Un'applicazione in esecuzione in un servizio cloud di Azure può archiviare la stringa di connessione nel [file dello schema di configurazione dei servizi di Azure (.cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Aggiungere la stringa di connessione alla sezione **ConfigurationSettings** del file di configurazione del servizio.
* È possibile usare la stringa di connessione direttamente nel codice. Per la maggior parte degli scenari è tuttavia consigliabile archiviare la stringa di connessione in un file di configurazione.

L'archiviazione della stringa di connessione in un file di configurazione renderà più semplice aggiornare la stringa per alternare tra l'emulatore di archiviazione e un account di archiviazione di Azure nel cloud. È sufficiente modificare la stringa di connessione in modo che faccia riferimento all'ambiente di destinazione.

È possibile usare [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) per accedere alla stringa di connessione in fase di runtime indipendentemente dall'ambiente in cui viene eseguita l'applicazione.

## <a name="create-a-connection-string-for-the-storage-emulator"></a>Creare una stringa di connessione per l'emulatore di archiviazione
[!INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Per altre informazioni sull'emulatore di archiviazione, vedere [Usare l'emulatore di archiviazione di Azure per sviluppo e test](storage-use-emulator.md).

## <a name="create-a-connection-string-for-an-azure-storage-account"></a>Creare una stringa di connessione per un account di archiviazione di Azure
Per creare una stringa di connessione per l'account di archiviazione di Azure, usare il seguente formato. Indicare se si vuole eseguire la connessione all'account di archiviazione tramite HTTPS (scelta consigliata) o HTTP, sostituire `myAccountName` con il nome dell'account di archiviazione e sostituire `myAccountKey` con la chiave di accesso dell'account:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Ad esempio, la stringa di connessione può essere simile alla seguente:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Anche se Archiviazione di Azure supporta sia HTTP che HTTPS in una stringa di connessione, *è consigliabile usare HTTPS*.

> [!TIP]
> Le stringhe di connessione dell'account di archiviazione sono disponibili nel [portale di Azure](https://portal.azure.com). Accedere a **Impostazioni** > **Chiavi di accesso** nel pannello del menu dell'account di archiviazione per impostare stringhe di connessione per le chiavi di accesso primaria e secondaria.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Creare una stringa di connessione usando una firma di accesso condiviso
[!INCLUDE [storage-use-sas-in-connection-string-include](../../includes/storage-use-sas-in-connection-string-include.md)]

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

Se l'endpoint di archiviazione BLOB è stato mappato su un [dominio personalizzato](storage-custom-domain-name.md) può risultare utile specificare un endpoint esplicito. In tal caso è possibile specificare l'endpoint personalizzato per l'archiviazione BLOB nella stringa di connessione. Facoltativamente è possibile specificare gli endpoint predefiniti per gli altri servizi, se l'applicazione li usa.

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

> [!IMPORTANT]
> I valori degli endpoint di servizio nelle stringhe di connessione devono essere URI formulati correttamente e includere `https://` (opzione consigliata) o `http://`. Dato che l'archiviazione di Azure non supporta ancora HTTPS per i domini personalizzati, *è necessario* specificare `http://` per gli URI di endpoint che fanno riferimento a domini personalizzati.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Creare una stringa di connessione con un suffisso dell'endpoint
Per creare una stringa di connessione per un servizio di archiviazione in aree o istanze con suffissi dell'endpoint diversi, ad esempio per Azure Cina o Azure per enti pubblici, usare il seguente formato per la stringa di connessione. Indicare se si vuole eseguire la connessione all'account di archiviazione tramite HTTPS (opzione consigliata) o HTTP, quindi sostituire `myAccountName` con il nome dell'account di archiviazione, sostituire `myAccountKey` con la chiave di accesso del proprio account e sostituire `mySuffix` con il suffisso URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Ecco una stringa di connessione di esempio per i servizi di archiviazione in Azure Cina:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Analisi di una stringa di connessione
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Passaggi successivi
* [Usare l'emulatore di archiviazione di Azure per sviluppo e test](storage-use-emulator.md)
* [Strumenti di esplorazione di Archiviazione di Azure](storage-explorers.md)
* [Uso delle firme di accesso condiviso](storage-dotnet-shared-access-signature-part-1.md)


