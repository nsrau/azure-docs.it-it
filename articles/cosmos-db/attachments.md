---
title: Allegati Azure Cosmos DB
description: Questo articolo presenta una panoramica degli allegati Azure Cosmos DB.
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/07/2020
ms.reviewer: sngun
ms.openlocfilehash: 847ae3688fb713ddbd2dbf196ad1b89a6bb472a1
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486634"
---
# <a name="azure-cosmos-db-attachments"></a>Allegati Azure Cosmos DB

Azure Cosmos DB allegati sono elementi speciali che contengono riferimenti a metadati associati con un BLOB o un file multimediale esterno.

Azure Cosmos DB supporta due tipi di allegati:

* Gli **allegati non gestiti** sono wrapper per un riferimento URI a un BLOB archiviato in un servizio esterno, ad esempio archiviazione di Azure, OneDrive e così via. Questo approccio è simile all'archiviazione di una proprietà URI in un elemento Azure Cosmos DB standard.
* Gli **allegati gestiti** sono BLOB gestiti e archiviati internamente da Azure Cosmos DB ed esposti tramite un mediaLink generato dal sistema.


> [!NOTE]
> L'allegato è una funzionalità legacy. Il supporto è limitato a offrire funzionalità continue se si usa già questa funzionalità.
> 
> Invece di usare gli allegati, è consigliabile usare l'archiviazione BLOB di Azure come servizio di archiviazione BLOB creato appositamente per archiviare i dati BLOB. È possibile continuare a archiviare i metadati relativi ai BLOB, insieme ai collegamenti URI di riferimento, in Azure Cosmos DB come proprietà degli elementi. L'archiviazione di questi dati in Azure Cosmos DB offre la possibilità di eseguire query sui metadati e i collegamenti ai BLOB archiviati nell'archivio BLOB di Azure.
> 
> Microsoft si impegna a fornire un preavviso minimo di 36 mesi prima della completa deprecazione degli allegati, che verrà annunciato a un ulteriore data.

## <a name="known-limitations"></a>Limitazioni note

Gli allegati gestiti di Azure Cosmos DB sono distinti dal supporto per gli elementi standard, per i quali offre scalabilità illimitata, distribuzione globale e integrazione con altri servizi di Azure.

- Gli allegati non sono supportati in tutte le versioni degli SDK del Azure Cosmos DB.
- Gli allegati gestiti sono limitati a 2 GB di spazio di archiviazione per ogni account di database.
- Gli allegati gestiti non sono compatibili con la distribuzione globale di Azure Cosmos DB e non vengono replicati tra le aree.

## <a name="migrating-attachments-to-azure-blob-storage"></a>Migrazione degli allegati all'archivio BLOB di Azure

È consigliabile eseguire la migrazione di Azure Cosmos DB allegati all'archivio BLOB di Azure attenendosi alla procedura seguente:

1. Copiare i dati allegati dal contenitore di Azure Cosmos DB di origine al contenitore di archiviazione BLOB di Azure di destinazione.
2. Convalidare i dati BLOB caricati nel contenitore di archiviazione BLOB di Azure di destinazione.
3. Se applicabile, aggiungere i riferimenti URI ai BLOB contenuti nell'archiviazione BLOB di Azure come proprietà di stringa all'interno del set di dati Azure Cosmos DB.
4. Effettuare il refactoring del codice dell'applicazione per leggere e scrivere BLOB dal nuovo contenitore di archiviazione BLOB di Azure.

L'esempio di codice DotNet seguente mostra come copiare gli allegati da Azure Cosmos DB nell'archiviazione BLOB di Azure come parte di un flusso di migrazione con Azure Cosmos DB .NET SDK v2 e Azure BLOB Storage .NET SDK V12. Assicurarsi di sostituire `<placeholder values>` per l'account Azure Cosmos DB di origine e il contenitore di archiviazione BLOB di Azure di destinazione.

