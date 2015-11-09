<properties
   pageTitle="Risoluzione dei problemi relativi agli aggiornamenti di applicazioni di Service Fabric"
   description="Questo articolo descrive alcuni problemi comuni relativi all'aggiornamento di un'applicazione di Service Fabric e come risolverli."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2015"
   ms.author="subramar"/>

# Risolvere i problemi relativi agli aggiornamenti delle applicazioni

Questo articolo descrive alcuni dei problemi comuni relativi all'aggiornamento di un'applicazione di Service Fabric e come risolverli.

## Risolvere i problemi relativi all'aggiornamento di un'applicazione non riuscito

Se un aggiornamento ha esito negativo, l'output del comando **Get-ServiceFabricApplicationUpgrade** conterrà alcune informazioni aggiuntive per il debug dell'errore. Queste informazioni possono essere usate per le operazioni seguenti:

1. Identificare il tipo di errore
2. Identificare la causa dell'errore
3. Isolare il componente o i componenti con errore per altre indagini

Queste informazioni saranno disponibili nel momento in cui Service Fabric rileva l'errore, indipendentemente dal fatto che la proprietà **FailureAction** sia impostata per il rollback o per la sospensione dell'aggiornamento.

### Identificare il tipo di errore

Nell'output di **Get-ServiceFabricApplicationUpgrade** l'elemento **FailureTimestampUtc** identifica il timestamp (in UTC) in corrispondenza del quale Service Fabric ha rilevato un errore di aggiornamento ed è stata attivata la proprietà **FailureAction**. **FailureReason** identifica una delle tre possibili cause generali dell'errore:

1. UpgradeDomainTimeout - Indica che uno specifico dominio di aggiornamento ha impiegato troppo tempo per il completamento e il valore di **UpgradeDomainTimeout** è scaduto.
2. OverallUpgradeTimeout - Indica che l'aggiornamento globale ha impiegato troppo tempo per il completamento e il valore di **UpgradeTimeout** è scaduto.
3. HealthCheck - Indica che dopo l'aggiornamento di un dominio di aggiornamento l'applicazione è rimasta non integra secondo i criteri di integrità specificati e il valore di **HealthCheckRetryTimeout** è scaduto.

Queste voci verranno visualizzate nell'output se l'aggiornamento ha esito negativo e viene avviato il rollback. A seconda del tipo di errore verranno visualizzate informazioni aggiuntive.

### Analizzare i timeout di aggiornamento

Gli errori di timeout di aggiornamento si verificano più comunemente a causa di problemi di disponibilità dei servizi. L'output seguente è tipico di aggiornamenti in cui le repliche o le istanze dei servizi non si avviano nella nuova versione di codice. Il campo **UpgradeDomainProgressAtFailure** acquisisce uno snapshot di eventuali processi di aggiornamento in sospeso al momento dell'errore.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
~~~

In questo esempio è possibile osservare che l'aggiornamento ha avuto esito negativo nel dominio di aggiornamento *MYUD1* e che due partizioni (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* e *4b43f4d8-b26b-424e-9307-7a7a62e79750*) si sono bloccate e non sono state in grado di posizionare le repliche primarie (*WaitForPrimaryPlacement*) nei nodi di destinazione *Node1* e *Node4*. È possibile usare il comando **Get-ServiceFabricNode** per verificare che questi due nodi siano contenuti nel dominio di aggiornamento *MYUD1*. Il valore di *UpgradePhase* è *PostUpgradeSafetyCheck*, a indicare che questi controlli di sicurezza vengono eseguiti al termine dell'aggiornamento di tutti i nodi del dominio di aggiornamento. La combinazione di tutte queste informazioni indica un possibile problema con la nuova versione del codice dell'applicazione. I problemi più comuni sono rappresentati da errori di servizi nell'apertura o nell'innalzamento di livello a percorsi di codice primari.

Se il valore di *UpgradePhase* è *PreUpgradeSafetyCheck*, si sono verificati problemi durante la preparazione del dominio di aggiornamento prima dell'effettiva esecuzione dell'aggiornamento. I problemi più comuni in questo caso sono errori di servizi nella chiusura o nell'abbassamento di livello da percorsi di codice primari.

