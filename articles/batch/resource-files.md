---
title: Creazione e utilizzo di file di risorse
description: Informazioni su come creare file di risorse batch da varie origini di input. Questo articolo illustra alcuni metodi comuni su come crearli e inserirli in una macchina virtuale.
ms.date: 03/18/2020
ms.topic: article
ms.openlocfilehash: c9a2e581d0cada467e89e3da731fac7f78b22992
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82117183"
---
# <a name="creating-and-using-resource-files"></a>Creazione e utilizzo di file di risorse

Un'attività Azure Batch spesso richiede un tipo di dati da elaborare. I file di risorse rappresentano il modo per fornire questi dati alla macchina virtuale (VM) batch tramite un'attività. Tutti i tipi di attività supportano i file di risorse: attività, attività di avvio, attività di preparazione dei processi, attività di rilascio del processo e così via. Questo articolo illustra alcuni metodi comuni per creare file di risorse e inserirli in una macchina virtuale.  

I file di risorse inseriscono i dati in una macchina virtuale in batch, ma il tipo di dati e il modo in cui vengono usati è flessibile. Esistono, tuttavia, alcuni casi d'uso comuni:

1. Eseguire il provisioning di file comuni in ogni macchina virtuale usando file di risorse in un'attività di avvio
1. Provisioning dei dati di input da elaborare con le attività

I file comuni potrebbero essere, ad esempio, file in un'attività di avvio utilizzata per installare le applicazioni eseguite dalle attività. I dati di input potrebbero essere immagini non elaborate o dati video o qualsiasi informazione da elaborare per batch.

## <a name="types-of-resource-files"></a>Tipi di file di risorse

Sono disponibili alcune opzioni diverse per generare file di risorse. Il processo di creazione per i file di risorse varia a seconda della posizione in cui sono archiviati i dati originali.

Opzioni per la creazione di un file di risorse:

