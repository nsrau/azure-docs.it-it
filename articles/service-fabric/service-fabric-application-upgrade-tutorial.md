---
title: Esercitazione sull'aggiornamento di un'app di Service Fabric | Documentazione Microsoft
description: Questo articolo esamina l'esperienza di distribuzione di un'applicazione di Service Fabric, la modifica del codice e l'implementazione di un aggiornamento tramite Visual Studio.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: a3181a7a-9ab1-4216-b07a-05b79bd826a4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 3388257399ce8d2e0ac84b9bff746e1acf153312
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Esercitazione sull'aggiornamento di un'applicazione di Service Fabric tramite Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Azure Service Fabric semplifica il processo di aggiornamento delle applicazioni cloud garantendo che solo i servizi modificati vengano aggiornati e che l'integrità delle applicazioni venga monitorata durante l'intero processo di aggiornamento. Se rileva un problema, esegue automaticamente il rollback dell'applicazione alla versione precedente. Gli aggiornamenti delle applicazioni di Service Fabric, inoltre, sono *privi di tempi di inattività*. Questa esercitazione illustra come completare un aggiornamento in sequenza da Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Passaggio 1: creare e pubblicare l'applicazione Oggetti visivi di esempio
Innanzitutto scaricare l'applicazione [Visual Objects](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) da GitHub. Quindi compilare e pubblicare l'applicazione facendo clic con il pulsante destro del mouse sul progetto dell'applicazione, **VisualObjects**, e selezionando il comando **Pubblica** dalla voce di menu Service Fabric.

