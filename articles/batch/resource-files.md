---
title: Creazione e utilizzo di file di risorse
description: Informazioni su come creare file di risorse di Batch da varie origini di input. Questo articolo illustra alcuni metodi comuni per creare tali file e inserirli in una macchina virtuale.
ms.date: 03/18/2020
ms.topic: how-to
ms.openlocfilehash: ea349c3a190b78297d9ad4555258d0cfd8828ed4
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723460"
---
# <a name="creating-and-using-resource-files"></a>Creazione e utilizzo di file di risorse

Un'attività di Azure Batch spesso richiede un tipo di dati da elaborare. I file di risorse rappresentano il modo per fornire questi dati alla macchina virtuale in Batch tramite un'attività. Tutti i tipi di attività supportano i file di risorse: attività, attività di avvio, attività di preparazione dei processi, attività di rilascio del processo e così via. Questo articolo illustra alcuni metodi comuni per creare i file di risorse e inserirli in una macchina virtuale.  

I file di risorse inseriscono i dati in una macchina virtuale in Batch, ma il tipo di dati e il modo in cui vengono usati sono flessibili. Esistono tuttavia alcuni casi d'uso comuni:

1. Provisioning di file comuni in ogni macchina virtuale usando file di risorse in un'attività di avvio
1. Provisioning di dati di input da elaborare tramite le attività

I file comuni potrebbero ad esempio essere file in un'attività di avvio usati per installare le applicazioni eseguite dalle attività. I dati di input potrebbero essere immagini o dati video non elaborati o qualsiasi informazione da elaborare tramite Batch.

## <a name="types-of-resource-files"></a>Tipi di file di risorse

Sono disponibili alcune opzioni diverse per generare file di risorse. Il processo di creazione per i file di risorse varia in base alla posizione in cui sono archiviati i dati originali.

Opzioni per la creazione di un file di risorse:

