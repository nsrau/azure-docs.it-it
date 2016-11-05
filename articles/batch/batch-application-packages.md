---
title: Processi semplici di installazione e gestione dell'applicazione in Azure Batch | Microsoft Docs
description: Usare la funzionalità dei pacchetti dell’applicazione di Azure Batch per gestire facilmente più applicazioni e versioni ed eseguire l'installazione su nodi di calcolo in Batch.
services: batch
documentationcenter: .net
author: mmacy
manager: timlt
editor: ''

ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 08/25/2016
ms.author: marsma

---
# Distribuzione delle applicazioni con i pacchetti dell’applicazione di Azure Batch.
I pacchetti dell'applicazione sono una funzionalità di Azure Batch che consente di gestire e distribuire facilmente le applicazioni per le attività nei nodi di calcolo del pool. I pacchetti dell'applicazione consentono di caricare e gestire più versioni delle applicazioni eseguite dalle attività, inclusi i file di supporto. È quindi possibile di distribuire automaticamente una o più applicazioni nei nodi di calcolo del pool.

In questo articolo si apprenderà come caricare e gestire pacchetti dell'applicazione nel portale di Azure. Si apprenderà quindi come installarli nei nodi di calcolo di un pool usando la libreria [Batch .NET][api_net].

> [!NOTE]
> La funzionalità dei pacchetti dell’applicazione descritta di seguito sostituisce la funzionalità App Batch disponibile nelle versioni precedenti del servizio.
> 
> 

## Requisiti dei pacchetti dell'applicazione
Per usare i pacchetti dell'applicazione è necessario [collegare un account di archiviazione di Azure](#link-a-storage-account) all'account Batch.

La funzionalità dei pacchetti dell'applicazione descritta in questo articolo è compatibile *solo* con i pool di Batch creati dopo il 10 marzo 2016. I pacchetti dell’applicazione non saranno distribuiti sui nodi di calcolo di pool creati prima di questa data.

Questa funzionalità è stata introdotta nella versione 2015-12-01.2.2 dell'[API Batch REST][api_rest] e nella versione 3.1.0 della libreria [Batch .NET][api_net] corrispondente. È consigliabile usare sempre la versione API più recente quando si usa Batch.