![Menu di scelta rapida per un'applicazione di Service Fabric][image1]

Selezionando **Pubblica** viene visualizzata una finestra popup in cui è possibile impostare il **Profilo di destinazione** su **PublishProfiles\Local.xml**. Fare clic su **Pubblica**: la finestra avrà un aspetto simile all'immagine seguente.

![Pubblicazione di un'applicazione di Service Fabric][image2]

Fare clic su **Pubblica** nella finestra di dialogo. È ora possibile usare [Service Fabric Explorer per visualizzare il cluster e l'applicazione](service-fabric-visualizing-your-cluster.md). L'applicazione Visual Objects include un servizio Web a cui è possibile accedere digitando [http://localhost:8081/visualobjects/](http://localhost:8081/visualobjects/) sulla barra degli indirizzi del browser.  Sullo schermo verranno visualizzati dieci oggetti visivi mobili.

**NOTA:** in caso di distribuzione nel profilo `Cloud.xml` (Azure Service Fabric), l'applicazione deve quindi essere disponibile all'indirizzo **http://{NomeServiceFabric}.{Area}.cloudapp.azure.com:8081/visualobjects/**. Assicurarsi che `8081/TCP` sia stato configurato nel servizio di bilanciamento del carico (il servizio di bilanciamento del carico si trova nello stesso gruppo di risorse dell'istanza di Service Fabric).

## <a name="step-2-update-the-visual-objects-sample"></a>Passaggio 2: aggiornare l'applicazione Oggetti visivi di esempio
È possibile osservare come con la versione distribuita al passaggio 1 gli oggetti visivi non ruotino. L'applicazione può essere aggiornata in modo che gli oggetti visivi ruotino.

Selezionare il progetto VisualObjects.ActorService nella soluzione VisualObjects e aprire il file **VisualObjectActor.cs** . All'interno del file passare al metodo `MoveObject`, impostare come commento `visualObject.Move(false)` e rimuovere il commento `visualObject.Move(true)`. Questa modifica del codice fa ruotare gli oggetti dopo l'aggiornamento del servizio.  **A questo punto è possibile compilare la soluzione (non ricompilarla)**, in modo che vengano compilati solo i progetti modificati. Se si seleziona *Ricompila tutto*, è necessario aggiornare le versioni di tutti i progetti.

È inoltre necessario specificare la versione dell'applicazione. Per apportare le modifiche di versione, fare clic con il pulsante destro del mouse sul progetto **VisualObjects** e scegliere l'opzione **Modifica versioni del manifesto** di Visual Studio. Selezionando questa opzione viene visualizzata la finestra di dialogo per le versioni dell'edizione come segue:

![Finestra di dialogo del controllo delle versioni][image3]

Aggiornare le versioni per i progetti modificati, i relativi pacchetti di codice e l'applicazione alla versione 2.0.0. Dopo aver apportate le modifiche, il manifesto dovrebbe essere simile al seguente (le parti in grassetto indicano le modifiche):

![Aggiornamento di versioni][image4]

Gli strumenti di Visual Studio possono eseguire il rollup automatico delle versioni quando si seleziona l'opzione **Aggiorna automaticamente le versioni di applicazioni e servizi**. Se si usa [SemVer](http://www.semver.org)è necessario aggiornare il codice e/o solo la versione del pacchetto di configurazione se è selezionata questa opzione.

Salvare le modifiche e controllare la casella **Aggiorna l'applicazione** .

## <a name="step-3--upgrade-your-application"></a>Passaggio 3: Aggiornare l'applicazione
È consigliabile acquisire familiarità con i [parametri di aggiornamento dell'applicazione](service-fabric-application-upgrade-parameters.md) e con il [processo di aggiornamento](service-fabric-application-upgrade.md) per conoscere i vari parametri di aggiornamento, i valori di timeout e i criteri di integrità applicabili. Per questa procedura dettagliata si usa l'impostazione predefinita per il criterio di valutazione dell'integrità del servizio (modalità UnMonitored). È possibile configurare queste impostazioni selezionando l'opzione **Configura le impostazioni di aggiornamento** e modificando i parametri in base alle esigenze.

È ora possibile avviare l'aggiornamento dell'applicazione selezionando il pulsante **Pubblica**. L'applicazione viene aggiornata alla versione 2.0.0, che supporta la rotazione degli oggetti. Service Fabric aggiorna un dominio di aggiornamento alla volta (alcuni oggetti vengono aggiornati prima, altri dopo) e durante l'aggiornamento il servizio rimane accessibile. L'accesso al servizio può essere controllato tramite il client (browser).  

È ora possibile monitorare lo stato di avanzamento dell'aggiornamento dell'applicazione tramite Service Fabric Explorer, usando la scheda **Upgrades in Progress** (Aggiornamenti in corso) sotto le applicazioni.

In pochi minuti tutti i domini di aggiornamento devono essere aggiornati (completati) e la finestra di output di Visual Studio deve inoltre indicare che l'aggiornamento è stato completato. A questo punto *tutti* gli oggetti visivi nella finestra del browser avranno iniziato a ruotare.

Come esercitazione, è possibile provare a cambiare le versioni e a passare dalla versione 2.0.0 alla versione 3.0.0 oppure addirittura dalla versione 2.0.0 alla versione 1.0.0. Esercitarsi inoltre con i timeout e i criteri di integrità per acquisire familiarità anche con questi concetti. Quando si esegue la distribuzione in un cluster di Azure, invece che in un cluster locale, i parametri usati possono essere diversi. È consigliabile impostare i timeout su valori contenuti.

## <a name="next-steps"></a>Passaggi successivi
[Aggiornamento di un'applicazione di Service Fabric mediante PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) descrive la procedura di aggiornamento di un'applicazione tramite PowerShell.

Controllare l'aggiornamento dell'applicazione tramite i [parametri di aggiornamento](service-fabric-application-upgrade-parameters.md).

Rendere compatibili gli aggiornamenti dell'applicazione imparando a usare la [serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md).

Per informazioni su come usare funzionalità avanzate durante l'aggiornamento dell'applicazione, vedere [Argomenti avanzati](service-fabric-application-upgrade-advanced.md).

Per informazioni su come risolvere problemi comuni negli aggiornamenti dell'applicazione, vedere i passaggi indicati in [Risoluzione dei problemi relativi agli aggiornamenti dell'applicazione](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
