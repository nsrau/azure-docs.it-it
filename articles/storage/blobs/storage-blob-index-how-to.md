---
title: Usare i tag degli indici BLOB per gestire e trovare i dati nell'archiviazione BLOB di Azure
description: Vedere esempi di come usare i tag di indice BLOB per categorizzare, gestire ed eseguire query per gli oggetti BLOB.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: hux
ms.custom: devx-track-csharp
ms.openlocfilehash: 175c9efd02665bf0212d7078a2ec2767ed1be6b9
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850983"
---
# <a name="utilize-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Usare i tag degli indici BLOB (anteprima) per gestire e trovare i dati nell'archiviazione BLOB di Azure

I tag dell'indice BLOB categorizzano i dati nell'account di archiviazione usando gli attributi di tag chiave-valore. Questi tag vengono automaticamente indicizzati ed esposti come indice multidimensionale disponibile per query per trovare facilmente i dati. Questo articolo illustra come impostare, ottenere e trovare i dati usando i tag indice BLOB.

Per altre informazioni sulla funzionalità per gli indici BLOB, vedere [gestire e trovare i dati nell'archivio BLOB di Azure con indice BLOB (anteprima)](storage-manage-find-blobs.md).

> [!NOTE]
> L'indice BLOB è in anteprima pubblica ed è disponibile nelle aree **Canada centrale**, **Canada orientale**, **Francia centrale** e **Francia meridionale** . Per altre informazioni su questa funzionalità insieme ai problemi noti e alle limitazioni, vedere [gestire e trovare i dati nell'archivio BLOB di Azure con indice BLOB (anteprima)](storage-manage-find-blobs.md).

## <a name="prerequisites"></a>Prerequisiti
# <a name="portal"></a>[Portale](#tab/azure-portal)
- Sottoscrizione registrata e approvata per l'accesso all'anteprima dell'indice BLOB
- Accesso al [portale di Azure](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)
Poiché l'indice BLOB è in anteprima pubblica, il pacchetto di archiviazione .NET viene rilasciato nel feed NuGet di anteprima. Questa libreria è soggetta a modifiche fino a quando non diventa ufficiale. 

1. Configurare il progetto di Visual Studio per iniziare a usare la libreria client di archiviazione BLOB di Azure V12 per .NET. Per altre informazioni, vedere [Guida introduttiva a .NET](storage-quickstart-blobs-dotnet.md)

2. In Gestione pacchetti NuGet trovare il pacchetto **Azure. storage. Blobs** e installare la versione **12.7.0-Preview. 1 o una** versione successiva del progetto. È anche possibile eseguire il comando ```Install-Package Azure.Storage.Blobs -Version 12.7.0-preview.1```

   Per informazioni, vedere [Trovare e installare un pacchetto](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

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

## <a name="upload-a-new-blob-with-index-tags"></a>Caricare un nuovo BLOB con tag indice
# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Selezionare l'account di archiviazione nel [portale di Azure](https://portal.azure.com/) 

2. Passare all'opzione **contenitori** in **servizio BLOB**, selezionare il contenitore

3. Selezionare il pulsante **Carica** per aprire il pannello Carica ed esplorare il file system locale per trovare un file da caricare come BLOB in blocchi.

4. Espandere l'elenco a discesa **Avanzate** e passare alla sezione **Tag indice BLOB**

5. Immettere i tag indice BLOB con i dati chiave/valore che si vuole applicare ai dati

6. Selezionare il pulsante **Carica** per caricare il BLOB

![Caricare i dati con i tag indice BLOB](media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png)

# <a name="net"></a>[.NET](#tab/net)

Nell'esempio seguente viene illustrato come creare un BLOB di accodamento con tag impostati durante la creazione.

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

          // Blob index tags to upload
          AppendBlobCreateOptions appendOptions = new AppendBlobCreateOptions();
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

## <a name="get-set-and-update-blob-index-tags"></a>Ottenere, impostare e aggiornare i tag indice BLOB
# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Selezionare l'account di archiviazione nel [portale di Azure](https://portal.azure.com/) 

2. Passare all'opzione **Contenitori** in **Servizio BLOB** e selezionare il contenitore

3. Selezionare il BLOB desiderato dall'elenco di BLOB all'interno del contenitore selezionato

4. La scheda Panoramica del BLOB visualizzerà le proprietà del BLOB, inclusi eventuali **tag indice BLOB**

5. È possibile ottenere, impostare, modificare o eliminare qualsiasi tag indice con chiave/valore per il BLOB

6. Seleziona il pulsante **Salva** per confermare eventuali aggiornamenti apportati al BLOB

![Ottenere, impostare, aggiornare ed eliminare tag indice BLOB negli oggetti](media/storage-blob-index-concepts/blob-index-get-set-tags.png)

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

          // Set or update blob index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get blob index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List blobs with all options returned including blob index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing blob index tags by replacing all tags
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

All'interno del portale di Azure, il filtro tag indice BLOB applica automaticamente il `@container` parametro per definire l'ambito del contenitore selezionato. Per filtrare e trovare i dati con tag nell'intero account di archiviazione, usare l'API REST, gli SDK o gli strumenti.

1. Selezionare l'account di archiviazione nel [portale di Azure](https://portal.azure.com/). 

2. Passare all'opzione **contenitori** in **servizio BLOB**, selezionare il contenitore

3. Selezionare il pulsante **Filtro tag indice BLOB** per applicare il filtro all'interno del contenitore selezionato

4. Immettere una chiave tag di indice BLOB e un valore di tag

5. Selezionare il pulsante **Filtro tag indice BLOB** per aggiungere altri filtri tag (fino a 10)

![Filtrare e trovare oggetti con tag con tag indice BLOB](media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob index queries and selection
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
           
          // Blob index tags to upload
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

          List<TaggedBlobItem> blobs = new List<TaggedBlobItem>();
          await foreach (TaggedBlobItem taggedBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(taggedBlobItem);
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

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>Gestione del ciclo di vita con filtri tag indice BLOB

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Selezionare l'account di archiviazione nel [portale di Azure](https://portal.azure.com/). 

2. Passare all'opzione **Gestione del ciclo di vita** in **Servizio BLOB**

3. Selezionare *Aggiungi regola* e quindi compilare i campi modulo di Set di azioni

4. Selezionare il set di **filtri** per aggiungere un filtro facoltativo per la corrispondenza del prefisso e la corrispondenza dell'indice BLOB ![ aggiungere i filtri dei tag di indice BLOB per la gestione](media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png)

5. Selezionare **Rivedi e aggiungi** per controllare le impostazioni delle regole ![Regola di gestione del ciclo di vita con un esempio del filtro tag indice BLOB](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

6. Selezionare **Aggiungi** per applicare la nuova regola ai criteri di gestione del ciclo di vita

# <a name="net"></a>[.NET](#tab/net)
I criteri di [gestione del ciclo di vita](storage-lifecycle-management-concepts.md) vengono applicati per ogni account di archiviazione a livello di piano di controllo. Per .NET, installare la [libreria di archiviazione di Microsoft Azure Management 16.0.0 o versione](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) successiva per sfruttare i vantaggi del filtro di corrispondenza dell'indice BLOB in una regola di gestione del ciclo di vita.

---

## <a name="next-steps"></a>Passaggi successivi

 - Per altre informazioni sull'indice BLOB, vedere [gestire e trovare i dati nell'archivio BLOB di Azure con indice BLOB (anteprima)](storage-manage-find-blobs.md )
 - Altre informazioni sulla gestione del ciclo di vita. Vedere [Gestire il ciclo di vita di Archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md)