```csharp

using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;

namespace attachments
{
    class Program
    {
        private static string cosmosAccount = "<Your_Azure_Cosmos_account_URI>";
        private static string cosmosKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>";
        private static string cosmosDatabaseName = "<Your_Azure_Cosmos_database>";
        private static string cosmosCollectionName = "<Your_Azure_Cosmos_collection>";
        private static string storageConnectionString = "<Your_Azure_Storage_connection_string>";
        private static string storageContainerName = "<Your_Azure_Storage_container_name>";
        private static DocumentClient cosmosClient = new DocumentClient(new Uri(cosmosAccount), cosmosKey);
        private static BlobServiceClient storageClient = new BlobServiceClient(storageConnectionString);
        private static BlobContainerClient storageContainerClient = storageClient.GetBlobContainerClient(storageContainerName);

        static void Main(string[] args)
        {
            CopyAttachmentsToBlobsAsync().Wait();
        }

        private async static Task CopyAttachmentsToBlobsAsync()
        {
            Console.WriteLine("Copying Azure Cosmos DB Attachments to Azure Blob Storage ...");

            int totalCount = 0;
            string docContinuation = null;

            // Iterate through each item (document in v2) in the Azure Cosmos DB container (collection in v2) to look for attachments.
            do
            {
                FeedResponse<dynamic> response = await cosmosClient.ReadDocumentFeedAsync(
                    UriFactory.CreateDocumentCollectionUri(cosmosDatabaseName, cosmosCollectionName),
                    new FeedOptions
                    {
                        MaxItemCount = -1,
                        RequestContinuation = docContinuation
                    });
                docContinuation = response.ResponseContinuation;

                foreach (Document document in response)
                {
                    string attachmentContinuation = null;
                    PartitionKey docPartitionKey = new PartitionKey(document.Id);

                    // Iterate through each attachment within the item (if any).
                    do
                    {
                        FeedResponse<Attachment> attachments = await cosmosClient.ReadAttachmentFeedAsync(
                            document.SelfLink,
                            new FeedOptions
                            {
                                PartitionKey = docPartitionKey,
                                RequestContinuation = attachmentContinuation
                            }
                        );
                        attachmentContinuation = attachments.ResponseContinuation;

                        foreach (var attachment in attachments)
                        {
                            // Download the attachment in to local memory.
                            MediaResponse content = await cosmosClient.ReadMediaAsync(attachment.MediaLink);

                            byte[] buffer = new byte[content.ContentLength];
                            await content.Media.ReadAsync(buffer, 0, buffer.Length);

                            // Upload the locally buffered attachment to blob storage
                            string blobId = String.Concat(document.Id, "-", attachment.Id);

                            Azure.Response<BlobContentInfo> uploadedBob = await storageContainerClient.GetBlobClient(blobId).UploadAsync(
                                new MemoryStream(buffer, writable: false),
                                true
                            );

                            Console.WriteLine("Copied attachment ... Item Id: {0} , Attachment Id: {1}, Blob Id: {2}", document.Id, attachment.Id, blobId);
                            totalCount++;

                            // Clean up attachment from Azure Cosmos DB.
                            // Warning: please verify you've succesfully migrated attachments to blog storage prior to cleaning up Azure Cosmos DB.
                            // await cosmosClient.DeleteAttachmentAsync(
                            //     attachment.SelfLink,
                            //     new RequestOptions { PartitionKey = docPartitionKey }
                            // );

                            // Console.WriteLine("Cleaned up attachment ... Document Id: {0} , Attachment Id: {1}", document.Id, attachment.Id);
                        }

                    } while (!string.IsNullOrEmpty(attachmentContinuation));
                }
            }
            while (!string.IsNullOrEmpty(docContinuation));

            Console.WriteLine("Finished copying {0} attachments to blob storage", totalCount);
        }
    }
}

```

## <a name="next-steps"></a>Passaggi successivi

- Introduzione all' [archiviazione BLOB di Azure](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- Ottenere i riferimenti per l'uso degli allegati tramite [.NET SDK v2 Azure Cosmos DB](/dotnet/api/microsoft.azure.documents.attachment?preserve-view=true&view=azure-dotnet)
- Ottenere i riferimenti per l'uso degli allegati tramite [Java SDK v2 di Azure Cosmos DB](/java/api/com.microsoft.azure.documentdb.attachment?preserve-view=true&view=azure-java-stable)
- Ottenere riferimenti per l'uso di allegati tramite [l'API REST di Azure Cosmos DB](/rest/api/cosmos-db/attachments)