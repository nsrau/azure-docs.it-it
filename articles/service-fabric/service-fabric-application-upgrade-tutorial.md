<properties
   pageTitle="Esercitazione sull'aggiornamento di un'applicazione di Service Fabric"
   description="Questo articolo descrive in modo dettagliato l'aggiornamento di un'applicazione di Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="subramar"/>



# Esercitazione sull'aggiornamento di un'applicazione

Il metodo di aggiornamento consigliato usato più frequentemente è l'aggiornamento in sequenza in modalità monitorata (Monitored). Service Fabric monitora l'integrità dell'applicazione che viene aggiornata in base a un set di criteri di integrità. Dopo che le applicazioni in un dominio di aggiornamento sono state aggiornate, Service Fabric ne valuta l'integrità e determina se passare al dominio di aggiornamento successivo o se considerare l'aggiornamento come non riuscito in base ai criteri di integrità. L'aggiornamento di un'applicazione in modalità monitorata può essere eseguito usando le API gestite o native, PowerShell o REST.

L'aggiornamento in sequenza in modalità monitorata di Service Fabric consente all'amministratore di applicazioni di configurare i criteri di valutazione dell'integrità usati da Service Fabric per determinare se l'applicazione è integra. Consente anche all'amministratore di configurare l'azione da intraprendere se la valutazione dell'integrità ha esito negativo, ad esempio il rollback automatico. Questa sezione descrive in modo dettagliato un aggiornamento in modalità monitorata per uno degli esempi di SDK.

## Passaggio 1: Creare e distribuire l'esempio di oggetti visivi

Queste operazioni possono essere eseguite aprendo il progetto in Visual Studio, facendo clic con il pulsante destro del mouse sulla soluzione e scegliendo il comando di distribuzione dal menu di Service Fabric. Per altre informazioni, vedere l'articolo relativo alla [gestione di un'applicazione di Service Fabric in Visual Studio](service-fabric-manage-application-in-visual-studio.md). In alternativa, è possibile usare PowerShell.

> [AZURE.NOTE]Per poter usare uno dei comandi di Service Fabric in PowerShell, è prima necessario connettersi al cluster mediante il cmdlet `Connect-ServiceFabricCluster`. Si presuppone che il cluster sia stato già configurato nel computer locale. Vedere l'articolo relativo alla [configurazione dell'ambiente di sviluppo di Service Fabric](service-fabric-get-started.md).

Dopo aver compilato il progetto in Visual Studio, è possibile usare il comando di PowerShell **Copy-ServiceFabricApplicationPackage** per copiare il pacchetto applicazione in ImageStore, registrare quindi l'applicazione nel runtime di Service Fabric con il cmdlet **Register-ServiceFabricApplicationPackage** e infine avviare un'istanza dell'applicazione con il cmdlet **New-ServiceFabricApplication**. Questi tre passaggi sono analoghi all'uso della voce di menu di Visual Studio Distribuisci.

