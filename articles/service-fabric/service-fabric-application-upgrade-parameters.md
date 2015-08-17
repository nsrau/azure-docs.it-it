
<properties
   pageTitle="Aggiornamento di un'applicazione di Service Fabric: parametri di aggiornamento"
   description="Questo articolo descrive i diversi parametri correlati all'aggiornamento di un'applicazione di Service Fabric."
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



# Parametri di aggiornamento di un'applicazione

Questo articolo descrive i diversi parametri che si applicano durante l'aggiornamento di un'applicazione di Service Fabric. I parametri includono il nome e la versione dell'applicazione e determinano i timeout e i controlli di integrità che vengono applicati durante l'aggiornamento, oltre a specificare i criteri da applicare in caso di esito negativo di un aggiornamento.


<br>

| Parametro | Descrizione |
| --- | --- |
| ApplicationName | Nome dell'applicazione che viene aggiornata. Esempi: fabric:/VisualObjects, fabric:/ClusterMonitor |
| TargetApplicationTypeVersion | Versione del tipo di applicazione a cui è destinato l'aggiornamento. |
| FailureAction | La proprietà FailureAction descrive l'azione che deve essere intrapresa da Service Fabric se l'aggiornamento ha esito negativo. È possibile eseguire il rollback dell'applicazione all'ultima versione precedente all'aggiornamento (Rollback) oppure arrestare l'aggiornamento dell'applicazione a livello del dominio di aggiornamento corrente e cambiare la modalità impostando l'aggiornamento manuale. I valori consentiti sono Rollback e Manual. |
| HealthCheckWaitDurationSec | Tempo di attesa (in secondi) dopo il completamento dell'aggiornamento nel dominio di aggiornamento prima che Service Fabric valuti l'integrità dell'applicazione. La durata può essere considerata anche come il tempo di esecuzione di un'applicazione prima che venga considerata integra. Se il controllo di integrità ha esito positivo, il processo di aggiornamento passa al dominio di aggiornamento successivo. Se invece il controllo di integrità ha esito negativo, Service Fabric attende un periodo di tempo (definito da UpgradeHealthCheckInterval) prima di riprovare a eseguire il controllo di integrità finché non viene raggiunto il valore definito da HealthCheckRetryTimeout. Il valore predefinito e consigliato è 0 secondi. |
| HealthCheckRetryTimeoutSec | Intervallo di tempo (in secondi) in cui Service Fabric continua a eseguire la valutazione dell'integrità prima di dichiarare l'aggiornamento come non riuscito. Il valore predefinito è 600 secondi. Il calcolo del tempo inizia dopo che viene raggiunto il valore definito da HealthCheckWaitDuration. Nell'ambito di questo intervallo di tempo definito da HealthCheckRetryTimeout, Service Fabric può eseguire più controlli di integrità dell'applicazione. Il valore predefinito è 10 minuti ed è consigliabile personalizzarlo in base all'applicazione. |
| HealthCheckStableDurationSec | Tempo di attesa (in secondi) per verificare che l'applicazione sia stabile prima di passare al dominio di aggiornamento successivo o di completare l'aggiornamento. Questo intervallo viene usato per evitare che immediatamente dopo il controllo di integrità vengono apportate modifiche all'integrità non rilevate. Il valore predefinito è 0 secondi ed è consigliabile personalizzarlo in base all'applicazione. |
| UpgradeDomainTimeoutSec | Tempo massimo (in secondi) per l'aggiornamento di un singolo dominio di aggiornamento. Se viene raggiunto questo timeout, l'aggiornamento si arresta ed esegue l'azione specificata da UpgradeFailureAction. Il valore predefinito è never (infinito) ed è consigliabile personalizzarlo in base all'applicazione. |
| UpgradeTimeout | Intervallo di timeout (in secondi) che si applica per l'intero aggiornamento. Se viene raggiunto questo timeout, l'aggiornamento si arresta e viene attivata l'azione specificata da UpgradeFailureAction. Il valore predefinito è never (infinito) ed è consigliabile personalizzarlo in base all'applicazione. |
| UpgradeHealthCheckInterval | Specificata nella sezione ClusterManager del _manifesto_ del _cluster_ (non specificata nel cmdlet di aggiornamento), questa impostazione indica la frequenza di controllo dello stato di integrità (il valore predefinito è 60 secondi). |






<br>
## Valutazione dell'integrità di un servizio durante l'aggiornamento dell'applicazione

<br> I criteri di valutazione dell'integrità sono facoltativi. Se all'avvio si un aggiornamento non vengono specificati i criteri di valutazione dell'integrità, Service Fabric usa i criteri di integrità dell'applicazione specificati nel file ApplicationManifest.xml dell'istanza dell'applicazione che viene aggiornata.


<br>