> [!IMPORTANT]
> Solo i pool *CloudServiceConfiguration* supportano attualmente pacchetti dell'applicazione. Non è possibile usare i pacchetti dell'applicazione in pool creati con immagini VirtualMachineConfiguration. Per altre informazioni sulle due diverse configurazioni, vedere la sezione [Configurazione macchina virtuale](batch-linux-nodes.md#virtual-machine-configuration) dell'articolo [Effettuare il provisioning di nodi di calcolo Linux nei pool di Azure Batch](batch-linux-nodes.md).
> 
> 

## Informazioni sulle applicazioni e sui pacchetti dell’applicazione
Per *applicazione* in Azure Batch si intende un set di file binari con versione che possono essere scaricati automaticamente nei nodi di calcolo del pool. Un *pacchetto dell'applicazione* è invece un *set specifico* di tali file binari e rappresenta una *versione* specifica dell'applicazione.

![Diagramma di alto livello di applicazioni e pacchetti applicazione][1]

### Applicazioni
Un'applicazione in Batch contiene uno o più pacchetti dell'applicazione e specifica le opzioni di configurazione per l'applicazione. Un'applicazione può ad esempio specificare la versione predefinita del pacchetto dell'applicazione da installare nei nodi di calcolo e se i pacchetti possono essere aggiornati o eliminati.

### Pacchetti dell'applicazione
Un pacchetto dell'applicazione è un file ZIP contenente i file binari e i file di supporto dell'applicazione necessari per l'esecuzione delle attività. Ogni pacchetto dell’applicazione rappresenta una versione specifica dell'applicazione.

È possibile specificare i pacchetti dell'applicazione a livello di pool e di attività. È possibile specificare uno o più di questi pacchetti ed eventualmente una versione quando si crea un pool o un'attività.

* I **pacchetti dell'applicazione del pool** vengono distribuiti in *ogni* nodo del pool. Le applicazioni vengono distribuite quando un nodo viene aggiunto a un pool e quando viene riavviato o oppure la sua immagine viene ricreata.
  
    I pacchetti dell'applicazione del pool possono essere usati quando tutti i nodi in un pool eseguono le attività di un processo. È possibile specificare uno o più pacchetti dell'applicazione quando si crea un pool e aggiungere o aggiornare i pacchetti di un pool esistente. Se si aggiornano i pacchetti dell'applicazione di un pool esistente, è necessario riavviare i nodi per installare il nuovo pacchetto.
* I **pacchetti dell'applicazione per le attività** vengono distribuiti solo in un nodo di calcolo che dovrà eseguire un'attività, appena prima di eseguire la riga di comando dell'attività. Se il pacchetto dell'applicazione specificato con la versione corrispondente si trova già nel nodo, non verrà ridistribuito e verrà usato il pacchetto esistente.
  
    I pacchetti dell'applicazione per le attività sono utili in ambienti di pool condivisi, in cui diversi processi vengono eseguiti in un pool e il pool non viene eliminato quando viene completato un processo. Se il processo ha meno attività che nodi nel pool, i pacchetti dell'applicazione di attività possono ridurre il trasferimento dei dati, perché l'applicazione viene distribuita solo nei nodi che eseguono le attività.
  
    Altri scenari che possono trarre vantaggio da pacchetti dell'applicazione per le attività sono processi che usano un'applicazione di dimensioni particolarmente elevate, ma solo per un numero ridotto di attività, ad esempio una fase di pre-elaborazione o un'attività di unione, in cui l'applicazione di pre-elaborazione o di unione è pesante.

> [!IMPORTANT]
> Sono previste restrizioni al numero di applicazioni e di pacchetti dell’applicazione in un account Batch. Anche la dimensione massima del pacchetto dell’applicazione è limitata. Per informazioni dettagliate su questi limiti, vedere [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md).
> 
> 

### Vantaggi dei pacchetti dell'applicazione
Con i pacchetti dell'applicazione è possibile semplificare il codice nella soluzione Batch e ridurre il sovraccarico richiesto in termini di gestione delle applicazioni eseguite delle attività.

Non è necessario che l'attività di avvio del pool specifichi un lungo elenco di singoli file di risorse per eseguire l'installazione nei nodi. Non è necessario gestire manualmente più versioni dei file dell'applicazione in Archiviazione di Azure o nei nodi. Per accedere ai file nell'account di archiviazione non è necessario generare un [URL di firma di accesso condiviso](../storage/storage-dotnet-shared-access-signature-part-1.md). Batch interagisce in background con Archiviazione di Azure per archiviare e distribuire i pacchetti dell'applicazione nei nodi di calcolo.

## Caricare e gestire le applicazioni
È possibile usare il [portale di Azure][portal] o la libreria di [.NET per Batch](batch-management-dotnet.md) per gestire i pacchetti dell'applicazione nell'account Batch. Nelle sezioni successive verrà prima di tutto collegato un account di archiviazione, quindi verrà descritta l'aggiunta di applicazioni e pacchetti e la loro gestione con il portale.

### Collegare un account di archiviazione
Per usare i pacchetti dell'applicazione, è prima necessario collegare un account di archiviazione di Azure all'account Batch. Se non è stato ancora configurato un account di archiviazione per l'account Batch, il portale di Azure visualizza un avviso nel momento in cui si seleziona per la prima volta il riquadro **Applicazioni** nel pannello **Account Batch**.

> [!IMPORTANT]
> Batch supporta attualmente *solo* account di archiviazione **per utilizzo generico**, come descritto nel passaggio 5 [Creare un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account) dell'articolo [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md). Quando si collega un account di archiviazione di Azure all'account Batch, collegare *solo* un account di archiviazione **per utilizzo generico**.
> 
> 

![Avviso. Nessun account di archiviazione configurato nel portale di Azure][9]

Il servizio Batch usa l'account di archiviazione associato per archiviare e recuperare i pacchetti dell’applicazione. Dopo aver collegato i due account, Batch può distribuire automaticamente i pacchetti archiviati nell'account di archiviazione collegato nei nodi di calcolo. Selezionare **Impostazioni account di archiviazione** nel pannello **Avviso** e quindi fare clic su **Account di archiviazione** nel pannello **Account di archiviazione** per collegare un account di archiviazione a un account Batch.

![Selezionare il pannello Account di archiviazione nel portale di Azure][10]

È consigliabile creare un account di archiviazione da usare *specificamente* con l'account Batch e selezionarlo qui. Per informazioni dettagliate sulla creazione di un account di archiviazione, vedere "Creare un account di archiviazione" in [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md). Dopo aver creato un account di archiviazione, è possibile collegarlo all'account Batch tramite il pannello **Account di archiviazione**.

> [!WARNING]
> Batch usa Archiviazione di Azure per archiviare i pacchetti dell'applicazione. L'[importo addebitato][storage_pricing] sarà quindi lo stesso calcolato per i dati dei BLOB in blocchi. Controllare la dimensione e il numero dei pacchetti dell’applicazione e rimuovere periodicamente i pacchetti obsoleti per ridurre al minimo il costo.
> 
> 

### Visualizzare le applicazioni correnti
Per visualizzare le applicazioni nell'account Batch, fare clic sul riquadro **Applicazioni** nel pannello **Account Batch**.

![Riquadro Applicazioni][2]

Verrà aperto il pannello **Applicazioni**:

![Elenco applicazioni][3]

Nel pannello **Applicazioni** vengono visualizzati l'ID di ogni applicazione nell'account e le proprietà seguenti:

* **Pacchetti**: il numero delle versioni associate a questa applicazione.
* **Versione predefinita**: la versione che verrà installata se non si specifica una versione durante l'impostazione dell'applicazione per un pool. Questa impostazione è facoltativa.
* **Consenti aggiornamenti**: il valore che specifica se sono consentiti aggiornamenti, eliminazioni e aggiunte per il pacchetto. Se l'opzione è impostata su **No**, gli aggiornamenti del pacchetto e le eliminazioni sono disabilitate per l'applicazione. È possibile aggiungere solo nuove versioni del pacchetto dell'applicazione. Il valore predefinito è **Sì**.

### Visualizzare i dettagli dell'applicazione
Se si seleziona un'applicazione nel pannello **Applicazioni**, verrà visualizzato il pannello che include i dettagli dell'applicazione.

![Dettagli applicazione][4]

Nel pannello dei dettagli dell'applicazione, è possibile configurare le impostazioni seguenti per l'applicazione.

* **Consenti aggiornamenti**: specificare se i pacchetti dell'applicazione possono essere aggiornati o eliminati. Vedere "Aggiornare o eliminare un pacchetto dell'applicazione" più avanti in questo articolo.
* **Versione predefinita**: specificare un pacchetto dell'applicazione predefinito da distribuire nei nodi di calcolo.
* **Nome visualizzato**: si tratta di un nome descrittivo che la soluzione Batch può usare per visualizzare informazioni sull'applicazione, ad esempio nell'interfaccia utente di un servizio offerto ai clienti tramite Batch.

### Aggiungere un’applicazione nuova
Per creare un'applicazione nuova, aggiungere un pacchetto dell'applicazione usando un ID applicazione nuovo e univoco. Il primo pacchetto dell'applicazione aggiunto usando il nuovo ID applicazione creerà la nuova applicazione.

Fare clic su **Aggiungi** nel pannello **Applicazioni** per aprire il pannello **Nuova applicazione**.

![Pannello Nuova applicazione nel portale di Azure][5]

Il pannello **Nuova applicazione** contiene i campi seguenti per specificare le impostazioni della nuova applicazione e del pacchetto dell'applicazione.

**ID applicazione**

Questo campo specifica l'ID della nuova applicazione, che è soggetto alle regole standard di convalida dell'ID di Azure Batch:

* Può contenere qualsiasi combinazione di caratteri alfanumerici, inclusi segni meno e caratteri di sottolineatura.
* Non può contenere più di 64 caratteri.
* Deve essere univoco nell’account Batch.
* Mantiene ma non fa distinzione tra maiuscole e minuscole.

**Versione**

Specifica la versione del pacchetto dell'applicazione che si sta caricando. Le stringhe della versione sono soggette alle regole di convalida seguenti:

* Possono contenere qualsiasi combinazione di caratteri alfanumerici, inclusi segni meno, caratteri di sottolineatura e periodi.
* Non possono contenere più di 64 caratteri.
* Devono essere univoche nell’applicazione.
* Mantengono ma non fanno distinzione tra maiuscole e minuscole.

**Pacchetto dell'applicazione**

Questo campo specifica il file ZIP contenente i file binari e i file di supporto dell'applicazione necessari per l'esecuzione dell'applicazione. Fare clic sulla casella **Selezionare un file** oppure sull'icona della cartella per cercare e selezionare un file ZIP contenente i file dell'applicazione.

Dopo aver selezionato un file, fare clic su **OK** per avviare il caricamento in Archiviazione di Azure. Al termine dell'operazione di caricamento, si riceverà una notifica e il pannello verrà chiuso. A seconda delle dimensioni del file che si sta caricando e della velocità della connessione di rete, l'operazione potrebbe richiedere alcuni minuti.

> [!WARNING]
> Non chiudere il pannello **Nuova applicazione** prima che l'operazione di caricamento sia terminata, altrimenti il processo di caricamento sarà interrotto.
> 
> 

### Aggiungere un nuovo pacchetto dell’applicazione
Per aggiungere una nuova versione del pacchetto dell'applicazione per un'applicazione esistente, selezionare un'applicazione nel pannello **Applicazioni**, fare clic su **Pacchetti** e quindi su **Aggiungi** per visualizzare il pannello **Aggiungi pacchetto**.

![Pannello per aggiungere pacchetto dell’applicazione nel portale di Azure][8]

Come si può notare, i campi corrispondono a quelli del pannello **Nuova applicazione**, ad eccezione della casella **ID applicazione** che è disabilitata. Come è stato fatto per la nuova applicazione, specificare la **versione** del nuovo pacchetto, scegliere il file ZIP del **pacchetto dell'applicazione** e quindi fare clic su **OK** per caricare il pacchetto.

### Aggiornare o eliminare un pacchetto dell'applicazione
Per aggiornare o eliminare un pacchetto dell'applicazione esistente, aprire il pannello dei dettagli relativo all'applicazione, fare clic su **Pacchetti** per visualizzare il pannello **Pacchetti**, fare clic sui **puntini di sospensione** nella riga del pacchetto dell'applicazione che si vuole modificare e quindi selezionare l'azione da eseguire.

![Aggiornare o eliminare pacchetto nel portale di Azure][7]

**Update**

Se si seleziona **Aggiorna**, verrà visualizzato il pannello *Aggiorna pacchetto*. Questo pannello è simile al pannello usato per creare un *nuovo pacchetto dell'applicazione*. In questo caso, però, è abilitato solo il campo di selezione del pacchetto, in cui è possibile specificare un nuovo file ZIP da caricare.

![Pannello Aggiorna pacchetto nel portale di Azure][11]

**Eliminazione**

Se si seleziona **Elimina**, verrà chiesto di confermare l'eliminazione della versione del pacchetto e Batch eliminerà il pacchetto da Archiviazione di Azure. Se si elimina la versione predefinita di un'applicazione, verrà rimossa l'impostazione **Versione predefinita** per l'applicazione.

![Eliminare applicazione][12]

## Installare le applicazioni su nodi di calcolo
Dopo aver visto come gestire i pacchetti dell'applicazione con il portale di Azure, verrà ora descritta la distribuzione dei pacchetti nei nodi di calcolo e la relativa esecuzione con attività di Batch.

### Installare pacchetti dell'applicazione nel pool
Per installare un pacchetto dell'applicazione in tutti i nodi di calcolo di un pool, specificare uno o più *riferimenti* al pacchetto dell'applicazione per il pool. I pacchetti dell'applicazione specificati per un pool vengono installati su ciascun nodo di calcolo quando tale nodo viene aggiunto al pool e quando il nodo viene riavviato o ne viene ricreata l'immagine.

In Batch .NET specificare uno o più [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] quando si crea un nuovo pool, oppure aggiungerli al pool esistente. La classe [ApplicationPackageReference][net_pkgref] specifica un ID applicazione e la versione da installare nei nodi di calcolo di un pool.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicated: "1",
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package will be installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> Se una distribuzione del pacchetto dell'applicazione non riesce per qualsiasi motivo, il servizio Batch contrassegna il nodo come [inutilizzabile][net_nodestate] e nessuna attività verrà pianificata per l'esecuzione in tale nodo. In questo caso è necessario **riavviare** il nodo per reinizializzare la distribuzione del pacchetto. Il riavvio del nodo consentirà anche di pianificarne di nuovo le attività.
> 
> 

### Installare pacchetti dell'applicazione per le attività
Come per un pool, specificare i *riferimenti* del pacchetto dell'applicazione per un'attività. Quando un'attività è pianificata per l'esecuzione su un nodo, il pacchetto viene scaricato ed estratto appena prima dell'esecuzione della riga di comando dell'attività. Se un pacchetto specificato con la versione corrispondente è già installato nel nodo, non verrà scaricato e verrà usato il pacchetto esistente.

Per installare un pacchetto dell'applicazione per le attività, configurare la proprietà [CloudTask][net_cloudtask].[ApplicationPackageReferences][net_cloudtask_pkgref] dell'attività:

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

## Eseguire le applicazioni installate
I pacchetti specificati per un pool o un'attività vengono scaricati ed estratti in una directory denominata all'interno del nodo `AZ_BATCH_ROOT_DIR`. Batch crea anche una variabile di ambiente che contiene il percorso della directory denominata. Le righe di comando dell'attività usano questa variabile di ambiente quando fanno riferimento all'applicazione nel nodo. La variabile è nel formato seguente:

`AZ_BATCH_APP_PACKAGE_APPLICATIONID#version`

`APPLICATIONID` e `version` sono valori che corrispondono all'applicazione e alla versione del pacchetto specificati per la distribuzione. Se ad esempio si specifica l'installazione della versione 2.7 dell'applicazione *blender*, le righe di comando dell'attività useranno questa variabile di ambiente per accedere ai file corrispondenti:

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

Se si specifica una versione predefinita per un'applicazione, è possibile omettere il suffisso della versione. Se ad esempio è stata specificata la versione predefinita "2.7" per l'applicazione *blender*, le attività possono fare riferimento alla variabile di ambiente seguente per eseguire la versione 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Il frammento di codice seguente mostra una riga di comando dell'attività di esempio che consente di avviare la versione predefinita dell'applicazione *blender*:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Per altre informazioni sulle impostazioni dell'ambiente dei nodi di calcolo, vedere [Impostazioni di ambiente per le attività](batch-api-basics.md#environment-settings-for-tasks) in [Panoramica delle funzionalità di Batch per sviluppatori](batch-api-basics.md).
> 
> 

## Aggiornare i pacchetti dell’applicazione di un pool
Se un pool esistente è già stato configurato con un pacchetto dell’applicazione, è possibile specificare un nuovo pacchetto per il pool. Se si specifica un nuovo riferimento al pacchetto per un pool, si applica quanto segue:

* Per tutti i nuovi nodi aggiunti al pool e tutti i nodi esistenti riavviati o per i quali viene ricreata l'immagine viene installato il pacchetto appena specificato.
* I nodi di calcolo che si trovano già nel pool quando si aggiornano i riferimenti al pacchetto non installano automaticamente il nuovo pacchetto dell’applicazione. Questi nodi di calcolo devono essere riavviati o ne deve essere ricreata l'immagine per ricevere il nuovo pacchetto.
* Quando viene distribuito un nuovo pacchetto, le variabili di ambiente create riflettono i riferimenti al nuovo pacchetto dell'applicazione.

In questo esempio il pool esistente ha la versione 2.7 dell'applicazione *blender* configurata come uno dei relativi [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref]. Per aggiornare i nodi del pool alla versione 2.76b, specificare un nuovo [ApplicationPackageReference][net_pkgref] con la nuova versione ed eseguire il commit della modifica.

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

Ora che è stata configurata la nuova versione, per qualsiasi *nuovo* nodo aggiunto al pool verrà distribuita la versione 2.76b. Per installare la versione 2.76b nei nodi *già* presenti nel pool, riavviarli o ricrearne l'immagine. Notare che i nodi riavviati manterranno i file delle distribuzioni precedenti del pacchetto.

## Elencare le applicazioni in un account Batch
È possibile elencare le applicazioni e i relativi pacchetti in un account Batch usando il metodo [ApplicationOperations][net_appops].[ListApplicationSummaries][net_appops_listappsummaries].

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

## Eseguire il wrapping
Con i pacchetti dell'applicazione è possibile assistere i clienti nella scelta delle applicazioni per i loro processi e specificare la versione corretta da usare durante l'elaborazione dei processi con il servizio abilitato per Batch. I clienti possono inoltre caricare e monitorare le applicazioni usate nel servizio.

## Passaggi successivi
* L'[API Batch REST][api_rest] fornisce anche il supporto per l'uso dei pacchetti dell'applicazione. Per informazioni su come specificare i pacchetti da installare con l'API REST, vedere ad esempio l'elemento [applicationPackageReferences][rest_add_pool_with_packages] in [Aggiungere un pool a un account][rest_add_pool]. Per dettagli su come ottenere informazioni sull'applicazione con l'API Batch REST, vedere [Applicazioni][rest_applications].
* È possibile scoprire come [gestire quote e account Azure Batch con la gestione .NET per Batch](batch-management-dotnet.md) a livello di codice. Con la libreria di [gestione .NET per Batch][api_net_mgmt] è possibile abilitare funzionalità di creazione ed eliminazione di account per l'applicazione o il servizio Batch.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagramma di alto livello di pacchetti applicazione"
[2]: ./media/batch-application-packages/app_pkg_02.png "Riquadro Applicazioni nel portale di Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Pannello Applicazioni nel portale di Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Pannello Dettagli applicazione nel portale di Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Pannello Nuova applicazione nel portale di Azure"
[7]: ./media/batch-application-packages/app_pkg_07.png "Elenco a discesa per aggiornamento o eliminazione pacchetto nel portale di Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Pannello per nuovo pacchetto dell’applicazione nel portale di Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png "Avviso. Nessun account di archiviazione collegato"
[10]: ./media/batch-application-packages/app_pkg_10.png "Selezionare il pannello Account di archiviazione nel portale di Azure"
[11]: ./media/batch-application-packages/app_pkg_11.png "Pannello Aggiorna pacchetto nel portale di Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Finestra di conferma eliminazione pacchetto nel portale di Azure"

<!---HONumber=AcomDC_0831_2016-->