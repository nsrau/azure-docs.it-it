---
title: Automatizzare la gestione dell&quot;applicazione di Service Fabric tramite PowerShell | Documentazione Microsoft
description: Distribuire, aggiornare, testare e rimuovere le applicazioni di Service Fabric tramite PowerShell.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: f03f4294-571d-4262-8a77-cc8b481b959d
ms.service: service-fabric
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/14/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 1eca8ece0fdefa711dd31ebcddd720d86b8f91b0
ms.openlocfilehash: 08d4e2044a5ef8fde073c37ea15c6388d2ea5b4e


---
# <a name="automate-the-application-lifecycle-using-powershell"></a>Automatizzare il ciclo di vita dell'applicazione tramite PowerShell
È possibile automatizzare numerosi aspetti del [ciclo di vita dell'applicazione Service Fabric](service-fabric-application-lifecycle.md) .  Questo articolo illustra come usare PowerShell per automatizzare attività comuni per la distribuzione, l'aggiornamento, la rimozione e il test delle applicazioni di Service Fabric di Azure.  Sono disponibili anche API gestite e HTTP per la gestione delle app. Per altre informazioni, vedere la pagina sul [ciclo di vita delle app](service-fabric-application-lifecycle.md) .  

## <a name="prerequisites"></a>Prerequisiti
Prima di passare alle attività nell'articolo, assicurarsi di:

* Acquisire familiarità con i concetti di Service Fabric descritti nell'articolo sulla [panoramica tecnica di Service Fabric](service-fabric-technical-overview.md).
* [Installare runtime, SDK e strumenti](service-fabric-get-started.md), che a loro volta installano il modulo PowerShell **ServiceFabric** .
* [Consentire l'esecuzione di script di PowerShell](service-fabric-get-started.md#enable-powershell-script-execution).
* Avviare un cluster locale.  Avviare una nuova finestra di PowerShell come amministratore ed eseguire lo script di configurazione del cluster dalla cartella SDK: `& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"`
* Prima di eseguire qualsiasi comando PowerShell descritto in questo articolo, connettersi al cluster di Service Fabric locale mediante [**Connect-ServiceFabricCluster**](https://docs.microsoft.com/powershell/servicefabric/vlatest/connect-servicefabriccluster): `Connect-ServiceFabricCluster localhost:19000`
* Per le attività seguenti è necessario distribuire un pacchetto dell'applicazione V1 e V2 per l'aggiornamento. Scaricare l'[applicazione di esempio **WordCount**](http://aka.ms/servicefabricsamples), che si trova negli esempi della guida introduttiva. Compilare e creare un pacchetto dell'applicazione in Visual Studio facendo clic con il pulsante destro del mouse su **WordCount** in Esplora soluzioni e selezionando **Pacchetto**. Copiare il pacchetto v1 da `C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug` a `C:\Temp\WordCount`. Copiare `C:\Temp\WordCount` in `C:\Temp\WordCountV2`, creando il pacchetto dell'applicazione v2 per l'aggiornamento. Aprire `C:\Temp\WordCountV2\ApplicationManifest.xml` in un editor di testo. Nell'elemento **ApplicationManifest** modificare l'attributo **ApplicationTypeVersion** da "1.0.0" in "2.0.0" per aggiornare il numero di versione dell'app. Salvare il file ApplicationManifest.xml modificato.

## <a name="task-deploy-a-service-fabric-application"></a>Attività: Distribuire un'applicazione di Service Fabric
Dopo aver compilato e creato o scaricato il pacchetto dell'applicazione, è possibile distribuire l'applicazione in un cluster di Service Fabric locale. La distribuzione comporta il caricamento del pacchetto dell'applicazione, la registrazione del tipo di applicazione e la creazione dell'istanza dell'applicazione. Utilizzare le istruzioni riportate in questa sezione per distribuire una nuova applicazione in un cluster.

### <a name="step-1-upload-the-application-package"></a>Passaggio 1: Caricare il pacchetto dell’applicazione
Quando si carica il pacchetto dell'applicazione nell'archivio immagini, lo si inserisce in un percorso accessibile ai componenti interni di Service Fabric.  Il pacchetto dell'applicazione contiene il manifesto dell'applicazione necessario, i manifesti del servizio e i pacchetti di codice, configurazione e dati per creare le istanze dell'applicazione e del servizio.  Il comando [**Copy-ServiceFabricApplicationPackage**](https://docs.microsoft.com/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) carica il pacchetto. Ad esempio:

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCount\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### <a name="step-2-register-the-application-type"></a>Passaggio 2: Registrare il tipo di applicazione
Quando si registra il pacchetto dell’applicazione, il tipo e la versione dell'applicazione dichiarati nel manifesto di quest'ultima diventano disponibili per l'uso  Il sistema legge il pacchetto caricato nel passaggio 1, lo verifica mediante un'operazione equivalente all'esecuzione di [**Test-ServiceFabricApplicationPackage**](https://docs.microsoft.com/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) in locale, ne elabora il contenuto e infine copia il pacchetto elaborato in un percorso di sistema interno.  Eseguire il cmdlet [**Register-ServiceFabricApplicationType**](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype):

```powershell
Register-ServiceFabricApplicationType WordCount
```
Per visualizzare tutti i tipi di applicazione registrati nel cluster, eseguire il cmdlet [Get-ServiceFabricApplicationType](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplicationtype):

```powershell
Get-ServiceFabricApplicationType
```

### <a name="step-3-create-the-application-instance"></a>Passaggio 3: Creare l'istanza dell'applicazione
È possibile creare un'istanza dell'applicazione con qualsiasi versione del tipo di applicazione registrata correttamente mediante il comando [**New-ServiceFabricApplication**](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricapplication). Il nome di ogni applicazione viene dichiarato al momento della distribuzione e deve iniziare con lo schema **fabric:** ed essere univoco per ogni istanza dell'applicazione. Il nome e la versione del tipo di applicazione vengono dichiarati nel file **ApplicationManifest.xml** del pacchetto dell'applicazione. Se nel manifesto dell'applicazione del tipo di applicazione di destinazione sono specificati servizi predefiniti, verranno creati in questa fase.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

Il comando [**Get-ServiceFabricApplication**](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplication) elenca tutte le istanze dell'applicazione create correttamente, indicando il relativo stato complessivo. Il comando [**Get-ServiceFabricService**](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricservice) elenca tutte le istanze del servizio create correttamente all'interno di una determinata istanza dell'applicazione. Vengono elencati anche gli eventuali servizi predefiniti.

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## <a name="task-upgrade-a-service-fabric-application"></a>Attività: Aggiornare un'applicazione di Service Fabric
È possibile aggiornare un'applicazione di Service Fabric distribuita in precedenza con un pacchetto dell'applicazione aggiornato. Questa attività consente di aggiornare l'applicazione WordCount distribuita in "Attività: Distribuire un'applicazione di Service Fabric". Per altre informazioni, vedere [Aggiornamento di un'applicazione di Service Fabric](service-fabric-application-upgrade.md) .

Per semplificare le operazioni in questo esempio, è stato aggiornato solo il numero di versione dell'applicazione nel pacchetto dell'applicazione WordCountV2 creato nei prerequisiti. Uno scenario più realistico comporta l'aggiornamento dei file di codice, configurazione o dati del servizio e la ricompilazione e creazione del pacchetto dell'applicazione con i numeri di versione aggiornati.  

### <a name="step-1-upload-the-updated-application-package"></a>Passaggio 1: Caricare il pacchetto dell'applicazione aggiornato
L'applicazione di V1 WordCount è pronta per l'aggiornamento. Se si apre una finestra di PowerShell come amministratore e si digita [**Get-ServiceFabricApplication**](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplication), si noterà che è stata distribuita la versione 1.0.0 del tipo di applicazione WordCount.  

Copiare ora il pacchetto dell'applicazione aggiornato nell'archivio immagini di Service Fabric (dove Service Fabric archivia i pacchetti dell'applicazione). Il parametro **ApplicationPackagePathInImageStore** indica a Service Fabric dove è contenuto il pacchetto applicazione. Il comando seguente copia il pacchetto dell'applicazione in **WordCountV2** nell'archivio immagini:  

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCountV2\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2

```
### <a name="step-2-register-the-updated-application-type"></a>Passaggio 2: Registrare il tipo di applicazione aggiornato
Il passaggio successivo consiste nel registrare la nuova versione dell'applicazione con Service Fabric usando il cmdlet [**Register-ServiceFabricApplicationType**](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype):

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

### <a name="step-3-start-the-upgrade"></a>Passaggio 3: Avviare l'aggiornamento
Vari parametri di aggiornamento, timeout e criteri di integrità possono essere applicati agli aggiornamenti dell'applicazione. Per altre informazioni, leggere [Parametri di aggiornamento dell'applicazione](service-fabric-application-upgrade-parameters.md) e [Processo di aggiornamento](service-fabric-application-upgrade.md). Tutti i servizi e le istanze devono essere *integri* dopo l'aggiornamento.  Impostare il valore di **HealthCheckStableDuration** su 60 secondi, in modo che i servizi siano integri per almeno 20 secondi prima che il processo di aggiornamento passi al dominio di aggiornamento successivo.  Impostare inoltre **UpgradeDomainTimeout** su 1200 secondi e **UpgradeTimeout** su 3000 secondi. Infine, impostare **UpgradeFailureAction** per eseguire il **rollback**, operazione che richiede che Service Fabric esegua il rollback dell'applicazione alla versione precedente, se vengono rilevati errori durante l'aggiornamento.

È ora possibile avviare l'aggiornamento dell'applicazione usando il cmdlet [**Start-ServiceFabricApplicationUpgrade**](https://docs.microsoft.com/powershell/servicefabric/vlatest/start-servicefabricapplicationupgrade):

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Si noti che il nome dell'applicazione è uguale al nome dell'applicazione v1.0.0 distribuita in precedenza (fabric:/WordCount). Service Fabric usa questo nome per identificare l'applicazione che viene aggiornata. Se si imposta un valore di timeout troppo breve, è possibile che venga visualizzato un messaggio di errore di timeout che indica il problema. Fare riferimento a [Risolvere i problemi degli aggiornamenti dell'applicazione](service-fabric-application-upgrade-troubleshooting.md)o aumentare i timeout.

### <a name="step-4-check-upgrade-progress"></a>Passaggio 4: Verificare lo stato dell'aggiornamento
È possibile monitorare lo stato dell'aggiornamento dell'applicazione usando [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) oppure il cmdlet [**Get-ServiceFabricApplicationUpgrade**](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade):

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

In pochi minuti, il cmdlet [Get-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade) indica che tutti i domini di aggiornamento sono stati aggiornati (completati).

## <a name="task-test-a-service-fabric-application"></a>Attività: Testare un'applicazione di Service Fabric
Per scrivere servizi di qualità elevata, gli sviluppatori devono essere in grado di mettere alla prova i difetti di un'infrastruttura inaffidabile per testarne la stabilità dei servizi. Service Fabric offre agli sviluppatori la possibilità di causare azioni di errore e testare i servizi in presenza di errori usando gli scenari di test chaos e failover.  Per altre informazioni, leggere [Panoramica di testabilità](service-fabric-testability-overview.md) .

### <a name="step-1-run-the-chaos-test-scenario"></a>Passaggio 1: Eseguire lo scenario di test chaos
Lo scenario di test chaos genera errori nell'intero cluster di Service Fabric. Lo scenario comprime in alcune ore gli errori che in genere si osservano in mesi o anni. La combinazione di errori interfoliati con la frequenza di errori elevata consente di trovare casi limite che altrimenti non verrebbero considerati. Nell'esempio seguente viene eseguito lo scenario di test chaos per 60 minuti.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### <a name="step-2-run-the-failover-test-scenario"></a>Passaggio 2: Eseguire lo scenario di test di failover
Lo scenario di test failover usa come destinazione la partizione di un servizio specifico, anziché l'intero cluster, e lascia inalterati altri servizi. Lo scenario ripete una sequenza di errori simulati nella convalida del servizio, mentre viene eseguita la logica di business. Un errore nella convalida del servizio indica un problema necessita di ulteriore analisi. Il test failover provoca solo un errore alla volta, a differenza dello scenario di test caos, che può provocare più errori. L'esempio seguente esegue il test di failover per 60 minuti sull'infrastruttura: servizio /WordCount/WordCountService.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## <a name="task-remove-a-service-fabric-application"></a>Attività: Rimuovere un'applicazione di Service Fabric
È possibile eliminare un'istanza di un'applicazione distribuita, rimuovere il tipo di applicazione fornito dal cluster e rimuovere il pacchetto dell’applicazione dall’ImageStore.

### <a name="step-1-remove-an-application-instance"></a>Passaggio 1: Rimuovere un'istanza dell'applicazione
Quando un'istanza dell'applicazione non è più necessaria, è possibile rimuoverla definitivamente usando il cmdlet [**Remove-ServiceFabricApplication**](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricapplication). In questo modo vengono rimossi automaticamente anche tutti i servizi appartenenti all'applicazione, nonché il relativo stato di servizio. Tale operazione non può essere annullata e lo stato dell'applicazione non può essere recuperato.

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### <a name="step-2-unregister-the-application-type"></a>Passaggio 2: Annullare la registrazione del tipo di applicazione.
Quando una determinata versione di un tipo di applicazione non è più necessaria, è possibile annullarne la registrazione usando il cmdlet [**Unregister-ServiceFabricApplicationType**](https://docs.microsoft.com/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype). L'annullamento della registrazione dei tipi inutilizzati rilascia lo spazio di archiviazione utilizzato dal pacchetto dell'applicazione nell'archivio immagini. È possibile annullare la registrazione di un tipo di applicazione solo se non sono state create istanze di applicazioni basate su di esso o non vi sono aggiornamenti di applicazioni in sospeso che vi fanno riferimento.

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### <a name="step-3-remove-the-application-package"></a>Passaggio 3: Rimuovere il pacchetto dell’applicazione
Dopo aver annullato la registrazione del tipo di applicazione, il pacchetto dell'applicazione può essere rimosso dall'archivio servizi usando il cmdlet [**Remove-ServiceFabricApplicationPackage**](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricapplicationpackage).

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## <a name="next-steps"></a>Passaggi successivi
[Ciclo di vita dell'applicazione Service Fabric](service-fabric-application-lifecycle.md)

[Distribuire un'applicazione](service-fabric-deploy-remove-applications.md)

[Aggiornamento dell'applicazione](service-fabric-application-upgrade.md)

[Cmdlet di Service Fabric di Azure](https://docs.microsoft.com/powershell/servicefabric/vlatest/servicefabric)




<!--HONumber=Nov16_HO3-->


