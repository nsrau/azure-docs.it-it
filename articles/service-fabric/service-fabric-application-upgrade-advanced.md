---
title: Argomenti avanzati sull'aggiornamento delle applicazioni
description: Questo articolo illustra alcuni degli argomenti avanzati relativi all'aggiornamento di un'applicazione di Service Fabric.
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: a12d2ec55bda95c1c61d4a73c76f4a777f4237f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414492"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Aggiornamento dell'applicazione Service Fabric: argomenti avanzatiService Fabric application upgrade: Advanced topics

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>Aggiungere o rimuovere tipi di servizio durante l'aggiornamento di un'applicazioneAdd or remove service types during an application upgrade

Se si aggiunge un nuovo tipo di servizio a un'applicazione pubblicata nel corso di un aggiornamento, il nuovo tipo di servizio viene aggiunto all'applicazione distribuita. Tale aggiornamento non influisce sulle istanze del servizio che facevano già parte dell'applicazione, ma è necessario creare un'istanza del tipo di servizio che è stato aggiunto per consentire l'attivazione del nuovo tipo di servizio (vedere [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Analogamente, i tipi di servizio possono essere rimossi da un'applicazione durante un aggiornamento. Tuttavia, prima di procedere con l'aggiornamento è necessario rimuovere tutte le istanze del tipo di servizio da rimuovere (vedere [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime"></a>Evitare l'eliminazione delle connessioni durante i tempi di inattività pianificati del servizio senza statoAvoid connection drops during stateless service planned downtime

Per i tempi di inattività pianificati dell'istanza senza stato, ad esempio l'aggiornamento dell'applicazione/cluster o la disattivazione del nodo, le connessioni possono essere eliminate a causa della rimozione dell'endpoint esposto dopo l'arresto dell'istanza, con conseguente chiusura forzata delle connessioni.

Per evitare questo problema, configurare la funzionalità *RequestDrain* (anteprima) aggiungendo una durata del ritardo di *chiusura dell'istanza* nella configurazione del servizio per consentire lo svuotamento durante la ricezione di richieste da altri servizi all'interno del cluster e si usano il proxy inverso o si usa l'API di risoluzione con il modello di notifica per l'aggiornamento degli endpoint. In questo modo si garantisce che l'endpoint annunciato dall'istanza senza stato venga rimosso *prima* dell'inizio del ritardo prima della chiusura dell'istanza. Questo ritardo consente alle richieste esistenti di eseguire correttamente prima che l'istanza si arresti effettivamente. I client ricevono una notifica della modifica dell'endpoint da una funzione di callback al momento dell'avvio del ritardo, in modo che possano risolvere nuovamente l'endpoint ed evitare di inviare nuove richieste all'istanza che sta inesecuzione.

### <a name="service-configuration"></a>Configurazione del servizio

Esistono diversi modi per configurare il ritardo sul lato servizio.

 * **Quando si crea un nuovo servizio,** specificare un `-InstanceCloseDelayDuration`:

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>`
    ```

 * **Durante la definizione del servizio nella sezione relativa alle impostazioni predefinite del manifesto dell'applicazione,** assegnare la `InstanceCloseDelayDurationSeconds` proprietà:

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **Quando si aggiorna un servizio esistente,** specificare un `-InstanceCloseDelayDuration`:

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

### <a name="client-configuration"></a>Configurazione del client

