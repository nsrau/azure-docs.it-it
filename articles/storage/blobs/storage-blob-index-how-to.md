---
title: Usare l'indice BLOB per gestire e trovare i dati nell'archivio BLOB di Azure
description: Vedere esempi di come usare i tag di indice BLOB per categorizzare, gestire ed eseguire query per individuare oggetti BLOB.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: f4c9fab3caf1089b97265d93db7d945604a59fd3
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82723012"
---
# <a name="utilize-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Usare i tag degli indici BLOB (anteprima) per gestire e trovare i dati nell'archiviazione BLOB di Azure

I tag dell'indice BLOB categorizzano i dati nell'account di archiviazione usando gli attributi di tag chiave-valore. Questi tag vengono automaticamente indicizzati ed esposti come indice multidimensionale Queryable per trovare facilmente i dati. Questo articolo illustra come impostare, ottenere e trovare i dati usando i tag degli indici BLOB.

Per altre informazioni sull'indice BLOB, vedere [gestire e trovare i dati nell'archivio BLOB di Azure con indice BLOB (anteprima)](storage-manage-find-blobs.md).

> [!NOTE]
> L'indice BLOB è in anteprima pubblica ed è disponibile nelle aree **Francia centrale** e **Francia meridionale** . Per altre informazioni su questa funzionalità insieme ai problemi noti e alle limitazioni, vedere [gestire e trovare i dati nell'archivio BLOB di Azure con indice BLOB (anteprima)](storage-manage-find-blobs.md).

## <a name="prerequisites"></a>Prerequisiti
# <a name="portal"></a>[Portale](#tab/azure-portal)
- Sottoscrizione registrata e approvata per l'accesso all'anteprima dell'indice BLOB
- Accesso a [portale di Azure](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)
Poiché l'indice BLOB è in anteprima pubblica, il pacchetto di archiviazione .NET viene rilasciato nel feed NuGet di anteprima. Questa libreria è soggetta a modifiche tra ora e quando diventa ufficiale. 

1. In Visual Studio aggiungere l'URL `https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json` alle origini dei pacchetti NuGet. 

   Per informazioni, vedere [origini di pacchetti](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. In Gestione pacchetti NuGet trovare il pacchetto **Azure. storage. Blobs** e installare la versione **12.5.0-dev. 20200422.2** nel progetto. È anche possibile eseguire il comando```Install-Package Azure.Storage.Blobs -Version12.5.0-dev.20200422.2```

   Per informazioni, vedere [trovare e installare un pacchetto](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

3. Aggiungere le istruzioni using seguenti all'inizio del file di codice.
```csharp
using Azure;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
```
---

## <a name="upload-a-new-blob-with-index-tags"></a>Caricare un nuovo BLOB con tag di indice
# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nella [portale di Azure](https://portal.azure.com/)selezionare l'account di archiviazione 

2. Passare all'opzione **contenitori** in **servizio BLOB**, selezionare il contenitore

3. Selezionare il pulsante **Carica** per aprire il pannello Carica ed esplorare il file system locale per trovare un file da caricare come BLOB in blocchi.

4. Espandere l'elenco a discesa **Avanzate** e passare alla sezione relativa ai **tag degli indici BLOB**

5. Immettere i tag dell'indice BLOB chiave/valore che si desidera applicare ai dati

6. Selezionare il pulsante **upload (carica** ) per caricare il BLOB

![Caricare dati con tag di indice BLOB](media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png)

# <a name="net"></a>[.NET](#tab/net)
Nell'esempio seguente viene illustrato come creare un BLOB di Accodamento con tag impostati durante la creazione.
```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");
         
          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>Ottenere, impostare e aggiornare i tag degli indici BLOB
# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nella [portale di Azure](https://portal.azure.com/)selezionare l'account di archiviazione 

2. Passare all'opzione **contenitori** in **servizio BLOB**, selezionare il contenitore

3. Consente di selezionare il BLOB desiderato dall'elenco dei BLOB all'interno del contenitore selezionato

4. Nella scheda Panoramica BLOB vengono visualizzate le proprietà del BLOB, inclusi i **tag degli indici BLOB**

5. È possibile ottenere, impostare, modificare o eliminare i tag di indice chiave/valore per il BLOB

6. Selezionare il pulsante **Salva** per confermare eventuali aggiornamenti al BLOB

![Ottenere, impostare, aggiornare ed eliminare tag di indice BLOB sugli oggetti](media/storage-blob-index-concepts/blob-index-get-set-tags.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or Update Blob Index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get Blob Index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List Blobs with all options returned including Blob Index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing Blob Index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>Filtrare e trovare i dati con i tag degli indici BLOB

# <a name="portal"></a>[Portale](#tab/azure-portal)

All'interno del portale di Azure, il filtro tag indice BLOB applica automaticamente `@container` il parametro per definire l'ambito del contenitore selezionato. Per filtrare e trovare i dati contrassegnati nell'intero account di archiviazione, usare l'API REST, gli SDK o gli strumenti.

1. Nella [portale di Azure](https://portal.azure.com/)selezionare l'account di archiviazione. 

2. Passare all'opzione **contenitori** in **servizio BLOB**, selezionare il contenitore

3. Selezionare il pulsante di filtro per gli **indici BLOB** per filtrare nel contenitore selezionato

4. Immettere una chiave tag di indice BLOB e un valore di tag

5. Selezionare il pulsante di filtro per gli **indici BLOB** per aggiungere altri filtri Tag (fino a 10)

![Filtrare e trovare oggetti con tag con tag di indice BLOB](media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<FilterBlobItem> blobs = new List<FilterBlobItem>();
          foreach (Page<FilterBlobItem> page in serviceClient.FindBlobsByTags(queryToUse).AsPages())
          {
              blobs.AddRange(page.Values);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>Gestione del ciclo di vita con filtri dei tag di indice BLOB

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nella [portale di Azure](https://portal.azure.com/)selezionare l'account di archiviazione. 

2. Passare all'opzione di **gestione del ciclo** di vita in **servizio BLOB**

3. Selezionare *Aggiungi regola* e quindi compilare i campi del modulo del set di azioni

4. Selezionare il set di filtri per aggiungere un filtro facoltativo per la corrispondenza del ![prefisso e la corrispondenza dell'indice BLOB aggiungere i filtri dei tag di indice BLOB per la gestione](media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png)

5. Selezionare **Verifica + Aggiungi** per esaminare la regola di ![gestione del ciclo di vita delle impostazioni regola con l'esempio di filtro tag di indice BLOB](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

6. Selezionare **Aggiungi** per applicare la nuova regola ai criteri di gestione del ciclo di vita

# <a name="net"></a>[.NET](#tab/net)
I criteri di [gestione del ciclo](storage-lifecycle-management-concepts.md) di vita vengono applicati per ogni account di archiviazione a livello di piano di controllo. Per .NET, installare la [libreria di archiviazione di Microsoft Azure Management 16.0.0 o versione](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) successiva per sfruttare i vantaggi del filtro di corrispondenza dell'indice BLOB in una regola di gestione del ciclo di vita.

---

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'indice BLOB. Vedere [gestire e trovare i dati nell'archivio BLOB di Azure con indice BLOB (anteprima)](storage-manage-find-blobs.md )

Altre informazioni sulla gestione del ciclo di vita. Vedere [gestire il ciclo di vita dell'archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md)
