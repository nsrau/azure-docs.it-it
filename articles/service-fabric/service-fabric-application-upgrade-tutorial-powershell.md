---
title: Aggiornamento di un'app di Service Fabric mediante PowerShell | Documentazione Microsoft
description: In questo articolo vengono esaminati l'esperienza di distribuzione di un'applicazione di Service Fabric, la modifica del codice e l'implementazione di un aggiornamento tramite PowerShell.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 9bc75748-96b0-49ca-8d8a-41fe08398f25
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/27/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: c685deaa008cfdba5971cef4cfc7dfa41b1df64d
ms.contentlocale: it-it
ms.lasthandoff: 04/27/2017

---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Aggiornamento di un'applicazione di Service Fabric mediante PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Il metodo di aggiornamento consigliato usato più frequentemente è l'aggiornamento in sequenza in modalità monitorata (Monitored).  Azure Service Fabric monitora l'integrità dell'applicazione che viene aggiornata in base a un set di criteri di integrità. Dopo che un dominio di aggiornamento è stato aggiornato, Service Fabric valuta l'integrità dell'applicazione e poi o passa al dominio di aggiornamento successivo o annulla l'aggiornamento in base ai criteri di integrità.

L'aggiornamento di un'applicazione in modalità monitorata può essere eseguito usando le API gestite o native, PowerShell o REST. Per istruzioni su come eseguire un aggiornamento usando Visual Studio, vedere [Esercitazione sull'aggiornamento di un'applicazione di Service Fabric tramite Visual Studio](service-fabric-application-upgrade-tutorial.md).

L'aggiornamento in sequenza in modalità monitorata di Service Fabric consente all'amministratore di applicazioni di configurare i criteri di valutazione dell'integrità usati da Service Fabric per determinare se l'applicazione è integra. L'amministratore può anche configurare l'azione da intraprendere se la valutazione dell'integrità non riesce, ad esempio l'esecuzione di un rollback automatico. Questa sezione descrive in dettaglio un aggiornamento monitorato per uno degli esempi di SDK che usa PowerShell. Il video di Microsoft Virtual Academy seguente illustra anche come aggiornare un'app: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=OrHJH66yC_6406218965">
<img src="./media/service-fabric-application-upgrade-tutorial-powershell/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Passaggio 1: creare e distribuire l'esempio di oggetti visivi
Compilare e pubblicare l'applicazione facendo clic con il pulsante destro del mouse sul progetto dell'applicazione, **VisualObjectsApplication**, e selezionando il comando **Pubblica**.  Per altre informazioni, vedere [Esercitazione sull'aggiornamento di un'applicazione di Service Fabric tramite Visual Studio](service-fabric-application-upgrade-tutorial.md).  In alternativa, è possibile usare PowerShell per distribuire l'applicazione.

> [!NOTE]
> Per poter usare uno dei comandi di Service Fabric in PowerShell è necessario connettersi prima al cluster con il cmdlet `Connect-ServiceFabricCluster`. Si presuppone che il cluster sia stato già configurato nel computer locale. Vedere l'articolo relativo alla [configurazione dell'ambiente di sviluppo di Service Fabric](service-fabric-get-started.md).
> 
> 

Dopo aver compilato il progetto in Visual Studio è possibile usare il comando di PowerShell [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) per copiare il pacchetto dell'applicazione in ImageStore. Se si desidera verificare il pacchetto dell'applicazione in locale, usare il cmdlet [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage). Il passaggio successivo consiste nel registrare l'applicazione nel runtime di Service Fabric mediante il cmdlet [Register-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) . Il passaggio finale consiste nell'avviare un'istanza dell'applicazione con il cmdlet [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) .  Questi tre passaggi sono analoghi all'uso della voce di menu **Distribuisci** in Visual Studio.

