---
title: Ciclo di vita di un&quot;applicazione in Service Fabric | Documentazione Microsoft
description: Vengono descritte le operazioni di sviluppo, distribuzione, test, aggiornamento, manutenzione e rimozione per le applicazioni di Infrastruttura di servizi.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 08837cca-5aa7-40da-b087-2b657224a097
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/14/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a33d25f3806090307f6fb14dbb821264f2e4f28b
ms.contentlocale: it-it
ms.lasthandoff: 04/27/2017


---
# <a name="service-fabric-application-lifecycle"></a>Ciclo di vita dell'applicazione Service Fabric
Analogamente ad altre piattaforme, un'applicazione su Service Fabric di Azure in genere passa attraverso le fasi seguenti: progettazione, sviluppo, test, distribuzione, aggiornamento, manutenzione e rimozione. Service Fabric di Azure offre un supporto di prima categoria per l'intero ciclo di vita delle applicazioni cloud, dallo sviluppo alla distribuzione, alla gestione giornaliera, alla manutenzione e infine alla rimozione delle autorizzazioni. Il modello di servizio abilita diversi ruoli per la partecipazione indipendente al ciclo di vita delle applicazioni. Questo articolo offre una panoramica delle interfacce API e del modo in cui vengono utilizzate dai diversi ruoli nelle fasi del ciclo di vita di un'applicazione di Service Fabric.

Il video di Microsoft Virtual Academy seguente illustra come gestire il ciclo di vita dell'applicazione:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-application-lifecycle/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="service-model-roles"></a>Ruoli del modello di servizio
I ruoli del modello di servizio sono i seguenti:

* **Sviluppatore di servizi**: sviluppa servizi modulari e generici che possono essere rimaneggiati per modificarne i fini e usati in più applicazioni dello stesso tipo o di tipi diversi. Un servizio di accodamento ad esempio può essere usato per creare un'applicazione di controllo di attività e richieste basata su ticket (supporto tecnico) o un'applicazione di e-commerce (carrello acquisti).
* **Sviluppatore di applicazioni**: crea applicazioni integrando una raccolta di servizi per soddisfare determinati requisiti o scenari. Un sito Web di e-commerce ad esempio può integrare i servizi "JSON Stateless Front-end Service", "Auction Stateful Service" e "Queue Stateful Service" per creare una soluzione per vendite all'asta.
* **Amministratore di applicazioni**: prende decisioni relative alla configurazione (specificando i parametri del modello di configurazione), alla distribuzione (eseguendo il mapping con le risorse disponibili) e alla qualità del servizio delle applicazioni. Un amministratore di applicazioni ad esempio decide le impostazioni locali della lingua (come inglese per gli Stati Uniti o giapponese per Giappone) dell'applicazione. Un'altra applicazione distribuita può avere impostazioni diverse.
* **Operatore**: distribuisce le applicazioni in base alla configurazione e ai requisiti specificati dall'amministratore di applicazioni. Un operatore ad esempio effettua il provisioning e la distribuzione dell'applicazione e verifica che sia in esecuzione in Azure. Gli operatori monitorano l'integrità e le informazioni sulle prestazioni delle applicazioni e gestiscono l'infrastruttura fisica a seconda delle esigenze.

## <a name="develop"></a>Sviluppare
1. Uno *sviluppatore di servizi* sviluppa diversi tipi di servizi usando il modello di programmazione [Reliable Actors](service-fabric-reliable-actors-introduction.md) o [Reliable Services](service-fabric-reliable-services-introduction.md).
2. Uno *sviluppatore di servizi* descrive in modo dichiarativo i tipi di servizi sviluppati in un file manifesto del servizio da uno o più pacchetti di codice, configurazione e dati.
3. Uno *sviluppatore di applicazioni* compila quindi un'applicazione usando diversi tipi di servizi.
4. Uno *sviluppatore di applicazioni* descrive in modo dichiarativo il tipo di applicazione nel manifesto di un'applicazione facendo riferimento ai manifesti dei servizi costituenti ed eseguendo in modo appropriato l'override e la parametrizzazione delle diverse impostazioni di configurazione e distribuzione dei servizi costituenti.