Per ricevere una notifica quando un endpoint è stato modificato, i client devono registrare un callback, vedere [ServiceNotificationFilterDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
La notifica di modifica è un'indicazione che gli endpoint sono stati modificati, il client deve risolvere nuovamente gli endpoint e non usare gli endpoint che non vengono più annunciati, in quanto andranno inattivo a breve.

### <a name="optional-upgrade-overrides"></a>Sostituzioni di aggiornamento facoltativeOptional upgrade override override overrides

Oltre a impostare le durate di ritardo predefinite per ogni servizio,`InstanceCloseDelayDurationSec`è anche possibile ignorare il ritardo durante l'aggiornamento dell'applicazione/cluster utilizzando la stessa opzione ( ):

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

La durata del ritardo si applica solo all'istanza di aggiornamento richiamata e non modifica in caso contrario le singole configurazioni di ritardo del servizio. Ad esempio, è possibile utilizzare questa `0` opzione per specificare un ritardo di per ignorare eventuali ritardi di aggiornamento preconfigurati.

> [!NOTE]
> L'impostazione per lo svuotamento delle richieste non viene rispettata per le richieste dal servizio di bilanciamento del carico di Azure.The setting to drain requests is not honored for requests from Azure Load balancer. L'impostazione non viene rispettata se il servizio chiamante utilizza la risoluzione basata su reclami.
>
>

> [!NOTE]
> Questa funzionalità può essere configurata nei servizi esistenti utilizzando il cmdlet Update-ServiceFabricService come indicato in precedenza, quando la versione del codice del cluster è 7.1.XXX o superiore.
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

## <a name="upgrade-application-parameters-independently-of-version"></a>Aggiornare i parametri dell'applicazione indipendentemente dalla versioneUpgrade application parameters independently of version

In alcuni modi, è opportuno modificare i parametri di un'applicazione Di Service Fabric senza modificare la versione del manifesto. Questa operazione può essere eseguita in modo pratico usando il flag -ApplicationParameter con il cmdlet **PowerShell Start-ServiceFabricApplicationUpgrade** di Azure Service Fabric.This can be done conveniently by using the **-ApplicationParameter** flag with the Start-ServiceFabricApplicationUpgrade Azure Service Fabric PowerShell cmdlet. Si supponga che un'applicazione Service Fabric con le proprietà seguenti:Assume a Service Fabric application with the following properties:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

A questo punto, aggiornare l'applicazione utilizzando il cmdlet **Start-ServiceFabricApplicationUpgrade** . In questo esempio viene illustrato un aggiornamento monitorato, ma è possibile utilizzare anche un aggiornamento non monitorato. Per visualizzare una descrizione completa dei flag accettati da questo cmdlet, vedere informazioni di riferimento sul [modulo PowerShell](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters) di Azure Service Fabric

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

## <a name="roll-back-application-upgrades"></a>Eseguire il rollback degli aggiornamenti delle applicazioni

Mentre il roll forward degli aggiornamenti può essere eseguito in tre modalità (*Monitored*, *UnmonitoredAuto* o *UnmonitoredManual*), il rollback può essere eseguito solo in modalità *UnmonitoredAuto* o *UnmonitoredManual*. Il rollback in modalità *UnmonitoredAuto* funziona esattamente come il roll forward, tranne per il fatto che il valore predefinito di *UpgradeReplicaSetCheckTimeout* è diverso. Vedere [Parametri di aggiornamento di un'applicazione](service-fabric-application-upgrade-parameters.md). Il rollback in modalità *UnmonitoredManual* funziona esattamente come il roll forward. Il rollback viene sospeso automaticamente al termine di ogni UD e, per continuare, deve essere ripreso in modo esplicito usando [ Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps).

Le operazioni di rollback possono essere attivate automaticamente quando non vengono rispettati i criteri di integrità di un aggiornamento in modalità *Monitored* con *FailureAction* impostato su *Rollback* (vedere [Parametri di aggiornamento di un'applicazione](service-fabric-application-upgrade-parameters.md)) oppure in modo esplicito usando [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Durante il rollback, il valore di *UpgradeReplicaSetCheckTimeout* e la modalità possono essere comunque modificati in qualsiasi momento usando [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Passaggi successivi
[Esercitazione sull'aggiornamento di un'applicazione di Service Fabric tramite Visual Studio](service-fabric-application-upgrade-tutorial.md) descrive la procedura di aggiornamento di un'applicazione con Visual Studio.

[L'aggiornamento dell'applicazione tramite Powershell](service-fabric-application-upgrade-tutorial-powershell.md) illustra l'aggiornamento di un'applicazione tramite PowerShell.Upgrading your Application Using Powershell walks you through an application upgrade using PowerShell.

Controllare la modalità di aggiornamento dell'applicazione utilizzando [i parametri](service-fabric-application-upgrade-parameters.md)di aggiornamento .

Rendere compatibili gli aggiornamenti dell'applicazione imparando a utilizzare [la serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md).

Risolvere i problemi comuni negli aggiornamenti delle applicazioni facendo riferimento alla procedura descritta in [Risoluzione dei problemi](service-fabric-application-upgrade-troubleshooting.md)relativi agli aggiornamenti delle applicazioni .