Il valore corrente di **UpgradeState** è *RollingBackCompleted*, pertanto l'aggiornamento originale deve essere stato eseguito con la proprietà **FailureAction** impostata per il rollback ed è stato eseguito automaticamente il rollback dell'aggiornamento al momento dell'errore. Se l'aggiornamento originale fosse stato eseguito con la proprietà **FailureAction** impostata per l'azione manuale, si sarebbe attivato lo stato di sospensione dell'aggiornamento per consentire il debug attivo dell'applicazione.

### Analizzare gli errori di controllo dell'integrità

Gli errori di controllo dell'integrità possono essere attivati da molti altri problemi che possono verificarsi dopo l'aggiornamento di tutti i nodi di un dominio di aggiornamento e dopo aver superato tutti i controlli di sicurezza. L'output seguente è tipico di un errore di aggiornamento causato da controlli di integrità non riusciti. Il campo **UnhealthyEvaluations** acquisisce uno snapshot di tutti i controlli di integrità non riusciti al momento dell'errore di aggiornamento in base ai [criteri di integrità](service-fabric-health-introduction.md) specificati dall'utente.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
~~~

Per analizzare gli errori dei controlli di integrità è prima necessario conoscere il modello di integrità di Service Fabric, ma anche senza questa conoscenza approfondita è possibile osservare che due servizi risultano non integri: *fabric:/DemoApp/Svc3* e *fabric:/DemoApp/Svc2* insieme ai report di errore di integrità (in questo caso "InjectedFault"). In questo esempio risultano non integri 2 servizi su 4, al di sotto dell'obiettivo predefinito di non integrità pari a 0% (*MaxPercentUnhealthyServices*).

L'aggiornamento è stato sospeso contestualmente all'errore mediante l'impostazione della proprietà **FailureAction** sull'azione manuale all'avvio dell'aggiornamento. È possibile quindi analizzare il sistema attivo nello stato di errore prima di intraprendere altre azioni.

### Eseguire il ripristino da un aggiornamento sospeso

Con una proprietà **FailureAction** impostata per il rollback, non è necessario alcun ripristino poiché l'aggiornamento eseguirà automaticamente il rollback in caso di errore. Con una proprietà **FailureAction** impostata per l'azione manuale invece sono disponibili diverse opzioni di ripristino:

1. Attivare manualmente un rollback
2. Procedere manualmente con il resto dell'aggiornamento
3. Riprendere l'aggiornamento monitorato

È possibile usare il comando **Start-ServiceFabricApplicationRollback** in qualsiasi momento per avviare il rollback dell'applicazione. Se il comando ha esito positivo, la richiesta di rollback viene registrata nel sistema e il rollback si avvia poco dopo.

È possibile usare il comando **Resume-ServiceFabricApplicationUpgrade** per procedere manualmente con il resto dell'aggiornamento, un dominio di aggiornamento per volta. In questa modalità il sistema eseguirà soltanto i controlli di sicurezza. Non verrà eseguito alcun controllo di integrità. Questo comando può essere usato soltanto se l'elemento *UpgradeState* è impostato su *RollingForwardPending*, che indica che l'aggiornamento del dominio di aggiornamento corrente è terminato e non è ancora iniziato l'aggiornamento del dominio di aggiornamento successivo (in sospeso).

Il comando **Update-ServiceFabricApplicationUpgrade** può essere usato per riprendere l'aggiornamento in modalità monitorata con l'esecuzione sia dei controlli di sicurezza sia dei controlli di integrità.

~~~
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
~~~

L'aggiornamento continuerà dal dominio di aggiornamento in corrispondenza del quale è stato sospeso e userà gli stessi parametri di aggiornamento e criteri di integrità usati precedentemente. Se necessario, i parametri di aggiornamento e i criteri di integrità mostrati nell'output precedente possono essere modificati nello stesso comando con cui si riprende l'aggiornamento. In questo esempio l'aggiornamento viene ripreso in modalità monitorata (Monitored) lasciando tutti gli altri parametri invariati e usando quindi gli stessi parametri e criteri di integrità usati precedentemente.

## Risoluzione di altri problemi

### Service Fabric non segue i criteri di integrità specificati

Possibile causa 1:

Service Fabric converte tutte le percentuali in numeri effettivi di entità (ad esempio repliche, partizioni e servizi) per la valutazione dell'integrità ed esegue l'arrotondamento sempre al numero intero di entità più prossimo. Se ad esempio il valore massimo di _MaxPercentUnhealthyReplicasPerPartition_ è 21% e sono presenti cinque repliche, Service Fabric accetterà la non integrità di un massimo di due repliche (ovvero `Math.Ceiling (5*0.21)`) durante la valutazione dell'integrità della partizione. I criteri di integrità devono essere impostati in modo da tenere conto di questo comportamento.

