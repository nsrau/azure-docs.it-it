<properties
   pageTitle="Esercitazione sull’aggiornamento di un'applicazione di Infrastruttura di servizi | Microsoft Azure"
   description="In questo articolo vengono esaminati l'esperienza di distribuzione di un'applicazione di Infrastruttura di servizi, la modifica del codice e l’implementazione di un aggiornamento tramite Visual Studio."
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
   ms.date="07/17/2015"
   ms.author="subramar"/>



# Esercitazione sull'aggiornamento di un'applicazione di Infrastruttura di servizi tramite Visual Studio

Infrastruttura di servizi semplifica il processo di aggiornamento di applicazioni cloud garantendo che solo i servizi modificati vengano aggiornati e che lo stato delle applicazioni venga monitorato durante tutto il processo di aggiornamento e automaticamente esegua il rollsback dell'applicazione alla versione precedente se viene rilevato un problema. Gli aggiornamenti di un'applicazione di Infrastruttura di servizi sono *privi di tempi di inattività*, dal momento che l'applicazione può essere aggiornata senza tempi di inattività. In questa esercitazione viene illustrato come completare un semplice aggiornamento in sequenza da Visual Studio.


## Passaggio 1: creare e pubblicare l'esempio di Oggetti visivi

Questi passaggi possono essere eseguiti tramite il download dell'applicazione da github e aggiungendo i file **webgl-utils.js** e **gl-matrice-min.js** nel progetto, come indicato nel file leggimi dell'esempio. Senza questo l'applicazione non funzionerà. Dopo aver aggiunto questi file al progetto, compilare e pubblicare l'applicazione facendo clic con il pulsante destro del mouse sul progetto dell’applicazione, **VisualObjectsApplication** e selezionare il comando pubblica nella voce di menu di Infrastruttura di servizi come indicato di seguito.

![Menu di scelta rapida per un’applicazione di Infrastruttura di servizi][image1]

Viene visualizzata un'altra finestra popup, ed è possibile impostare l’**Endpoint della connessione** su **Cluster locale**. La finestra dovrebbe essere simile a quanto segue prima di fare clic su **Pubblica**.

![Pubblicazione di un'applicazione di Infrastruttura di servizi][image2]

Ora è possibile fare clic su pubblica nella finestra di dialogo. È ora possibile usare [Service Fabric Explorer per visualizzare il cluster e l'applicazione](service-fabric-visualizing-your-cluster.md). L'applicazione Oggetti visivi dispone di un servizio Web a cui è possibile accedere dal browser digitando [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) sulla barra degli indirizzi. Verranno visualizzati dieci oggetti visivi mobili sullo schermo.

## Passaggio 2: aggiornare l'esempio di oggetti visivi

È possibile osservare che con la versione distribuita al passaggio 1 gli oggetti visivi non ruotano. L'applicazione può essere aggiornata in modo che gli oggetti visivi ruotino.

Selezionare il progetto VisualObjects.ActorService nella soluzione VisualObjects e aprire il file StatefulVisualObjectActor.cs. All'interno del file, passare al metodo `MoveObject`, e impostare come commento `this.State.Move()` e rimuovere il commento `this.State.Move(true)`. Questa modifica fa ruotare gli oggetti dopo l’aggiornamento del servizio. A questo punto, è possibile compilare la soluzione (non ricompilarla) che compilerà i progetti modificati (se si seleziona Ricompila tutto, è necessario aggiornare le versioni di tutti i progetti).

È inoltre necessario specificare la versione dell'applicazione. È possibile utilizzare l’opzione **Modifica i file manifesto** di Visual Studio dopo aver fatto clic con il pulsante destro del mouse sulla soluzione per apportare le modifiche di versione. Viene visualizzata la finestra di dialogo per le versioni Edition come segue:

![Finestra di dialogo del controllo delle versioni][image3]

Selezionare la scheda **Modifica versioni manifesto** e aggiornare le versioni per i progetti modificati e i relativi pacchetti di codice, insieme all'applicazione alla versione 2.0.0. Dopo aver apportate le modifiche, il manifesto dovrebbe essere simile al seguente (le parti in grassetto indicano le modifiche):

![Aggiornamento di versioni][image4]

Gli strumenti di Visual Studio possono eseguire il rollup automatico delle versioni (selezionando **Aggiorna automaticamente le applicazioni e le versioni del servizio**) se si utilizza [SemVer](http://www.semver.org) è necessario aggiornare il codice e/o la versione del pacchetto config solo se tale opzione è selezionata. Salvare le modifiche e controllare la casella **Aggiorna l'applicazione**.


## Passaggio 3: Aggiornare l'applicazione

È consigliabile acquisire familiarità con i [parametri di aggiornamento dell'applicazione](service-fabric-application-upgrade-parameters.md) e con il [processo di aggiornamento](service-fabric-application-upgrade.md) per conoscere i diversi parametri di aggiornamento, valori di timeout e criteri di integrità applicati. Per questa procedura dettagliata, lasciare il criterio di valutazione dell’integrità del servizio sull’impostazione predefinita (modalità UnMonitored). È possibile impostarle selezionando **Configura le impostazioni di aggiornamento** e modificando i parametri in base alle esigenze.

È ora possibile avviare l'aggiornamento dell'applicazione selezionando **Pubblica**. Questo aggiorna l’applicazione alla versione 2.0.0 in cui gli oggetti ruotano. Si noterà che Infrastruttura di servizi consente di aggiornare un dominio di aggiornamento alla volta (alcuni oggetti verranno aggiornati per primi, seguiti da altri) e il servizio è accessibile durante questo periodo tramite il client (browser).


Ora, man mano che procede l'aggiornamento dell'applicazione, è possibile monitorarlo utilizzando Service Fabric Explorer (la scheda**Aggiornamenti in corso** sotto alle applicazioni).

In pochi minuti, tutti i domini di aggiornamento devono essere aggiornati (completati) e la finestra di output di Visual Studio dovrebbe indicare inoltre che l'aggiornamento è stato completato. A questo punto *tutti* gli oggetti visivi nella finestra del browser avranno iniziato a ruotare.

È possibile provare a cambiare le versioni e a passare dalla versione 2.0.0 alla versione 3.0.0 come esercitazione oppure addirittura dalla versione 2.0.0 alla versione 1.0.0. Approfittare anche per acquisire familiarità con i timeout e i criteri di integrità. Per la distribuzione in un cluster di Azure, i parametri usati saranno diversi da quelli usati per la distribuzione in un cluster locale. È consigliabile impostare i timeout su valori contenuti.


## Passaggi successivi

[Aggiornare l'applicazione tramite Powershell](service-fabric-application-upgrade-tutorial-powershell.md) illustra l'aggiornamento di un’applicazione tramite PowerShell.

Controllare l’aggiornamento dell'applicazione tramite [Parametri di aggiornamento](service-fabric-application-upgrade-parameters.md).

Rendere compatibili gli aggiornamenti dell'applicazione imparando a utilizzare [Serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md).

Informazioni su come utilizzare funzionalità avanzate durante l'aggiornamento dell'applicazione facendo riferimento a [Argomenti avanzati](service-fabric-application-upgrade-advanced.md).

Risolvere problemi comuni negli aggiornamenti dell'applicazione facendo riferimento ai passaggi elencati in [Risoluzione dei problemi relativi agli aggiornamenti dell'applicazione ](service-fabric-application-upgrade-troubleshooting.md).
 


[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png

<!---HONumber=Nov15_HO4-->