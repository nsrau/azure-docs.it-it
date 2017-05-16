---
title: Risoluzione dei problemi di aggiornamento delle applicazioni | Documentazione Microsoft
description: Questo articolo descrive alcuni problemi comuni relativi all&quot;aggiornamento di un&quot;applicazione di Service Fabric e come risolverli.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 19ad152e-ec50-4327-9f19-065c875c003c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/02/2017
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 7fc832ff23f5ad652df3cb9c689180c92952ba8e
ms.lasthandoff: 04/26/2017


---
# <a name="troubleshoot-application-upgrades"></a>Risolvere i problemi relativi agli aggiornamenti delle applicazioni
Questo articolo descrive alcuni dei problemi comuni relativi all'aggiornamento di un'applicazione di Azure Service Fabric e come risolverli.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Risolvere i problemi relativi all'aggiornamento di un'applicazione non riuscito
Se un aggiornamento ha esito negativo, l'output del comando **Get-ServiceFabricApplicationUpgrade** contiene alcune informazioni aggiuntive per il debug dell'errore.  L'elenco seguente specifica come si possono usare le informazioni aggiuntive:

1. Identificare il tipo di errore.
2. Identificare la causa dell'errore.
3. Isolare uno o più componenti che hanno causato l'errore per altre indagini.

Queste informazioni sono disponibili nel momento in cui Service Fabric rileva l'errore, indipendentemente dal fatto che la proprietà **FailureAction** sia impostata per il rollback o la sospensione dell'aggiornamento.

### <a name="identify-the-failure-type"></a>Identificare il tipo di errore
Nell'output di **Get-ServiceFabricApplicationUpgrade** l'elemento **FailureTimestampUtc** identifica il timestamp (in UTC) in corrispondenza del quale Service Fabric ha rilevato un errore di aggiornamento ed è stata attivata la proprietà **FailureAction**. **FailureReason** identifica una delle tre possibili cause generali dell'errore:

1. UpgradeDomainTimeout: indica che uno specifico dominio di aggiornamento ha impiegato troppo tempo per il completamento e il valore di **UpgradeDomainTimeout** è scaduto.
2. OverallUpgradeTimeout: indica che l'aggiornamento globale ha impiegato troppo tempo per il completamento e il valore di **UpgradeTimeout** è scaduto.
3. HealthCheck: indica che, dopo l'aggiornamento di un dominio di aggiornamento, l'applicazione è rimasta in uno stato non integro secondo i criteri di integrità specificati e il valore di **HealthCheckRetryTimeout** è scaduto.

Queste voci sono visualizzate nell'output se l'aggiornamento ha esito negativo e viene avviato il rollback. A seconda del tipo di errore sono visualizzate informazioni aggiuntive.

### <a name="investigate-upgrade-timeouts"></a>Analizzare i timeout di aggiornamento
Gli errori di timeout di aggiornamento si verificano più comunemente a causa di problemi di disponibilità dei servizi. L'output che segue questo paragrafo è tipico di aggiornamenti in cui le repliche o le istanze dei servizi non si avviano nella nuova versione del codice. Il campo **UpgradeDomainProgressAtFailure** acquisisce uno snapshot di eventuali processi di aggiornamento in sospeso al momento dell'errore.

```
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
```

In questo esempio l'aggiornamento non è riuscito nel dominio di aggiornamento *MYUD1* e due partizioni (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* e *4b43f4d8-b26b-424e-9307-7a7a62e79750*) sono state bloccate. Le partizioni sono state bloccate perché il runtime non è stato in grado di posizionare le repliche primarie (*WaitForPrimaryPlacement*) nei nodi di destinazione *Node1* e *Node4*.

È possibile usare il comando **Get-ServiceFabricNode** per verificare che questi due nodi siano contenuti nel dominio di aggiornamento *MYUD1*. Il valore di *UpgradePhase* è *PostUpgradeSafetyCheck*, a indicare che questi controlli di sicurezza vengono eseguiti al termine dell'aggiornamento di tutti i nodi del dominio di aggiornamento. Tutte queste informazioni indicano un possibile problema con la nuova versione del codice dell'applicazione. I problemi più comuni sono rappresentati da errori di servizi nell'apertura o nell'innalzamento di livello a percorsi di codice primari.

Se il valore di *UpgradePhase* è *PreUpgradeSafetyCheck*, si sono verificati problemi durante la preparazione del dominio di aggiornamento prima dell'esecuzione. I problemi più comuni in questo caso sono errori di servizi nella chiusura o nell'abbassamento di livello da percorsi di codice primari.

