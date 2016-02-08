<properties
   pageTitle="Ciclo di vita di un'applicazione in Service Fabric | Microsoft Azure"
   description="Vengono descritte le operazioni di sviluppo, distribuzione, test, aggiornamento, manutenzione e rimozione per le applicazioni di Infrastruttura di servizi."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>


<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/20/2016"
   ms.author="ryanwi; mani-ramaswamy"/>


# Ciclo di vita dell'applicazione Service Fabric
Analogamente ad altre piattaforme, un'applicazione su Service Fabric di Azure in genere passa attraverso le fasi seguenti: progettazione, sviluppo, test, distribuzione, aggiornamento, manutenzione e rimozione. Service Fabric di Azure offre un supporto di prima categoria per l'intero ciclo di vita delle applicazioni cloud, dallo sviluppo alla distribuzione, alla gestione giornaliera, alla manutenzione e infine alla rimozione delle autorizzazioni. Il modello di servizio abilita diversi ruoli per la partecipazione indipendente al ciclo di vita delle applicazioni. Questo articolo offre una panoramica delle interfacce API e del modo in cui vengono utilizzate dai diversi ruoli nelle fasi del ciclo di vita di un'applicazione di Service Fabric.

## Ruoli del modello di servizio
I ruoli del modello di servizio sono i seguenti:

- **Sviluppatore di servizi**: sviluppa servizi modulari e generici che possono essere rimaneggiati per modificarne i fini e usati in più applicazioni dello stesso tipo o di tipi diversi. Un servizio di accodamento ad esempio può essere usato per creare un'applicazione di controllo di attività e richieste basata su ticket (supporto tecnico) o un'applicazione di e-commerce (carrello acquisti).

- **Sviluppatore di applicazioni**: crea applicazioni integrando una raccolta di servizi per soddisfare determinati requisiti o scenari. Un sito Web di e-commerce ad esempio può integrare i servizi "JSON Stateless Front-end Service", "Auction Stateful Service" e "Queue Stateful Service" per creare una soluzione per vendite all'asta.

- **Amministratore di applicazioni**: prende decisioni relative alla configurazione (specificando i parametri del modello di configurazione), alla distribuzione (eseguendo il mapping con le risorse disponibili) e alla qualità del servizio delle applicazioni. Un amministratore di applicazioni ad esempio decide le impostazioni locali della lingua (come inglese per gli Stati Uniti o giapponese per Giappone) dell'applicazione. Un'altra applicazione distribuita può avere impostazioni diverse.

- **Operatore**: distribuisce le applicazioni in base alla configurazione e ai requisiti specificati dall'amministratore di applicazioni. Un operatore ad esempio effettua il provisioning e la distribuzione dell'applicazione e verifica che sia in esecuzione in Azure. Gli operatori monitorano l'integrità e le informazioni sulle prestazioni delle applicazioni e gestiscono l'infrastruttura fisica a seconda delle esigenze.


## Sviluppare
1. Uno *sviluppatore di servizi* sviluppa diversi tipi di servizi usando il modello di programmazione [Reliable Actors](service-fabric-reliable-actors-introduction.md) o [Reliable Services](../service-fabric/service-fabric-reliable-services-introduction.md).
2. Uno *sviluppatore di servizi* descrive in modo dichiarativo i tipi di servizi sviluppati in un file manifesto del servizio da uno o più pacchetti di codice, configurazione e dati.
3. Uno *sviluppatore di applicazioni* compila quindi un'applicazione usando diversi tipi di servizi.
4. Uno *sviluppatore di applicazioni* descrive in modo dichiarativo il tipo di applicazione nel manifesto di un'applicazione facendo riferimento ai manifesti dei servizi costituenti ed eseguendo in modo appropriato l'override e la parametrizzazione delle diverse impostazioni di configurazione e distribuzione dei servizi costituenti.

Per gli esempi, vedere gli articoli relativi all'[introduzione ai modelli di programmazione Reliable Actors](service-fabric-reliable-actors-get-started.md) e all'[introduzione ai modelli di programmazione Reliable Services](service-fabric-reliable-services-quick-start.md).

## Distribuire
1. Un *amministratore di applicazioni* personalizza il tipo di applicazione in un'applicazione specifica da distribuire in un cluster di Service Fabric specificando i parametri appropriati dell'elemento **ApplicationType** nel manifesto dell'applicazione.

2. Un *operatore* carica il pacchetto applicazione nell’archivio immagini cluster usando il [**metodo CopyApplicationPackage**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) o il [**cmdlet Copy-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx). Il pacchetto applicazione contiene il manifesto dell'applicazione e la raccolta di pacchetti servizio. Service Fabric distribuisce le applicazioni dal relativo pacchetto archiviato nell’archivio immagini, che può essere un archivio BLOB di Azure o un servizio di sistema di Service Fabric.

