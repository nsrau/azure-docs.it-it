---
title: Distribuire i pacchetti dell'applicazione nei nodi di calcolo
description: Usare la funzionalità dei pacchetti dell’applicazione di Azure Batch per gestire facilmente più applicazioni e versioni ed eseguire l'installazione su nodi di calcolo in Batch.
ms.topic: how-to
ms.date: 09/24/2020
ms.custom:
- H1Hack27Feb2017
- devx-track-csharp
- contperfq1
ms.openlocfilehash: 1bacb0c71c05aeb983bfa9ebf71873a22fea39a1
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "91277700"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Distribuire le applicazioni nei nodi di calcolo con i pacchetti dell'applicazione Batch

I pacchetti dell'applicazione possono semplificare il codice nella soluzione Azure Batch e rendere più semplice la gestione delle applicazioni eseguite dalle attività. Con i pacchetti dell'applicazione è possibile caricare e gestire più versioni delle applicazioni eseguite dalle attività, inclusi i file di supporto. È quindi possibile di distribuire automaticamente una o più applicazioni nei nodi di calcolo del pool.

Le API per la creazione e la gestione dei pacchetti dell'applicazione fanno parte della libreria di [gestione Batch per .NET](/dotnet/api/overview/azure/batch/management). Le API per l'installazione dei pacchetti dell'applicazione in un nodo di calcolo sono parte della raccolta [Batch .NET](/dotnet/api/overview/azure/batch/client). Funzionalità simili si trovano nelle API Batch disponibili per altri linguaggi.

Questo articolo illustra come caricare e gestire i pacchetti dell'applicazione nella portale di Azure. Viene inoltre illustrato come installarli nei nodi di calcolo di un pool con la libreria [batch .NET](/dotnet/api/overview/azure/batch/client) .

## <a name="application-package-requirements"></a>Requisiti dei pacchetti dell'applicazione

Per usare i pacchetti dell'applicazione, è necessario [collegare un account di archiviazione di Azure](#link-a-storage-account) all'account Batch.

Sono previste restrizioni al numero di applicazioni e di pacchetti dell'applicazione in un account Batch e alle dimensioni massime del pacchetto dell'applicazione. Per ulteriori informazioni, vedere [quote e limiti per il servizio Azure batch](batch-quota-limit.md).

> [!NOTE]
> I pool di batch creati prima del 5 luglio 2017 non supportano i pacchetti dell'applicazione (a meno che non siano stati creati dopo il 10 marzo 2016 usando la configurazione dei servizi cloud). La funzionalità dei pacchetti dell’applicazione descritta di seguito sostituisce la funzionalità App Batch disponibile nelle versioni precedenti del servizio.

## <a name="understand-applications-and-application-packages"></a>Informazioni sulle applicazioni e sui pacchetti dell'applicazione

Per *applicazione* in Azure Batch si intende un set di file binari con versione che possono essere scaricati automaticamente nei nodi di calcolo del pool. Un'applicazione contiene uno o più *pacchetti dell'applicazione* , che rappresentano versioni diverse dell'applicazione.

Ogni *pacchetto dell'applicazione* è un file zip che contiene i file binari dell'applicazione e tutti i file di supporto. È supportato solo il formato zip.

:::image type="content" source="media/batch-application-packages/app_pkg_01.png" alt-text="Diagramma che mostra una visualizzazione di alto livello di applicazioni e pacchetti dell'applicazione.":::

È possibile specificare i pacchetti dell'applicazione a livello di pool o di attività.

- **pacchetti dell'applicazione del pool** vengono distribuiti in ogni nodo del pool. Le applicazioni vengono distribuite quando un nodo viene aggiunto a un pool e quando viene riavviato o oppure la sua immagine viene ricreata.
  
    I pacchetti dell'applicazione del pool sono appropriati quando tutti i nodi in un pool eseguiranno le attività di un processo. Quando si crea un pool, è possibile specificare uno o più pacchetti dell'applicazione da distribuire. È inoltre possibile aggiungere o aggiornare i pacchetti di un pool esistente. Per installare un nuovo pacchetto in un pool esistente, è necessario riavviare i relativi nodi.

