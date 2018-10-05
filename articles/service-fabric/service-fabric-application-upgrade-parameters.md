---
title: "Aggiornamento di un'applicazione: parametri di aggiornamento | Documentazione Microsoft"
description: Vengono descritti i parametri relativi all'aggiornamento di un'applicazione dell’infrastruttura di servizi, inclusi i controlli di integrità per eseguire e i criteri per annullare automaticamente l'aggiornamento.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: a4170ac6-192e-44a8-b93d-7e39c92a347e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/17/2018
ms.author: subramar
ms.openlocfilehash: 3f321775ba112471760e627e6b43ed17ff8c5b6b
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182876"
---
# <a name="application-upgrade-parameters"></a>Parametri di aggiornamento di un'applicazione
Questo articolo descrive i diversi parametri che si applicano durante l'aggiornamento di un'applicazione di Azure Service Fabric. I parametri di aggiornamento di un'applicazione determinano i timeout e i controlli di integrità che vengono applicati durante l'aggiornamento e specificano i criteri da applicare quando un aggiornamento non riesce.

I parametri dell'applicazione si applicano agli aggiornamenti tramite PowerShell o Visual Studio. I parametri obbligatori e facoltativi applicabili a PowerShell e/o Visual Studio sono descritti nelle tabelle Parametri obbligatori e Parametri facoltativi, come indicato di seguito.

Gli aggiornamenti dell'applicazione possono essere avviati tramite una delle tre modalità di aggiornamento selezionabili dall'utente. Ogni modalità ha un proprio set di parametri dell'applicazione:
- Monitored
- Unmonitored Auto
- Unmonitored Manual

Gli aggiornamenti dell'applicazione di Service Fabric con PowerShell usano il comando [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade). Si seleziona la modalità di aggiornamento passando il parametro **Monitored**, **UnmonitoredAuto** o **UnmonitoredManual** a [ Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade).

I parametri di aggiornamento delle applicazioni di Service Fabric in Visual Studio vengono impostati tramite la finestra di dialogo di Visual Studio Aggiornamento impostazioni. Si seleziona la modalità di aggiornamento di Visual Studio usando l'elenco a discesa sia su **Modalità di aggiornamento** che su **Monitored**, **UnmonitoredAuto** o **UnmonitoredManual** . Per altre informazioni, vedere [Configurare l'aggiornamento di un'applicazione di Service Fabric in Visual Studio](service-fabric-visualstudio-configure-upgrade.md).

## <a name="required-parameters"></a>Parametri obbligatori
(PS=PowerShell, VS=Visual Studio)

| Parametro | Si applica a | DESCRIZIONE |
| --- | --- | --- |
ApplicationName |PS| Nome dell'applicazione che viene aggiornata. Esempi: fabric:/VisualObjects, fabric:/ClusterMonitor. |
ApplicationTypeVersion|PS|Versione del tipo di applicazione a cui è destinato l'aggiornamento. |
FailureAction |PS, VS|I valori consentiti sono **Rollback**, **Manual**, e **Invalid**. L'azione di compensazione da eseguire quando un aggiornamento di *monitoraggio* rileva violazioni dei criteri di integrità o dei criteri di monitoraggio. <br>**Rollback** specifica che l'aggiornamento eseguirà il rollback automaticamente alla versione precedente. <br>**Manual** indica che l'aggiornamento passerà alla modalità di aggiornamento *UnmonitoredManual*. <br>**Invalid** indica che l'azione di errore non è valida.|
Monitored |PS|Indica che la modalità di aggiornamento viene monitorata. Al termine dell'aggiornamento per un dominio di aggiornamento del cmdlet, se l'integrità del dominio di aggiornamento e il cluster soddisfano i criteri di integrità definiti, Service Fabric consente di aggiornare il dominio di aggiornamento successivo. Se il dominio di aggiornamento o il cluster non riescono a soddisfare i criteri di integrità, l'aggiornamento non riesce, quindi la risorsa Service Fabric esegue il rollback dell'aggiornamento per il dominio di aggiornamento o viene ripristinata la modalità manuale per i criteri specificati. Si tratta della modalità consigliata per gli aggiornamenti dell'applicazione in un ambiente di produzione. |
UpgradeMode | VS | I valori consentiti sono **Monitored** (impostazione predefinita), **UnmonitoredAuto** o **UnmonitoredManual**. Vedere i parametri di PowerShell per ogni modalità, in questo articolo, per informazioni dettagliate. |
UnmonitoredAuto | PS | Indica che la modalità di aggiornamento non viene monitorata automaticamente. Dopo che la risorsa Service Fabric effettua un aggiornamento su un dominio di aggiornamento, Service Fabric consente di aggiornare il dominio di aggiornamento successivo, qualunque sia lo stato di integrità dell'applicazione. Questa modalità non è consigliabile per la produzione ed è utile solo durante lo sviluppo di un'applicazione. |
UnmonitoredManual | PS | Indica che la modalità di aggiornamento non viene monitorata manualmente. Dopo che la risorsa Service Fabric effettua un aggiornamento su un dominio di aggiornamento, rimane in attesa per poter aggiornare il dominio di aggiornamento successivo con il cmdlet*Resume-ServiceFabricApplicationUpgrade*. |

