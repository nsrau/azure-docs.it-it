---
title: 'Aggiornamento di un''applicazione: parametri di aggiornamento | Microsoft Docs'
description: "Vengono descritti i parametri relativi all'aggiornamento di un'applicazione dell’infrastruttura di servizi, inclusi i controlli di integrità per eseguire e i criteri per annullare automaticamente l'aggiornamento."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: a4170ac6-192e-44a8-b93d-7e39c92a347e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: c12c4fc6cabd695101abf922eba77b9cd3ee00fa
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="application-upgrade-parameters"></a>Parametri di aggiornamento di un'applicazione
Questo articolo descrive i diversi parametri che si applicano durante l'aggiornamento di un'applicazione di Azure Service Fabric. I parametri includono il nome e la versione dell'applicazione. Determinano i timeout e i controlli di integrità che vengono applicati durante l'aggiornamento e specificano i criteri da applicare quando un aggiornamento non riesce.

<br>

| Parametro | DESCRIZIONE |
| --- | --- |
| ApplicationName |Nome dell'applicazione che viene aggiornata. Esempi: fabric:/VisualObjects, fabric:/ClusterMonitor |
| TargetApplicationTypeVersion |Versione del tipo di applicazione a cui è destinato l'aggiornamento. |
| FailureAction |L'azione eseguita da Service Fabric se l'aggiornamento non riesce. È possibile che venga eseguito il rollback dell'applicazione alla versione pre-aggiornamento oppure l'aggiornamento può essere arrestato nel dominio di aggiornamento corrente. Nel secondo caso la modalità di aggiornamento viene impostata su manuale. I valori consentiti sono Rollback e Manual. |
| HealthCheckWaitDurationSec |Tempo di attesa, in secondi, dopo il completamento dell'aggiornamento nel dominio di aggiornamento prima che Service Fabric valuti l'integrità dell'applicazione. La durata può essere considerata anche come il tempo di esecuzione di un'applicazione prima che venga considerata integra. Se il controllo di integrità ha esito positivo, il processo di aggiornamento passa al dominio di aggiornamento successivo.  Se invece il controllo di integrità ha esito negativo, Service Fabric attende un periodo di tempo (definito da UpgradeHealthCheckInterval) prima di riprovare a eseguire il controllo di integrità finché non viene raggiunto il valore definito da HealthCheckRetryTimeout. Il valore predefinito e consigliato è 0 secondi. |
| HealthCheckRetryTimeoutSec |Intervallo di tempo (in secondi) in cui Service Fabric continua a eseguire la valutazione dell'integrità prima di dichiarare l'aggiornamento come non riuscito. Il valore predefinito è 600 secondi. Il calcolo del tempo inizia dopo che viene raggiunto il valore definito da HealthCheckWaitDuration. Nell'ambito di questo intervallo di tempo definito da HealthCheckRetryTimeout, Service Fabric può eseguire più controlli di integrità dell'applicazione. Il valore predefinito è 10 minuti ed è consigliabile personalizzarlo in base all'applicazione. |
| HealthCheckStableDurationSec |Il periodo (in secondi) per verificare che l'applicazione sia stabile prima di passare al dominio di aggiornamento successivo o di completare l'aggiornamento. Questo intervallo viene usato per evitare che immediatamente dopo il controllo di integrità vengono apportate modifiche all'integrità non rilevate. Il valore predefinito è 120 secondi ed è consigliabile personalizzarlo in base all'applicazione. |
| UpgradeDomainTimeoutSec |Tempo massimo (in secondi) per l'aggiornamento di un singolo dominio di aggiornamento. Se viene raggiunto questo timeout, l'aggiornamento si arresta e procede in base all'impostazione di UpgradeFailureAction. Il valore predefinito è mai (Infinito) ed è consigliabile personalizzarlo in base all'applicazione. |
| UpgradeTimeout |Intervallo di timeout, in secondi, che si applica per l'intero aggiornamento. Se viene raggiunto questo timeout, l'aggiornamento si arresta e viene attivata l'azione specificata da UpgradeFailureAction. Il valore predefinito è mai (Infinito) ed è consigliabile personalizzarlo in base all'applicazione. |
| UpgradeHealthCheckInterval |Frequenza di controllo dello stato di integrità. Questo parametro viene specificato nella sezione ClusterManager del *manifesto del* *cluster* e non viene specificato come parte del cmdlet di aggiornamento. Il valore predefinito è 60 secondi. |

<br>

## <a name="service-health-evaluation-during-application-upgrade"></a>Valutazione dell'integrità di un servizio durante l'aggiornamento dell'applicazione
<br>
I criteri di valutazione dell'integrità sono facoltativi. Se all'avvio di un aggiornamento non vengono specificati i criteri di valutazione dell'integrità, Service Fabric usa i criteri di integrità dell'applicazione specificati nel file ApplicationManifest.xml dell'istanza dell'applicazione.

<br>

