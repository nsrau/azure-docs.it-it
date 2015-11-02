<properties
	pageTitle="Automatizzare la gestione dell’applicazione dell'infrastruttura di servizi tramite PowerShell | Microsoft Azure"
	description="Distribuire, aggiornare, testare e rimuovere le applicazioni dell’infrastruttura di servizi tramite PowerShell."
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
	ms.date="10/16/2015"
	ms.author="ryanwi"/>

# Distribuire, aggiornare, testare e rimuovere le applicazioni dell’infrastruttura di servizi tramite PowerShell

In questo articolo viene illustrato come utilizzare PowerShell per automatizzare attività comuni per la distribuzione, l'aggiornamento, la rimozione e il test delle applicazioni dell’infrastruttura di servizi.

## Prerequisiti

Prima di procedere alle attività nell'articolo, assicurarsi di [installare runtime, SDK e strumenti](service-fabric-get-started.md) che consentono inoltre di installare i moduli ServiceFabric e ServiceFabricTestability di PowerShell. [Abilitare l'esecuzione degli script di PowerShell](service-fabric-get-started.md#enable-powershell-script-execution) e [installare e avviare un cluster locale](service-fabric-get-started.md#install-and-start-a-local-cluster) in modo che sia possibile eseguire gli esempi in questo articolo.

Negli esempi inclusi in questo articolo usare [l'applicazione di esempio HelloWorldStateful](https://github.com/Azure/servicefabric-samples/tree/master/samples/Services/VS2015/HelloWorldStateful). Scaricare e compilare l'applicazione di esempio.

Prima di eseguire qualsiasi comando di PowerShell incluso in questo articolo, connettersi al cluster dell’infrastruttura di servizi mediante [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/azure/mt125938.aspx).

```powershell
Connect-ServiceFabricCluster
```

## ATTIVITÀ: Distribuire un’applicazione dell’infrastruttura di servizi

Dopo aver compilato l'applicazione e aver creato il pacchetto del tipo di applicazione, è possibile distribuire l'applicazione in un cluster dell’infrastruttura di servizi locale. Innanzitutto, creare il pacchetto dell'applicazione HelloWorldStateful in Visual Studio facendo clic con il pulsante destro su **HelloWorldStatefulApplication** in Esplora soluzioni e selezionando **Crea pacchetto**. Vedere [Modellare un'applicazione nell'infrastruttura di servizi](service-fabric-application-model.md) per informazioni sui manifesti dei servizio e delle applicazioni e sul layout del pacchetto. La distribuzione comporta il caricamento del pacchetto dell'applicazione, la registrazione del tipo di applicazione e la creazione dell'istanza dell'applicazione. Utilizzare le istruzioni riportate in questa sezione per distribuire una nuova applicazione in un cluster.

### Passaggio 1: Caricare il pacchetto dell’applicazione
Quando si carica il pacchetto dell’applicazione nell’ImageStore, la si inserisce in un percorso accessibile ai componenti interni dell’infrastruttura di servizi. Il pacchetto dell’applicazione contiene il manifesto dell'applicazione necessaria, i manifesti del servizio e i pacchetti di configurazione/codice/dati per creare le istanze dell'applicazione e del servizio. Il comando [Copy-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/azure/mt125905.aspx) consente di caricare il pacchetto. Ad esempio:

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\VS2015\HelloWorldStateful\HelloWorldStatefulApplication\pkg\Debug -ImageStoreConnectionString fabric:ImageStore -ApplicationPackagePathInImageStore HelloWorldStateful
```

### Passaggio 2: Registrare il tipo di applicazione
Quando si registra il pacchetto dell’applicazione, il tipo e la versione dell'applicazione dichiarati nel manifesto di quest'ultima diventano disponibili per l'uso Il sistema leggerà il pacchetto caricato nel passaggio precedente, lo verificherà (mediante un'operazione equivalente all'esecuzione di [Test-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/azure/mt125950.aspx) in locale), ne elaborerà il contenuto e infine copierà il pacchetto elaborato in un percorso di sistema interno. Eseguire il cmdlet [Register-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125958.aspx):

```powershell
Register-ServiceFabricApplicationType HelloWorldStateful
```
Per visualizzare i tipi di applicazioni registrati nel cluster, eseguire il cmdlet:

```powershell
Get-ServiceFabricApplicationType
```

### Passaggio 3: Creare l'istanza dell'applicazione
È possibile creare un'istanza dell'applicazione con qualsiasi versione del tipo di applicazione registrata correttamente mediante il comando [New-ServiceFabricApplication](https://msdn.microsoft.com/library/azure/mt125913.aspx). Il nome di ogni applicazione deve iniziare con lo schema **fabric:** ed essere univoco per ogni istanza dell'applicazione. Il nome del tipo di applicazione e la versione del tipo di applicazione vengono dichiarati nel file ApplicationManifest.xml. Se nel manifesto dell'applicazione del tipo di applicazione di destinazione sono specificati servizi predefiniti, verranno creati anch'essi in questa fase.

```powershell
New-ServiceFabricApplication fabric:/HelloWorldStatefulApplication HelloWorldStatefulApplication 1.0.0.0
```

Il comando [Get-ServiceFabricApplication](https://msdn.microsoft.com/library/azure/mt163515.aspx) elenca tutte le istanze dell'applicazione create correttamente, indicando il relativo stato complessivo. Il comando [Get-ServiceFabricService](https://msdn.microsoft.com/library/azure/mt125889.aspx) elenca tutte le istanze del servizio create correttamente all'interno di una determinata istanza dell'applicazione. Verranno elencati anche gli eventuali servizi predefiniti.

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## ATTIVITÀ: Creare un’applicazione dell’infrastruttura di servizi

È possibile aggiornare un'applicazione dell’infrastruttura di servizi distribuita in precedenza. Questa attività consente di aggiornare l'applicazione HelloWorldStateful distribuita nell’ATTIVITÀ: distribuire un'applicazione dell’infrastruttura di servizi. Leggere [Aggiornamento dell'applicazione](service-fabric-application-upgrade.md) per ulteriori informazioni.

### Passaggio 1: Aggiornare l'applicazione

Apportare modifiche al codice nel servizio HelloWorldStateful.

Dopo aver aggiornato il codice del servizio, è necessario incrementare il numero di versione del servizio nel file ServiceManifest.xml (che si trova nella directory PackageRoot del progetto HelloWorldStateful). Trovare l’elemento **CodePackage** del manifesto e modificare la versione del servizio in 2.0.0.0. Le righe corrispondenti nel file ServiceManifest.xml dovrebbero essere simile a quanto segue:

```xml
<ServiceManifest Name="HelloWorldStatefulPkg"
                 Version="2.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0.0.0">
```

A questo punto, è necessario aggiornare il file ApplicationManifest.xml (trovato nel progetto HelloWorldStatefulApplication nella soluzione HelloWorldStateful). Aggiornare l’elemento **ServiceManifestRef** per utilizzare la versione 2.0.0.0 del progetto HelloWorldStatefulPkg. Aggiornare anche **ApplicationTypeVersion** a 2.0.0.0 da 1.0.0.0. Le righe corrispondenti in ApplicationManifest.xml dovrebbero essere:

```xml
<ApplicationManifest ApplicationTypeName="HelloWorldStatefulApplication" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<ServiceManifestRef ServiceManifestName="HelloWorldStatefulPkg" ServiceManifestVersion="2.0.0.0" />
```

Dopo aver apportato queste modifiche, salvare i file e ricompilare il progetto HelloWorldStateful. Ora creare il pacchetto dell’applicazione aggiornata facendo clic con il pulsante destro sul progetto HelloWorldStatefulApplication, selezionando il menu Infrastruttura di servizi e quindi scegliendo Crea pacchetto. In questo modo verrà creato un pacchetto applicazione che può essere distribuito. L'applicazione aggiornata è ora pronta per essere distribuita.

### Passaggio 2: Copiare e registrare il pacchetto dell’applicazione aggiornata

A questo punto, l'applicazione è compilata, inserita nel pacchetto e pronta per l'aggiornamento. Se si apre una finestra di PowerShell come amministratore e si digita [Get-ServiceFabricApplication](https://msdn.microsoft.com/library/azure/mt163515.aspx), si nota che il tipo di applicazione 1.0.0.0 di **HelloWorldStatefulApplication** è stato distribuito. Per l’esempio HelloWorldStateful, il pacchetto dell’applicazione si trova in: *C:\\ServiceFabricSamples\\Services\\VS2015\\HelloWorldStateful\\HelloWorldStatefulApplication\\pkg\\Debug*.

Copiare ora il pacchetto dell’applicazione aggiornato in ImageStore dell’infrastruttura di servizi (dove l’infrastruttura di servizi archivia i pacchetti applicazione). Il parametro *ApplicationPackagePathInImageStore* indica a Service Fabric dove è contenuto il pacchetto applicazione. Il comando seguente consente di copiare il pacchetto dell’applicazione in *HelloWorldStatefulV2* nell’ImageStore:

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath C:\ServiceFabricSamples\Services\VS2015\HelloWorldStateful\HelloWorldStatefulApplication\pkg\Debug -ImageStoreConnectionString fabric:ImageStore -ApplicationPackagePathInImageStore HelloWorldStatefulV2
```

Il passaggio successivo consiste nel registrare la nuova versione dell'applicazione con l'infrastruttura di servizi, operazione che può essere eseguita utilizzando il cmdlet [Register-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125958.aspx):

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore HelloWorldStatefulV2
```

Se questo comando non funziona, potrebbe essere necessario ricompilare il servizio, come indicato nel Passaggio 1.

### Passaggio 3: Avviare l'aggiornamento
Vari parametri di aggiornamento, timeout e criterio di integrità possono essere applicati agli aggiornamenti dell'applicazione. Leggere [Parametri di aggiornamento dell'applicazione](service-fabric-application-upgrade-parameters.md) e [Processo di aggiornamento](service-fabric-application-upgrade.md) per ulteriori informazioni. Per questa procedura dettagliata, lasciare il criterio di valutazione dell’integrità del servizio sull’impostazione predefinita (e sui valori consigliati). Tutti i servizi e le istanze devono essere _integri_ dopo l'aggiornamento. Aumentare tuttavia il valore di *HealthCheckStableDuration* impostandolo su 60 secondi (in modo che i servizi siano integri per almeno 20 secondi prima che il processo di aggiornamento passi al dominio di aggiornamento successivo). Impostare inoltre *UpgradeDomainTimeout* su 1200 secondi e *UpgradeTimeout* su 3000 secondi. Infine, impostare *UpgradeFailureAction* per eseguire il rollback, che richiede che l’infrastruttura di servizi esegua il rollback dell'applicazione alla versione precedente se vengono rilevati errori durante l'aggiornamento.

È ora possibile avviare l'aggiornamento dell’applicazione usando il cmdlet [Start-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125975.aspx):

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/HelloWorldStatefulApplication -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Si noti che il nome dell'applicazione è uguale al nome dell'applicazione v1.0.0.0 distribuita in precedenza (fabric:/HelloWorldStatefulApplication). Service Fabric usa questo nome per identificare l'applicazione che viene aggiornata. Se si imposta un valore di timeout troppo breve, è possibile che venga visualizzato un messaggio di errore di timeout che indica il problema. Fare riferimento a [Risolvere i problemi degli aggiornamenti dell'applicazione](service-fabric-application-upgrade-troubleshooting.md) o aumentare i timeout.

È possibile monitorare lo stato dell'aggiornamento dell’applicazione utilizzando [Esplora infrastruttura di servizi](service-fabric-visualizing-your-cluster.md) oppure utilizzando il cmdlet [Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx):

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/HelloWorldStatefulApplication
```

In pochi minuti, il cmdlet [Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) mostra che tutti i domini di aggiornamento sono stati aggiornati (completati).

## ATTIVITÀ: Testare un’applicazione dell’infrastruttura di servizi

Per poter scrivere servizi di qualità elevata, gli sviluppatori devono essere in grado di mettere alla prova i difetti di un’infrastruttura inaffidabile per testarne la stabilità dei servizi. L’infrastruttura di servizi offre agli sviluppatori la possibilità di causare azioni di errore e testare i servizi in presenza di errori usando gli scenari di test chaos e failover. Leggere [Panoramica di testabilità](service-fabric-testability-overview.md) per ulteriori informazioni.

### Passaggio 1: Eseguire lo scenario di test chaos
Lo scenario chaos genera errori nell’intero cluster dell’infrastruttura di servizi. Lo scenario comprime in alcune ore gli errori che in genere si osservano in mesi o anni. La combinazione di errori interfoliati con la frequenza di errori elevata consente di trovare casi limite che in altri casi non sarebbero considerati. Nell'esempio seguente viene eseguito lo scenario di test chaos per 60 minuti.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### Passaggio 2: Eseguire lo scenario di test di failover
Lo scenario di test failover usa come destinazione la partizione di un servizio specifico, anziché l'intero cluster, e lascia inalterati altri servizi. Lo scenario ripete una sequenza di errori simulati e convalida del servizio, mentre viene eseguita la logica di business. Un errore nella convalida del servizio indica un problema necessita di ulteriore analisi. Il test di failover provoca solo un errore alla volta, a differenza dello scenario di test caos, che può provocare più errori. Nell'esempio seguente viene eseguito il test di failover per 60 minuti per il servizio fabric:/HelloWorldStatefulApplication/HelloWorldStateful.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/HelloWorldStatefulApplication/HelloWorldStateful"

Connect-ServiceFabricCluster

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## ATTIVITÀ: Rimuovere un’applicazione dell’infrastruttura di servizi
È possibile eliminare un'istanza di un'applicazione distribuita, rimuovere il tipo di applicazione fornito dal cluster e rimuovere il pacchetto dell’applicazione dall’ImageStore.

### Passaggio 1: Rimuovere un'istanza dell'applicazione
Quando un'istanza dell'applicazione non è più necessaria, è possibile rimuoverla definitivamente usando il cmdlet [Remove-ServiceFabricApplication](https://msdn.microsoft.com/library/azure/mt125914.aspx). In questo modo verranno rimossi automaticamente anche tutti i servizi appartenenti all'applicazione, nonché il relativo stato di servizio. Tale operazione non può essere annullata e lo stato dell'applicazione non può essere recuperato.

```powershell
Remove-ServiceFabricApplication fabric:/HelloWorldStatefulApplication
```

### Passaggio 2: Annullare la registrazione del tipo di applicazione.
Quando una determinata versione di un tipo di applicazione non è più necessaria, è possibile annullarne la registrazione usando il cmdlet [Unregister-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125885.aspx). L'annullamento della registrazione dei tipi inutilizzati determinerà il rilascio dello spazio di archiviazione usato dal pacchetto dell’applicazione nell’Image Store. È possibile annullare la registrazione di un tipo di applicazione solo se non sono state create istanze di applicazioni basate su di esso o non vi sono aggiornamenti di applicazioni in sospeso che vi fanno riferimento.

```powershell
Unregister-ServiceFabricApplicationType HelloWorldStatefulApplication 1.0.0.0
```

### Passaggio 3: Rimuovere il pacchetto dell’applicazione
Dopo aver annullato la registrazione del tipo di applicazione, il pacchetto dell’applicazione può essere rimosso dall’ImageStore utilizzando il cmdlet [Remove-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/azure/mt163532.aspx).

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString fabric:ImageStore -ApplicationPackagePathInImageStore HelloWorldStateful
```

## Risorse aggiuntive
[Ciclo di vita di un'applicazione di Service Fabric](service-fabric-application-lifecycle.md)

[Gestire un servizio di Service Fabric](service-fabric-manage-your-service-index.md)

[Cmdlet dell’infrastruttura di servizi di Azure](https://msdn.microsoft.com/library/azure/mt125965.aspx)

[Cmdlet di testabilità dell’infrastruttura di servizi di Azure](https://msdn.microsoft.com/library/azure/mt125844.aspx)

<!---HONumber=Oct15_HO4-->