## <a name="optional-parameters"></a>Parametri facoltativi

I criteri di valutazione dell'integrità sono facoltativi. Se all'avvio di un aggiornamento non vengono specificati i criteri di valutazione dell'integrità, Service Fabric usa i criteri di integrità dell'applicazione specificati nel file ApplicationManifest.xml dell'istanza dell'applicazione.

Per visualizzare il campo descrizione completo, usare la barra di scorrimento orizzontale nella parte inferiore della tabella.

(PS=PowerShell, VS=Visual Studio)

| Parametro | Si applica a | DESCRIZIONE |
| --- | --- | --- |
| ApplicationParameter |PS, VS| Specifica le sostituzioni per i parametri dell'applicazione.<br>I parametri delle applicazioni di PowerShell vengono specificati come coppie nome/valore hashtable. Ad esempio, @{"VotingData_MinReplicaSetSize" = "3". "VotingData_PartitionCount" = "1"}.<br>I parametri dell'applicazione di Visual Studio possono essere specificati nella finestra di dialogo Pubblica applicazione di Service Fabric nel campo **File di parametri dell'applicazione**.
| Confirm |PS| I valori consentiti sono **true** e **false**. Richiede la conferma dell'utente prima di eseguire il cmdlet. |
| ConsiderWarningAsError |PS, VS |I valori consentiti sono **true** e **false**. Il valore predefinito è **False**. Considerare gli eventi di avviso relativi all'integrità dell'applicazione come errori quando si valuta l'integrità dell'applicazione durante l'aggiornamento. Per impostazione predefinita, Service Fabric non valuta eventi di avviso relativi all'integrità come errori, quindi l'aggiornamento può continuare anche se vengono generati questi eventi. |
| DefaultServiceTypeHealthPolicy | PS, VS |Specifica i criteri di integrità per il tipo di servizio predefinito da usare per l'aggiornamento monitorato nei formati, MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices. Ad esempio, 5,10,15 indica i valori seguenti: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
| Force | PS, VS | I valori consentiti sono **true** e **false**. Indica che il processo di aggiornamento ignora il messaggio di avviso e forza l'aggiornamento anche quando il numero di versione non è stato modificato. Ciò è utile per il test locale ma non è consigliato per l'uso in un ambiente di produzione perché è necessario rimuovere la distribuzione esistente che causa tempi di inattività e perdita potenziale di dati. |
| ForceRestart |PS, VS |Se si aggiorna un pacchetto di configurazione o di dati senza aggiornare il codice del servizio, il servizio viene riavviato solo se la proprietà ForceRestart è impostata su **True**. Quando l'aggiornamento è completo, Service Fabric notifica al servizio che è disponibile un nuovo pacchetto di configurazione o di dati. Il servizio è responsabile dell'applicazione delle modifiche. Se necessario, il servizio può riavviarsi. |
| HealthCheckRetryTimeoutSec |PS, VS |Intervallo di tempo (in secondi) in cui Service Fabric continua a eseguire la valutazione dell'integrità prima di dichiarare l'aggiornamento come non riuscito. Il valore predefinito è 600 secondi. Il calcolo del tempo inizia dopo che viene raggiunto il valore definito da *HealthCheckWaitDuration*. Nell'ambito di questo intervallo di tempo definito da *HealthCheckRetryTimeout*, Service Fabric può eseguire più controlli di integrità dell'applicazione. Il valore predefinito è 10 minuti ed è consigliabile personalizzarlo in base all'applicazione. |
| HealthCheckStableDurationSec |PS, VS |Il periodo (in secondi) per verificare che l'applicazione sia stabile prima di passare al dominio di aggiornamento successivo o di completare l'aggiornamento. Questo intervallo viene usato per evitare che immediatamente dopo il controllo di integrità vengono apportate modifiche all'integrità non rilevate. Il valore predefinito è 120 secondi ed è consigliabile personalizzarlo in base all'applicazione. |
| HealthCheckWaitDurationSec |PS, VS | Tempo di attesa, in secondi, dopo il completamento dell'aggiornamento nel dominio di aggiornamento prima che Service Fabric valuti l'integrità dell'applicazione. La durata può essere considerata anche come il tempo di esecuzione di un'applicazione prima che venga considerata integra. Se il controllo di integrità ha esito positivo, il processo di aggiornamento passa al dominio di aggiornamento successivo.  Se invece il controllo di integrità ha esito negativo, Service Fabric attende [UpgradeHealthCheckInterval](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#clustermanager) prima di riprovare a eseguire il controllo di integrità finché non viene raggiunto il valore definito da *HealthCheckRetryTimeoutSec*. Il valore predefinito e consigliato è 0 secondi. |
| MaxPercentUnhealthyDeployedApplications|PS, VS |Il valore predefinito e consigliato è 0. Specificare il numero massimo di applicazioni distribuite (vedere la [sezione relativa all'integrità](service-fabric-health-introduction.md)) che possono essere non integre prima che l'applicazione venga considerata non integra e l'aggiornamento non riesca. Questo parametro definisce l'integrità dell'applicazione sul nodo e consente di rilevare eventuali problemi durante l'aggiornamento. Normalmente alle repliche dell'applicazione viene applicato un bilanciamento del carico basato sull'altro nodo, in modo che l'applicazione risulti integra e l'aggiornamento possa continuare. Specificando uno stato di integrità *MaxPercentUnhealthyDeployedApplications* rigoroso, Service Fabric può rilevare rapidamente un problema con il pacchetto dell'applicazione, determinando un aggiornamento con risposta immediata agli errori. |
| MaxPercentUnhealthyServices |PS, VS |Un parametro a *DefaultServiceTypeHealthPolicy* e *ServiceTypeHealthPolicyMap*. Il valore predefinito e consigliato è 0. Specificare il numero massimo di servizi dell'istanza dell'applicazione che possono essere non integri prima che l'applicazione venga considerata non integra e l'aggiornamento non riesca. |
| MaxPercentUnhealthyPartitionsPerService|PS, VS |Il valore predefinito e consigliato è 0. Specificare il numero massimo di partizioni di un servizio che possono essere non integre prima che il servizio venga considerato non integro. |
| MaxPercentUnhealthyReplicasPerPartition|PS, VS |Il valore predefinito e consigliato è 0. Specificare il numero massimo di repliche nella partizione che possono essere non integre prima che la partizione venga considerata non integra. |
| ServiceTypeHealthPolicyMap | PS, VS | Descrive i criteri di integrità usati per valutare l'integrità dei servizi che appartengono a un tipo di servizio. Accetta un input di tabella hash nel formato seguente: @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"} Ad esempio: @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" } |
| TimeoutSec | PS, VS | Specifica il periodo di timeout in secondi per l'operazione. |
| UpgradeDomainTimeoutSec |PS, VS |Tempo massimo (in secondi) per l'aggiornamento di un singolo dominio di aggiornamento. Se viene raggiunto questo timeout, l'aggiornamento si arresta e procede in base all'impostazione di *FailureAction*. Il valore predefinito è mai (Infinito) ed è consigliabile personalizzarlo in base all'applicazione. |
| UpgradeReplicaSetCheckTimeoutSec |PS, VS |**Servizio senza stato**: in un singolo dominio di aggiornamento Service Fabric prova a verificare che siano disponibili istanze aggiuntive del servizio. Se il numero di istanze di destinazione è maggiore di uno, Service Fabric attende che sia disponibile più di un'istanza fino al valore di timeout massimo. Questo timeout è specificato dalla proprietà *UpgradeReplicaSetCheckTimeoutSec*. Se il timeout scade, Service Fabric procede con l'aggiornamento indipendentemente dal numero di istanze del servizio. Se il numero di istanze di destinazione è pari a uno, Service Fabric non attende e procede immediatamente con l'aggiornamento.<br><br>**Servizio con stato**: in un singolo dominio di aggiornamento Service Fabric prova a verificare che il set di repliche abbia un quorum. Service Fabric attende che sia disponibile un quorum fino al valore di timeout massimo, (specificato dalla proprietà *UpgradeReplicaSetCheckTimeoutsec*). Se il timeout scade, Service Fabric procede con l'aggiornamento indipendentemente dal quorum. Il valore di questa impostazione è never (infinito) per il roll forward e 1200 secondi per il rollback. |
| UpgradeTimeoutSec |PS, VS |Intervallo di timeout, in secondi, che si applica per l'intero aggiornamento. Se viene raggiunto questo timeout, l'aggiornamento si arresta e viene attivata l'azione specificata da *FailureAction*. Il valore predefinito è mai (Infinito) ed è consigliabile personalizzarlo in base all'applicazione. |
| WhatIf | PS | I valori consentiti sono **true** e **false**. Mostra l'esito in caso di esecuzione del cmdlet. Il cmdlet non viene eseguito. |

I criteri *MaxPercentUnhealthyServices*, *MaxPercentUnhealthyPartitionsPerService* e *MaxPercentUnhealthyReplicasPerPartition* possono essere specificati per tipo di servizio per un'istanza dell'applicazione. Impostando questi parametri "per servizio" un'applicazione può contenere tipi di servizi diversi con criteri di valutazione diversi. Ad esempio, un tipo di servizio gateway senza stato può avere un parametro *MaxPercentUnhealthyPartitionsPerService* diverso da un tipo di servizio motore con stato per una specifica istanza dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi
[Esercitazione sull'aggiornamento di un'applicazione di Service Fabric tramite Visual Studio](service-fabric-application-upgrade-tutorial.md) descrive la procedura di aggiornamento di un'applicazione con Visual Studio.

[Aggiornamento di un'applicazione di Service Fabric mediante PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) descrive la procedura di aggiornamento di un'applicazione tramite PowerShell.

[Aggiornamento dell'applicazione](service-fabric-application-lifecycle-sfctl.md#upgrade-application) descrive la procedura di aggiornamento di un'applicazione con l'interfaccia della riga di comando di Service Fabric.

[Aggiornamento dell'applicazione con il plug-in Eclipse per Service Fabric](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Rendere compatibili gli aggiornamenti dell'applicazione imparando a usare [Serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md).

Per informazioni su come usare funzionalità avanzate durante l'aggiornamento dell'applicazione, vedere [Argomenti avanzati](service-fabric-application-upgrade-advanced.md).

Per informazioni su come risolvere problemi comuni negli aggiornamenti dell'applicazione, vedere i passaggi indicati in [Risoluzione dei problemi relativi agli aggiornamenti dell'applicazione](service-fabric-application-upgrade-troubleshooting.md).