| Parametro | DESCRIZIONE |
| --- | --- |
| ConsiderWarningAsError |Il valore predefinito è False. Considerare gli eventi di avviso relativi all'integrità dell'applicazione come errori quando si valuta l'integrità dell'applicazione durante l'aggiornamento. Per impostazione predefinita, Service Fabric non valuta eventi di avviso relativi all'integrità come errori, quindi l'aggiornamento può continuare anche se vengono generati questi eventi. |
| MaxPercentUnhealthyDeployedApplications |Il valore predefinito e consigliato è 0. Specificare il numero massimo di applicazioni distribuite (vedere la [sezione relativa all'integrità](service-fabric-health-introduction.md)) che possono essere non integre prima che l'applicazione venga considerata non integra e l'aggiornamento non riesca. Questo parametro definisce l'integrità dell'applicazione sul nodo e consente di rilevare eventuali problemi durante l'aggiornamento. Normalmente alle repliche dell'applicazione viene applicato un bilanciamento del carico basato sull'altro nodo, in modo che l'applicazione risulti integra e l'aggiornamento possa continuare. Specificando uno stato di integrità MaxPercentUnhealthyDeployedApplications rigoroso, Service Fabric può rilevare rapidamente un problema con il pacchetto dell'applicazione, determinando un aggiornamento con risposta immediata agli errori. |
| MaxPercentUnhealthyServices |Il valore predefinito e consigliato è 0. Specificare il numero massimo di servizi dell'istanza dell'applicazione che possono essere non integri prima che l'applicazione venga considerata non integra e l'aggiornamento non riesca. |
| MaxPercentUnhealthyPartitionsPerService |Il valore predefinito e consigliato è 0. Specificare il numero massimo di partizioni di un servizio che possono essere non integre prima che il servizio venga considerato non integro. |
| MaxPercentUnhealthyReplicasPerPartition |Il valore predefinito e consigliato è 0. Specificare il numero massimo di repliche nella partizione che possono essere non integre prima che la partizione venga considerata non integra. |
| UpgradeReplicaSetCheckTimeout |<p>**Servizio senza stato**: in un singolo dominio di aggiornamento Service Fabric prova a verificare che siano disponibili istanze aggiuntive del servizio. Se il numero di istanze di destinazione è maggiore di uno, Service Fabric attende che sia disponibile più di un'istanza fino al valore di timeout massimo. Questo timeout è specificato dalla proprietà UpgradeReplicaSetCheckTimeout. Se il timeout scade, Service Fabric procede con l'aggiornamento indipendentemente dal numero di istanze del servizio. Se il numero di istanze di destinazione è pari a uno, Service Fabric non attende e procede immediatamente con l'aggiornamento.</p><p>**Servizio con stato**: in un singolo dominio di aggiornamento Service Fabric prova a verificare che il set di repliche abbia un quorum. Service Fabric attende che sia disponibile un quorum fino al valore di timeout massimo, specificato dalla proprietà UpgradeReplicaSetCheckTimeout. Se il timeout scade, Service Fabric procede con l'aggiornamento indipendentemente dal quorum. Il valore di questa impostazione è never (infinito) per il roll forward e 1200 secondi per il rollback.</p> |
| ForceRestart |Se si aggiorna un pacchetto di configurazione o di dati senza aggiornare il codice del servizio, il servizio viene riavviato solo se la proprietà ForceRestart è impostata su true. Quando l'aggiornamento è completo, Service Fabric notifica al servizio che è disponibile un nuovo pacchetto di configurazione o di dati. Il servizio è responsabile dell'applicazione delle modifiche. Se necessario, il servizio può riavviarsi. |

<br>
<br>
I criteri MaxPercentUnhealthyServices, MaxPercentUnhealthyPartitionsPerService e MaxPercentUnhealthyReplicasPerPartition possono essere specificati per tipo di servizio per un'istanza dell'applicazione. Impostando questi parametri "per servizio" un'applicazione può contenere tipi di servizi diversi con criteri di valutazione diversi. Ad esempio, un tipo di servizio gateway senza stato può avere un parametro MaxPercentUnhealthyPartitionsPerService diverso da un tipo di servizio motore con stato per una specifica istanza dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi
[Esercitazione sull'aggiornamento di un'applicazione di Service Fabric tramite Visual Studio](service-fabric-application-upgrade-tutorial.md) descrive la procedura di aggiornamento di un'applicazione con Visual Studio.

[Aggiornamento di un'applicazione di Service Fabric mediante PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) descrive la procedura di aggiornamento di un'applicazione tramite PowerShell.

[Aggiornamento dell'applicazione](service-fabric-application-lifecycle-sfctl.md#upgrade-application) descrive la procedura di aggiornamento di un'applicazione con l'interfaccia della riga di comando di Service Fabric.

[Aggiornamento dell'applicazione con il plug-in Eclipse per Service Fabric](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Rendere compatibili gli aggiornamenti dell'applicazione imparando a usare [Serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md).

Per informazioni su come usare funzionalità avanzate durante l'aggiornamento dell'applicazione, vedere [Argomenti avanzati](service-fabric-application-upgrade-advanced.md).

Per informazioni su come risolvere problemi comuni negli aggiornamenti dell'applicazione, vedere i passaggi indicati in [Risoluzione dei problemi relativi agli aggiornamenti dell'applicazione](service-fabric-application-upgrade-troubleshooting.md).