- **pacchetti dell'applicazione per le attività** vengono distribuiti solo in un nodo di calcolo che dovrà eseguire un'attività, appena prima di eseguire la riga di comando dell'attività. Se il pacchetto dell'applicazione specificato con la versione corrispondente si trova già nel nodo, non verrà ridistribuito e verrà usato il pacchetto esistente.
  
    I pacchetti dell'applicazione per le attività sono utili in ambienti di pool condivisi, in cui diversi processi vengono eseguiti in un pool e il pool non viene eliminato al termine di un processo. Se il processo ha meno attività rispetto ai nodi nel pool, i pacchetti dell'applicazione di attività possono ridurre il trasferimento dei dati, perché l'applicazione viene distribuita solo nei nodi che eseguono le attività.
  
    Altri scenari che possono trarre vantaggio dai pacchetti dell'applicazione per le attività sono i processi che usano un'applicazione di dimensioni elevate, ma solo per poche attività. Ad esempio, le applicazioni per le attività possono essere utili per una fase di pre-elaborazione pesante o un'attività di Unione.

Con i pacchetti dell'applicazione non è necessario che l'attività di avvio del pool specifichi un lungo elenco di singoli file di risorse da installare nei nodi. Non è necessario gestire manualmente più versioni dei file dell'applicazione in Archiviazione di Azure o nei nodi. Non è quindi necessario preoccuparsi di generare [URL SAS](../storage/common/storage-sas-overview.md) per fornire l'accesso ai file nell'account di archiviazione. Batch interagisce in background con Archiviazione di Azure per archiviare e distribuire i pacchetti dell'applicazione nei nodi di calcolo.

> [!NOTE]
> La dimensione totale di un'attività di avvio deve essere inferiore o uguale a 32768 caratteri, inclusi i file di risorse e le variabili di ambiente. Se l'attività di avvio supera questo limite, l'utilizzo di pacchetti dell'applicazione è un'altra opzione. È anche possibile creare un file con estensione zip contenente i file di risorse, caricarlo come BLOB in archiviazione di Azure e quindi decomprimerlo dalla riga di comando dell'attività di avvio.

## <a name="upload-and-manage-applications"></a>Caricare e gestire le applicazioni

