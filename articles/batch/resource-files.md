---
title: Creazione e utilizzo di file di risorse - Azure BatchCreating and using resource files - Azure Batch
description: Informazioni su come creare file di risorse Batch da varie origini di input. Questo articolo illustra alcuni metodi comuni su come crearli e inserirli in una macchina virtuale.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 03/18/2020
ms.author: labrenne
ms.openlocfilehash: 0fe859ac30e7b8050d1f4688d7cf106a465e7566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531142"
---
# <a name="creating-and-using-resource-files"></a>Creazione e utilizzo di file di risorse

Un'attività Batch di Azure spesso richiede una qualche forma di dati da elaborare. I file di risorse sono il modo per fornire questi dati alla macchina virtuale Batch (VM) tramite un'attività. Tutti i tipi di attività supportano i file di risorse: attività, attività di avvio, attività di preparazione dei processi, attività di rilascio dei processi e così via. Questo articolo illustra alcuni metodi comuni per creare file di risorse e inserirli in una macchina virtuale.  

I file di risorse inseriscono i dati in una macchina virtuale in Batch, ma il tipo di dati e il modo in cui vengono usati è flessibile. Esistono, tuttavia, alcuni casi d'uso comuni:

1. Effettuare il provisioning di file comuni in ogni macchina virtuale usando i file di risorse in un'attività di avvioProvision common files on each VM using resource files on a start task
1. Effettuare il provisioning dei dati di input che devono essere elaborati dalle attività

I file comuni possono essere, ad esempio, i file in un'attività di avvio utilizzata per installare le applicazioni eseguite dalle attività. I dati di input possono essere dati di immagine o video non elaborati o qualsiasi informazione che deve essere elaborata da Batch.

## <a name="types-of-resource-files"></a>Tipi di file di risorse

Sono disponibili diverse opzioni per generare file di risorse. Il processo di creazione dei file di risorse varia a seconda della posizione in cui sono archiviati i dati originali.

Opzioni per la creazione di un file di risorse:Options for creating a resource file:

