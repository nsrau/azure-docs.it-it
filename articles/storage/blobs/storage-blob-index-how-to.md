---
title: Usare i tag degli indici BLOB per gestire e trovare i dati nell'archiviazione BLOB di Azure
description: Vedere esempi di come usare i tag di indice BLOB per categorizzare, gestire ed eseguire query per gli oggetti BLOB.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/19/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.custom: devx-track-csharp
ms.openlocfilehash: 2e3e16b71d52edd9ab4eaf55651567b95e334b84
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961788"
---
# <a name="use-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Usare i tag degli indici BLOB (anteprima) per gestire e trovare i dati nell'archiviazione BLOB di Azure

I tag dell'indice BLOB categorizzano i dati nell'account di archiviazione usando gli attributi di tag chiave-valore. Questi tag vengono automaticamente indicizzati ed esposti come indice multidimensionale ricercabile per trovare facilmente i dati. Questo articolo illustra come impostare, ottenere e trovare i dati usando i tag indice BLOB.

> [!NOTE]
> L'indice BLOB è in anteprima pubblica ed è disponibile nelle aree **Canada centrale**, **Canada orientale**, **Francia centrale** e **Francia meridionale** . Per altre informazioni su questa funzionalità insieme ai problemi noti e alle limitazioni, vedere [gestire e trovare i dati BLOB di Azure con i tag di indice BLOB (anteprima)](storage-manage-find-blobs.md).

## <a name="prerequisites"></a>Prerequisiti

# <a name="portal"></a>[Portale](#tab/azure-portal)

- Una sottoscrizione di Azure registrata e approvata per l'accesso all'anteprima dell'indice BLOB
- Accesso al [portale di Azure](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)

Poiché l'indice BLOB è in anteprima, il pacchetto di archiviazione .NET viene rilasciato nel feed NuGet di anteprima. Questa libreria è soggetta a modifiche durante il periodo di anteprima.

1. Configurare il progetto di Visual Studio per iniziare a usare la libreria client di archiviazione BLOB di Azure V12 per .NET. Per altre informazioni, vedere [Guida introduttiva a .NET](storage-quickstart-blobs-dotnet.md)

2. In Gestione pacchetti NuGet trovare il pacchetto **Azure. storage. Blobs** e installare la versione **12.7.0-Preview. 1 o una** versione successiva del progetto. È anche possibile eseguire il comando di PowerShell: `Install-Package Azure.Storage.Blobs -Version 12.7.0-preview.1`

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

