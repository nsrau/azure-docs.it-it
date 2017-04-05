---
title: Esempi di Archiviazione di Azure che usano .NET | Microsoft Docs
description: Visualizzare, scaricare ed eseguire codici di esempio e applicazioni per l&quot;Archiviazione di Azure. Individuare esempi introduttivi per BLOB, code, tabelle e file usando le librerie client di archiviazione .NET.
services: storage
documentationcenter: na
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 01/12/2017
ms.author: seguler
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: d2b6b3d9483f230ad25ae47255a4f28c1a67e064
ms.lasthandoff: 03/29/2017


---
# <a name="azure-storage-samples-using-net"></a>Esempi di Archiviazione di Azure che usano .NET

## <a name="net-sample-index"></a>Indice degli esempi .NET

La tabella seguente fornisce una panoramica del repository di esempi e degli scenari presentati in ogni esempio. Fare clic sui collegamenti per visualizzare il codice di esempio corrispondente in GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Endpoint</th><th style="font-size:110%">Scenario</th><th style="font-size:110%">Codice di esempio</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>BLOB</b></td>
<td>Append Blob</td> 
<td><a href="https://msdn.microsoft.com/en-us/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.getappendblobreference.aspx">CloudBlobContainer.GetAppendBlobReference Method Example</a> (Esempio di metodo CloudBlobContainer.GetAppendBlobReference)</td> 
</tr> 
<tr> 
<td>BLOB in blocchi</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery Web Application</a></td>
</tr> 
<tr> 
<td>Crittografia lato client</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">Blob Encryption Samples</a> (Esempi di crittografia dei BLOB)</td>
</tr> 
<tr> 
<td>Copia di BLOB</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Getting Started with Blobs</a> (Introduzione ai BLOB)</td>
</tr> 
<tr> 
<td>Creazione contenitore</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery Web Application</a></td>
</tr> 
<tr> 
<td>Eliminazione BLOB</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery Web Application</a></td>
</tr> 
<tr> 
<td>Eliminazione contenitore</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Getting Started with Blobs</a> (Introduzione ai BLOB)</td>
</tr> 
<tr> 
<td>Metadati/proprietà/statistiche BLOB</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Getting Started with Blobs</a> (Introduzione ai BLOB)</td>
</tr> 
<tr> 
<td>ACL/metadati/proprietà contenitore</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery Web Application</a></td>
</tr> 
<tr> 
<td>Ottenere intervalli di pagine</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Getting Started with Blobs</a> (Introduzione ai BLOB)</td>
</tr> 
<tr> 
<td>BLOB/contenitore di lease</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Getting Started with Blobs</a> (Introduzione ai BLOB)</td>
</tr> 
<tr> 
<td>Elenco BLOB/contenitore</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Getting Started with Blobs</a> (Introduzione ai BLOB)</td>
</tr> 
<tr> 
<td>BLOB di pagine</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Getting Started with Blobs</a> (Introduzione ai BLOB)</td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Getting Started with Blobs</a> (Introduzione ai BLOB)</td>
</tr>     
<tr> 
<td>Proprietà del servizio</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Getting Started with Blobs</a> (Introduzione ai BLOB)</td>
</tr>             
<tr> 
<td>Snapshot di BLOB</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Backup dei dischi delle macchine virtuali di Azure con snapshot incrementali</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>File</b></td>
<td>Creazione condivisioni/directory/file</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET File Storage Sample</a> (Esempio di archiviazione file .NET in Archiviazione di Azure)</td> 
</tr>
<tr> 
<td>Eliminazione condivisioni/directory/file</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Getting Started with Azure File Service in .NET</a></td> 
</tr> 
<tr> 
<td>Proprietà/metadati di directory</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage Sample</a> (Esempio di archiviazione file .NET in Archiviazione di Azure)</td> 
</tr> 
<tr> 
<td>Download di file</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET File Storage Sample</a> (Esempio di archiviazione file .NET in Archiviazione di Azure)</td> 
</tr> 
<tr> 
<td>Proprietà/metadati/metriche di file</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage Sample</a> (Esempio di archiviazione file .NET in Archiviazione di Azure)</td> 
</tr> 
<tr> 
<td>Proprietà del servizio file</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage Sample</a> (Esempio di archiviazione file .NET in Archiviazione di Azure)</td> 
</tr> 
<tr> 
<td>Elenco di directory e file</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET File Storage Sample</a> (Esempio di archiviazione file .NET in Archiviazione di Azure)</td> 
</tr>
<tr> 
<td>Elenco di condivisioni</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage Sample</a> (Esempio di archiviazione file .NET in Archiviazione di Azure)</td> 
</tr>
<tr> 
<td>Condivisione di proprietà/metadati/statistiche</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage Sample</a> (Esempio di archiviazione file .NET in Archiviazione di Azure)</td> 
</tr>
<tr> 
<td rowspan="8"><b>Coda</b></td>
<td>Aggiunta di un messaggio</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Getting Started with Azure Queue Service in .NET</a></td> 
</tr> 
<tr> 
<td>Crittografia lato client</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Azure Storage .NET Queue Client-Side Encryption</a> (Crittografia lato client della coda di Archiviazione di Azure .NET)</td> 
</tr> 
<tr> 
<td>Creazione di code</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Getting Started with Azure Queue Service in .NET</a></td> 
</tr> 
<tr> 
<td>Eliminazione di messaggio/coda</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Getting Started with Azure Queue Service in .NET</a></td> 
</tr> 
<tr> 
<td>Visualizzazione di messaggio</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Getting Started with Azure Queue Service in .NET</a></td> 
</tr> 
<tr> 
<td>ACL/metadati/statistiche di coda</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Getting Started with Azure Queue Service in .NET</a></td> 
</tr> 
<tr> 
<td>Proprietà del servizio di accodamento</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Getting Started with Azure Queue Service in .NET</a></td> 
</tr> 
<tr> 
<td>Aggiornamento messaggio</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Getting Started with Azure Queue Service in .NET</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tabella</b></td>
<td>Creazione di tabella</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Gestione della concorrenza con l'archiviazione di Azure - Applicazione di esempio</a></td> 
</tr> 
<tr> 
<td>Eliminazione entità/tabella</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Introduzione all'archiviazione tabelle di Azure con .NET</a></td> 
</tr> 
<tr> 
<td>Inserimento/unione/sostituzione di entità</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Gestione della concorrenza con l'archiviazione di Azure - Applicazione di esempio</a></td> 
</tr> 
<tr> 
<td>Query su entità</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Introduzione all'archiviazione tabelle di Azure con .NET</a></td> 
</tr> 
<tr> 
<td>Query su tabelle</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Introduzione all'archiviazione tabelle di Azure con .NET</a></td> 
</tr> 
<tr> 
<td>ACL/proprietà di tabella</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">Introduzione all'archiviazione tabelle di Azure con .NET</a></td> 
</tr> 
<tr> 
<td>Aggiornamento di entità</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Gestione della concorrenza con l'archiviazione di Azure - Applicazione di esempio</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Libreria di esempi di codice per Azure

Per visualizzare la libreria completa, accedere alla libreria [Esempi di codice di Azure](https://azure.microsoft.com/resources/samples/?service=storage), nella quale sono disponibili esempi per Archiviazione di Azure scaricabili ed eseguibili in locale. La libreria degli esempi di codice fornisce il codice di esempio in formato .zip. In alternativa, è possibile esplorare e clonare l'archivio GitHub per ogni esempio.

[!INCLUDE [storage-dotnet-samples-include](../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Guide introduttive

Per istruzioni su come installare e iniziare a utilizzare le librerie client di Archiviazione di Azure, consultare le seguenti guide.

* [Getting Started with Azure Blob Service in .NET](storage-dotnet-how-to-use-blobs.md)
* [Getting Started with Azure Queue Service in .NET](storage-dotnet-how-to-use-queues.md)
* [Getting Started with Azure Table Service in .NET](storage-dotnet-how-to-use-tables.md)
* [Getting Started with Azure File Service in .NET](storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su esempi con altri linguaggi:

* Java: [Esempi di Archiviazione di Azure con Java](storage-samples-java.md)
* Tutti gli altri linguaggi: [Esempi di archiviazione di Azure](storage-samples.md)

