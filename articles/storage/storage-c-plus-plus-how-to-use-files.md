---
title: Eseguire lo sviluppo per l'archiviazione file di Azure con C++ | Microsoft Docs
description: Informazioni su come sviluppare applicazioni e servizi C++ che usano l'archiviazione file di Azure per archiviare i dati dei file.
services: storage
documentationcenter: .net
author: renashahmsft
manager: aungoo
editor: tysonn
ms.assetid: a1e8c99e-47a6-43a9-9541-c9262eb00b38
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2017
ms.author: renashahmsft
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: fc0d8451442f1337db4a36718c3fc746f8eb5125
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="develop-for-azure-file-storage-with-c"></a>Eseguire lo sviluppo per l'archiviazione file di Azure con C++
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>Informazioni sull'esercitazione

In questa esercitazione verrà illustrato come eseguire operazioni di base nell'archiviazione file di Azure. Gli esempi scritti in C++ consentono di apprendere come creare condivisioni e directory, caricare, elencare ed eliminare file. Se non si ha familiarità con l'archiviazione file di Azure, leggere le sezioni seguenti per comprendere gli esempi.


* Creare ed eliminare condivisioni file di Azure
* Creare ed eliminare directory
* Enumerare file e directory in una condivisione file di Azure
* Caricare, scaricare ed eliminare un file
* Impostare la quota (dimensione massima) per la condivisione file di Azure
* Creare una firma di accesso condiviso (chiave di firma di accesso condiviso) per un file che usa criteri di accesso condiviso definiti nella condivisione.

