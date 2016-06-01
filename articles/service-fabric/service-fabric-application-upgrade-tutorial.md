 <properties
   pageTitle="Esercitazione sull'aggiornamento di un'app di Service Fabric | Microsoft Azure"
   description="Questo articolo esamina l'esperienza di distribuzione di un'applicazione di Service Fabric, la modifica del codice e l'implementazione di un aggiornamento tramite Visual Studio."
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
   ms.date="05/13/2016"
   ms.author="subramar"/>



# Esercitazione sull'aggiornamento di un'applicazione di Service Fabric tramite Visual Studio

Azure Service Fabric semplifica il processo di aggiornamento delle applicazioni cloud garantendo che solo i servizi modificati vengano aggiornati e che l'integrità delle applicazioni venga monitorata durante l'intero processo di aggiornamento. Se rileva un problema, esegue automaticamente il rollback dell'applicazione alla versione precedente. Gli aggiornamenti delle applicazioni di Service Fabric, inoltre, sono *privi di tempi di inattività*. In questa esercitazione viene illustrato come completare un semplice aggiornamento in sequenza da Visual Studio.


## Passaggio 1: creare e pubblicare l'applicazione Oggetti visivi di esempio

È possibile eseguire questa procedura scaricando l'applicazione [Visual Objects](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Actors/VisualObjects) da GitHub. Compilare e pubblicare l'applicazione facendo clic con il pulsante destro del mouse sul progetto dell'applicazione **VisualObjects** e quindi selezionare il comando **Pubblica** nella voce di menu di Service Fabric, come indicato di seguito.

![Menu di scelta rapida per un'applicazione di Service Fabric][image1]

Verrà visualizzata un'altra finestra popup in cui è possibile impostare il **Profilo di destinazione** su **PublishProfiles\\Local.xml**. Prima di fare clic su **Pubblica**, la finestra avrà un aspetto simile all'immagine seguente.

![Pubblicazione di un'applicazione di Service Fabric][image2]

Fare clic su **Pubblica** nella finestra di dialogo. È ora possibile usare [Service Fabric Explorer per visualizzare il cluster e l'applicazione](service-fabric-visualizing-your-cluster.md). L'applicazione Visual Objects include un servizio Web a cui è possibile accedere digitando [http://localhost:8082/visualobjects/](http://localhost:8082/visualobjects/) sulla barra degli indirizzi del browser. Sullo schermo verranno visualizzati dieci oggetti visivi mobili.

## Passaggio 2: aggiornare l'applicazione Oggetti visivi di esempio

È possibile osservare come con la versione distribuita al passaggio 1 gli oggetti visivi non ruotino. L'applicazione può essere aggiornata in modo che gli oggetti visivi ruotino.

Selezionare il progetto VisualObjects.ActorService nella soluzione VisualObjects e aprire il file **VisualObjectActor.cs**. All'interno del file passare al metodo `MoveObject`, impostare come commento `visualObject.Move(false)` e rimuovere il commento `visualObject.Move(true)`. Questa modifica fa ruotare gli oggetti dopo l'aggiornamento del servizio. **A questo punto, è possibile compilare la soluzione (non ricompilarla)**, in modo che vengano compilati solo i progetti modificati. Se si seleziona *Ricompila tutto*, è necessario aggiornare le versioni di tutti i progetti.

È inoltre necessario specificare la versione dell'applicazione. Per apportare le modifiche di versione, fare clic con il pulsante destro del mouse sul progetto **VisualObjects** e scegliere l'opzione **Modifica versioni del manifesto** di Visual Studio. Viene visualizzata la finestra di dialogo per le versioni Edition come segue:

![Finestra di dialogo del controllo delle versioni][image3]

Aggiornare le versioni per i progetti modificati, i relativi pacchetti di codice e l'applicazione alla versione 2.0.0. Dopo aver apportate le modifiche, il manifesto dovrebbe essere simile al seguente (le parti in grassetto indicano le modifiche):

![Aggiornamento di versioni][image4]

Gli strumenti di Visual Studio possono eseguire il rollup automatico delle versioni se è stata selezionata l'opzione **Aggiorna automaticamente le versioni di applicazioni e servizi**. Se si usa [SemVer](http://www.semver.org), è necessario aggiornare la versione dei pacchetti di codice e/o di configurazione solo se è stata selezionata questa opzione.

Salvare le modifiche e controllare la casella **Aggiorna l'applicazione**.


## Passaggio 3: Aggiornare l'applicazione

È consigliabile acquisire familiarità con i [parametri di aggiornamento dell'applicazione](service-fabric-application-upgrade-parameters.md) e con il [processo di aggiornamento](service-fabric-application-upgrade.md) per conoscere i vari parametri di aggiornamento, i valori di timeout e i criteri di integrità applicabili. Per questa procedura dettagliata, lasciare il criterio di valutazione dell'integrità del servizio sull'impostazione predefinita (modalità UnMonitored). È possibile configurare queste impostazioni selezionando l'opzione **Configura le impostazioni di aggiornamento** e modificando i parametri in base alle esigenze.

È ora possibile avviare l'aggiornamento dell'applicazione selezionando il pulsante **Pubblica**. L'applicazione verrà aggiornata alla versione 2.0.0, che supporta la rotazione degli oggetti. Si noterà che Service Fabric esegue l'aggiornamento di un dominio di aggiornamento per volta (alcuni oggetti verranno aggiornati prima, altri dopo) e che, durante questo intervallo di tempo, il servizio rimane accessibile tramite il client (browser).


È ora possibile monitorare lo stato di avanzamento dell'aggiornamento dell'applicazione tramite Service Fabric Explorer, usando la scheda **Upgrades in Progress** (Aggiornamenti in corso) sotto le applicazioni.

In pochi minuti tutti i domini di aggiornamento devono essere aggiornati (completati) e la finestra di output di Visual Studio deve inoltre indicare che l'aggiornamento è stato completato. A questo punto, *tutti* gli oggetti visivi nella finestra del browser avranno iniziato a ruotare.

Come esercitazione, è possibile provare a cambiare le versioni e a passare dalla versione 2.0.0 alla versione 3.0.0 oppure addirittura dalla versione 2.0.0 alla versione 1.0.0. Esercitarsi inoltre con i timeout e i criteri di integrità per acquisire familiarità anche con questi concetti. Per la distribuzione in un cluster di Azure, i parametri usati saranno diversi da quelli usati per la distribuzione in un cluster locale. È consigliabile, ad esempio, impostare i timeout su valori contenuti.


## Passaggi successivi

[Aggiornamento di un'applicazione di Service Fabric mediante PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) descrive la procedura di aggiornamento di un'applicazione tramite PowerShell.

Controllare l'aggiornamento dell'applicazione tramite i [parametri di aggiornamento](service-fabric-application-upgrade-parameters.md).

Rendere compatibili gli aggiornamenti dell'applicazione imparando a usare la [serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md).

Informazioni su come usare funzionalità avanzate durante l'aggiornamento dell'applicazione facendo riferimento ad [Argomenti avanzati](service-fabric-application-upgrade-advanced.md).

Risolvere problemi comuni negli aggiornamenti dell'applicazione facendo riferimento ai passaggi elencati in [Risolvere i problemi relativi agli aggiornamenti delle applicazioni](service-fabric-application-upgrade-troubleshooting.md).



[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png

<!---HONumber=AcomDC_0518_2016-->