- [Storage container URL](#storage-container-url): genera un file di risorse da qualsiasi contenitore di archiviazione in Azure
- [Storage Container Name](#storage-container-name): genera un file di risorse dal nome di un contenitore in un account di archiviazione di Azure collegato al batch
- [Endpoint Web](#web-endpoint): genera un file di risorse da qualsiasi URL http valido

### <a name="storage-container-url"></a>URL del contenitore di archiviazione

L'uso di un URL del contenitore di archiviazione significa che, con le autorizzazioni corrette, è possibile accedere ai file in qualsiasi contenitore di archiviazione in Azure. 

In questo esempio C# i file sono già stati caricati in un contenitore di archiviazione di Azure come archivio BLOB. Per accedere ai dati necessari per creare un file di risorse, è prima di tutto necessario ottenere l'accesso al contenitore di archiviazione.

Creare un URI di firma di accesso condiviso con le autorizzazioni corrette per accedere al contenitore di archiviazione. Impostare l'ora di scadenza e le autorizzazioni per la firma di accesso condiviso. In questo caso, non viene specificata alcuna ora di inizio, quindi la firma di accesso condiviso diventa valida immediatamente e scade due ore dopo la generazione.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Per l'accesso al contenitore è necessario disporre `Read` di `List` entrambe le autorizzazioni e, mentre con l'accesso al `Read` BLOB è necessaria solo l'autorizzazione.

Una volta configurate le autorizzazioni, creare il token SAS e formattare l'URL di firma di accesso condiviso per accedere al contenitore di archiviazione. Usando l'URL SAS formattato per il contenitore di archiviazione, generare un file [`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet)di risorse con.

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Un'alternativa alla generazione di un URL di firma di accesso condiviso consiste nell'abilitare l'accesso in lettura pubblico anonimo a un contenitore e ai relativi BLOB nell'archivio BLOB di Azure. In questo modo, è possibile concedere l'accesso in sola lettura a queste risorse senza condividere la chiave dell'account e senza richiedere una firma di accesso condiviso. L'accesso in lettura pubblico viene in genere usato per gli scenari in cui si vuole che determinati BLOB siano sempre disponibili per l'accesso in lettura anonimo. Se questo scenario è adatto alla soluzione, vedere l'articolo [accesso anonimo ai BLOB](../storage/blobs/storage-manage-access-to-resources.md) per altre informazioni sulla gestione dell'accesso ai dati BLOB.

### <a name="storage-container-name"></a>Nome del contenitore di archiviazione

Anziché configurare e creare un URL di firma di accesso condiviso, è possibile usare il nome del contenitore di archiviazione di Azure per accedere ai dati BLOB. Il contenitore di archiviazione usato deve trovarsi nell'account di archiviazione di Azure collegato all'account batch. L'account di archiviazione è noto come account di archiviazione autonome. L'uso del contenitore autostorage consente di ignorare la configurazione e la creazione di un URL di firma di accesso condiviso per accedere a un contenitore di archiviazione.

In questo esempio si presuppone che i dati da usare per la creazione di file di risorse si trovino già in un account di archiviazione di Azure collegato all'account batch. Se non si ha un account di archiviazione, vedere i passaggi descritti in [creare un account batch](batch-account-create-portal.md) per informazioni dettagliate su come creare e collegare un account.

Usando un account di archiviazione collegato, non è necessario creare e configurare un URL di firma di accesso condiviso in un contenitore di archiviazione. Specificare invece il nome del contenitore di archiviazione nell'account di archiviazione collegato.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Endpoint Web

I dati che non vengono caricati in archiviazione di Azure possono comunque essere usati per creare file di risorse. È possibile specificare qualsiasi URL HTTP valido contenente i dati di input. L'URL viene fornito all'API batch, quindi i dati vengono usati per creare un file di risorse.

Nell'esempio C# seguente i dati di input sono ospitati in un endpoint GitHub fittizio. L'API recupera il file dall'endpoint Web valido e genera un file di risorse che verrà utilizzato dall'attività. Per questo scenario non sono necessarie credenziali.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Suggerimenti

Ogni attività Azure Batch usa i file in modo diverso, motivo per cui in batch sono disponibili opzioni per la gestione dei file nelle attività. Gli scenari seguenti non sono destinati a essere completi, ma riguardano alcune situazioni comuni e forniscono consigli.

### <a name="many-resource-files"></a>Molti file di risorse

Il processo batch può contenere diverse attività che usano tutti gli stessi file comuni. Se i file delle attività comuni sono condivisi tra molte attività, l'uso di un pacchetto dell'applicazione per contenere i file anziché usare i file di risorse può essere un'opzione migliore. I pacchetti dell'applicazione forniscono l'ottimizzazione per la velocità di download. Inoltre, i dati nei pacchetti dell'applicazione vengono memorizzati nella cache tra le attività, pertanto se i file dell'attività non cambiano spesso, i pacchetti dell'applicazione possono essere appropriati per la soluzione. Con i pacchetti dell'applicazione non è necessario gestire manualmente diversi file di risorse o generare URL SAS per accedere ai file in archiviazione di Azure. Batch funziona in background con archiviazione di Azure per archiviare e distribuire i pacchetti dell'applicazione nei nodi di calcolo.

Se ogni attività dispone di molti file univoci per tale attività, i file di risorse rappresentano la scelta migliore perché le attività che usano file univoci spesso devono essere aggiornate o sostituite, operazione non semplice con il contenuto dei pacchetti dell'applicazione. I file di risorse offrono maggiore flessibilità per l'aggiornamento, l'aggiunta o la modifica di singoli file.

### <a name="number-of-resource-files-per-task"></a>Numero di file di risorse per attività

Se in un'attività sono specificati diversi file di risorse, il batch potrebbe rifiutare l'attività come troppo grande. È preferibile ridurre al minimo il numero di file di risorse nell'attività stessa.

Se non esiste alcun modo per ridurre al minimo il numero di file necessari per l'attività, è possibile ottimizzare l'attività creando un unico file di risorse che fa riferimento a un contenitore di archiviazione di file di risorse. A tale scopo, inserire i file di risorse in un contenitore di archiviazione di Azure e usare i diversi [Metodi](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile?view=azure-dotnet#methods) "container" per i file di risorse. Usare le opzioni relative al prefisso BLOB per specificare le raccolte di file da scaricare per le attività.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui [pacchetti di applicazioni](batch-application-packages.md) come alternativa ai file di risorse.
- Per altre informazioni sull'uso dei contenitori per i file di risorse, vedere [carichi di lavoro](batch-docker-container-workloads.md)dei contenitori.
- Per informazioni su come raccogliere e salvare i dati di output dalle attività, vedere salvare in modo [permanente l'output di processi e attività](batch-task-output.md).
- Informazioni sulle [API e gli strumenti di Batch](batch-apis-tools.md) disponibili per la compilazione di soluzioni Batch.