| Parametro | Descrizione |
| --- | --- |
| ConsiderWarningAsError | Il valore predefinito è False. Considerare gli eventi di avviso relativi all'integrità dell'applicazione come errori durante la valutazione dell'integrità dell'applicazione nel corso dell'aggiornamento. Per impostazione predefinita, Service Fabric non valuta eventi di avviso relativi all'integrità come errori, pertanto l'aggiornamento può continuare anche se vengono generati questi eventi. |
| MaxPercentUnhealthyDeployedApplications | Il valore predefinito e consigliato è 0. Specificare il numero massimo di applicazioni distribuite (vedere la [sezione relativa all'integrità](service-fabric-health-introduction.md)) che possono essere non integre prima che l'applicazione venga considerata non integra e l'aggiornamento abbia esito negativo. Questo è lo stato di integrità dell'applicazione inclusa nel pacchetto e contenuta nel nodo ed è utile per rilevare problemi immediati durante l'aggiornamento e la posizione in cui il pacchetto applicazione distribuito nel nodo ha stato non integro (arresto anomalo e così via). In un caso tipico, alle repliche dell'applicazione verrà applicato il bilanciamento del carico per l'altro nodo, in modo che l'applicazione risulti integra e l'aggiornamento possa continuare. Specificando uno stato di integrità MaxPercentUnhealthyDeployedApplications rigoroso, Service Fabric può rilevare rapidamente un problema con il pacchetto applicazione determinando un aggiornamento di errore immediato. |
| MaxPercentUnhealthyServices | Il valore predefinito e consigliato è 0. Specificare il numero massimo di servizi dell'istanza dell'applicazione che possono essere non integri prima che l'applicazione venga considerata non integra e l'aggiornamento abbia esito negativo. |
| MaxPercentUnhealthyPartitionsPerService | Il valore predefinito e consigliato è 0. Specificare il numero massimo di partizioni di un servizio che possono essere non integre prima che il servizio venga considerato non integro. |
| MaxPercentUnhealthyReplicasPerPartition | Il valore predefinito e consigliato è 0. Specificare il numero massimo di repliche nella partizione che possono essere non integre prima che la partizione venga considerata non integra. |
| UpgradeReplicaSetCheckTimeout | Servizio senza stato - In un singolo dominio di aggiornamento Service Fabric tenta di verificare che siano disponibili istanze aggiuntive del servizio. Se il numero di istanze di destinazione è maggiore di uno, Service Fabric attende che sia disponibile più di un'istanza fino al valore di timeout massimo (specificato dalla proprietà UpgradeReplicaSetCheckTimeout). Se il timeout scade, Service Fabric procede con l'aggiornamento indipendentemente dal numero di istanze del servizio. Se il numero di istanze di destinazione è pari a uno, Service Fabric non attende e procede immediatamente con l'aggiornamento. Servizio con stato - In un singolo dominio di aggiornamento Service Fabric tenta di verificare che il set di repliche abbia un quorum. Service Fabric attende che sia disponibile un quorum fino al valore di timeout massimo (specificato dalla proprietà UpgradeReplicaSetCheckTimeout). Se il timeout scade, Service Fabric procede con l'aggiornamento indipendentemente dal quorum. Questo parametro è impostato su never (infinito) quando viene eseguito il roll forward (applicato se l'aggiornamento procede come previsto) e su 900 secondi quando viene eseguito il rollback (applicato se l'aggiornamento ha rilevato errori e viene eseguito il rollback). |
| ForceRestart | Se si aggiorna un pacchetto di configurazione o di dati senza aggiornare il codice del servizio, Service Fabric non riavvia il servizio a meno che la proprietà ForceRestart non sia impostata su true nell'ambito della chiamata API. Quando l'aggiornamento è completo, Service Fabric notifica al servizio che è disponibile un nuovo pacchetto di configurazione o di dati. Il servizio è responsabile dell'applicazione delle modifiche. Se necessario, il servizio può riavviarsi. |



<br> <br> I criteri MaxPercentUnhealthyServices, MaxPercentUnhealthyPartitionsPerService e MaxPercentUnhealthyReplicasPerPartition possono essere specificati per tipo di servizio per un'istanza dell'applicazione. In questo modo è possibile assicurarsi che un'applicazione contenente tipi diversi di servizi possa disporre di criteri di valutazione diversi per ogni tipo di servizio. Come esempio, un tipo di servizio gateway senza stato può avere un parametro MaxPercentUnhealthyPartitionsPerService diverso da un tipo di servizio motore con stato per una specifica istanza dell'applicazione.

## Passaggi successivi


[Esercitazione sull'aggiornamento](service-fabric-application-upgrade-tutorial.md)

[Argomenti avanzati](service-fabric-application-upgrade-advanced.md)

[Risoluzione dei problemi relativi all'aggiornamento di un'applicazione](service-fabric-application-upgrade-troubleshooting.md)

[Serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md)
 

<!---HONumber=August15_HO6-->