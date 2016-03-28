<properties
	pageTitle="Processi semplici di installazione e gestione dell'applicazione in Azure Batch | Microsoft Azure"
	description="Usare la funzionalità dei pacchetti dell’applicazione di Azure Batch per gestire facilmente più applicazioni e versioni ed eseguire l'installazione su nodi di calcolo in Batch."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="03/14/2016"
	ms.author="marsma" />

# Distribuzione delle applicazioni con i pacchetti dell’applicazione di Azure Batch.

I pacchetti dell’applicazione sono una funzionalità di Azure Batch che consente di gestire e distribuire facilmente le applicazioni sui nodi di calcolo del pool. Con i pacchetti dell’applicazione è possibile caricare e gestire versioni diverse delle applicazioni eseguite dalle attività, inclusi i file binari e i file di supporto, e distribuire poi automaticamente una o più applicazioni sui nodi di calcolo del pool.

In questo articolo vengono indicate tutte le informazioni necessarie per caricare e gestire i pacchetti dell’applicazione usando il portale di Azure e per installare poi i pacchetti sui nodi di calcolo del pool tramite la libreria [.NET di Batch][api_net].

> [AZURE.NOTE] La funzionalità dei pacchetti dell’applicazione descritta di seguito sostituisce la funzionalità App Batch disponibile nelle versioni precedenti del servizio.

## Requisiti dei pacchetti dell’applicazione

La funzionalità dei pacchetti dell’applicazione descritta in questo articolo è compatibile *solo* con i pool di Batch creati dopo il 10 marzo 2016. I pacchetti dell’applicazione non saranno distribuiti sui nodi di calcolo di pool creati prima di questa data.

Questa funzionalità è stata introdotta nella versione 2015-12-01.2.2 dell’[API REST di Batch][api_rest] e nella versione 3.1.0 della libreria [ .NET di Batch][api_net] corrispondente. È consigliabile usare sempre la versione API più recente quando si usa Batch.

## Informazioni sulle applicazioni e sui pacchetti dell’applicazione

Con **applicazione**, in Azure Batch, si intende un set di file binari, la cui versione è stata controllata, che possono essere scaricati automaticamente sui nodi di calcolo del pool. Un **pacchetto dell’applicazione** è invece un *set specifico* di tali file binari e rappresenta una *versione* precisa dell’applicazione.

![Diagramma di alto livello di applicazioni e pacchetti applicazione][1]

### Applicazioni

In Batch un’applicazione contiene una o più pacchetti. Specifica le opzioni di configurazione per l'applicazione, ad esempio la versione predefinita del pacchetto dell'applicazione da installare sui nodi di calcolo, e se i pacchetti possono essere aggiornati o eliminati.

### Pacchetti dell'applicazione

Un pacchetto dell’applicazione è un file ZIP contenente i file binari e i file di supporto dell’applicazione, necessari per l'esecuzione delle attività. Ogni pacchetto dell’applicazione rappresenta una versione specifica dell'applicazione. Quando si crea un pool nel servizio Batch, è possibile specificare una o più applicazioni e, facoltativamente, una versione. Tali pacchetti dell’applicazione vengono automaticamente scaricati ed estratti in ogni nodo quando sono aggiunti al pool.

> [AZURE.IMPORTANT] Sono previste restrizioni al numero di applicazioni e di pacchetti dell’applicazione in un account Batch. Anche la dimensione massima del pacchetto dell’applicazione è limitata. Per informazioni dettagliate su questi limiti, vedere [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md)

### Vantaggi dei pacchetti dell'applicazione

Con i pacchetti dell’applicazione è possibile semplificare il codice nella soluzione Batch, nonché ridurre il sovraccarico in termini di gestione delle applicazioni eseguite delle attività.

