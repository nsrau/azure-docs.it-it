<properties
	pageTitle="Automatizzare la gestione dell'applicazione di Service Fabric tramite PowerShell | Microsoft Azure"
	description="Distribuire, aggiornare, testare e rimuovere le applicazioni di Service Fabric tramite PowerShell."
	services="service-fabric"
	documentationCenter=".net"
	authors="rwike77"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-fabric"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="ryanwi"/>

# Distribuire, aggiornare, testare e rimuovere le applicazioni di Service Fabric tramite PowerShell

Questo articolo illustra come usare PowerShell per automatizzare attività comuni per la distribuzione, l'aggiornamento, la rimozione e il test delle applicazioni di Service Fabric di Azure.

## Prerequisiti

Prima di procedere alle attività nell'articolo, assicurarsi di [installare runtime, SDK e strumenti](service-fabric-get-started.md) che consentono inoltre di installare i moduli **ServiceFabric** e **ServiceFabricTestability** di PowerShell. [Abilitare l'esecuzione degli script di PowerShell](service-fabric-get-started.md#enable-powershell-script-execution) e [installare e avviare un cluster locale](service-fabric-get-started.md#install-and-start-a-local-cluster) in modo che sia possibile eseguire gli esempi in questo articolo.

Gli esempi in questo articolo usano l'applicazione di esempio [**WordCount**](http://aka.ms/servicefabricsamples) (che si trova negli esempi della guida introduttiva). Scaricare e compilare l'applicazione di esempio.

Prima di eseguire qualsiasi comando di PowerShell incluso in questo articolo, connettersi al cluster di Service Fabric mediante [**Connect-ServiceFabricCluster**](https://msdn.microsoft.com/library/azure/mt125938.aspx):

```powershell
Connect-ServiceFabricCluster localhost:19000
```

## Attività: Distribuire un'applicazione di Service Fabric

Dopo aver compilato l'applicazione e aver creato il pacchetto del tipo di applicazione, è possibile distribuire l'applicazione in un cluster dell’infrastruttura di servizi locale. Innanzitutto, creare il pacchetto dell'applicazione WordCount in Visual Studio facendo clic con il pulsante destro del mouse su **WordCount** in Esplora soluzioni e selezionando **Crea pacchetto**. Per informazioni sui manifesti del servizio e delle applicazioni e sul layout del pacchetto, vedere [Modellare un'applicazione in Service Fabric](service-fabric-application-model.md). La distribuzione comporta il caricamento del pacchetto dell'applicazione, la registrazione del tipo di applicazione e la creazione dell'istanza dell'applicazione. Utilizzare le istruzioni riportate in questa sezione per distribuire una nuova applicazione in un cluster.

### Passaggio 1: Caricare il pacchetto dell’applicazione
Quando si carica il pacchetto dell'applicazione nell'archivio immagini, lo si inserisce in un percorso accessibile ai componenti interni di Service Fabric. Il pacchetto dell’applicazione contiene il manifesto dell'applicazione necessaria, i manifesti del servizio e i pacchetti di configurazione/codice/dati per creare le istanze dell'applicazione e del servizio. Il comando [**Copy-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx) consente di caricare il pacchetto. Ad esempio:

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### Passaggio 2: Registrare il tipo di applicazione
Quando si registra il pacchetto dell’applicazione, il tipo e la versione dell'applicazione dichiarati nel manifesto di quest'ultima diventano disponibili per l'uso Il sistema leggerà il pacchetto caricato nel passaggio precedente, lo verificherà (mediante un'operazione equivalente all'esecuzione di [**Test-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125950.aspx) in locale), ne elaborerà il contenuto e infine copierà il pacchetto elaborato in un percorso di sistema interno. Eseguire il cmdlet [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx):

```powershell
Register-ServiceFabricApplicationType WordCount
```
Per visualizzare i tipi di applicazioni registrati nel cluster, eseguire il cmdlet:

```powershell
Get-ServiceFabricApplicationType
```

### Passaggio 3: Creare l'istanza dell'applicazione
È possibile creare un'istanza dell'applicazione con qualsiasi versione del tipo di applicazione registrata correttamente mediante il comando [**New-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx). Il nome di ogni applicazione deve iniziare con lo schema **fabric:** ed essere univoco per ogni istanza dell'applicazione. Il nome del tipo di applicazione e la versione del tipo di applicazione vengono dichiarati nel file **ApplicationManifest.xml**. Se nel manifesto dell'applicazione del tipo di applicazione di destinazione sono specificati servizi predefiniti, verranno creati anch'essi in questa fase.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

Il comando [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx) elenca tutte le istanze dell'applicazione create correttamente, indicando il relativo stato complessivo. Il comando [**Get-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125889.aspx) elenca tutte le istanze del servizio create correttamente all'interno di una determinata istanza dell'applicazione. Verranno elencati anche gli eventuali servizi predefiniti.

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## Attività: Aggiornare un'applicazione di Service Fabric

È possibile aggiornare un'applicazione dell’infrastruttura di servizi distribuita in precedenza. Questa attività consente di aggiornare l'applicazione WordCount distribuita in "Attività: Distribuire un'applicazione di Service Fabric". Per altre informazioni, vedere [Aggiornamento di un'applicazione di Service Fabric](service-fabric-application-upgrade.md).

### Passaggio 1: Aggiornare l'applicazione

Apportare modifiche al codice nel servizio WordCount.

Dopo aver aggiornato il codice del servizio, è necessario incrementare il numero di versione del servizio nel file **ServiceManifest.xml** (che si trova nella directory **PackageRoot** del progetto WordCount). Trovare l'elemento **CodePackage** del manifesto e modificare la versione del servizio in 2.0.0. Le righe corrispondenti nel file ServiceManifest.xml dovrebbero essere simile a quanto segue:

```xml
<ServiceManifest Name="WordCountServicePkg" Version="2.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatefulServiceType ServiceTypeName="WordCountServiceType" HasPersistedState="true" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="2.0.0">
	  ...
```

A questo punto, è necessario aggiornare il file ApplicationManifest.xml (trovato nel progetto dell'applicazione WordCount nella soluzione WordCount). Aggiornare l'elemento **ServiceManifestRef** per usare la versione 2.0.0.0 del progetto **WordCountServicePkg**. Aggiornare anche **ApplicationTypeVersion** a 2.0.0.0 da 1.0.0.0. Le righe corrispondenti in ApplicationManifest.xml dovrebbero essere:

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="WordCount" ApplicationTypeVersion="2.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
...
<ServiceManifestRef ServiceManifestName="WordCountServicePkg" ServiceManifestVersion="2.0.0" />
```

Dopo aver apportato queste modifiche, salvare i file e ricompilare il progetto WordCount. A questo punto creare il pacchetto dell'applicazione aggiornata facendo clic con il pulsante destro del mouse sul progetto WordCount e scegliendo **Crea pacchetto**. In questo modo verrà creato un pacchetto applicazione che può essere distribuito. L'applicazione aggiornata è ora pronta per essere distribuita.

### Passaggio 2: Copiare e registrare il pacchetto dell’applicazione aggiornata

A questo punto, l'applicazione è compilata, inserita nel pacchetto e pronta per l'aggiornamento. Se si apre una finestra di PowerShell come amministratore e si digita [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx), si noterà che è stata distribuita la versione 1.0.0 del tipo di applicazione WordCount. Per l'esempio WordCount, il pacchetto dell'applicazione si trova in *C:\\ServiceFabricSamples\\Services\\WordCount\\WordCount\\pkg\\Debug*.

Copiare ora il pacchetto dell'applicazione aggiornato nell'archivio immagini di Service Fabric (dove Service Fabric archivia i pacchetti dell'applicazione). Il parametro **ApplicationPackagePathInImageStore** indica a Service Fabric dove è contenuto il pacchetto applicazione. Il comando seguente consente di copiare il pacchetto dell'applicazione in **WordCountV2** nell'archivio immagini:

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2
```

Il passaggio successivo consiste nel registrare la nuova versione dell'applicazione con Service Fabric, operazione che può essere eseguita usando il cmdlet [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx):

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

Se questo comando non funziona, può essere necessario ricompilare il servizio, come indicato nel Passaggio 1.

### Passaggio 3: Avviare l'aggiornamento
Vari parametri di aggiornamento, timeout e criteri di integrità possono essere applicati agli aggiornamenti dell'applicazione. Per altre informazioni, leggere [Parametri di aggiornamento dell'applicazione](service-fabric-application-upgrade-parameters.md) e [Processo di aggiornamento](service-fabric-application-upgrade.md). Per questa procedura dettagliata, lasciare il criterio di valutazione dell’integrità del servizio sull’impostazione predefinita (e sui valori consigliati). Tutti i servizi e le istanze devono essere _integri_ dopo l'aggiornamento. Può tuttavia essere necessario aumentare il valore di **HealthCheckStableDuration** impostandolo su 60 secondi (in modo che i servizi siano integri per almeno 20 secondi prima che il processo di aggiornamento passi al dominio di aggiornamento successivo). Impostare inoltre **UpgradeDomainTimeout** su 1200 secondi e **UpgradeTimeout** su 3000 secondi. Infine, impostare **UpgradeFailureAction** per eseguire il **rollback**, che richiede che Service Fabric esegua il rollback dell'applicazione alla versione precedente, se vengono rilevati errori durante l'aggiornamento.

È ora possibile avviare l'aggiornamento dell'applicazione usando il cmdlet [**Start-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx):

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Si noti che il nome dell'applicazione è uguale al nome dell'applicazione v1.0.0 distribuita in precedenza (fabric:/WordCount). Service Fabric usa questo nome per identificare l'applicazione che viene aggiornata. Se si imposta un valore di timeout troppo breve, è possibile che venga visualizzato un messaggio di errore di timeout che indica il problema. Fare riferimento a [Risolvere i problemi degli aggiornamenti dell'applicazione](service-fabric-application-upgrade-troubleshooting.md) o aumentare i timeout.

È possibile monitorare lo stato dell'aggiornamento dell'applicazione usando [Esplora Service Fabric](service-fabric-visualizing-your-cluster.md) oppure usando il cmdlet [**Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx):

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

In pochi minuti, il cmdlet [Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) indica che tutti i domini di aggiornamento sono stati aggiornati (completati).

## Attività: Testare un'applicazione di Service Fabric

Per scrivere servizi di qualità elevata, gli sviluppatori devono essere in grado di mettere alla prova i difetti di un'infrastruttura inaffidabile per testarne la stabilità dei servizi. Service Fabric offre agli sviluppatori la possibilità di causare azioni di errore e testare i servizi in presenza di errori usando gli scenari di test chaos e failover. Per altre informazioni, leggere [Panoramica di testabilità](service-fabric-testability-overview.md).

### Passaggio 1: Eseguire lo scenario di test chaos
Lo scenario di test chaos genera errori nell'intero cluster di Service Fabric. Lo scenario comprime in alcune ore gli errori che in genere si osservano in mesi o anni. La combinazione di errori interfoliati con la frequenza di errori elevata consente di trovare casi limite che altrimenti non verrebbero considerati. Nell'esempio seguente viene eseguito lo scenario di test chaos per 60 minuti.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### Passaggio 2: Eseguire lo scenario di test di failover
Lo scenario di test failover usa come destinazione la partizione di un servizio specifico, anziché l'intero cluster, e lascia inalterati altri servizi. Lo scenario ripete una sequenza di errori simulati nella convalida del servizio, mentre viene eseguita la logica di business. Un errore nella convalida del servizio indica un problema necessita di ulteriore analisi. Il test failover provoca solo un errore alla volta, a differenza dello scenario di test caos, che può provocare più errori. L'esempio seguente esegue il test di failover per 60 minuti sull'infrastruttura: servizio /WordCount/WordCountService.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Connect-ServiceFabricCluster

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## Attività: Rimuovere un'applicazione di Service Fabric
È possibile eliminare un'istanza di un'applicazione distribuita, rimuovere il tipo di applicazione fornito dal cluster e rimuovere il pacchetto dell’applicazione dall’ImageStore.

### Passaggio 1: Rimuovere un'istanza dell'applicazione
Quando un'istanza dell'applicazione non è più necessaria, è possibile rimuoverla definitivamente usando il cmdlet [**Remove-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125914.aspx). In questo modo verranno rimossi automaticamente anche tutti i servizi appartenenti all'applicazione, nonché il relativo stato di servizio. Tale operazione non può essere annullata e lo stato dell'applicazione non può essere recuperato.

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### Passaggio 2: Annullare la registrazione del tipo di applicazione.
Quando una determinata versione di un tipo di applicazione non è più necessaria, è possibile annullarne la registrazione usando il cmdlet [**Unregister-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125885.aspx). L'annullamento della registrazione dei tipi inutilizzati determinerà il rilascio dello spazio di archiviazione usato dal pacchetto dell'applicazione nell'archivio immagini. È possibile annullare la registrazione di un tipo di applicazione solo se non sono state create istanze di applicazioni basate su di esso o non vi sono aggiornamenti di applicazioni in sospeso che vi fanno riferimento.

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### Passaggio 3: Rimuovere il pacchetto dell’applicazione
Dopo aver annullato la registrazione del tipo di applicazione, il pacchetto dell'applicazione può essere rimosso dall'archivio servizi usando il cmdlet [**Remove-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt163532.aspx).

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## Passaggi successivi
[Ciclo di vita dell'applicazione Service Fabric](service-fabric-application-lifecycle.md)

[Distribuire un'applicazione](service-fabric-deploy-remove-applications.md)

[Aggiornamento dell'applicazione](service-fabric-application-upgrade.md)

[Cmdlet di Service Fabric di Azure](https://msdn.microsoft.com/library/azure/mt125965.aspx)

[Cmdlet di testabilità di Service Fabric di Azure](https://msdn.microsoft.com/library/azure/mt125844.aspx)

<!---HONumber=AcomDC_1217_2015-->