3. L'*operatore* effettua quindi il provisioning del tipo di applicazione nel cluster di destinazione dal pacchetto dell'applicazione caricato mediante il metodo[** ProvisionApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), il cmdlet [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) o l'operazione REST di [**provisioning di un'applicazione**](https://msdn.microsoft.com/library/azure/dn707672.aspx).

4. Dopo aver effettuato il provisioning dell'applicazione, un *operatore* avvia l'applicazione con i parametri forniti dall'*amministratore di applicazioni* mediante il metodo [**CreateApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync.aspx), il cmdlet [**New-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx) o l'operazione [**Create Application REST**](https://msdn.microsoft.com/library/azure/dn707676.aspx).

5. Dopo la distribuzione dell'applicazione, un *operatore* usa il metodo [**CreateServiceAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.createserviceasync.aspx), il cmdlet [**New-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125874.aspx) o l'operazione REST di [**creazione di un servizio**](https://msdn.microsoft.com/library/azure/dn707657.aspx) per creare nuove istanze di servizi per l'applicazione in base ai tipi di servizio disponibili.

6. L'applicazione è ora in esecuzione nel cluster di Service Fabric.

Per gli esempi, vedere l'articolo relativo alla [distribuzione di un'applicazione](service-fabric-deploy-remove-applications.md).

## Test
1. Dopo la distribuzione nel cluster di sviluppo locale o in un cluster di test, uno *sviluppatore di servizi* esegue lo scenario di test di failover predefinito usando le classi [**FailoverTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenarioparameters.aspx) e [**FailoverTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenario.aspx) o il cmdlet [**Invoke-ServiceFabricFailoverTestScenario**](https://msdn.microsoft.com/library/azure/mt125935.aspx). Lo scenario di test di failover esegue un servizio specifico attraverso importanti transizioni e failover per verificare che sia sempre disponibile e funzionante.

2. Lo *sviluppatore di servizi* esegue quindi lo scenario di test CHAOS predefinito usando le classi [**ChaosTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenarioparameters.aspx) e [**ChaosTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenario.aspx) o il [**cmdlet Invoke-ServiceFabricChaosTestScenario**](https://msdn.microsoft.com/library/azure/mt126036.aspx). Lo scenario di test CHAOS induce nel cluster più errori casuali di nodi, pacchetti di codice e repliche.

Per gli esempi, vedere l'articolo relativo agli [scenari di testabilità](service-fabric-testability-scenarios.md).

## Aggiornare
1. Uno *sviluppatore di servizi* aggiorna i servizi costituenti dell'applicazione di cui sono state create istanze e/o corregge i bug e fornisce una nuova versione del manifesto del servizio.

2. Uno *sviluppatore di applicazioni* esegue l'override e la parametrizzazione delle impostazioni di configurazione e distribuzione dei servizi coerenti e fornisce una nuova versione del manifesto dell'applicazione. Lo sviluppatore di applicazioni incorpora quindi le nuove versioni dei manifesti dei servizi nell'applicazione e fornisce una nuova versione del tipo di applicazione in un pacchetto applicazione aggiornato.

3. Un *amministratore di applicazioni* incorpora la nuova versione del tipo di applicazione nell'applicazione di destinazione aggiornando i parametri appropriati.

4. Un *operatore* carica il pacchetto applicazione aggiornato nell’archivio immagini cluster usando il metodo [**CopyApplicationPackage**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) o il cmdlet [**Copy-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx). Il pacchetto applicazione contiene il manifesto dell'applicazione e la raccolta di pacchetti servizio.

5. Un *operatore* effettua il provisioning della nuova versione dell'applicazione nel cluster di destinazione usando il metodo [**ProvisionApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), il cmdlet [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) o l'operazione [**Provision an Application** REST](https://msdn.microsoft.com/library/azure/dn707672.aspx).

6. Un *operatore* aggiorna l'applicazione di destinazione alla nuova versione usando il metodo [**UpgradeApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.upgradeapplicationasync.aspx), il cmdlet [**Start-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx) o l'operazione REST di [**aggiornamento di un'applicazione**](https://msdn.microsoft.com/library/azure/dn707633.aspx).

7. Un *operatore* controlla lo stato dell'aggiornamento usando il metodo [**GetApplicationUpgradeProgressAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.getapplicationupgradeprogressasync.aspx), il cmdlet [**Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx) o l'operazione [**Get Application Upgrade Progress** REST](https://msdn.microsoft.com/library/azure/dn707631.aspx).

8. Se necessario, l'*operatore* modifica e riapplica i parametri dell'aggiornamento dell'applicazione corrente usando il metodo [**UpdateApplicationUpgradeAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.updateapplicationupgradeasync.aspx), il cmdlet [**Update-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt126030.aspx) o l'operazione [**Update Application Upgrade** REST](https://msdn.microsoft.com/library/azure/mt628489.aspx).

9. Se necessario, l'*operatore* esegue il rollback dell'aggiornamento dell'applicazione corrente usando il metodo [**RollbackApplicationUpgradeAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.rollbackapplicationupgradeasync.aspx), il cmdlet [**Start-ServiceFabricApplicationRollback**](https://msdn.microsoft.com/library/azure/mt125833.aspx) o l'operazione [**Rollback Application Upgrade** REST](https://msdn.microsoft.com/library/azure/mt628494.aspx).

10. Service Fabric aggiorna l'applicazione di destinazione in esecuzione nel cluster senza perdere la disponibilità dei servizi costituenti.

Per gli esempi, vedere l'articolo relativo all'[esercitazione sull'aggiornamento di un'applicazione](service-fabric-application-upgrade-tutorial.md).

## Effettuare la manutenzione
1. Per gli aggiornamenti e le patch del sistema operativo, Service Fabric interagisce con l'infrastruttura di Azure per garantire la disponibilità di tutte le applicazioni in esecuzione nel cluster.

2. Per gli aggiornamenti e le patch della piattaforma Service Fabric, Service Fabric si aggiorna automaticamente senza perdere la disponibilità delle applicazioni in esecuzione nel cluster.

3. Un *amministratore di applicazioni* approva l'aggiunta o la rimozione di nodi da un cluster dopo aver analizzato i dati cronologici di uso della capacità e la proiezione della domanda futura.

4. Un *operatore* aggiunge e rimuove i nodi specificati dall'*amministratore di applicazioni*.

5. Quando nel cluster vengono aggiunti nuovi nodi o rimossi nodi esistenti, Service Fabric esegue automaticamente il bilanciamento del carico delle applicazioni in esecuzione in tutti i nodi del cluster per ottenere prestazioni ottimali.

## Rimuovere
1. Un *operatore* può eliminare un'istanza specifica di un servizio in esecuzione nel cluster senza rimuovere l'intera applicazione usando il metodo [**DeleteServiceAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync.aspx), il cmdlet [**Remove-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt126033.aspx) o l'operazione [**Delete Service** REST](https://msdn.microsoft.com/library/azure/dn707687.aspx).  

2. Un *operatore* può anche eliminare un'istanza di un'applicazione e tutti i relativi servizi usando il metodo [**DeleteApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync.aspx), il cmdlet [**Remove-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125914.aspx) o l'operazione [**Delete Application** REST](https://msdn.microsoft.com/library/azure/dn707651.aspx).

3. Dopo l'arresto dell'applicazione e dei servizi, l'*operatore* può annullare il provisioning del tipo di applicazione usando il metodo [**UnprovisionApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync.aspx), il cmdlet [**Unregister-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125885.aspx) o l'operazione [**Unprovision an Application** REST](https://msdn.microsoft.com/library/azure/dn707671.aspx). L'annullamento del provisioning del tipo di applicazione non comporta la rimozione del pacchetto applicazione da ImageStore. Il pacchetto applicazione dovrà essere rimosso manualmente.

4. Un *operatore* rimuove il pacchetto applicazione da ImageStore usando il metodo [**RemoveApplicationPackage**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage.aspx) o il cmdlet [**Remove-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt163532.aspx).

Per gli esempi, vedere l'articolo relativo alla [distribuzione di un'applicazione](service-fabric-deploy-remove-applications.md).

## Passaggi successivi

Per altre informazioni sullo sviluppo, il test e la gestione di applicazioni e servizi di Service Fabric, vedere:

- [Reliable Actors](service-fabric-reliable-actors-introduction.md)
- [Reliable Services](../service-fabric/service-fabric-reliable-services-introduction.md)
- [Distribuire un'applicazione](service-fabric-deploy-remove-applications.md)
- [Aggiornamento dell'applicazione](service-fabric-application-upgrade.md)
- [Panoramica di Testabilità](service-fabric-testability-overview.md)
- [Esempio di ciclo di vita di un'applicazione basata su REST](service-fabric-rest-based-application-lifecycle-sample.md)

<!---HONumber=AcomDC_0128_2016-->