Con i pacchetti dell’applicazione, non è necessario che l’attività d’avvio del pool debba specificare un lungo elenco di singoli file di risorse per eseguire l’installazione sui nodi. Non è necessario gestire manualmente più versioni di questi file in Archiviazione di Azure o sui nodi. Per accedere ai file dell’account di archiviazione di Azure, non è fondamentale generare un [URL di firma di accesso condiviso](../storage/storage-dotnet-shared-access-signature-part-1.md).

Batch gestisce i dettagli d’uso di Archiviazione di Azure in background per archiviare e distribuire i pacchetti dell’applicazione su nodi di calcolo. Il codice risulta quindi semplificato e il sovraccarico in termini di gestione è ridotto.

## Caricare e gestire le applicazioni

Usando il portale di Azure, è possibile aggiungere, aggiornare ed eliminare pacchetti dell’applicazione e configurare le versioni predefinite di ciascuna applicazione. Tali operazioni sono attualmente supportate solo nel portale di Azure.

Nelle sezioni seguenti viene illustrato come associare un account di archiviazione ad un account Batch e vengono analizzate le funzionalità per la gestione dei pacchetti nel portale di Azure. Viene inoltre spiegato come distribuire tali pacchetti sui nodi di calcolo usando la libreria [.NET di Batch][api_net].

### Collegare un account di archiviazione

Per usare i pacchetti dell’applicazione, è prima necessario collegare un account di archiviazione di Azure all’account Batch. Se non è stato ancora configurato un account di archiviazione per l’account Batch, il portale di Azure visualizza un avviso nel momento in cui si seleziona il riquadro *Applicazioni* per la prima volta nel pannello Account Batch.

![Avviso. Nessun account di archiviazione configurato nel portale di Azure][9]

Il servizio Batch usa l'account di archiviazione associato per archiviare e recuperare i pacchetti dell’applicazione. Dopo aver collegato i due account, Batch può distribuire automaticamente i pacchetti archiviati nell'account di archiviazione collegato sui nodi di calcolo. Selezionare le **impostazioni dell'account di archiviazione** nel pannello *Avviso* e quindi fare clic su **Account di archiviazione** nel pannello *Account di archiviazione * per collegare un account di archiviazione esistente ad un account Batch.

![Selezionare il pannello Account di archiviazione nel portale di Azure][10]

È possibile utilizzare un account di archiviazione esistente nella sottoscrizione o crearne uno nuovo per l'archiviazione del pacchetto dell’applicazione. Per informazioni dettagliate sulla creazione di un account di archiviazione, vedere "Crea un account di archiviazione" in [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md). Dopo aver creato un account di archiviazione, è possibile collegarlo all'account Batch usando il pannello *Account di archiviazione*.

> [AZURE.WARNING] Batch archivia i pacchetti dell’applicazione usando Archiviazione di Azure. L’[importo addebitato][storage_pricing] sarà pertanto lo stesso calcolato per i dati BLOB in blocchi. Controllare la dimensione e il numero dei pacchetti dell’applicazione e rimuovere periodicamente i pacchetti obsoleti per ridurre al minimo il costo.

### Visualizzare le applicazioni correnti

Selezionare il riquadro **Applicazioni** dal pannello Account Batch per visualizzare le applicazioni nell’account Batch.

![Riquadro Applicazioni][2]

Si aprirà il pannello *Applicazioni*:

![Elenco applicazioni][3]

Nel pannello *Applicazioni* viene visualizzato l'ID di ogni applicazione nell’account e le proprietà seguenti:

* **Pacchetti**: il numero delle versioni associate a questa applicazione.
* **Versione predefinita**: se non si specifica una versione durante l'impostazione dell'applicazione per un pool, viene installata questa versione. Questa impostazione è facoltativa.
* **Consenti aggiornamenti**: se è impostato su *No*, gli aggiornamenti e le eliminazioni dei pacchetti sono disabilitati. È possibile soltanto aggiungere versioni al pacchetto dell’applicazione nuovo. Il valore predefinito è *Sì*.

### Visualizzare i dettagli dell'applicazione

