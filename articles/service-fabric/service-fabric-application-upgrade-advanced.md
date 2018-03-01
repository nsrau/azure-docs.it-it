---
title: Argomenti avanzati sull'aggiornamento di un'applicazione | Documentazione Microsoft
description: Questo articolo illustra alcuni degli argomenti avanzati relativi all'aggiornamento di un'applicazione di Service Fabric.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar;chackdan
ms.openlocfilehash: 1bb3a5e215e3a664cf67727255840696c7f58e8c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Aggiornamento di un'applicazione di Service Fabric: argomenti avanzati
## <a name="adding-or-removing-service-types-during-an-application-upgrade"></a>Aggiunta o rimozione di tipi di servizio durante l'aggiornamento di un'applicazione
Se si aggiunge un nuovo tipo di servizio a un'applicazione pubblicata nel corso di un aggiornamento, il nuovo tipo di servizio viene aggiunto all'applicazione distribuita. Tale aggiornamento non influisce sulle istanze del servizio che facevano già parte dell'applicazione, ma è necessario creare un'istanza del tipo di servizio che è stato aggiunto per consentire l'attivazione del nuovo tipo di servizio (vedere [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Analogamente, i tipi di servizio possono essere rimossi da un'applicazione durante un aggiornamento. Tuttavia, prima di procedere con l'aggiornamento è necessario rimuovere tutte le istanze del tipo di servizio da rimuovere (vedere [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

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

## <a name="rolling-back-application-upgrades"></a>Rollback degli aggiornamenti dell'applicazione

Mentre il roll forward degli aggiornamenti può essere eseguito in tre modalità (*Monitored*, *UnmonitoredAuto* o *UnmonitoredManual*), il rollback può essere eseguito solo in modalità *UnmonitoredAuto* o *UnmonitoredManual*. Il rollback in modalità *UnmonitoredAuto* funziona esattamente come il roll forward, tranne per il fatto che il valore predefinito di *UpgradeReplicaSetCheckTimeout* è diverso. Vedere [Parametri di aggiornamento di un'applicazione](service-fabric-application-upgrade-parameters.md). Il rollback in modalità *UnmonitoredManual* funziona esattamente come il roll forward. Il rollback viene sospeso automaticamente al termine di ogni UD e, per continuare, deve essere ripreso in modo esplicito usando [ Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps).

Le operazioni di rollback possono essere attivate automaticamente quando non vengono rispettati i criteri di integrità di un aggiornamento in modalità *Monitored* con *FailureAction* impostato su *Rollback* (vedere [Parametri di aggiornamento di un'applicazione](service-fabric-application-upgrade-parameters.md)) oppure in modo esplicito usando [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Durante il rollback, il valore di *UpgradeReplicaSetCheckTimeout* e la modalità possono essere comunque modificati in qualsiasi momento usando [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Passaggi successivi
[Esercitazione sull'aggiornamento di un'applicazione di Service Fabric tramite Visual Studio](service-fabric-application-upgrade-tutorial.md) descrive la procedura di aggiornamento di un'applicazione con Visual Studio.

[Aggiornamento di un'applicazione di Service Fabric mediante PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) descrive la procedura di aggiornamento di un'applicazione tramite PowerShell.

Controllare l’aggiornamento dell'applicazione tramite [Parametri di aggiornamento](service-fabric-application-upgrade-parameters.md).

Rendere compatibili gli aggiornamenti dell'applicazione imparando a usare [Serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md).

Per informazioni su come risolvere problemi comuni negli aggiornamenti dell'applicazione, vedere i passaggi indicati in [Risoluzione dei problemi relativi agli aggiornamenti dell'applicazione](service-fabric-application-upgrade-troubleshooting.md).