È ora possibile usare [Service Fabric Explorer per visualizzare il cluster e l'applicazione](service-fabric-visualizing-your-cluster.md). L'applicazione dispone di un servizio Web a cui è possibile accedere in Internet Explorer digitando [http://localhost:80](http://localhost:80) sulla barra degli indirizzi. Verranno visualizzati alcuni oggetti visivi mobili sullo schermo. È anche possibile usare **Get-ServiceFabricApplication** per controllare lo stato dell'applicazione.

## Passaggio 2: Aggiornare l'esempio di oggetti visivi

È possibile osservare che con la versione distribuita al passaggio 1 gli oggetti visivi non ruotano. L'applicazione può essere aggiornata in modo che gli oggetti visivi ruotino.

Selezionare il progetto VisualObjects.DataService nella soluzione VisualObjects e aprire il file VisualObjects.cs nel progetto. All'interno del file passare al metodo `CreateMovedObject` e trovare la riga in cui è impostata la rotazione dell'oggetto visivo (cercare le righe seguenti nel metodo `CreateMovedObject`). Impostare come commento la riga `nextObject.Rotation = 1` e rimuovere il commento dalla riga sottostante. Dopo le modifiche, il codice dovrebbe essere simile al seguente:

```c#
            //nextObject.Rotation = 1;

            // Once that's deployed, uncomment this line and upgrade the application:

             nextObject.Rotation = (nextObject.Rotation + 10) % 360;
```

È anche necessario aggiornare il file *ServiceManifest.xml* (in PackageRoot) del progetto **VisualObjects.DataService**. Aggiornare *CodePackage* e la versione del servizio alla versione 2.0. Le righe corrispondenti nel file *ServiceManifest.xml* dovrebbero essere simili alle seguenti (le parti evidenziate mostrano le modifiche):

```xml
<ServiceManifestName="VisualObjects.DataService"Version="2.0"xmlns="http://schemas.microsoft.com/2011/01/fabric"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code"Version="2.0">
```

È ora necessario aggiornare il file *ApplicationManifest.xml* (nel progetto **VisualObjects** della soluzione **VisualObjects**) in modo da usare la versione 2.0 del progetto **VisualObjects.DataService** e aggiornare anche la versione dell'applicazione a 2.0.0.0 da 1.0.0.0. Le righe corrispondenti nel file *ApplicationManifest.xml* ora dovrebbero essere simili alle seguenti:

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"ApplicationTypeName="VisualObjects"ApplicationTypeVersion="2.0.0.0"xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.DataService"ServiceManifestVersion="2.0" />
```

Compilare ora il progetto selezionando **VisualObjects**, facendo clic con il pulsante destro del mouse e scegliendo Compila in Visual Studio. Se si sceglie Ricompila tutto, potrebbe essere necessario aggiornare le versioni anche per il progetto **VisualObjects.WebService** nei file *ServiceManifest.xml* e *ApplicationManifest.xml*, poiché il codice verrebbe modificato. È ora possibile inserire l'applicazione aggiornata nel pacchetto facendo clic con il pulsante destro del mouse sul progetto *VisualObjects*, scegliendo il menu Service Fabric e quindi il comando Pacchetto. In questo modo verrà creato un pacchetto applicazione che può essere distribuito. L'applicazione aggiornata è ora pronta per essere distribuita.

## Passaggio 3: Scegliere i criteri di integrità e i parametri di aggiornamento

È consigliabile acquisire familiarità con i [parametri di aggiornamento dell'applicazione](service-fabric-application-upgrade-parameters.md) e con il [processo di aggiornamento](service-fabric-application-upgrade.md) per conoscere i diversi parametri di aggiornamento, valori di timeout e criteri di integrità applicati. Per questa procedura dettagliata verranno mantenuti i criteri predefiniti di valutazione dell'integrità dei servizi (con i valori consigliati), pertanto tutti i servizi e tutte le istanze dovrebbero essere _integri_ dopo l'aggiornamento. Aumentare tuttavia il valore di *HealthCheckStableDuration* impostandolo su 60 secondi (in modo che i servizi siano integri per almeno 20 secondi prima che il processo di aggiornamento passi al dominio di aggiornamento successivo). Impostare inoltre *UpgradeDomainTimeout* su 1200 secondi e *UpgradeTimeout* su 3000 secondi. Impostare infine *UpgradeFailureAction* sull'azione di rollback richiedendo in questo modo a Service Fabric di eseguire il rollback dell'applicazione alla versione precedente in caso di problemi durante l'aggiornamento. I parametri di aggiornamento che verranno specificati all'avvio dell'aggiornamento (nel passaggio 4) saranno quindi i seguenti:

FailureAction = Rollback

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## Passaggio 4: Preparare l'applicazione per l'aggiornamento

L'applicazione ora è compilata e pronta per l'aggiornamento. Se si apre una finestra di PowerShell come amministratore e si digita **Get-ServiceFabricApplication**, verrà indicato che è stato distribuito il tipo di applicazione 1.0.0.0 di **VisualObjects**. Il pacchetto applicazione è archiviato nel percorso relativo seguente, dove è stato decompresso Service Fabric SDK - *Samples\\Services\\Stateful\\VisualObjects\\VisualObjects\\obj\\x64\\Debug*. In questa directory deve essere contenuta una cartella "Package", dove è archiviato il pacchetto applicazione. Controllare i timestamp per assicurarsi che si tratti dell'ultima build (potrebbe essere anche necessario modificare i percorsi in base alle esigenze).

Copiare ora il pacchetto applicazione aggiornato in ImageStore di Service Fabric (dove Service Fabric archivia i pacchetti applicazione). Il parametro *ApplicationPackagePathInImageStore* indica a Service Fabric dove è contenuto il pacchetto applicazione. L'applicazione aggiornata è stata posizionata in "VisualObjects\_V2" con il comando seguente (potrebbe essere necessario di nuovo modificare i percorsi in base alle esigenze).

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

Il passaggio successivo consiste nel registrare l'applicazione in Service Fabric. A tale scopo, usare il comando seguente:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Se il comando precedente ha esito negativo, è probabile che sia necessario ricompilare tutti i servizi. Come indicato nel passaggio 2, potrebbe essere necessario aggiornare anche la versione di WebService.

## Passaggio 5: Avviare l'aggiornamento dell'applicazione

È ora possibile avviare l'aggiornamento dell'applicazione usando il comando seguente:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Si noti che il nome dell'applicazione è quello descritto nel file *ApplicationManifest.xml*. Service Fabric usa questo nome per identificare l'applicazione che viene aggiornata. Se si imposta un valore di timeout troppo breve, è possibile che venga visualizzato un messaggio di errore che indica il problema. Vedere la sezione relativa alla risoluzione dei problemi o aumentare il valore dei timeout.

Man mano che procede l'aggiornamento dell'applicazione, è possibile monitorarlo usando Service Fabric Explorer oppure il comando di PowerShell seguente: **Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects**.

In pochi minuti lo stato che usa il comando di PowerShell precedente dovrebbe indicare che sono stati aggiornati (completati) tutti i domini di aggiornamento. A questo punto gli oggetti visivi nella finestra del browser avranno iniziato a ruotare.

È possibile provare a cambiare le versioni e a passare dalla versione 2 alla versione 3 come esercitazione oppure addirittura dalla versione 2 alla versione 1 (è possibile effettuare l'aggiornamento dalla versione 2 alla versione 1). Approfittare anche per acquisire familiarità con i timeout e i criteri di integrità. Per la distribuzione in un cluster di Azure, i parametri usati saranno diversi da quelli usati per la distribuzione in un cluster locale. È consigliabile impostare i timeout su valori contenuti.


## Passaggi successivi

[Parametri di aggiornamento](service-fabric-application-upgrade-parameters.md)

[Serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md)

[Argomenti avanzati](service-fabric-application-upgrade-advanced.md)

[Risoluzione dei problemi relativi all'aggiornamento di un'applicazione](service-fabric-application-upgrade-troubleshooting.md)
 

<!---HONumber=Oct15_HO3-->