- [URL contenitore di archiviazione:](#storage-container-url)genera un file di risorse da qualsiasi contenitore di archiviazione in AzureStorage container URL : Generates a resource file from any storage container in Azure
- [Nome contenitore di archiviazione:](#storage-container-name)genera un file di risorse dal nome di un contenitore in un account di archiviazione di Azure collegato a BatchStorage container name : Generates a resource file from the name of a container in an Azure storage account linked to Batch
- [Endpoint Web:](#web-endpoint)genera un file di risorse da qualsiasi URL HTTP valido

### <a name="storage-container-url"></a>URL del contenitore di archiviazione

L'uso di un URL del contenitore di archiviazione significa, con le autorizzazioni corrette, che è possibile accedere ai file in qualsiasi contenitore di archiviazione in Azure.Using a storage container URL means, with the correct permissions, you can access files in any storage container in Azure. 

In questo esempio di C, i file sono già stati caricati in un contenitore di archiviazione di Azure come archiviazione BLOB. Per accedere ai dati necessari per creare un file di risorse, è innanzitutto necessario ottenere l'accesso al contenitore di archiviazione.

Creare un URI della firma di accesso condiviso con le autorizzazioni corrette per accedere al contenitore di archiviazione. Impostare l'ora di scadenza e le autorizzazioni per la sAS. In questo caso, non viene specificata alcuna ora di inizio, pertanto la firma di firma di rete diventa immediatamente valida e scade due ore dopo la generazione.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Per l'accesso al `Read` contenitore, è necessario disporre di entrambe le `List` autorizzazioni, mentre con l'accesso al BLOB è necessaria `Read` solo l'autorizzazione.

Dopo aver configurato le autorizzazioni, creare il token di firma di accesso condiviso e formattare l'URL di firma di accesso condiviso per l'accesso al contenitore di archiviazione. Utilizzando l'URL di chiamata formattato per il [`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet)contenitore di archiviazione, generare un file di risorse con .

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Un'alternativa alla generazione di un URL di accesso condiviso consiste nell'abilitare l'accesso in lettura pubblico anonimo a un contenitore e ai relativi BLOB nell'archiviazione BLOB di Azure.An alternative to generating a SAS URL is to enable anonymous, public read-access to a container and its blobs in Azure Blob storage. In questo modo, è possibile concedere l'accesso in sola lettura a queste risorse senza condividere la chiave dell'account e senza richiedere una sAS. L'accesso in lettura pubblico viene in genere usato per gli scenari in cui si vuole che determinati BLOB siano sempre disponibili per l'accesso in lettura anonimo. Se questo scenario si adatta alla soluzione, vedere [l'articolo Accesso anonimo ai BLOB](../storage/blobs/storage-manage-access-to-resources.md) per altre informazioni sulla gestione dell'accesso ai dati BLOB.

### <a name="storage-container-name"></a>Nome del contenitore di archiviazione

Anziché configurare e creare un URL di accesso condiviso, è possibile usare il nome del contenitore di archiviazione di Azure per accedere ai dati BLOB. Il contenitore di archiviazione usato deve trovarsi nell'account di archiviazione di Azure collegato all'account Batch.The storage container you use must be in the Azure storage account that's linked to your Batch account. Tale account di archiviazione è noto come account di archiviazione automatica. L'uso del contenitore di archiviazione automatica consente di ignorare la configurazione e la creazione di un URL di accesso condiviso per accedere a un contenitore di archiviazione.

In questo esempio si presuppone che i dati da usare per la creazione di file di risorse si trovano già in un account di Archiviazione di Azure collegato all'account Batch.In this example, we assume that the data to be used for resource file creation is already in an Azure Storage account linked to your Batch account. Se non si dispone di un account di archiviazione automatica, vedere la procedura descritta in [Creare un account Batch](batch-account-create-portal.md) per informazioni dettagliate su come creare e collegare un account.

Usando un account di archiviazione collegato, non è necessario creare e configurare un URL di chiamata in stato di scambio in un contenitore di archiviazione. Specificare invece il nome del contenitore di archiviazione nell'account di archiviazione collegato.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Endpoint Web

I dati non caricati in Archiviazione di Azure possono comunque essere usati per creare file di risorse. È possibile specificare qualsiasi URL HTTP valido contenente i dati di input. L'URL viene fornito all'API Batch, quindi i dati vengono utilizzati per creare un file di risorse.

Nell'esempio in C, i dati di input sono ospitati in un endpoint GitHub fittizio. L'API recupera il file dall'endpoint Web valido e genera un file di risorse che deve essere utilizzato dall'attività. Non sono necessarie credenziali per questo scenario.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Suggerimenti

Ogni attività batch di Azure usa i file in modo diverso, motivo per cui Batch include opzioni disponibili per la gestione dei file nelle attività. Gli scenari seguenti non sono pensati per essere completi, ma coprono alcune situazioni comuni e forniscono consigli.

### <a name="many-resource-files"></a>Molti file di risorse

Il processo Batch può contenere diverse attività che utilizzano tutti gli stessi file comuni. Se i file di attività comuni vengono condivisi tra molte attività, l'utilizzo di un pacchetto dell'applicazione per contenere i file anziché i file di risorse può essere un'opzione migliore. I pacchetti di applicazioni forniscono l'ottimizzazione per la velocità di download. Inoltre, i dati nei pacchetti dell'applicazione vengono memorizzati nella cache tra le attività, pertanto se i file delle attività non cambiano spesso, i pacchetti dell'applicazione potrebbero essere adatti alla soluzione. Con i pacchetti dell'applicazione, non è necessario gestire manualmente diversi file di risorse o generare URL di accesso sas per accedere ai file in Archiviazione di Azure.With application packages, you don't need to manually manage several resource files or generate SAS URLs to access the files in Azure Storage. Batch funziona in background con Archiviazione di Azure per archiviare e distribuire pacchetti dell'applicazione nei nodi di calcolo.

Se ogni attività ha molti file univoci per tale attività, i file di risorse sono l'opzione migliore perché le attività che utilizzano file univoci spesso devono essere aggiornate o sostituite, il che non è così facile da fare con il contenuto dei pacchetti di applicazioni. I file di risorse offrono maggiore flessibilità per l'aggiornamento, l'aggiunta o la modifica di singoli file.

### <a name="number-of-resource-files-per-task"></a>Numero di file di risorse per attività

Se per un'attività sono specificati diverse centinaia di file di risorse, Batch potrebbe rifiutare l'attività come troppo grande. È consigliabile ridurre al minimo le attività riducendo al minimo il numero di file di risorse per l'attività stessa.

Se non è possibile ridurre al minimo il numero di file necessari per l'attività, è possibile ottimizzare l'attività creando un singolo file di risorse che fa riferimento a un contenitore di archiviazione di file di risorse. A tale scopo, inserire i file di risorse in un contenitore di Archiviazione di Azure e usare i diversi [metodi](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile?view=azure-dotnet#methods) "contenitore" per i file di risorse. Usare le opzioni del prefisso BLOB per specificare raccolte di file da scaricare per le attività.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui [pacchetti dell'applicazione](batch-application-packages.md) in alternativa ai file di risorse.
- Per altre informazioni sull'uso dei contenitori per i file di risorse, vedere [Carichi di lavoro del contenitore.](batch-docker-container-workloads.md)
- Per informazioni su come raccogliere e salvare i dati di output dalle attività, vedere Rendere persistente l'output del [processo e dell'attività](batch-task-output.md).
- Informazioni sulle [API e gli strumenti di Batch](batch-apis-tools.md) disponibili per la compilazione di soluzioni Batch.