> [!Note]  
> Poiché l'archiviazione file di Azure è accessibile tramite SMB, è possibile scrivere semplici applicazioni che accedono alla condivisione file di Azure usando le classi e le funzioni standard I/O di C++. Questo articolo descrive come scrivere applicazioni basate su Azure Storage .NET SDK, che usa l'[API REST dell'archiviazione file Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api) per comunicare con l'archiviazione file di Azure.

## <a name="create-a-c-application"></a>Creazione di un’applicazione C++
Per compilare gli esempi, è necessario installare la libreria client di archiviazione di Azure 2.4.0 per C++. È inoltre necessario aver creato un account di archiviazione di Azure.

Per installare il client di archiviazione di Azure 2.4.0 per C++, è possibile usare i metodi seguenti:

* **Linux:** seguire le istruzioni fornite nella pagina [README della libreria client di Archiviazione di Azure per C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .
* **Windows:** in Visual Studio fare clic su **Strumenti &gt; Gestione pacchetti NuGet &gt; console di Gestione pacchetti**. Digitare il seguente comando nella [console Gestione pacchetti NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e premere **INVIO**.
  
```
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-azure-file-storage"></a>Configurare l'applicazione per usare l'archiviazione file di Azure
Aggiungere le istruzioni include seguenti all'inizio del file di origine C++ in cui si vuole modificare l'archiviazione file di Azure:

```cpp
#include <was/storage_account.h>
#include <was/file.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Impostare una stringa di connessione di archiviazione di Azure
Per utilizzare la condivisione di file, è necessario connettersi all'account di archiviazione di Azure. Il primo passaggio consiste nel configurare una stringa di connessione che verrà usata per connettersi all'account di archiviazione. È importante definire una variabile statica a tale scopo.

```cpp
// Define the connection-string with your values.
const utility::string_t 
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>Connessione a un account di archiviazione di Azure
Per visualizzare le informazioni dell'account di archiviazione, è possibile usare la classe **cloud_storage_account**. Per recuperare le informazioni sull'account di archiviazione dalla stringa di connessione alla risorsa di archiviazione, è possibile utilizzare il metodo **parse** .

```cpp
// Retrieve storage account from connection string.    
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure
Tutti i file e le directory nell'archiviazione file di Azure si trovano in un contenitore denominato **Share**. L'account di archiviazione può disporre del numero di condivisioni consentite dalla capacità dell'account. Per ottenere l'accesso a una condivisione e ai suoi contenuti, è necessario usare un client per l'archiviazione file di Azure.

```cpp
// Create the Azure File storage client.
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();
```

In questo modo è possibile ottenere un riferimento a una condivisione.

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

Per creare la condivisione, usare il metodo **create_if_not_exists** dell'oggetto **cloud_file_share**.

```cpp
if (share.create_if_not_exists()) {    
    std::wcout << U("New share created") << std::endl;    
}
```

A questo punto, **share** contiene un riferimento a una condivisione denominata **my-sample-share**.

## <a name="delete-an-azure-file-share"></a>Eliminare una condivisione file di Azure
L'eliminazione di una condivisione viene eseguita chiamando il metodo **delete_if_exists** in un oggetto cloud_file_share. Ecco il codice di esempio che esegue tale operazione.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="create-a-directory"></a>Creare una directory
È possibile organizzare l'archiviazione inserendo i file all'interno di sottodirectory anziché inserirli tutti nella directory radice. L'archiviazione file di Azure consente di creare tutte le directory consentite dall'account. Il codice seguente creerà una directory denominata **my-sample-directory** sotto la directory radice e una sottodirectory denominata **my-sample-subdirectory**.

```cpp
// Retrieve a reference to a directory
azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Return value is true if the share did not exist and was successfully created.
directory.create_if_not_exists();

// Create a subdirectory.
azure::storage::cloud_file_directory subdirectory = 
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
subdirectory.create_if_not_exists();
```

## <a name="delete-a-directory"></a>Eliminare una directory
Eliminare una directory è un'attività semplice, anche se occorre tenere presente che non è possibile eliminare una directory che contiene ancora file o altre directory.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// Get a reference to the directory.
azure::storage::cloud_file_directory directory = 
  share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Get a reference to the subdirectory you want to delete.
azure::storage::cloud_file_directory sub_directory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));

// Delete the subdirectory and the sample directory.
sub_directory.delete_directory_if_exists();

directory.delete_directory_if_exists();
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerare file e directory in una condivisione file di Azure
Ottenere un elenco di file e directory all'interno di una condivisione è facile chiamando **list_files_and_directories** in un riferimento **cloud_file_directory**. Per accedere al set completo di proprietà e metodi per un **list_file_and_directory_item** restituito, è necessario chiamare il metodo **list_file_and_directory_item.as_file** per ottenere un oggetto **cloud_file** oppure il metodo **list_file_and_directory_item.as_directory** per ottenere un oggetto **cloud_file_directory**.

Il codice seguente illustra come recuperare e visualizzare l'URI di ogni elemento della directory radice della condivisione.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = 
  share.get_root_directory_reference();

// Output URI of each item.
azure::storage::list_file_and_diretory_result_iterator end_of_results;

for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
{
    if(it->is_directory())
    {
        ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
    else if (it->is_file())
    {
        ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
    }        
}
```

## <a name="upload-a-file"></a>Caricare un file
Una condivisione file di Azure contiene almeno una directory radice in cui possono risiedere i file. In questa sezione verrà illustrato come caricare un file dall'archiviazione locale nella directory radice di una condivisione.

Il primo passaggio del caricamento di un file consiste nell'ottenere un riferimento alla directory in cui risiederà. È possibile eseguire questa operazione chiamando il metodo **get_root_directory_reference** dell'oggetto condivisione.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

Ora che si dispone di un riferimento alla directory radice della condivisione, è possibile caricarvi un file. Questo esempio esegue il caricamento da un file, da testo e da un flusso.

```cpp
// Upload a file from a stream.
concurrency::streams::istream input_stream = 
  concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

azure::storage::cloud_file file1 = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
file1.upload_from_stream(input_stream);

// Upload some files from text.
azure::storage::cloud_file file2 = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
file2.upload_text(_XPLATSTR("more text"));

// Upload a file from a file.
azure::storage::cloud_file file4 = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
file4.upload_from_file(_XPLATSTR("DataFile.txt"));    
```

## <a name="download-a-file"></a>Scaricare un file
Per scaricare i file, recuperare prima un riferimento al file e quindi chiamare il metodo **download_to_stream** per trasferire il contenuto del file in un oggetto flusso che è possibile memorizzare in modo permanente in un file locale. In alternativa, è possibile usare il metodo **download_to_file** per scaricare il contenuto di un file in un file locale. È possibile usare il metodo **download_text** per scaricare il contenuto di un file come stringa di testo.

L'esempio seguente usa i metodi **download_to_stream** e **download_text** per illustrare il download dei file creati nelle sezioni precedenti.

```cpp
// Download as text
azure::storage::cloud_file text_file = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
utility::string_t text = text_file.download_text();
ucout << "File Text: " << text << std::endl;

// Download as a stream.
azure::storage::cloud_file stream_file = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
stream_file.download_to_stream(output_stream);
std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();
outfile.write((char *)&data[0], buffer.size());
outfile.close();
```

## <a name="delete-a-file"></a>Eliminare un file
Un'altra operazione comunemente eseguita nell'archiviazione file di Azure è l'eliminazione dei file. Il codice seguente elimina un file denominato my-sample-file-3 archiviato nella directory radice.

```cpp
// Get a reference to the root directory for the share.    
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

azure::storage::cloud_file_directory root_dir = 
  share.get_root_directory_reference();

azure::storage::cloud_file file = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

file.delete_file_if_exists();
```

## <a name="set-the-quota-maximum-size-for-an-azure-file-share"></a>Impostare la quota (dimensione massima) per la condivisione file di Azure
È possibile impostare la quota o dimensione massima per una condivisione file in gigabyte. È anche possibile controllare la quantità di dati archiviata attualmente nella condivisione.

Impostando la quota per una condivisione, è possibile limitare la dimensione totale dei file archiviati nella condivisione. Se la dimensione totale dei file nella condivisione supera la quota impostata per la condivisione, i client non saranno in grado di aumentare le dimensioni dei file esistenti o creare nuovi file, a meno che tali file non siano vuoti.

L'esempio seguente illustra come controllare l'uso corrente per una condivisione e come impostare la quota per la condivisione.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client.
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();

// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
if (share.exists())
{
    std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();

    //This line sets the quota to 2560GB
    share.resize(2560);

    std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();

}
```

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Generare la firma di accesso condiviso per un file o una condivisione file
È possibile generare una firma di accesso condiviso per una condivisione file o un singolo file. È inoltre possibile creare un criterio di accesso condiviso in una condivisione file per gestire le firme di accesso condiviso. È consigliabile creare un criterio di accesso condiviso, in quanto fornisce un modo per revocare la firma SAS se necessario.

Nell'esempio seguente viene creato un criterio di accesso condiviso in una condivisione e quindi viene usato tale criterio per fornire i vincoli per una firma di accesso condiviso su un file della condivisione.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client and get a reference to the share
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();

azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

if (share.exists())
{
    // Create and assign a policy
    utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

    azure::storage::file_shared_access_policy sharedPolicy = 
      azure::storage::file_shared_access_policy();

    //set permissions to expire in 90 minutes
    sharedPolicy.set_expiry(utility::datetime::utc_now() + 
       utility::datetime::from_minutes(90));

    //give read and write permissions
    sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

    //set permissions for the share
    azure::storage::file_share_permissions permissions;    

    //retrieve the current list of shared access policies
    azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;

    //add the new shared policy
    policies.insert(std::make_pair(policy_name, sharedPolicy));

    //save the updated policy list
    permissions.set_policies(policies);
    share.upload_permissions(permissions);

    //Retrieve the root directory and file references
    azure::storage::cloud_file_directory root_dir = 
        share.get_root_directory_reference();
    azure::storage::cloud_file file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

    // Generate a SAS for a file in the share 
    //  and associate this access policy with it.        
    utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);

    // Create a new CloudFile object from the SAS, and write some text to the file.        
    azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
    utility::string_t text = _XPLATSTR("My sample content");        
    file_with_sas.upload_text(text);        

    //Download and print URL with SAS.
    utility::string_t downloaded_text = file_with_sas.download_text();        
    ucout << downloaded_text << std::endl;        
    ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;

}
```
## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Archiviazione di Azure, vedere le risorse seguenti:

* [Libreria client di archiviazione per C++](https://github.com/Azure/azure-storage-cpp)
* [Esempi del servizio di archiviazione file di Azure scritti in C++] (https://github.com/Azure-Samples/storage-file-cpp-getting-started)
* [Azure Storage Explorer](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Documentazione di Archiviazione di Azure](https://azure.microsoft.com/documentation/services/storage/)