È possibile usare il [portale di Azure](https://portal.azure.com) o le API Management di Batch per gestire i pacchetti dell'applicazione nell'account Batch. Le sezioni seguenti illustrano come collegare un account di archiviazione e come aggiungere e gestire le applicazioni e i pacchetti dell'applicazione nella portale di Azure.

### <a name="link-a-storage-account"></a>Collegare un account di archiviazione

Per usare i pacchetti dell'applicazione, è necessario collegare un [account di archiviazione di Azure](accounts.md#azure-storage-accounts) all'account batch. Il servizio batch userà l'account di archiviazione associato per archiviare i pacchetti dell'applicazione. Si consiglia di creare un account di archiviazione specifico per l'uso con l'account batch.

Se non è stato ancora configurato un account di archiviazione, nel portale di Azure viene visualizzato un avviso la prima volta che si selezionano le **applicazioni** nell'account batch. Per collegare un account di archiviazione all'account batch, selezionare **account di archiviazione** nella finestra di **avviso** e quindi selezionare di nuovo **account di archiviazione** .

Dopo aver collegato i due account, Batch può distribuire automaticamente i pacchetti archiviati nell'account di archiviazione collegato nei nodi di calcolo.

> [!IMPORTANT]
> Non è possibile usare i pacchetti dell'applicazione con account di archiviazione di Azure configurati con [regole del firewall](../storage/common/storage-network-security.md)o con **lo spazio dei nomi gerarchico** impostato su **abilitato** .

Il servizio Batch usa Archiviazione di Azure per archiviare i pacchetti dell'applicazione come BLOB in blocchi. L'[importo addebitato](https://azure.microsoft.com/pricing/details/storage/) è lo stesso calcolato per i dati BLOB in blocchi e la dimensione di ogni pacchetto non può superare la dimensione massima del BLOB in blocchi. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni di Archiviazione di Azure per gli account di archiviazione](../storage/blobs/scalability-targets.md). Per ridurre al minimo i costi, assicurarsi di prendere in considerazione le dimensioni e il numero dei pacchetti dell'applicazione e rimuovere periodicamente i Pacchetti deprecati.

### <a name="view-current-applications"></a>Visualizzare le applicazioni correnti

Per visualizzare le applicazioni nell'account batch, selezionare **applicazioni** nel menu di spostamento a sinistra.

:::image type="content" source="media/batch-application-packages/app_pkg_02.png" alt-text="Diagramma che mostra una visualizzazione di alto livello di applicazioni e pacchetti dell'applicazione.":::

Selezionando questa opzione di menu viene visualizzata la finestra **applicazioni** . La finestra visualizza l'ID di ogni applicazione nell'account e le proprietà seguenti:

- **Pacchetti** : il numero delle versioni associate a questa applicazione.
- **Versione predefinita** : se applicabile, la versione dell'applicazione che verrà installata se non viene specificata alcuna versione quando si distribuisce l'applicazione.
- **Consenti aggiornamenti** : specifica se sono consentiti gli aggiornamenti e le eliminazioni del pacchetto.

Per visualizzare la [struttura di file](files-and-directories.md) del pacchetto dell'applicazione in un nodo di calcolo, passare all'account Batch nel portale di Azure. Selezionare **pool** . quindi selezionare il pool che contiene il nodo di calcolo. Selezionare il nodo di calcolo in cui è installato il pacchetto dell'applicazione e aprire la cartella **applicazioni** .

### <a name="view-application-details"></a>Visualizzare i dettagli dell'applicazione

Per visualizzare i dettagli di un'applicazione, selezionarla nella finestra **applicazioni** . È possibile configurare le impostazioni seguenti per l'applicazione.

- **Consenti aggiornamenti** : indica se i pacchetti dell'applicazione possono essere [aggiornati o eliminati](#update-or-delete-an-application-package). Il valore predefinito è **Sì** . Se impostato su **No** , i pacchetti dell'applicazione esistenti non possono essere aggiornati o eliminati, ma è ancora possibile aggiungere nuove versioni del pacchetto dell'applicazione.
- **Versione predefinita** : pacchetto dell'applicazione predefinito da usare quando viene distribuita l'applicazione, se non è specificata alcuna versione.
- **Nome visualizzato** : nome descrittivo che la soluzione batch può usare per visualizzare informazioni sull'applicazione. Questo nome, ad esempio, può essere usato nell'interfaccia utente di un servizio fornito ai clienti tramite batch.

### <a name="add-a-new-application"></a>Aggiungere un’applicazione nuova

Per creare una nuova applicazione, è necessario aggiungere un pacchetto dell'applicazione e specificare un ID applicazione univoco.

Nell'account batch selezionare **applicazioni** , quindi selezionare **Aggiungi** .

:::image type="content" source="media/batch-application-packages/app_pkg_05.png" alt-text="Diagramma che mostra una visualizzazione di alto livello di applicazioni e pacchetti dell'applicazione.":::

Immettere le seguenti informazioni:

- **ID applicazione** : ID della nuova applicazione.
- **Version** ": la versione per il pacchetto dell'applicazione che si sta caricando.
- **Pacchetto dell'applicazione** : il file zip contenente i file binari dell'applicazione e i file di supporto necessari per eseguire l'applicazione.

L' **ID** e la **versione** dell'applicazione immessi devono rispettare i requisiti seguenti:

- Nei nodi di Windows, l'ID può contenere qualsiasi combinazione di caratteri alfanumerici, trattini e caratteri di sottolineatura. Nei nodi di Linux, sono consentiti solo caratteri alfanumerici e caratteri di sottolineatura.
- Non può contenere più di 64 caratteri.
- Deve essere univoco nell’account Batch.
- Gli ID eseguono il mantenimento del case e non fanno distinzione tra maiuscole e minuscole.

Al termine, selezionare **Invia** . Dopo che il file zip è stato caricato nell'account di archiviazione di Azure, il portale Visualizza una notifica. A seconda delle dimensioni del file che si sta caricando e della velocità della connessione di rete, l'operazione potrebbe richiedere del tempo.

### <a name="add-a-new-application-package"></a>Aggiungere un nuovo pacchetto dell’applicazione

Per aggiungere una versione del pacchetto dell'applicazione per un'applicazione esistente, selezionare l'applicazione nella sezione **applicazioni** dell'account batch, quindi selezionare **Aggiungi** .

Come per la nuova applicazione, specificare la **versione** del nuovo pacchetto, caricare il file con estensione zip nel campo **pacchetto dell'applicazione** e quindi selezionare **Invia** .

### <a name="update-or-delete-an-application-package"></a>Aggiornare o eliminare un pacchetto dell'applicazione

Per aggiornare o eliminare un pacchetto dell'applicazione esistente, selezionare l'applicazione nella sezione **applicazioni** dell'account batch. Selezionare i puntini di sospensione nella riga del pacchetto dell'applicazione che si desidera modificare, quindi selezionare l'azione che si desidera eseguire.

:::image type="content" source="media/batch-application-packages/app_pkg_07.png" alt-text="Diagramma che mostra una visualizzazione di alto livello di applicazioni e pacchetti dell'applicazione.":::

Se si seleziona **Aggiorna** , sarà possibile caricare un nuovo file con estensione zip. Verrà sostituito il file zip precedente caricato per la versione.

Se si seleziona **Elimina** , verrà richiesto di confermare l'eliminazione di tale versione. Dopo aver selezionato **OK** , batch eliminerà il file con estensione zip dall'account di archiviazione di Azure. Se si elimina la versione predefinita di un'applicazione, viene rimossa l'impostazione della **versione predefinita** per l'applicazione.

## <a name="install-applications-on-compute-nodes"></a>Installare le applicazioni su nodi di calcolo

Ora che si è appreso come gestire i pacchetti dell'applicazione nella portale di Azure, è possibile illustrare come distribuirli nei nodi di calcolo ed eseguirli con attività batch.

### <a name="install-pool-application-packages"></a>Installare pacchetti dell'applicazione nel pool

Per installare un pacchetto dell'applicazione in tutti i nodi di calcolo di un pool, specificare uno o più riferimenti al pacchetto dell'applicazione per il pool. I pacchetti dell'applicazione specificati per un pool vengono installati in ogni nodo di calcolo che unisce il pool e in qualsiasi nodo riavviato o ricreata l'immagine.

In batch .NET specificare uno o più [CloudPool. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) quando si crea un nuovo pool o un pool esistente. La classe [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) specifica un ID applicazione e la versione da installare nei nodi di calcolo di un pool.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> Se la distribuzione di un pacchetto dell'applicazione non riesce, il servizio batch contrassegna il nodo come [inutilizzabile](/dotnet/api/microsoft.azure.batch.computenode.state)e nessuna attività è pianificata per l'esecuzione in tale nodo. In tal caso, riavviare il nodo per riavviare la distribuzione del pacchetto. Il riavvio del nodo consente anche di pianificarne di nuovo le attività.

### <a name="install-task-application-packages"></a>Installare pacchetti dell'applicazione per le attività

Come per un pool, specificare i riferimenti del pacchetto dell'applicazione per un'attività. Quando un'attività è pianificata per l'esecuzione su un nodo, il pacchetto viene scaricato ed estratto appena prima dell'esecuzione della riga di comando dell'attività. Se un pacchetto specificato con la versione corrispondente è già installato nel nodo, non verrà scaricato e verrà usato il pacchetto esistente.

Per installare un pacchetto dell'applicazione di attività, configurare la proprietà [CloudTask. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudtask.applicationpackagereferences) dell'attività:

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Eseguire le applicazioni installate

I pacchetti specificati per un pool o un'attività vengono scaricati ed estratti in una directory denominata all'interno del nodo `AZ_BATCH_ROOT_DIR` . Batch crea anche una variabile di ambiente che contiene il percorso della directory denominata. Le righe di comando dell'attività usano questa variabile di ambiente quando fanno riferimento all'applicazione nel nodo.

Nei nodi di Windows la variabile è nel formato seguente:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Nei nodi di Linux, il formato è leggermente diverso. Punti (.), trattini (-) e simboli di cancelletto (#) vengono convertiti in caratteri di sottolineatura nella variabile di ambiente. Si noti inoltre che il caso dell'ID dell'applicazione viene mantenuto. Ad esempio:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` e `version` sono valori che corrispondono all'applicazione e alla versione del pacchetto specificati per la distribuzione. Se ad esempio si specifica l'installazione della versione 2.7 dell'applicazione *blender* nei nodi di Windows, le righe di comando dell'attività useranno questa variabile di ambiente per accedere ai file corrispondenti:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Nei nodi di Linux, specificare la variabile di ambiente nel formato seguente: Convertire punti (.), trattini (-) e cancelletti (#) in caratteri di sottolineatura e mantenere le lettere maiuscole/minuscole dell'ID applicazione:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
```

Quando si carica un pacchetto dell'applicazione, è possibile specificare una versione predefinita da distribuire ai nodi di calcolo. Se è stata specificata una versione predefinita per un'applicazione, è possibile omettere il suffisso della versione quando si fa riferimento l'applicazione. È possibile specificare la versione predefinita dell'applicazione nella finestra **Applicazioni** del portale di Azure, come illustrato in [Caricare e gestire le applicazioni](#upload-and-manage-applications).

Se ad esempio è stata specificata la versione predefinita "2.7" per l'applicazione *blender* e le attività fanno riferimento alla variabile di ambiente seguente, i nodi di Windows eseguiranno la versione 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Il frammento di codice seguente mostra una riga di comando dell'attività di esempio che consente di avviare la versione predefinita dell'applicazione *blender* :

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Per altre informazioni sulle impostazioni dell'ambiente dei nodi di calcolo, vedere [Impostazioni di ambiente per le attività](jobs-and-tasks.md#environment-settings-for-tasks).

## <a name="update-a-pools-application-packages"></a>Aggiornare i pacchetti dell’applicazione di un pool

Se un pool esistente è già stato configurato con un pacchetto dell’applicazione, è possibile specificare un nuovo pacchetto per il pool. Ciò significa:

- Il servizio Batch installa il pacchetto appena specificato su tutti i nuovi nodi aggiunti al pool e su tutti i nodi esistenti riavviati o per i quali viene ricreata l'immagine.
- I nodi di calcolo che si trovano già nel pool quando si aggiornano i riferimenti al pacchetto non installano automaticamente il nuovo pacchetto dell’applicazione. Questi nodi di calcolo devono essere riavviati o ne deve essere ricreata l'immagine per ricevere il nuovo pacchetto.
- Quando viene distribuito un nuovo pacchetto, le variabili di ambiente create riflettono i riferimenti al nuovo pacchetto dell'applicazione.

In questo esempio il pool esistente ha la versione 2,7 dell'applicazione *Blender* configurata come uno dei relativi [CloudPool. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences). Per aggiornare i nodi del pool alla versione 2.76b, specificare un nuovo [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) con la nuova versione ed eseguire il commit della modifica.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Ora che è stata configurata la nuova versione, il servizio Batch installa la versione 2.76b su qualsiasi nuovo nodo aggiunto al pool. Per installare la versione 2.76b nei nodi già presenti nel pool, riavviarli o ricrearne l'immagine. Si noti che i nodi riavviati mantengono i file delle distribuzioni precedenti del pacchetto.

## <a name="list-the-applications-in-a-batch-account"></a>Elencare le applicazioni in un account Batch

È possibile elencare le applicazioni e i relativi pacchetti in un account batch usando il metodo [Metodo applicationoperations. ListApplicationSummaries](/dotnet/api/microsoft.azure.batch.applicationoperations.listapplicationsummaries) .

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

- L'[API REST Batch](/rest/api/batchservice) consente anche di usare i pacchetti dell'applicazione. Vedere ad esempio l'elemento [applicationPackageReferences](/rest/api/batchservice/pool/add#applicationpackagereference) per informazioni su come specificare i pacchetti da installare e [le applicazioni](/rest/api/batchservice/application) per ottenere informazioni sull'applicazione.
- È possibile scoprire come [gestire quote e account Azure Batch con la gestione .NET per Batch](batch-management-dotnet.md)a livello di codice. Con la libreria di [gestione .NET per Batch](/dotnet/api/overview/azure/batch/management) è possibile abilitare funzionalità di creazione ed eliminazione di account per l'applicazione o il servizio Batch.