Selezionando un'applicazione nel pannello *Applicazioni*, viene visualizzato il pannello con i dettagli sull'applicazione specificata.

![Dettagli applicazione][4]

Nel pannello dei dettagli dell'applicazione, è possibile configurare le impostazioni seguenti per l'applicazione.

* **Consenti aggiornamenti**: specificare se i relativi pacchetti dell’applicazione possono essere aggiornati o eliminati (vedere la sezione "Aggiornare o eliminare un pacchetto dell’applicazione" riportata di seguito).
* **Versione predefinita**: specificare un pacchetto dell’applicazione predefinito da distribuire sui nodi di calcolo.
* **Nome visualizzato**: si tratta di un nome descrittivo che la soluzione Batch può usare per visualizzare informazioni sull'applicazione, ad esempio nell'interfaccia utente di un servizio offerto ai clienti tramite Batch.

### Aggiungere un’applicazione nuova

Per creare un’applicazione nuova, aggiungere un pacchetto dell’applicazione usando un ID applicazione nuovo e univoco. Il primo pacchetto dell’applicazione aggiunto usando il nuovo ID applicazione creerà la nuova applicazione.

Fare clic su **Aggiungi** nel pannello *Applicazioni* per aprire il pannello *Nuova applicazione*.

![Pannello Nuova applicazione nel portale di Azure][5]

Il pannello *Nuova applicazione* contiene i campi seguenti per specificare le impostazioni della nuova applicazione e del pacchetto.

**Metadata**

È possibile indicare i metadati dell'applicazione manualmente immettendo i valori direttamente nelle caselle di testo **ID applicazione** e **Versione** oppure è possibile caricare un file JSON che contiene i metadati. Per specificare l'ID applicazione e la versione manualmente, è sufficiente lasciare il selettore dell’elenco a discesa di **Metadati** su **Immetti metadati** (valore predefinito) e immettere manualmente i valori nelle caselle di testo **ID applicazione** e **Versione**.

Per specificare un file di metadati in formato JSON contenente l'ID e la versione di un pacchetto, selezionare **Carica file dei metatdati file** dal menu a discesa **Metadati**:

![Selettore elenco a discesa per caricare il file di metadati][6]

Fare quindi clic sull'icona cartella accanto alla casella di testo **File di metadati** visualizzata e selezionare il file locale che contiene i dati JSON. In questo esempio, si è scelto di caricare il file `litware_1.1001.2b.json`. Le caselle di testo **ID applicazione** e **Versione** sono state compilate automaticamente con le informazioni contenute nel file:

![Dettaglio file metadati selezionato][13]

Usare il formato JSON seguente per specificare i metadati del pacchetto dell'applicazione in un file:

```
{
    "id": "litware",
    "version": "1.1001.2b"
}
```

> [AZURE.NOTE] Se si carica un file di metadati JSON per l'ID e la versione, *non* è necessario modificare anche le caselle di testo ID applicazione o Versione, poiché vengono compilate automaticamente con i dati contenuti nel file JSON.

**ID applicazione**

Specifica l'ID della nuova applicazione. È soggetto alle regole standard di convalida dell’ID di Azure Batch:

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

Specifica il file ZIP contenente i file binari e i file di supporto dell’applicazione necessari per l'esecuzione delle attività. Fare clic sulla casella di testo **Seleziona file** oppure o sull'icona cartella per cercare e selezionare un file ZIP contenente i file dell'applicazione.

Dopo aver selezionato un file, fare clic su **OK** per avviare il caricamento in Archiviazione di Azure. Al termine dell'operazione di caricamento, si riceverà una notifica e il pannello verrà chiuso. A seconda delle dimensioni del file che si sta caricando e della velocità della connessione di rete, l'operazione potrebbe richiedere alcuni minuti.

> [AZURE.WARNING] Non chiudere il pannello *Nuova applicazione* prima che l'operazione di caricamento sia terminata, altrimenti il processo di caricamento sarà interrotto.

### Aggiungere un nuovo pacchetto dell’applicazione

