---
title: Argomenti avanzati sull'aggiornamento delle applicazioni
description: Questo articolo illustra alcuni degli argomenti avanzati relativi all'aggiornamento di un'applicazione di Service Fabric.
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 98d8213cc50f73ef2c053e1fe5574fe33a2f3cb6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84263092"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Aggiornamento dell'applicazione Service Fabric: argomenti avanzati

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>Aggiungere o rimuovere i tipi di servizio durante l'aggiornamento di un'applicazione

Se si aggiunge un nuovo tipo di servizio a un'applicazione pubblicata nel corso di un aggiornamento, il nuovo tipo di servizio viene aggiunto all'applicazione distribuita. Tale aggiornamento non influisce sulle istanze del servizio che facevano già parte dell'applicazione, ma è necessario creare un'istanza del tipo di servizio che è stato aggiunto per consentire l'attivazione del nuovo tipo di servizio (vedere [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Analogamente, i tipi di servizio possono essere rimossi da un'applicazione durante un aggiornamento. Tuttavia, prima di procedere con l'aggiornamento è necessario rimuovere tutte le istanze del tipo di servizio da rimuovere (vedere [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime"></a>Evitare le perdite di connessione durante i tempi di inattività pianificati

Per i tempi di inattività pianificati per le istanze senza stato, ad esempio l'aggiornamento dell'applicazione/cluster o la disattivazione del nodo, le connessioni possono essere eliminate quando l'endpoint esposto viene rimosso dopo che l'istanza diventa inattiva e ciò comporta chiusure della connessione valide.

Per evitare questo problema, configurare la funzionalità *RequestDrain* aggiungendo una *durata del ritardo di chiusura dell'istanza* nella configurazione del servizio per consentire lo svuotamento delle richieste esistenti all'interno del cluster negli endpoint esposti. Questa operazione viene eseguita perché l'endpoint annunciato dall'istanza senza stato viene rimosso *prima* dell'avvio del ritardo prima della chiusura dell'istanza. Questo ritardo consente lo svuotamento normale delle richieste esistenti prima che l'istanza diventi effettivamente inattiva. Ai client viene notificata la modifica dell'endpoint da parte di una funzione di callback al momento dell'avvio del ritardo, in modo che possano risolvere nuovamente l'endpoint ed evitare di inviare nuove richieste all'istanza che sta per essere interrotta. Queste richieste possono essere originate da client che usano il [proxy inverso](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) o usando le API di risoluzione degli endpoint di servizio con il modello di notifica ([ServiceNotificationFilterDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicenotificationfilterdescription)) per l'aggiornamento degli endpoint.

### <a name="service-configuration"></a>Configurazione del servizio

