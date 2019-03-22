---
title: '.NET SDK: Operazioni del file system in Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Usare Azure Data Lake Storage Gen1 .NET SDK per eseguire operazioni del file System in Data Lake Storage Gen1, ad esempio creare cartelle e così via.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 0a6cfc5ca9a0b788075f4472649a6e1bcdf87637
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58119497"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Operazioni del file System in Azure Data Lake Storage Gen1 con .NET SDK
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [SDK per Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

Questo articolo descrive come eseguire operazioni del file system in Data Lake Storage Gen1 con .NET SDK. Le operazioni del file system includono la creazione di cartelle in un account Data Lake Storage Gen1, il caricamento di file, il download di file e così via.

Per istruzioni su come eseguire le operazioni di gestione di account in Data Lake Storage Gen1 con .NET SDK, consultare [Operazioni di gestione di account in Data Lake Storage Gen1 con .NET SDK](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Prerequisiti
* **Visual Studio 2013, 2015 o 2017**. Le istruzioni seguenti fanno riferimento a Visual Studio 2017.

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Account Azure Data Lake Storage Gen1**. Per istruzioni su come creare un account, consultare [Introduzione ad Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="create-a-net-application"></a>Creare un'applicazione .NET
Il codice di esempio disponibile in [GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) offre una descrizione dei processi di creazione dei file nell'archivio, concatenazione dei file, download di un file ed eliminazione di alcuni file nell'archivio. Questa sezione dell'articolo descrive in modo dettagliato le parti principali del codice.

1. Aprire Visual Studio e creare un'applicazione console.
2. Scegliere **Nuovo** dal menu **File** e quindi fare clic su **Progetto**.
3. In **Nuovo progetto**digitare o selezionare i valori seguenti:

   | Proprietà | Valore |
   | --- | --- |
   | Categoria |Templates/Visual C#/Windows |
   | Modello |Applicazione console |
   | NOME |CreateADLApplication |

4. Fare clic su **OK** per creare il progetto.

5. Aggiungere i pacchetti NuGet al progetto.

   1. Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**.
   2. Nella scheda **Gestione pacchetti NuGet** assicurarsi che **Origine pacchetto** sia impostato su **nuget.org** e che la casella di controllo **Includi versione preliminare** sia selezionata.
   3. Cercare e installare i pacchetti NuGet seguenti:

      * `Microsoft.Azure.DataLake.Store` - Questa esercitazione usa la versione 1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - Questa esercitazione usa la versione 2.3.1.
    
      Chiudere **Gestione pacchetti NuGet**.

6. Aprire **Program.cs**, eliminare il codice esistente e quindi includere le istruzioni seguenti per aggiungere riferimenti agli spazi dei nomi.

        using System;
        using System.IO;using System.Threading;
        using System.Linq;
        using System.Text;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.DataLake.Store;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. Dichiarare le variabili come illustrato di seguito e fornire i valori per i segnaposto. Assicurarsi anche che il percorso locale e il nome file forniti siano presenti nel computer.

        namespace SdkSample
        {
            class Program
            {
                private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";        
            }
        }

Nelle sezioni rimanenti dell'articolo è possibile vedere come usare i metodi .NET disponibili per eseguire operazioni come autenticazione, caricamento di file e così via.

## <a name="authentication"></a>Authentication

* Per l'autenticazione dell'utente finale per l'applicazione, vedere [Autenticazione dell'utente finale con Data Lake Storage Gen1 con .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Per l'autenticazione da servizio a servizio per l'applicazione, vedere [Autenticazione da servizio a servizio con Data Lake Storage Gen1 tramite .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).


## <a name="create-client-object"></a>Creare un oggetto client
Il frammento di codice seguente crea l'oggetto client del file System di Data Lake Storage Gen1, che viene usato per inviare le richieste al servizio.

    // Create client objects
    AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);

## <a name="create-a-file-and-directory"></a>Creare un file e una directory
Aggiungere il frammento di codice seguente all'applicazione. Questo frammento di codice aggiunge un file ed eventuali directory padre ancora inesistenti.

    // Create a file - automatically creates any parent directories that don't exist
    // The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store
    using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);

        textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="append-to-a-file"></a>Aggiungere a un file
Il frammento di codice seguente accoda dati a un file esistente nell'account Data Lake Storage Gen1.

    // Append to existing file
    using (var stream = client.GetAppendStream(fileName))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="read-a-file"></a>Leggere un file
Il frammento di codice seguente legge i contenuti di un file in Data Lake Storage Gen1.

    //Read file contents
    using (var readStream = new StreamReader(client.GetReadStream(fileName)))
    {
        string line;
        while ((line = readStream.ReadLine()) != null)
        {
            Console.WriteLine(line);
        }
    }

## <a name="get-file-properties"></a>Ottenere le proprietà dei file
Il frammento di codice seguente restituisce le proprietà associate a un file o una directory.

    // Get file properties
    var directoryEntry = client.GetDirectoryEntry(fileName);
    PrintDirectoryEntry(directoryEntry);

La definizione del metodo `PrintDirectoryEntry` è disponibile come parte dell'esempio [su GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted). 

## <a name="rename-a-file"></a>Rinominare un file
Il frammento di codice seguente rinomina un file esistente in un account Data Lake Storage Gen1.

    // Rename a file
    string destFilePath = "/Test/testRenameDest3.txt";
    client.Rename(fileName, destFilePath, true);

## <a name="enumerate-a-directory"></a>Enumerare una directory
Il frammento di codice seguente enumera le directory in un account Data Lake Storage Gen1

    // Enumerate directory
    foreach (var entry in client.EnumerateDirectory("/Test"))
    {
        PrintDirectoryEntry(entry);
    }

La definizione del metodo `PrintDirectoryEntry` è disponibile come parte dell'esempio [su GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Eliminare directory in modo ricorsivo
Il frammento di codice seguente elimina una directory e tutte le relative sottodirectory in modo ricorsivo.

    // Delete a directory and all its subdirectories and files
    client.DeleteRecursive("/Test");

## <a name="samples"></a>Esempi
Ecco alcuni esempi che illustrano come usare il SDK del file System di Data Lake Storage Gen1.
* [Esempio di base in GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Esempio avanzato in GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Vedere anche 
* [Operazioni di gestione di account in Data Lake Storage Gen1 con .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Riferimento a .NET SDK di Data Lake Storage Gen1](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Passaggi successivi
* [Proteggere i dati in Data Lake Storage Gen1](data-lake-store-secure-data.md)