Il valore corrente di **UpgradeState** è *RollingBackCompleted*, pertanto l'aggiornamento originale deve essere stato eseguito con la proprietà **FailureAction** impostata per il rollback ed è stato eseguito automaticamente il rollback dell'aggiornamento al momento dell'errore. Se l'aggiornamento originale fosse stato eseguito con la proprietà **FailureAction**impostata per l'azione manuale, si sarebbe attivato lo stato di sospensione dell'aggiornamento per consentire il debug attivo dell'applicazione.

### <a name="investigate-health-check-failures"></a>Analizzare gli errori di controllo dell'integrità
Gli errori di controllo dell'integrità possono essere attivati da vari problemi che possono verificarsi dopo l'aggiornamento di tutti i nodi di un dominio di aggiornamento e dopo aver superato tutti i controlli di sicurezza. L'output che segue questo paragrafo è tipico di un errore di aggiornamento causato da controlli di integrità non riusciti. Il campo **UnhealthyEvaluations** acquisisce uno snapshot dei controlli di integrità non riusciti al momento dell'aggiornamento in base ai [criteri di integrità](service-fabric-health-introduction.md)specificati dall'utente.

```
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
```

Per analizzare gli errori dei controlli di integrità è prima necessario conoscere il modello di integrità di Service Fabric. Anche senza questa conoscenza approfondita è comunque possibile osservare che due servizi risultano non integri: *fabric:/DemoApp/Svc3* e *fabric:/DemoApp/Svc2*, insieme ai report sugli errori di integrità, in questo caso "InjectedFault". In questo esempio risultano non integri 2 servizi su 4, quindi al di sotto dell'obiettivo predefinito di non integrità pari a 0% (*MaxPercentUnhealthyServices*).

L'aggiornamento è stato sospeso contestualmente all'errore grazie all'impostazione di **FailureAction** per l'azione manuale all'avvio dell'aggiornamento. Questa modalità consente di analizzare il sistema nello stato di errore prima di eseguire altre azioni.

### <a name="recover-from-a-suspended-upgrade"></a>Eseguire il ripristino da un aggiornamento sospeso
Con la proprietà **FailureAction**impostata per il rollback, non è necessario alcun ripristino in quanto l'aggiornamento esegue automaticamente il rollback in caso di errore. Con una proprietà **FailureAction**impostata per l'azione manuale invece sono disponibili diverse opzioni di ripristino:

1.  attivare un rollback
2. Procedere manualmente con il resto dell'aggiornamento
3. Riprendere l'aggiornamento monitorato

È possibile usare il comando **Start-ServiceFabricApplicationRollback** in qualsiasi momento per avviare il rollback dell'applicazione. Se il comando ha esito positivo, la richiesta di rollback viene registrata nel sistema e il rollback si avvia poco dopo.

È possibile usare il comando **Resume-ServiceFabricApplicationUpgrade** per procedere manualmente con il resto dell'aggiornamento, un dominio di aggiornamento per volta. In questa modalità il sistema esegue solo i controlli di sicurezza. Non viene eseguito alcun controllo di integrità. Questo comando può essere usato soltanto se l'elemento *UpgradeState* è impostato su *RollingForwardPending*, a indicare che l'aggiornamento del dominio di aggiornamento corrente è terminato e non è ancora iniziato quello successivo (in sospeso).

Il comando **Update-ServiceFabricApplicationUpgrade** può essere usato per riprendere l'aggiornamento in modalità monitorata con l'esecuzione sia dei controlli di sicurezza sia dei controlli di integrità.

```
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
```

L'aggiornamento continua dal dominio di aggiornamento in corrispondenza del quale è stato sospeso e userà gli stessi parametri di aggiornamento e criteri di integrità usati precedentemente. Se necessario, i parametri di aggiornamento e i criteri di integrità mostrati nell'output precedente possono essere modificati nello stesso comando alla ripresa dell'aggiornamento. In questo esempio l'aggiornamento è stato ripreso in modalità monitorata con i parametri e i criteri di integrità invariati.

## <a name="further-troubleshooting"></a>Risoluzione di altri problemi
### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Service Fabric non segue i criteri di integrità specificati
Possibile causa 1:

Service Fabric converte tutte le percentuali in numeri effettivi di entità (ad esempio repliche, partizioni e servizi) per la valutazione dell'integrità ed esegue sempre l'arrotondamento alle entità intere. Se ad esempio il valore massimo di *MaxPercentUnhealthyReplicasPerPartition* è 21% e ci sono cinque repliche, Service Fabric accetta un massimo di due repliche non integre, ovvero `Math.Ceiling (5*0.21)`. Perciò, i criteri di integrità devono essere impostati in modo da tenere conto di questo.

Possibile causa 2:

I criteri di integrità sono specificati in termini di percentuali dei servizi totali e non di specifiche istanze di servizi. Si consideri ad esempio uno scenario in cui, prima di un aggiornamento, un'applicazione dispone di quattro istanze di servizi, A, B, C e D, in cui il servizio D non è integro tuttavia non ha un impatto significativo sull'applicazione. Si vuole ignorare il servizio D non integro durante l'aggiornamento e impostare il parametro *MaxPercentUnhealthyServices* su 25%, presupponendo che solo i servizi A, B e C debbano essere integri.

Durante l'aggiornamento però D diventa integro mentre C diventa non integro. L'aggiornamento riesce comunque perché solo il 25% dei servizi non è integro. Tuttavia c'è la possibilità di errori imprevisti causati dalla non integrità di C invece di D. In questa situazione D dovrebbe essere modellato come tipo di servizio diverso rispetto ad A, B e C. Poiché i criteri di integrità sono specificati per tipo di servizio, è possibile applicare soglie di percentuali di non integrità diverse a servizi diversi. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Non sono stati specificati criteri di integrità per l'aggiornamento dell'applicazione, ma l'aggiornamento non riesce a causa di alcuni timeout che però non sono mai stati specificati.
Se i criteri di integrità non vengono forniti alla richiesta di aggiornamento, vengono rilevati dal file *ApplicationManifest.xml* della versione dell'applicazione corrente. Ad esempio, se si aggiorna l'applicazione X dalla versione 1.0 alla versione 2.0, vengono usati i criteri di integrità specificati per la versione 1.0. Se devono essere usati criteri di integrità diversi per l'aggiornamento, specificarli nella chiamata API di aggiornamento dell'applicazione. I criteri specificati nella chiamata API si applicano soltanto durante l'aggiornamento. Una volta completato l'aggiornamento, vengono usati i criteri specificati nel file *ApplicationManifest.xml* .

### <a name="incorrect-time-outs-are-specified"></a>Sono stati specificati timeout non corretti
Ci si potrebbe chiedere cosa succede quando i timeout sono impostati in modo incoerente. Ad esempio, può essere impostato un valore *UpgradeTimeout* inferiore al valore *UpgradeDomainTimeout*. viene restituito un errore. Si verificano errori se il valore di *UpgradeDomainTimeout* è inferiore alla somma di *HealthCheckWaitDuration* e *HealthCheckRetryTimeout* o se il valore di *UpgradeDomainTimeout* è inferiore alla somma di *HealthCheckWaitDuration* e *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Gli aggiornamenti richiedono troppo tempo
Il tempo per il completamento dell'aggiornamento dipende dai controlli di integrità e dai timeout specificati. I controlli di integrità e i timeout dipendono dal tempo necessario per copiare, distribuire e stabilizzare l'applicazione. L'uso di timeout eccessivamente rigidi può comportare un numero elevato di aggiornamenti non riusciti. È quindi consigliabile iniziare prudenzialmente con valori di timeout più lunghi.

Segue un rapido ripasso sull'interazione dei timeout con i tempi di aggiornamento:

Gli aggiornamenti di un dominio di aggiornamento non possono completarsi in tempi più rapidi della somma dei valori di *HealthCheckWaitDuration*  +  *HealthCheckStableDuration*.

Un errore di aggiornamento non può verificarsi in tempi più rapidi della somma dei valori di *HealthCheckWaitDuration*  +  *HealthCheckRetryTimeout*.

Il tempo di aggiornamento di un dominio di aggiornamento è limitato da *UpgradeDomainTimeout*.  Se i valori di *HealthCheckRetryTimeout* e *HealthCheckStableDuration* sono entrambi diversi da zero e l'integrità dell'applicazione continua a oscillare, si verifica il timeout dell'aggiornamento in *UpgradeDomainTimeout*. *UpgradeDomainTimeout* inizia il conto alla rovescia dopo l'avvio dell'aggiornamento del dominio di aggiornamento corrente.

## <a name="next-steps"></a>Passaggi successivi
[Esercitazione sull'aggiornamento di un'applicazione di Service Fabric tramite Visual Studio](service-fabric-application-upgrade-tutorial.md) descrive la procedura di aggiornamento di un'applicazione con Visual Studio.

[Aggiornamento di un'applicazione di Service Fabric mediante PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) descrive la procedura di aggiornamento di un'applicazione tramite PowerShell.

Controllare l’aggiornamento dell'applicazione tramite [Parametri di aggiornamento](service-fabric-application-upgrade-parameters.md).

Rendere compatibili gli aggiornamenti dell'applicazione imparando a usare [Serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md).

Per informazioni su come usare funzionalità avanzate durante l'aggiornamento dell'applicazione, vedere [Argomenti avanzati](service-fabric-application-upgrade-advanced.md).