Esistono diversi modi per configurare il ritardo sul lato del servizio.

 * **Quando si crea un nuovo servizio**, specificare `-InstanceCloseDelayDuration` :

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>
    ```

 * **Quando si definisce il servizio nella sezione impostazioni predefinite del manifesto dell'applicazione**, assegnare la `InstanceCloseDelayDurationSeconds` proprietà:

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **Quando si aggiorna un servizio esistente**, specificare `-InstanceCloseDelayDuration` :

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

 * **Quando si crea o si aggiorna un servizio esistente tramite il modello ARM**, specificare il `InstanceCloseDelayDuration` valore (versione API minima supportata: 2019-11-01-Preview):

    ```ARM template to define InstanceCloseDelayDuration of 30seconds
    {
      "apiVersion": "2019-11-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications/services",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
      "location": "[variables('clusterLocation')]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
      ],
      "properties": {
        "provisioningState": "Default",
        "serviceKind": "Stateless",
        "serviceTypeName": "[parameters('serviceTypeName')]",
        "instanceCount": "-1",
        "partitionDescription": {
          "partitionScheme": "Singleton"
        },
        "serviceLoadMetrics": [],
        "servicePlacementPolicies": [],
        "defaultMoveCost": "",
        "instanceCloseDelayDuration": "00:00:30.0"
      }
    }
    ```

### <a name="client-configuration"></a>Configurazione del client

Per ricevere una notifica quando un endpoint è stato modificato, i client devono registrare un callback, vedere [ServiceNotificationFilterDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
La notifica delle modifiche indica che gli endpoint sono stati modificati, che il client deve risolvere di nuovo gli endpoint e non usare gli endpoint che non sono più annunciati, perché saranno presto disponibili.

### <a name="optional-upgrade-overrides"></a>Sostituzioni di aggiornamento facoltative

Oltre a impostare le durate di ritardo predefinite per ogni servizio, è anche possibile eseguire l'override del ritardo durante l'aggiornamento dell'applicazione o del cluster usando la stessa `InstanceCloseDelayDurationSec` opzione ():

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

La durata del ritardo sottoposta a override si applica solo all'istanza di aggiornamento richiamata e in caso contrario non modifica le singole configurazioni di ritardo del servizio. Ad esempio, è possibile usare questo valore per specificare un ritardo di per `0` ignorare eventuali ritardi di aggiornamento preconfigurati.

> [!NOTE]
> * Le impostazioni per lo svuotamento delle richieste non saranno in grado di impedire al servizio di bilanciamento del carico di Azure di inviare nuove richieste agli endpoint che sono in fase di svuotamento.
> * Un meccanismo di risoluzione basato su reclamo non comporterà lo svuotamento normale delle richieste, perché attiva una risoluzione del servizio dopo un errore. Come descritto in precedenza, questo dovrebbe essere migliorato per sottoscrivere le notifiche di modifica dell'endpoint usando [ServiceNotificationFilterDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
> * Le impostazioni non vengono rispettate quando l'aggiornamento è insufficiente, ad esempio Quando le repliche non verranno ridotte durante l'aggiornamento.
>
>

> [!NOTE]
> Questa funzionalità può essere configurata nei servizi esistenti usando il cmdlet Update-ServiceFabricService o il modello ARM come indicato in precedenza, quando la versione del codice del cluster è 7.1.XXX o successiva.
>
>

## <a name="manual-upgrade-mode"></a>Modalità di aggiornamento manuale

> [!NOTE]
> Per tutti gli aggiornamenti di Service Fabric è consigliabile usare la modalità di aggiornamento *Monitored*.
> La modalità di aggiornamento*UnmonitoredManual* deve essere presa in considerazione solo per gli aggiornamenti non riusciti e sospesi. 
>
>

In modalità *Monitored* Service Fabric applica criteri specifici per assicurare l'integrità dell'applicazione durante l'avanzamento dell'aggiornamento. Se i criteri di integrità non vengono rispettati, l'aggiornamento viene sospeso oppure viene eseguito il rollback automatico dell'operazione, a seconda dell'opzione specificata in *FailureAction*.

In modalità *UnmonitoredManual* l'amministratore dell'applicazione ha il controllo completo sull'avanzamento dell'aggiornamento. Questa modalità è utile quando si applicano criteri di valutazione dell'integrità personalizzati o quando si eseguono aggiornamenti non convenzionali per escludere completamente il monitoraggio dello stato (ad esempio, l'applicazione ha già perso dati). Un aggiornamento in esecuzione in questa modalità viene sospeso automaticamente al termine di ogni UD e deve essere ripreso in modo esplicito usando [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). Quando un aggiornamento sospeso è pronto per essere ripreso dall'utente, lo stato corrispondente sarà *RollforwardPending* (vedere [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Infine, la modalità *UnmonitoredAuto* è utile per l'esecuzione di iterazioni di aggiornamento rapide durante lo sviluppo o il test del servizio, poiché non è richiesto alcun input da parte dell'utente e non viene valutato alcun criterio di integrità dell'applicazione.

## <a name="upgrade-with-a-diff-package"></a>Aggiornare con un pacchetto Diff

Anziché con il provisioning di un pacchetto dell'applicazione completo, gli aggiornamenti possono essere eseguiti anche con il provisioning di pacchetti diff che contengono solo i pacchetti di codice/configurazione/dati aggiornati insieme al manifesto completo dell'applicazione e ai manifesti completi del servizio. I pacchetti dell'applicazione completi sono necessari solo per l'installazione iniziale di un'applicazione nel cluster. Gli aggiornamenti successivi possono essere eseguiti da pacchetti dell'applicazione completi o da pacchetti diff.  

Qualsiasi riferimento nel manifesto dell'applicazione o nei manifesti del servizio di un pacchetto diff che non è presente nel pacchetto dell'applicazione viene automaticamente sostituito con la versione di cui è stato eseguito il provisioning.

Di seguito sono indicati gli scenari adatti per l'uso di un pacchetto diff:

* Quando si ha un pacchetto dell'applicazione di grandi dimensioni che fa riferimento a più file manifesto del servizio e/o a più pacchetti di codice, configurazione o dati.
* Quando si ha un sistema di distribuzione che genera il layout della build direttamente dal processo di compilazione dell'applicazione. In questo caso, anche se il codice è rimasto invariato, i nuovi assembly compilati avranno un checksum diverso. L'uso del pacchetto applicazione completo richiederebbe l'aggiornamento della versione in tutti i pacchetti di codice. Usando un pacchetto Diff invece è necessario fornire soltanto i file che sono stati modificati e i file manifesto in cui è cambiata la versione.

Quando si aggiorna un'applicazione tramite Visual Studio, viene pubblicato un pacchetto diff automaticamente. Per creare un pacchetto diff manualmente, è necessario aggiornare il manifesto dell'applicazione e i manifesti del servizio, ma solo i pacchetti modificati devono essere inclusi nel pacchetto finale dell'applicazione.

Si supponga, ad esempio, di iniziare con la seguente applicazione (vengono specificati i numeri di versione per facilitare la comprensione):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

A questo punto, si supponga di voler aggiornare solo il pacchetto di codice di service1 usando un pacchetto diff. L'applicazione aggiornata presenta le modifiche di versione seguenti:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

In questo caso, il manifesto dell'applicazione viene aggiornato alla versione 2.0.0 e il manifesto del servizio per service1 viene aggiornato in modo da riflettere l'aggiornamento del pacchetto di codice. La cartella per il pacchetto dell'applicazione sarebbe simile alla seguente:

```text
app1/
  service1/
    code/