- [URL contenitore di archiviazione](#storage-container-url): Genera un file di risorse da un contenitore di archiviazione in Azure
- [Nome contenitore di archiviazione](#storage-container-name): Genera un file di risorse dal nome di un contenitore in un account di archiviazione di Azure collegato a Batch
- [Endpoint Web](#web-endpoint): Genera un file di risorse da qualsiasi URL HTTP valido

### <a name="storage-container-url"></a>URL contenitore di archiviazione

Se si usa un URL del contenitore di archiviazione, con le autorizzazioni corrette, è possibile accedere ai file in un contenitore di archiviazione in Azure. 

In questo esempio C# i file sono già stati caricati in un contenitore di archiviazione di Azure come archiviazione BLOB. Per accedere ai dati necessari per creare un file di risorse, è prima di tutto necessario ottenere l'accesso al contenitore di archiviazione.

Creare un URI di firma di accesso condiviso (SAS) con le autorizzazioni corrette per accedere al contenitore di archiviazione. Impostare l'ora di scadenza e le autorizzazioni per la firma di accesso condiviso. In questo caso, non viene specificata alcuna ora di inizio, quindi la firma di accesso condiviso diventa valida immediatamente e scade due ore dopo la generazione.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Per l'accesso al contenitore è necessario disporre di entrambe le autorizzazioni `Read` e `List`, mentre con l'accesso al BLOB è necessaria solo l'autorizzazione `Read`.

Una volta configurate le autorizzazioni, creare il token SAS e formattare l'URL SAS per accedere al contenitore di archiviazione. Usando l'URL SAS formattato per il contenitore di archiviazione, generare un file di risorse con [`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet).

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Un'alternativa alla generazione di un URL SAS consiste nell'abilitare l'accesso in lettura pubblico anonimo per un contenitore e i relativi BLOB in Archiviazione BLOB di Azure. Ciò permette di concedere l'accesso in sola lettura a queste risorse senza condividere la chiave dell'account e senza richiedere una firma di accesso condiviso (SAS). L'accesso in lettura pubblico è in genere usato per scenari in cui si vuole che BLOB specifici siano sempre disponibili per l'accesso in lettura anonimo. Se questo scenario è adatto alla soluzione, vedere l'articolo relativo all'[accesso anonimo ai BLOB](../storage/blobs/storage-manage-access-to-resources.md) per altre informazioni sulla gestione dell'accesso ai dati BLOB.

### <a name="storage-container-name"></a>Nome contenitore di archiviazione

Anziché configurare e creare un URL di firma di accesso condiviso, è possibile usare il nome del contenitore di archiviazione di Azure per accedere ai dati BLOB. Il contenitore di archiviazione usato deve trovarsi nell'account di archiviazione di Azure collegato all'account Batch. L'account di archiviazione è noto come account di archiviazione automatica. L'uso del contenitore di archiviazione automatica consente di ignorare la configurazione e la creazione di un URL SAS per accedere a un contenitore di archiviazione.

In questo esempio si presuppone che i dati da usare per la creazione di file di risorse si trovino già in un account di Archiviazione di Azure collegato all'account Batch. Se non si dispone di un account di archiviazione automatica, vedere i passaggi descritti in [Creare un account Batch](batch-account-create-portal.md) per informazioni dettagliate su come creare e collegare un account.

Se si usa un account di archiviazione collegato, non è necessario creare e configurare un URL di firma di accesso condiviso in un contenitore di archiviazione. Specificare invece il nome del contenitore di archiviazione nell'account di archiviazione collegato.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Endpoint Web

I dati che non vengono caricati in Archiviazione di Azure possono comunque essere usati per creare file di risorse. È possibile specificare un URL HTTP valido contenente i dati di input. L'URL viene fornito all'API Batch, quindi i dati vengono usati per creare un file di risorse.

Nell'esempio C# seguente i dati di input sono ospitati in un endpoint GitHub fittizio. L'API recupera il file dall'endpoint Web valido e genera un file di risorse che verrà utilizzato dall'attività. Non sono necessarie credenziali per questo scenario.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Suggerimenti

Ogni attività di Azure Batch usa i file in modo diverso, motivo per cui in Batch sono disponibili opzioni per la gestione dei file nelle attività. Gli scenari seguenti non si intendono completi, ma riguardano alcune situazioni comuni e offrono consigli.

### <a name="many-resource-files"></a>Molti file di risorse

Il processo Batch può contenere diverse attività che usano tutte gli stessi file comuni. Se i file delle attività comuni sono condivisi tra molte attività, è meglio usare un pacchetto dell'applicazione per contenere i file anziché usare i file di risorse. I pacchetti dell'applicazione assicurano l'ottimizzazione per la velocità di download. Inoltre, i dati nei pacchetti dell'applicazione vengono memorizzati nella cache tra le attività, pertanto se i file dell'attività non cambiano spesso, i pacchetti dell'applicazione possono essere appropriati per la soluzione. Con i pacchetti dell'applicazione non è necessario gestire manualmente diversi file di risorse né generare URL SAS per accedere ai file in Archiviazione di Azure. Batch interagisce in background con Archiviazione di Azure per archiviare e distribuire i pacchetti dell'applicazione nei nodi di calcolo.

Se ogni attività dispone di molti file univoci per tale attività, i file di risorse rappresentano la scelta migliore perché le attività che usano file univoci spesso devono essere aggiornate o sostituite, operazione non semplice con il contenuto dei pacchetti dell'applicazione. I file di risorse offrono maggiore flessibilità per l'aggiornamento, l'aggiunta o la modifica di singoli file.

### <a name="number-of-resource-files-per-task"></a>Numero di file di risorse per attività

Se in un'attività sono specificate diverse centinaia di file di risorse, Batch potrebbe rifiutare l'attività perché considerata troppo grande. È preferibile mantenere l'attività di piccole dimensioni riducendo al minimo il numero di file di risorse presenti in essa.

Se non è possibile ridurre al minimo il numero di file necessari per l'attività, ottimizzare l'attività creando un unico file di risorse che fa riferimento a un contenitore di archiviazione dei file di risorse. A tale scopo, inserire i file di risorse in un contenitore di Archiviazione di Azure e usare i diversi [metodi](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile?view=azure-dotnet#methods) "container" per i file di risorse. Usare le opzioni del prefisso BLOB per specificare le raccolte di file da scaricare per le attività.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui [pacchetti dell'applicazione](batch-application-packages.md) come alternativa ai file di risorse.
- Altre informazioni sull'uso dei contenitori per i file di risorse disponibili in [carichi di lavoro dei contenitori](batch-docker-container-workloads.md).
- Informazioni su come raccogliere e salvare i dati di output dalle attività disponibili in [Salvare in modo permanente l'output di processi e attività](batch-task-output.md).
- Informazioni sulle [API e gli strumenti di Batch](batch-apis-tools.md) disponibili per la compilazione di soluzioni Batch.
