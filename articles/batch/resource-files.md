---
title: Creazione e utilizzo di file di risorse - Azure Batch | Microsoft Docs
description: Informazioni su come creare i file di risorse di Azure Batch da diverse origini di input.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 03/14/2019
ms.author: lahugh
ms.openlocfilehash: 113faffb0ebac50a67c96ce21e0ee2c1564bb4fc
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65405649"
---
# <a name="creating-and-using-resource-files"></a>Creazione e utilizzo di file di risorse

Attività di Azure Batch richiede spesso alcune operazioni di dati da elaborare. File di risorse sono la possibilità di fornire questi dati alla macchina virtuale Batch (VM) tramite un'attività. Tutti i tipi di attività supportano i file di risorse: inizio delle attività, attività, attività di preparazione dei processi, le attività di rilascio e così via. Questo articolo descrive alcuni metodi comuni su come creare i file di risorse e inserirli in una macchina virtuale.  

File di risorse sono un meccanismo per inserire dati in una macchina virtuale in Batch, ma il tipo di dati e modalità di utilizzo è flessibile. Esistono, tuttavia, casi d'uso comuni:

1. Effettuare il provisioning di file comuni in ogni macchina virtuale usando file di risorse in un'attività di avvio
1. Effettuare il provisioning di dati di input per essere elaborati dalle attività

File comuni potrebbe essere, ad esempio, i file in un'attività di avvio usata per installare le applicazioni eseguite dalle attività. I dati di input può essere dati video o immagine non elaborata o informazioni devono essere elaborati dal Batch.

## <a name="types-of-resource-files"></a>Tipi di file di risorse

Esistono alcune diverse opzioni disponibili per generare i file di risorse. Il processo di creazione per i file di risorse varia a seconda di dove sono archiviati i dati originali.

Opzioni per la creazione di un file di risorse:

- [URL del contenitore di archiviazione](#storage-container-url): Genera un file di risorse da qualsiasi contenitore di archiviazione in Azure
- [Nome del contenitore di archiviazione](#storage-container-name): Genera un file di risorse dal nome di un contenitore in un account di archiviazione di Azure collegato a Batch
- [Endpoint Web](#web-endpoint): Genera un file di risorse da qualsiasi URL HTTP valido

### <a name="storage-container-url"></a>URL del contenitore di archiviazione

Usando un URL del contenitore di archiviazione, significa che è possibile accedere ai file in qualsiasi contenitore di archiviazione in Azure con le autorizzazioni corrette.

In questo C# esempio, i file sono già stati caricati in un contenitore di archiviazione di Azure come risorsa di archiviazione blob. Per accedere a tutti i dati necessari per creare un file di risorse, è necessario innanzitutto ottenere l'accesso al contenitore di archiviazione.

Creare una firma di accesso condiviso (SAS) URI con le autorizzazioni corrette per accedere al contenitore di archiviazione. Impostare la data di scadenza e le autorizzazioni per la firma di accesso condiviso. In questo caso, non viene specificata nessuna ora di inizio, in modo che la firma di accesso condiviso diventa valida immediatamente e scade due ore dopo essere stato generato.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Per l'accesso al contenitore, deve avere entrambe `Read` e `List` le autorizzazioni, mentre con l'accesso al blob, è sufficiente `Read` l'autorizzazione.

Dopo aver configurate le autorizzazioni, creare il token di firma di accesso condiviso e formattare l'URL di firma di accesso condiviso per l'accesso al contenitore di archiviazione. Usando l'URL di firma di accesso condiviso formattato per il contenitore di archiviazione, generare un file di risorse con [ `FromStorageContainerUrl` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet).

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Un'alternativa alla generazione di un URL di firma di accesso condiviso consiste nell'abilitare l'accesso in lettura anonimo, pubblica a un contenitore e ai relativi BLOB in archiviazione Blob di Azure. In questo modo, è possibile concedere l'accesso di sola lettura a queste risorse senza condividere la chiave dell'account e senza richiedere una firma di accesso condiviso. Accesso in lettura pubblico viene in genere usato per gli scenari in cui si vuole che BLOB specifici siano sempre disponibili per l'accesso in lettura anonimo. Se questo scenario adatta la soluzione, vedere la [l'accesso anonimo ai BLOB](../storage/blobs/storage-manage-access-to-resources.md) articolo per altre informazioni sulla gestione dell'accesso ai dati blob.

### <a name="storage-container-name"></a>Nome del contenitore di archiviazione

Invece di configurazione e creazione di un URL di firma di accesso condiviso, è possibile usare il nome del contenitore di archiviazione di Azure per accedere ai dati blob. Il contenitore di archiviazione usato deve nell'account di archiviazione di Azure collegata all'account Batch, noto come account autostorage. Usando il nome di contenitore di archiviazione di un account autostorage consente di ignorare la configurazione e la creazione di un URL di firma di accesso condiviso per accedere a un contenitore di archiviazione.

In questo esempio si presuppone che i dati da utilizzare per la creazione di file di risorse sono già in un account di archiviazione di Azure collegato all'account Batch. Se non hai un account autostorage, vedere la procedura descritta in [creare un account Batch](batch-account-create-portal.md) per informazioni dettagliate su come creare e collegare un account.

Usando un account di archiviazione collegato, non è necessario creare e configurare un URL di firma di accesso condiviso per un contenitore di archiviazione. In alternativa, specificare il nome del contenitore di archiviazione nell'account di archiviazione collegato.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Endpoint Web

I dati che non vero caricati in archiviazione di Azure sono ancora utilizzabile per creare i file di risorse. È possibile specificare qualsiasi URL HTTP valido che contiene i dati di input. L'URL è disponibile per l'API Batch e quindi i dati vengono utilizzati per creare un file di risorse.

Nell'esempio seguente C# esempio, i dati di input è ospitata nell'endpoint di GitHub fittizia. L'API consente di recuperare il file dall'endpoint di web valido e genera un file di risorse da parte dell'attività. Non sono necessarie credenziali per questo scenario.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Suggerimenti

Ogni attività Batch di Azure Usa i file in modo diverso, motivo per cui il Batch contiene le opzioni disponibili per la gestione dei file delle attività. Gli scenari seguenti non sono destinate per completezza, ma invece coprire alcune situazioni comuni e fornire raccomandazioni.

### <a name="many-resource-files"></a>Numero di file di risorse

Il processo Batch può contenere diverse attività che utilizzano tutti i file comuni, stessi. Se i file delle attività comuni sono condivisi da numerose attività, l'utilizzo di un pacchetto di applicazione per contenere i file invece di usare i file di risorse può essere un'opzione migliore. I pacchetti dell'applicazione provvedere all'ottimizzazione per la velocità di download. Inoltre, vengono memorizzati i dati nei pacchetti dell'applicazione tra le attività, pertanto se i file di attività non viene modificato spesso, i pacchetti dell'applicazione potrebbero essere una scelta ideale per la soluzione. I pacchetti dell'applicazione, non è necessario gestire vari file di risorse manualmente o generare gli URL di firma di accesso condiviso per accedere ai file in archiviazione di Azure. Batch interagisce in background con archiviazione di Azure per archiviare e distribuire i pacchetti dell'applicazione nei nodi di calcolo.

Se ogni attività dispone di molti file univoci per tale attività, file di risorse sono prevalentemente probabilmente la scelta migliore. Attività che usano file univoci spesso necessario essere aggiornati o sostituiti, che non è sufficiente a che fare con il contenuto dei pacchetti dell'applicazione. File di risorse offrono una maggiore flessibilità per l'aggiornamento, l'aggiunta o modifica di file singoli.

### <a name="number-of-resource-files-per-task"></a>Numero di file di risorse per attività

Se sono presenti più file di centinaia di risorse specificati su un'attività, Batch potrebbe rifiutare l'attività per un'esecuzione troppo grande. È consigliabile mantenere ridotte le attività, riducendo al minimo il numero di file di risorse dell'attività stessa.

Se non esiste un modo per ridurre al minimo il numero di file all'attività alle esigenze, è possibile ottimizzare l'attività mediante la creazione di un singolo file di risorse che fa riferimento a un contenitore di archiviazione di file di risorse. A tale scopo, inserire i file di risorse in un contenitore di archiviazione di Azure e usare le diverse modalità di "Container" del file di risorse. Usare le opzioni di prefisso blob per specificare le raccolte di file da scaricare per le attività.

## <a name="next-steps"></a>Passaggi successivi

- Scopri [pacchetti di applicazioni](batch-application-packages.md) come alternativa ai file di risorse.
- Per altre informazioni sull'uso di contenitori per i file di risorse, vedere [carichi di lavoro contenitore](batch-docker-container-workloads.md).
- Per informazioni su come raccogliere e salvare i dati di output da attività, vedere [rendere persistente l'output di processi e attività](batch-task-output.md).
- Informazioni sulle [API e gli strumenti di Batch](batch-apis-tools.md) disponibili per la compilazione di soluzioni Batch.