È ora possibile usare [Service Fabric Explorer per visualizzare il cluster e l'applicazione](service-fabric-visualizing-your-cluster.md). L'applicazione dispone di un servizio Web a cui è possibile accedere in Internet Explorer digitando [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) sulla barra degli indirizzi.  Verranno visualizzati alcuni oggetti visivi mobili sullo schermo.  È anche possibile usare [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) per verificare lo stato dell'applicazione.

## <a name="step-2-update-the-visual-objects-sample"></a>Passaggio 2: aggiornare l'applicazione Oggetti visivi di esempio
È possibile osservare che con la versione distribuita al passaggio 1 gli oggetti visivi non ruotano. L'applicazione può essere aggiornata in modo che gli oggetti visivi ruotino.

Selezionare il progetto VisualObjects.ActorService nella soluzione VisualObjects e aprire il file StatefulVisualObjectActor.cs. All'interno del file passare al metodo `MoveObject`, impostare come commento `this.State.Move()` e rimuovere il commento `this.State.Move(true)`. Questa modifica fa ruotare gli oggetti dopo l'aggiornamento del servizio.

È anche necessario aggiornare il file *ServiceManifest.xml* (in PackageRoot) del progetto **VisualObjects.ActorService**. Aggiornare *CodePackage* e la versione del servizio alla versione 2.0, nonché le righe corrispondenti nel file *ServiceManifest.xml*.
È possibile usare l'opzione *Edit Manifest Files* (Modifica file manifesto) di Visual Studio dopo aver fatto clic con il pulsante destro del mouse sulla soluzione per apportare le modifiche al file manifesto.

Dopo aver apportato le modifiche, il manifesto dovrebbe essere simile al seguente (le parti evidenziate indicano le modifiche):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

A questo punto il file *ApplicationManifest.xml*, disponibile nel progetto **VisualObjects** della soluzione **VisualObjects**, viene aggiornato alla versione 2.0 del progetto **VisualObjects.ActorService**. Anche la versione dell'applicazione viene aggiornata a 2.0.0.0 da 1.0.0.0. Il file *ApplicationManifest.xml* sarà simile al seguente frammento di codice:

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```


A questo punto compilare il progetto selezionando solo il progetto **ActorService** e quindi facendo clic con il pulsante destro del mouse e scegliendo l'opzione **Compila** in Visual Studio. Se si seleziona **Ricompila tutto**, è necessario aggiornare le versioni per tutti i progetti, poiché il codice è stato modificato. È quindi possibile aggiungere l'applicazione aggiornata al pacchetto facendo clic con il pulsante destro del mouse sul progetto ***VisualObjectsApplication***, scegliendo il menu Service Fabric e quindi **Pacchetto**. In questo modo viene creato un pacchetto dell'applicazione che può essere distribuito.  L'applicazione aggiornata è pronta per essere distribuita.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Passaggio 3: Scegliere i criteri di integrità e i parametri di aggiornamento
È consigliabile acquisire familiarità con i [parametri di aggiornamento dell'applicazione](service-fabric-application-upgrade-parameters.md) e con il [processo di aggiornamento](service-fabric-application-upgrade.md) per conoscere i diversi parametri di aggiornamento, valori di timeout e criteri di integrità applicati. Per questa procedura dettagliata i criteri predefiniti di valutazione dell'integrità dei servizi sono impostati con i valori consigliati, quindi tutti i servizi e tutte le istanze saranno *integri* dopo l'aggiornamento.  

Aumentare tuttavia il valore di *HealthCheckStableDuration* impostandolo su 60 secondi, in modo che i servizi siano integri per almeno 20 secondi prima che il processo di aggiornamento passi al dominio di aggiornamento successivo.  Impostare quindi *UpgradeDomainTimeout* su 1200 secondi e *UpgradeTimeout* su 3000 secondi.

Infine impostare anche *UpgradeFailureAction* in modo che venga eseguito il rollback. Questa opzione richiede che Service Fabric esegua il rollback dell'applicazione alla versione precedente se riscontra problemi durante l'aggiornamento. Perciò, all'avvio dell'aggiornamento (al passaggio 4), vengono specificati i parametri seguenti:

FailureAction = Rollback

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Passaggio 4: preparare l'applicazione per l'aggiornamento
L'applicazione ora è compilata e pronta per l'aggiornamento. Se si apre una finestra di PowerShell come amministratore e si digita [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps), verrà indicato che è stato distribuito il tipo di applicazione 1.0.0.0 di **VisualObject**.  

Il pacchetto applicazione è archiviato nel percorso relativo seguente, dove è stato decompresso Service Fabric SDK - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. In questa directory deve essere disponibile una cartella "Package", dove è archiviato il pacchetto dell'applicazione. Controllare i timestamp per assicurarsi che si tratti dell'ultima build. Potrebbe essere anche necessario modificare i percorsi in base alle esigenze.

Copiare ora il pacchetto applicazione aggiornato in ImageStore di Service Fabric (dove Service Fabric archivia i pacchetti applicazione). Il parametro *ApplicationPackagePathInImageStore* indica a Service Fabric dove è contenuto il pacchetto applicazione. L'applicazione aggiornata è stata inserita in "VisualObjects\_V2" con il comando seguente. Potrebbe essere di nuovo necessario modificare i percorsi in base alle esigenze.

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

Il passaggio successivo consiste nel registrare l'applicazione con Service Fabric, operazione che può essere eseguita usando il comando [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps):

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Se il comando precedente non riesce, è probabile che si debbano ricompilare tutti i servizi. Come indicato nel passaggio 2, potrebbe essere necessario aggiornare anche la versione di WebService.

## <a name="step-5-start-the-application-upgrade"></a>Passaggio 5: avviare l'aggiornamento dell'applicazione
Ora è tutto pronto per avviare l'aggiornamento dell'applicazione usando il comando [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps):

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Il nome dell'applicazione è lo stesso descritto nel file *ApplicationManifest.xml* . Service Fabric usa questo nome per identificare l'applicazione che viene aggiornata. Se si imposta un valore di timeout troppo breve, è possibile che venga visualizzato un messaggio di errore che indica il problema. Vedere la sezione relativa alla risoluzione dei problemi o aumentare i valori di timeout.

Man mano che l'aggiornamento dell'applicazione procede, è possibile monitorarlo con Service Fabric Explorer oppure con il comando di PowerShell [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps): 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

In pochi minuti lo stato ottenuto usando il comando di PowerShell precedente indicherà che sono stati aggiornati (completati) tutti i domini di aggiornamento. Inoltre gli oggetti visivi nella finestra del browser hanno iniziato a ruotare.

Come esercizio provare a effettuare l'aggiornamento dalla versione 2 alla versione 3 o dalla versione 2 alla versione 1: anche il passaggio dalla versione 2 alla versione 1 è considerato un aggiornamento. Esercitarsi inoltre con i timeout e i criteri di integrità per acquisire familiarità anche con questi concetti. Quando si esegue la distribuzione in un cluster di Azure, è necessario impostare i parametri in modo appropriato. È consigliabile impostare i timeout su valori contenuti.

## <a name="next-steps"></a>Passaggi successivi
[Esercitazione sull'aggiornamento di un'applicazione di Service Fabric tramite Visual Studio](service-fabric-application-upgrade-tutorial.md) descrive la procedura di aggiornamento di un'applicazione con Visual Studio.

Controllare l'aggiornamento dell'applicazione tramite [Parametri di aggiornamento](service-fabric-application-upgrade-parameters.md).

Rendere compatibili gli aggiornamenti dell'applicazione imparando a usare la [serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md).

Per informazioni su come usare funzionalità avanzate durante l'aggiornamento dell'applicazione, vedere [Argomenti avanzati](service-fabric-application-upgrade-advanced.md).

Per informazioni su come risolvere problemi comuni negli aggiornamenti dell'applicazione, vedere i passaggi indicati in [Risoluzione dei problemi relativi agli aggiornamenti dell'applicazione](service-fabric-application-upgrade-troubleshooting.md).


