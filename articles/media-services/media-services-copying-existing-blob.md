---
title: Copiare BLOB da un account di archiviazione in un asset di Servizi multimediali di Azure | Microsoft Docs
description: "Questo argomento illustra come copiare un BLOB esistente in un asset di Servizi multimediali. L’esempio usa le estensioni SDK .NET dei Servizi multimediali di Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 6a63823f-f3c9-424c-91b8-566f70bec346
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/31/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 197bf0f0d95422f5bd696a8e9b0abc799f2951fc
ms.openlocfilehash: f71adf1f12e2be0c63465eff7d61a35d3256d4f2


---
# <a name="copying-existing-blobs-into-a-media-services-asset"></a>Copia di un BLOB esistente in un asset di Servizi multimediali
Questo argomento illustra come copiare BLOB da un account di archiviazione in un nuovo asset di Servizi multimediali di Microsoft Azure usando le [estensioni SDK .NET dei Servizi multimediali di Azure](https://github.com/Azure/azure-sdk-for-media-services-extensions/).

Questo metodo di estensione funziona con:

- Asset regolari.
- Asset di archivio live (formato FragBlob).
- Asset di origine e destinazione appartenenti a diversi account di Servizi multimediali (anche in datacenter diversi). Questa operazione, tuttavia, può comportare l'addebito di costi. Per altre informazioni sui prezzi, vedere [Trasferimenti di dati](https://azure.microsoft.com/pricing/#header-11).

> [!NOTE]
> È sconsigliabile tentare di modificare i contenuti dei contenitori BLOB generati da Servizi multimediali senza usare le API di Servizi multimediali.
> 

## <a name="download-sample"></a>Scaricare un esempio
È possibile seguire i passaggi descritti in questo articolo oppure scaricare un esempio che contiene il codice descritto in questo articolo da [qui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/).

## <a name="prerequisites"></a>Prerequisiti
* Due account di Servizi multimediali in una sottoscrizione di Azure nuova o esistente. Vedere l'argomento [Come creare un account di Servizi multimediali](media-services-portal-create-account.md).
* Sistemi operativi: Windows 10, Windows 7, Windows 2008 R2 o Windows 8.
* .NET Framework 4.5
* Visual Studio 2010, SP1 (Professional, Premium, Ultimate, o Express) o versioni successive.

## <a name="set-up-your-project"></a>Configurare il progetto
In questa sezione si creerà e si configurerà un progetto di applicazione console in C#.

1. Usare Visual Studio per creare una nuova soluzione contenente il progetto di applicazione console in C#. 
2. Immettere CopyExistingBlobsIntoAsset per il nome e quindi fare clic su OK.
3. Usare il pacchetto [Nuget](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) per installare e aggiungere le estensioni SDK .NET dei Servizi multimediali di Azure (windowsazure.mediaservices.extensions). Insieme al pacchetto viene installato anche l'SDK di Servizi multimediali per .NET e vengono aggiunte tutte le altre dipendenze necessarie.
4. Aggiungere gli altri riferimenti richiesti per il progetto System.Configuration.
6. Aggiungere la sezione appSettings al file con estensione CONFIG e aggiornare i valori in base all'account di Servizi multimediali, all'account di archiviazione di destinazione e all'ID dell'asset di origine. 
   
        <appSettings>
              <add key="MediaServicesSourceAccountName" value="name"/>
              <add key="MediaServicesSourceAccountKey" value="key"/>
              <add key="MediaServicesDestAccountName" value="name"/>
              <add key="MediaServicesDestAccountKey" value="key"/>
              <add key="DestStorageAccountName" value="name"/>
              <add key="DestStorageAccountKey" value="key"/>
              <add key="SourceAssetID" value="nb:cid:UUID:assetID"/>       
        </appSettings>

## <a name="copy-blobs-from-a-storage-account-into-a-media-services-asset"></a>Copiare BLOB da un account di archiviazione in un asset di Servizi multimediali

Il codice seguente usa il metodo dell'estensione **IAsset.Copy** per copiare tutti i file di un asset di origine in un asset di destinazione usando un'unica estensione. Esiste un overload aggiuntivo con il supporto asincrono.

Sostituire il codice nella classe Program.cs con il codice seguente:

    using System;
    using System.Linq;
    using System.Configuration;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.Storage.Auth;
    
    namespace CopyExistingBlobsIntoAsset
    {
        class Program
        {
            static string _sourceAaccountName = ConfigurationManager.AppSettings["MediaServicesSourceAccountName"];
            static string _sourceAccountKey = ConfigurationManager.AppSettings["MediaServicesSourceAccountKey"];
            static string _destAccountName = ConfigurationManager.AppSettings["MediaServicesDestAccountName"];
            static string _destAccountKey = ConfigurationManager.AppSettings["MediaServicesDestAccountKey"];
            static string _destStorageAccountName = ConfigurationManager.AppSettings["DestStorageAccountName"];
            static string _destStorageAccountKey = ConfigurationManager.AppSettings["DestStorageAccountKey"];
            static string _sourceAssetID = ConfigurationManager.AppSettings["SourceAssetID"];
            
    
            private static CloudMediaContext _sourceContext = null;
            private static CloudMediaContext _destContext = null;
    
            static void Main(string[] args)
            {
                // Create the context for your source Media Services account.
    
                // Use the cached credentials to create CloudMediaContext.
                _sourceContext = new CloudMediaContext(new MediaServicesCredentials(
                    _sourceAaccountName,
                    _sourceAccountKey));

                // Create the context for your destination Media Services account.
                _destContext = new CloudMediaContext(new MediaServicesCredentials(
                    _destAccountName,
                    _destAccountKey));
    
                // Get the credentials of the default Storage account bound to your destination Media Services account.
                StorageCredentials destinationStorageCredentials =
                    new StorageCredentials(_destStorageAccountName, _destStorageAccountKey);
    
                // Get a reference to the source asset in the source context.
                IAsset sourceAsset = _sourceContext.Assets.Where(a => a.Id == _sourceAssetID).First();
    
                // Create an empty destination asset in the destination context.
                IAsset destinationAsset = _destContext.Assets.Create(sourceAsset.Name, AssetCreationOptions.None);
    
                // Copy the files in the source asset instance into the destination asset instance.
                // There is an additional overload with async support.
                sourceAsset.Copy(destinationAsset, destinationStorageCredentials);

                
            }
        }
    }


## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Feb17_HO1-->