Possibile causa 2:

I criteri di integrità sono specificati in termini di percentuali dei servizi totali e non di specifiche istanze di servizi. Si consideri ad esempio uno scenario in cui prima di un aggiornamento un'applicazione dispone di quattro istanze di servizi A, B, C e D e che il servizio D non sia integro, senza produrre però un impatto significativo sull'applicazione. Si vuole ignorare il servizio D non integro e impostare il parametro *MaxPercentUnhealthyServices* su 25%, presupponendo che solo i servizi A, B e C debbano essere integri. Durante l'aggiornamento però D diventa integro mentre C diventa non integro. In questo caso l'aggiornamento verrà comunque completato, dal momento che soltanto il 25% dei servizi non è integro, con la possibilità di errori imprevisti causati dalla non integrità di C anziché D. In questa situazione D dovrebbe essere modellato come tipo di servizio diverso rispetto ad A, B e C. Poiché i criteri di integrità possono essere specificati per tipo di servizio, in questo modo sarebbe possibile applicare soglie di percentuali di non integrità diverse per servizi diversi in base ai ruoli dei servizi nell'applicazione.

### Non sono stati specificati criteri di integrità per l'aggiornamento dell'applicazione, ma l'aggiornamento continua ad avere esito negativo per alcuni timeout che però non sono stati specificati

Se non vengono specificati criteri di integrità per la richiesta di aggiornamento, tali criteri vengono derivati dal file *ApplicationManifest.xml* della versione dell'applicazione corrente. Se ad esempio si aggiorna l'applicazione X dalla versione 1 alla versione 2, vengono usati i criteri di integrità dell'applicazione specificati per la versione 1. Se devono essere usati criteri di integrità diversi per l'aggiornamento, specificarli nella chiamata API di aggiornamento dell'applicazione. Si noti che i criteri specificati nella chiamata API si applicano soltanto per la durata dell'aggiornamento. Dopo che è stato completato l'aggiornamento, vengono usati i criteri specificati nel file *ApplicationManifest.xml*.

### Specifica di timeout non corretti

Se vengono impostati valori di timeout non coerenti, ad esempio un valore di *UpgradeTimeout* inferiore al valore di *UpgradeDomainTimeout*, viene restituito un errore. Questo problema si verifica anche se il valore di *UpgradeDomainTimeout* è inferiore alla somma di *HealthCheckWaitDuration* e *HealthCheckRetryTimeout* o se il valore di *UpgradeDomainTimeout* è inferiore alla somma di *HealthCheckWaitDuration* e *HealthCheckStableDuration*.

### Gli aggiornamenti richiedono troppo tempo

Il tempo richiesto per il completamento di un aggiornamento dipende dai diversi controlli di integrità e dai valori di timeout specificati, che a loro volta dipendono dal tempo impiegato per l'aggiornamento dell'applicazione, inclusi la copia del pacchetto, la distribuzione e la stabilizzazione. Un uso di timeout eccessivamente rigidi può comportare un numero elevato di aggiornamenti non riusciti. È consigliabile pertanto iniziare in modo contenuto con valori di timeout più lunghi.

Segue un rapido ripasso sull'interazione dei timeout con i tempi di aggiornamento:

L'aggiornamento di un dominio di aggiornamento non può completarsi in tempi più rapidi della somma dei valori di *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Un errore di aggiornamento non può verificarsi in tempi più rapidi della somma dei valori di *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

Il tempo di aggiornamento di un dominio di aggiornamento è limitato da *UpgradeDomainTimeout*. Se i valori di *HealthCheckRetryTimeout* e *HealthCheckStableDuration* sono entrambi diversi da zero e l'integrità dell'applicazione continua a oscillare, l'aggiornamento alla fine andrà in timeout in *UpgradeDomainTimeout*. *UpgradeDomainTimeout* inizia il conto alla rovescia dopo l'avvio dell'aggiornamento del dominio di aggiornamento corrente.

## Passaggi successivi

[Esercitazione sull'aggiornamento](service-fabric-application-upgrade-tutorial.md)

[Parametri di aggiornamento](service-fabric-application-upgrade-parameters.md)

[Argomenti avanzati](service-fabric-application-upgrade-advanced.md)

[Serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md)
 

<!---HONumber=Nov15_HO1-->