Questa attività può essere eseguita da un [proprietario di dati BLOB di archiviazione](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) o da un'entità di sicurezza a cui è stata assegnata l'autorizzazione per l' `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [operazione del provider di risorse di Azure](/azure/role-based-access-control/resource-provider-operations.md#microsoftstorage) tramite un ruolo personalizzato di Azure.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Selezionare l'account di archiviazione nel [portale di Azure](https://portal.azure.com/) 

2. Passare all'opzione **contenitori** in **servizio BLOB**, selezionare il contenitore

3. Selezionare il pulsante **upload (carica** ) ed esplorare il file system locale per trovare un file da caricare come BLOB in blocchi.

4. Espandere l'elenco a discesa **Avanzate** e passare alla sezione **Tag indice BLOB**

5. Immettere i tag indice BLOB con i dati chiave/valore che si vuole applicare ai dati

6. Selezionare il pulsante **Carica** per caricare il BLOB

:::image type="content" source="media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png" alt-text="Screenshot della portale di Azure che illustra come caricare un BLOB con tag di indice.":::

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

Il recupero dei tag dell'indice BLOB può essere eseguito da un [proprietario di dati BLOB di archiviazione](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) o da un'entità di sicurezza a cui è stata assegnata l'autorizzazione per l' `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` [operazione del provider di risorse di Azure](/azure/role-based-access-control/resource-provider-operations.md#microsoftstorage) tramite un ruolo personalizzato di Azure.

L'impostazione e l'aggiornamento dei tag dell'indice BLOB possono essere eseguite da un [proprietario di dati BLOB di archiviazione](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) o da un'entità di sicurezza a cui è stata assegnata l'autorizzazione per l' `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [operazione del provider di risorse di Azure](/azure/role-based-access-control/resource-provider-operations.md#microsoftstorage) tramite un ruolo di Azure

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Selezionare l'account di archiviazione nel [portale di Azure](https://portal.azure.com/) 

2. Passare all'opzione **Contenitori** in **Servizio BLOB** e selezionare il contenitore

3. Selezionare il BLOB dall'elenco dei BLOB all'interno del contenitore selezionato

4. La scheda Panoramica del BLOB visualizzerà le proprietà del BLOB, inclusi eventuali **tag indice BLOB**

5. È possibile ottenere, impostare, modificare o eliminare qualsiasi tag indice con chiave/valore per il BLOB

6. Seleziona il pulsante **Salva** per confermare eventuali aggiornamenti apportati al BLOB

:::image type="content" source="media/storage-blob-index-concepts/blob-index-get-set-tags.png" alt-text="Screenshot della portale di Azure che Mostra come ottenere, impostare, aggiornare ed eliminare tag di indice nei BLOB.":::

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

Questa attività può essere eseguita da un [proprietario di dati BLOB di archiviazione](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) o da un'entità di sicurezza a cui è stata assegnata l'autorizzazione per l' `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` [operazione del provider di risorse di Azure](/azure/role-based-access-control/resource-provider-operations.md#microsoftstorage) tramite un ruolo personalizzato di Azure.

# <a name="portal"></a>[Portale](#tab/azure-portal)

All'interno del portale di Azure, il filtro tag indice BLOB applica automaticamente il `@container` parametro per definire l'ambito del contenitore selezionato. Se si vogliono filtrare e trovare i dati contrassegnati nell'intero account di archiviazione, usare l'API REST, gli SDK o gli strumenti.

1. Selezionare l'account di archiviazione nel [portale di Azure](https://portal.azure.com/). 

2. Passare all'opzione **contenitori** in **servizio BLOB**, selezionare il contenitore

3. Selezionare il pulsante **Filtro tag indice BLOB** per applicare il filtro all'interno del contenitore selezionato

4. Immettere una chiave tag di indice BLOB e un valore di tag

5. Selezionare il pulsante **Filtro tag indice BLOB** per aggiungere altri filtri tag (fino a 10)

:::image type="content" source="media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png" alt-text="Screenshot della portale di Azure che Mostra come filtrare e trovare i BLOB con tag con i tag di indice":::

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

4. Selezionare il set di **filtri** per aggiungere un filtro facoltativo per corrispondenza prefisso e indice BLOB

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png" alt-text="Screenshot della portale di Azure che illustra come aggiungere tag di indice per la gestione del ciclo di vita.":::

5. Selezionare **Verifica + Aggiungi** per esaminare le impostazioni della regola

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png" alt-text="Screenshot della portale di Azure che mostra una regola di gestione del ciclo di vita con esempio di filtro di tag di indice BLOB":::

6. Selezionare **Aggiungi** per applicare la nuova regola ai criteri di gestione del ciclo di vita

# <a name="net"></a>[.NET](#tab/net)

I criteri di [gestione del ciclo di vita](storage-lifecycle-management-concepts.md) vengono applicati per ogni account di archiviazione a livello di piano di controllo. Per .NET, installare la [libreria di archiviazione di Microsoft Azure Management](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) 16.0.0 o versione successiva.

---

## <a name="next-steps"></a>Passaggi successivi

 - Per altre informazioni sui tag degli indici BLOB, vedere [gestire e trovare i dati BLOB di Azure con tag di indice BLOB (anteprima)](storage-manage-find-blobs.md )
 - Altre informazioni sulla gestione del ciclo di vita, vedere [gestire il ciclo di vita dell'archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md)