Per aggiungere la versione di un nuovo pacchetto dell’applicazione, selezionare un’applicazione nel pannello *Applicazioni*, fare clic su **Pacchetti**e quindi fare clic su **Aggiungi** per visualizzare il pannello *Aggiungi pacchetto*.

![Pannello per aggiungere pacchetto dell’applicazione nel portale di Azure][8]

Come si può notare, i campi corrispondono a quelli nel pannello *Nuova applicazione*, ad eccezione della casella di testo ID applicazione. Come descritto in precedenza, specificare la **Versione** per il nuovo pacchetto, scegliere il file ZIP in **Pacchetto applicazione** e quindi fare clic su **OK** per caricare il pacchetto.

### Aggiornare o eliminare un pacchetto dell’applicazione

Per aggiornare o eliminare un pacchetto dell’applicazione esistente, aprire il pannello dei dettagli relativo all'applicazione, fare clic su **Pacchetti** per visualizzare il pannello *Pacchetti*, selezionare i **puntini di sospensione** nella riga del pacchetto dell'applicazione che si desidera modificare e selezionare l'azione da eseguire.

![Aggiornare o eliminare pacchetto nel portale di Azure][7]

**Update**

Se si seleziona l’**aggiornamento**, si apre il pannello *Aggiorna pacchetto*. Questo pannello è simile al pannello usato per creare un *nuovo pacchetto dell’applicazione*. In questo caso però, è abilitato solo il campo di selezione del pacchetto, consentendo così di specificare un nuovo file ZIP da caricare.

![Pannello Aggiorna pacchetto nel portale di Azure][11]

**Eliminazione**

Se si seleziona l’**eliminazione**, verrà richiesto di confermare l'eliminazione della versione del pacchetto e Batch eliminerà il pacchetto da Archiviazione di Azure. Se si elimina la versione predefinita di un'applicazione, viene rimossa l'impostazione della versione predefinita per l'applicazione.

![Eliminare applicazione][12]

## Installare le applicazioni su nodi di calcolo

Dopo aver trattato il caricamento e la gestione dei pacchetti dell’applicazione per mezzo del portale di Azure, è possibile discutere l’effettiva distribuzione dei pacchetti sui nodi di calcolo e la loro esecuzione con attività Batch.

Per installare un pacchetto dell’applicazione sui nodi di calcolo di un pool, specificare uno o più *riferimenti* del pacchetto dell’applicazione per il pool. In .NET di Batch aggiungere uno o più [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] quando si crea il pool, oppure aggiungerli al pool esistente.

La classe [ApplicationPackageReference][net_pkgref] specifica un ID applicazione e la versione da installare sui nodi di calcolo di un pool.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(poolId: "myPool",
                                          osFamily: "4",
                                          virtualMachineSize: "small",
                                          targetDedicated: "1");

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

## Eseguire le applicazioni installate

Quando ciascun nodo di calcolo viene aggiunto ad un pool (o viene riavviato o ne viene ricreata l’immagine), i pacchetti specificati vengono scaricati ed estratti in una directory denominata all'interno del nodo `AZ_BATCH_ROOT_DIR`. Batch crea inoltre una variabile di ambiente per le righe di comando dell’attività da usare quando si chiamano i file binari dell'applicazione. Questa variabile è conforme allo schema di denominazione seguente:

`AZ_BATCH_APP_PACKAGE_appid#version`

Ad esempio, se si specifica di installare la versione 2.7 per l’applicazione *blender*, le attività possono accedere ai rispettivi file binari facendo riferimento alla variabile di ambiente specificata nelle relative righe di comando:

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

Se l’applicazione specifica una versione predefinita, è possibile fare riferimento alla variabile di ambiente senza indicare il suffisso stringa relativo alla versione. Ad esempio, se è stata specificata la versione predefinita 2.7 per l’applicazione *blender* nel portale di Azure, le attività possono fare riferimento alla variabile di ambiente seguente:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Il frammento di codice seguente spiega come un'attività potrebbe essere configurata quando è stata specificata una versione predefinita per l’applicazione *blender*.