```

In altre parole, si crea un pacchetto completo dell'applicazione nel modo consueto e quindi si rimuovono le cartelle dei pacchetti di codice/configurazione/dati delle quali non è stata modificata la versione.

## <a name="upgrade-application-parameters-independently-of-version"></a>Aggiornare i parametri dell'applicazione indipendentemente dalla versione

In alcuni casi è consigliabile modificare i parametri di un'applicazione Service Fabric senza modificare la versione del manifesto. Questa operazione può essere eseguita comodamente usando il flag **-ApplicationParameter** con il cmdlet **Start-ServiceFabricApplicationUpgrade** di Azure Service Fabric PowerShell. Si supponga che un'applicazione Service Fabric con le proprietà seguenti:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

A questo punto, aggiornare l'applicazione usando il cmdlet **Start-ServiceFabricApplicationUpgrade** . Questo esempio illustra un aggiornamento monitorato, ma è anche possibile usare un aggiornamento non monitorato. Per una descrizione completa dei flag accettati da questo cmdlet, vedere le informazioni di [riferimento sui moduli di Azure Service Fabric PowerShell](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters)

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

Dopo l'aggiornamento, verificare che l'applicazione disponga dei parametri aggiornati e della stessa versione:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>Eseguire il rollback degli aggiornamenti dell'applicazione

Mentre il roll forward degli aggiornamenti può essere eseguito in tre modalità (*Monitored*, *UnmonitoredAuto* o *UnmonitoredManual*), il rollback può essere eseguito solo in modalità *UnmonitoredAuto* o *UnmonitoredManual*. Il rollback in modalità *UnmonitoredAuto* funziona esattamente come il roll forward, tranne per il fatto che il valore predefinito di *UpgradeReplicaSetCheckTimeout* è diverso. Vedere [Parametri di aggiornamento di un'applicazione](service-fabric-application-upgrade-parameters.md). Il rollback in modalità *UnmonitoredManual* funziona esattamente come il roll forward. Il rollback viene sospeso automaticamente al termine di ogni UD e, per continuare, deve essere ripreso in modo esplicito usando [ Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps).

Le operazioni di rollback possono essere attivate automaticamente quando non vengono rispettati i criteri di integrità di un aggiornamento in modalità *Monitored* con *FailureAction* impostato su *Rollback* (vedere [Parametri di aggiornamento di un'applicazione](service-fabric-application-upgrade-parameters.md)) oppure in modo esplicito usando [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Durante il rollback, il valore di *UpgradeReplicaSetCheckTimeout* e la modalità possono essere comunque modificati in qualsiasi momento usando [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Passaggi successivi
[Esercitazione sull'aggiornamento di un'applicazione di Service Fabric tramite Visual Studio](service-fabric-application-upgrade-tutorial.md) descrive la procedura di aggiornamento di un'applicazione con Visual Studio.

L' [aggiornamento dell'applicazione tramite PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) illustra l'aggiornamento di un'applicazione tramite PowerShell.

Controllare il modo in cui l'applicazione viene aggiornata usando i [parametri di aggiornamento](service-fabric-application-upgrade-parameters.md).

Rendere compatibili gli aggiornamenti dell'applicazione imparando a usare la [serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md).

Per risolvere i problemi comuni negli aggiornamenti dell'applicazione, fare riferimento ai passaggi descritti in [risoluzione dei problemi relativi agli aggiornamenti dell'applicazione](service-fabric-application-upgrade-troubleshooting.md).