Per gli esempi, vedere gli articoli relativi all'[introduzione ai modelli di programmazione Reliable Actors](service-fabric-reliable-actors-get-started.md) e all'[introduzione ai modelli di programmazione Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="deploy"></a>Distribuisci
1. Un *amministratore di applicazioni* personalizza il tipo di applicazione in un'applicazione specifica da distribuire in un cluster di Service Fabric specificando i parametri appropriati dell'elemento **ApplicationType** nel manifesto dell'applicazione.
2. Un *operatore* carica il pacchetto dell'applicazione nell'archivio immagini cluster usando il metodo [ **CopyApplicationPackage**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CopyApplicationPackage_System_String_System_String_System_String_) o il cmdlet [**Copy-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Il pacchetto applicazione contiene il manifesto dell'applicazione e la raccolta di pacchetti servizio. Service Fabric distribuisce le applicazioni dal relativo pacchetto archiviato nell’archivio immagini, che può essere un archivio BLOB di Azure o un servizio di sistema di Service Fabric.
3. L'*operatore* esegue quindi il provisioning del tipo di applicazione nel cluster di destinazione dal pacchetto dell'applicazione caricato mediante il metodo [**ProvisionApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_ProvisionApplicationAsync_System_String_System_TimeSpan_System_Threading_CancellationToken_), il cmdlet [**Register-ServiceFabricApplicationType**](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) o l'operazione [**provisioning di un'applicazione** REST](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
4. Dopo aver eseguito il provisioning dell'applicazione, un *operatore* avvia l'applicazione con i parametri forniti dall'*amministratore di applicazioni* mediante il metodo [**CreateApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CreateApplicationAsync_System_Fabric_Description_ApplicationDescription_System_TimeSpan_System_Threading_CancellationToken_), il cmdlet [**New-ServiceFabricApplication**](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricapplication) o l'operazione [**Create Application**  REST](https://docs.microsoft.com/rest/api/servicefabric/create-an-application).
5. Dopo la distribuzione dell'applicazione, un *operatore* usa il metodo [**CreateServiceAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient#System_Fabric_FabricClient_ServiceManagementClient_CreateServiceAsync_System_Fabric_Description_ServiceDescription_System_TimeSpan_System_Threading_CancellationToken_), il cmdlet [**New-ServiceFabricService**](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice) o l'operazione [**Create Service** REST](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) per creare nuove istanze di servizi per l'applicazione in base ai tipi di servizio disponibili.
6. L'applicazione è ora in esecuzione nel cluster di Service Fabric.

Per gli esempi, vedere l'articolo relativo alla [distribuzione di un'applicazione](service-fabric-deploy-remove-applications.md) .

## <a name="test"></a>Test
1. Dopo la distribuzione nel cluster di sviluppo locale o in un cluster di test, uno *sviluppatore di servizi* esegue lo scenario di test di failover predefinito usando le classi [**FailoverTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters#System_Fabric_Testability_Scenario_FailoverTestScenarioParameters) e [**FailoverTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario#System_Fabric_Testability_Scenario_FailoverTestScenario) o il cmdlet [**Invoke-ServiceFabricFailoverTestScenario**](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps). Lo scenario di test di failover esegue un servizio specifico attraverso importanti transizioni e failover per verificare che sia sempre disponibile e funzionante.
2. Lo *sviluppatore di servizi* esegue quindi lo scenario di test CHAOS predefinito usando le classi [**ChaosTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters#System_Fabric_Testability_Scenario_ChaosTestScenarioParameters) e [**ChaosTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario#System_Fabric_Testability_Scenario_ChaosTestScenario) o il cmdlet [**Invoke-ServiceFabricChaosTestScenario**](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). Lo scenario di test CHAOS induce nel cluster più errori casuali di nodi, pacchetti di codice e repliche.
3. Lo *sviluppatore di servizi* [testa la comunicazione tra servizi](service-fabric-testability-scenarios-service-communication.md) creando scenari di test che spostano le repliche primarie nel cluster.

Per altre informazioni, vedere la pagina sull' [introduzione al servizio di analisi degli errori](service-fabric-testability-overview.md) .

## <a name="upgrade"></a>Aggiornamento
1. Uno *sviluppatore di servizi* aggiorna i servizi costituenti dell'applicazione di cui sono state create istanze e/o corregge i bug e fornisce una nuova versione del manifesto del servizio.
2. Uno *sviluppatore di applicazioni* esegue l'override e la parametrizzazione delle impostazioni di configurazione e distribuzione dei servizi coerenti e fornisce una nuova versione del manifesto dell'applicazione. Lo sviluppatore di applicazioni incorpora quindi le nuove versioni dei manifesti dei servizi nell'applicazione e fornisce una nuova versione del tipo di applicazione in un pacchetto applicazione aggiornato.
3. Un *amministratore di applicazioni* incorpora la nuova versione del tipo di applicazione nell'applicazione di destinazione aggiornando i parametri appropriati.
4. Un *operatore* carica il pacchetto dell'applicazione aggiornato nell'archivio immagini cluster usando il metodo [**CopyApplicationPackage**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CopyApplicationPackage_System_String_System_String_System_String_) o il cmdlet [**Copy-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Il pacchetto applicazione contiene il manifesto dell'applicazione e la raccolta di pacchetti servizio.
5. Un *operatore* esegue il provisioning della nuova versione dell'applicazione nel cluster di destinazione usando il metodo [**ProvisionApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_ProvisionApplicationAsync_System_String_System_TimeSpan_System_Threading_CancellationToken_), il cmdlet [**Register-ServiceFabricApplicationType**](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) o l'operazione [**Provision an Application** REST](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
6. Un *operatore* aggiorna l'applicazione di destinazione alla nuova versione usando il metodo [**UpgradeApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UpgradeApplicationAsync_System_Fabric_Description_ApplicationUpgradeDescription_System_TimeSpan_System_Threading_CancellationToken_), il cmdlet [**Start-ServiceFabricApplicationUpgrade**](https://docs.microsoft.com/powershell/servicefabric/vlatest/start-servicefabricapplicationupgrade) o l'operazione [**Upgrade an Application** REST](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application).
7. Un *operatore* controlla lo stato dell'aggiornamento usando il metodo [**GetApplicationUpgradeProgressAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_GetApplicationUpgradeProgressAsync_System_Uri_System_TimeSpan_System_Threading_CancellationToken_), il cmdlet [**Get-ServiceFabricApplicationUpgrade**](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade) o l'operazione [**Get Application Upgrade Progress** REST](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. Se necessario, l'*operatore* modifica e riapplica i parametri dell'aggiornamento dell'applicazione corrente usando il metodo [**UpdateApplicationUpgradeAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UpdateApplicationUpgradeAsync_System_Fabric_Description_ApplicationUpgradeUpdateDescription_System_TimeSpan_System_Threading_CancellationToken_), il cmdlet [**Update-ServiceFabricApplicationUpgrade**](https://docs.microsoft.com/powershell/servicefabric/vlatest/update-servicefabricapplicationupgrade) o l'operazione [**Update Application Upgrade** REST](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. Se necessario, l'*operatore* esegue il rollback dell'aggiornamento dell'applicazione corrente usando il metodo [**RollbackApplicationUpgradeAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_RollbackApplicationUpgradeAsync_System_Uri_System_TimeSpan_System_Threading_CancellationToken_), il cmdlet [**Start-ServiceFabricApplicationRollback**](https://docs.microsoft.com/powershell/servicefabric/vlatest/start-servicefabricapplicationrollback) o l'operazione [**Rollback Application Upgrade** REST](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. Service Fabric aggiorna l'applicazione di destinazione in esecuzione nel cluster senza perdere la disponibilità dei servizi costituenti.

Per gli esempi, vedere l'articolo relativo all' [esercitazione sull'aggiornamento di un'applicazione](service-fabric-application-upgrade-tutorial.md) .

## <a name="maintain"></a>Effettuare la manutenzione
1. Per gli aggiornamenti e le patch del sistema operativo, Service Fabric interagisce con l'infrastruttura di Azure per garantire la disponibilità di tutte le applicazioni in esecuzione nel cluster.
2. Per gli aggiornamenti e le patch della piattaforma Service Fabric, Service Fabric si aggiorna automaticamente senza perdere la disponibilità delle applicazioni in esecuzione nel cluster.
3. Un *amministratore di applicazioni* approva l'aggiunta o la rimozione di nodi da un cluster dopo aver analizzato i dati cronologici di uso della capacità e la proiezione della domanda futura.
4. Un *operatore* aggiunge e rimuove i nodi specificati dall'*amministratore di applicazioni*.
5. Quando nel cluster vengono aggiunti nuovi nodi o rimossi nodi esistenti, Service Fabric esegue automaticamente il bilanciamento del carico delle applicazioni in esecuzione in tutti i nodi del cluster per ottenere prestazioni ottimali.

## <a name="remove"></a>Rimuovere
1. Un *operatore* può eliminare un'istanza specifica di un servizio in esecuzione nel cluster senza rimuovere l'intera applicazione usando il metodo [**DeleteServiceAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient#System_Fabric_FabricClient_ServiceManagementClient_DeleteServiceAsync_System_Fabric_Description_DeleteServiceDescription_System_TimeSpan_System_Threading_CancellationToken_), il cmdlet [**Remove-ServiceFabricService**](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricservice) o l'operazione [**Delete Service** REST](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. Un *operatore* può anche eliminare un'istanza di un'applicazione e tutti i relativi servizi usando il metodo [**DeleteApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_DeleteApplicationAsync_System_Fabric_Description_DeleteApplicationDescription_System_TimeSpan_System_Threading_CancellationToken_), il cmdlet [**Remove-ServiceFabricApplication**](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricapplication) o l'operazione [**Delete Application** REST](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application).
3. Dopo l'arresto dell'applicazione e dei servizi, l'*operatore* può annullare il provisioning del tipo di applicazione usando il metodo [**UnprovisionApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UnprovisionApplicationAsync_System_String_System_String_System_TimeSpan_System_Threading_CancellationToken_), il cmdlet [**Unregister-ServiceFabricApplicationType**](https://docs.microsoft.com/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) o l'operazione [**Unprovision an Application** REST](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application). L'annullamento del provisioning del tipo di applicazione non comporta la rimozione del pacchetto applicazione da ImageStore. Il pacchetto applicazione dovrà essere rimosso manualmente.
4. Un *operatore* rimuove il pacchetto dell'applicazione da ImageStore usando il metodo [**RemoveApplicationPackage**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_RemoveApplicationPackage_System_String_System_String_) o il cmdlet [**Remove-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps).

Per gli esempi, vedere l'articolo relativo alla [distribuzione di un'applicazione](service-fabric-deploy-remove-applications.md) .

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sullo sviluppo, il test e la gestione di applicazioni e servizi di Service Fabric, vedere:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Distribuire un'applicazione](service-fabric-deploy-remove-applications.md)
* [Aggiornamento dell'applicazione](service-fabric-application-upgrade.md)
* [Panoramica di Testabilità](service-fabric-testability-overview.md)