```csharp
string taskId = "blendertask01";
string commandLine = @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -my -command -args";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] Per altre informazioni sulle impostazione di ambiente dei nodi di calcolo, vedere "Impostazioni di ambiente per le attività" in [Cenni preliminari sulle funzionalità di Azure Batch](batch-api-basics.md).

## Aggiornare i pacchetti dell’applicazione di un pool

Se un pool esistente è già stato configurato con un pacchetto dell’applicazione, è possibile specificare un nuovo pacchetto per il pool. Tutti i nuovi nodi che si aggiungono al pool installano il pacchetto appena specificato, esattamente come fa ciascun nodo quando viene riavviato o per il quale viene ricreata l’immagine. I nodi di calcolo che si trovano già nel pool quando si aggiornano i riferimenti al pacchetto non installano automaticamente il nuovo pacchetto dell’applicazione.

In questo esempio il pool esistente ha la versione 2.7 dell’applicazione *blender* configurata come uno dei relativi [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref]. Per aggiornare i nodi del pool alla versione 2.76b, specificare un nuovo [ApplicationPackageReference][net_pkgref] con la nuova versione e il commit della modifica.

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

Ora che è stata configurata la versione nuova, qualsiasi nodo *nuovo* che si aggiunge al pool avrà la versione 2.76b. Per installare la versione 2.76b sui nodi che si trovano *già* nel pool, riavviarli o ricrearne l'immagine. Notare che i nodi riavviati manterranno i file delle distribuzioni precedenti del pacchetto.

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

## Conclusioni

Con i pacchetti dell’applicazione, è più semplice per i clienti scegliere le applicazioni per determinati processi e specificare la versione corretta da usare durante l’elaborazione dei processi con il servizio abilitato per Batch. I clienti possono inoltre caricare e monitorare le applicazioni usate nel servizio.

## Passaggi successivi

* L’[API REST di Batch][api_rest] garantisce inoltre supporto durante l’uso dei pacchetti dell’applicazione. Per specificare i pacchetti da installare con l’API REST, vedere ad esempio l’elemento [applicationPackageReferences][rest_add_pool_with_packages] in [Aggiungere un pool a un account][rest_add_pool]. Per informazioni dettagliate sull’applicazione se si usa l’API REST di Batch, vedere [Applicazioni][rest_applications]

* È possibile scoprire come [gestire account e quote Azure Batch con la gestione .NET per Batch](batch-management-dotnet.md) a livello di programmazione. Con la libreria di [gestione .NET per Batch][api_net_mgmt] è possibile abilitare funzionalità di creazione ed eliminazione di account per l'applicazione o il servizio Batch.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagramma di alto livello di pacchetti applicazione"
[2]: ./media/batch-application-packages/app_pkg_02.png "Riquadro Applicazioni nel portale di Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Pannello Applicazioni nel portale di Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Pannello Dettagli applicazione nel portale di Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Pannello Nuova applicazione nel portale di Azure"
[6]: ./media/batch-application-packages/app_pkg_06.png "Selettore elenco a discesa per caricare il file di metadati"
[7]: ./media/batch-application-packages/app_pkg_07.png "Elenco a discesa per aggiornamento o eliminazione pacchetto nel portale di Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Pannello per nuovo pacchetto dell’applicazione nel portale di Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png "Avviso. Nessun account di archiviazione collegato"
[10]: ./media/batch-application-packages/app_pkg_10.png "Selezionare il pannello Account di archiviazione nel portale di Azure"
[11]: ./media/batch-application-packages/app_pkg_11.png "Pannello Aggiorna pacchetto nel portale di Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Finestra di conferma eliminazione pacchetto nel portale di Azure"
[13]: ./media/batch-application-packages/app_pkg_13.png "Dettaglio file metadati selezionato"

<!---HONumber=AcomDC_0316_2016-->