<properties
	pageTitle="Introduzione alle soluzioni preconfigurate IoT | Microsoft Azure"
	description="Seguire questa esercitazione per apprendere come distribuire una soluzione preconfigurata di Azure IoT Suite."
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# Esercitazione: Introduzione alle soluzioni preconfigurate

## Introduzione

Le [soluzioni preconfigurate ][lnk-preconfigured-solutions] di Azure IoT Suite combinano più servizi IoT di Azure per fornire soluzioni end-to-end che implementano scenari aziendali IoT comuni. La soluzione preconfigurata per il *monitoraggio remoto* si connette ai dispositivi e li monitora. È possibile usare la soluzione per analizzare il flusso di dati dai dispositivi e di migliorare i risultati aziendali facendo in modo che i processi rispondano automaticamente a quel flusso di dati.

Questa esercitazione illustra come effettuare il provisioning della soluzione preconfigurata per il monitoraggio remoto. Ne descrive anche le funzionalità di base. È possibile accedere a molte di queste funzionalità tramite il dashboard distribuito insieme alla soluzione preconfigurata:

![Dashboard della soluzione preconfigurata per il monitoraggio remoto][img-dashboard]

Per completare l'esercitazione, è necessaria una sottoscrizione di Azure attiva.

> [AZURE.NOTE]  Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk_free_trial].

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## Visualizzare il dashboard della soluzione

Il dashboard della soluzione consente di gestire la soluzione distribuita. Ad esempio, è possibile visualizzare dati di telemetria, aggiungere dispositivi e configurare regole.

1.  Al termine del provisioning quando il riquadro della soluzione preconfigurata indica **Pronto**, fare clic su **Avvia** per aprire il portale della soluzione di monitoraggio remoto in una nuova scheda.

    ![Avviare la soluzione preconfigurata][img-launch-solution]

2.  Per impostazione predefinita, il portale della soluzione visualizza il *dashboard della soluzione*. È possibile selezionare altre viste usando il menu a sinistra.

    ![Dashboard della soluzione preconfigurata per il monitoraggio remoto][img-dashboard]

Il dashboard visualizza le informazioni seguenti:

- La mappa visualizza la posizione di ogni dispositivo connesso alla soluzione. Quando si esegue la soluzione per la prima volta, sono disponibili quattro dispositivi simulati. I dispositivi simulati vengono implementati come Processi Web di Azure e la soluzione usa l'API Bing Maps per tracciare le informazioni sulla mappa.
- Il pannello **Cronologia telemetria** traccia la telemetria di umidità e temperatura da un dispositivo selezionato in tempo quasi reale e visualizza i dati aggregati, ad esempio l'umidità massima, minima e media.
- Il pannello **Cronologia avvisi** mostra gli eventi di avviso recenti relativi a quando un valore di telemetria ha superato una soglia. È possibile definire i propri avvisi oltre agli esempi creati dalla soluzione preconfigurata.

## Visualizzare l'elenco dei dispositivi

L'elenco dei dispositivi mostra tutti i dispositivi registrati nella soluzione. Permette di visualizzare e modificare metadati dei dispositivi, aggiungere o rimuovere dispositivi e inviare comandi ai dispositivi.

1.  Fare clic su **Dispositivi** nel menu a sinistra per passare all'*elenco dei dispositivi* per questa soluzione.

    ![Elenco dei dispositivi nel dashboard][img-devicelist]

2.  L'elenco di dispositivi mostra che sono presenti quattro dispositivi simulati creati dal processo di provisioning.

3.  Fare clic su un dispositivo nell'elenco dei dispositivi per visualizzarne i dettagli.

    ![Dettagli del dispositivo nel dashboard][img-devicedetails]

Il pannello **Dettagli dispositivo** contiene tre sezioni:

- La sezione **Azioni** elenca le azioni che è possibile eseguire sul dispositivo. Se si disabilita il dispositivo, non è più consentito inviare la telemetria o ricevere comandi. Se si disabilita un dispositivo, sarà poi possibile abilitarlo di nuovo. È possibile aggiungere una regola associata al dispositivo, che attiva un avviso quando un valore della telemetria supera una soglia. È anche possibile inviare un comando a un dispositivo. Quando un dispositivo si connette per la prima volta, comunica alla soluzione i comandi a cui può rispondere.
- La sezione **Proprietà dispositivo** elenca i metadati del dispositivo. Alcuni di questi metadati provengono dal dispositivo stesso (ad esempio, il produttore), altri invece vengono generati dalla soluzione (ad esempio, l'ora di creazione). È possibile modificare i metadati del dispositivo da qui.
- La sezione **Chiavi di autenticazione** elenca le chiavi che il dispositivo può usare per l'autenticazione con la soluzione.

## Inviare un comando a un dispositivo

Il riquadro dei dettagli del dispositivo mostra tutti i comandi supportati da un dispositivo specifico e consente di inviare comandi a un dispositivo. Quando un dispositivo viene avviato per la prima volta, invia alla soluzione informazioni sui comandi che supporta.

1.  Fare clic su **Comandi** nel riquadro dei dettagli del dispositivo selezionato.

    ![Comandi del dispositivo nel dashboard][img-devicecommands]

2.  Selezionare **PingDevice** dall'elenco dei comandi.

3.  Fare clic su **Invia comando**.

4.  È possibile visualizzare lo stato del comando nella cronologia dei comandi.

    ![Stato dei comandi nel dashboard][img-pingcommand]

La soluzione tiene traccia dello stato di ogni comando inviato. All'inizio il risultato è **In sospeso**. Quando il dispositivo segnala che il comando è stato eseguito, il risultato viene impostato su **Operazione riuscita**.

## Aggiungere un nuovo dispositivo simulato

Quando si distribuisce la soluzione preconfigurata, viene effettuato automaticamente il provisioning di quattro dispositivi di esempio visibili nell'elenco dei dispositivi. Si tratta di *dispositivi simulati* in esecuzione in un processo Web di Azure. I dispositivi simulati consentono di provare facilmente la soluzione preconfigurata senza la necessità di distribuire dispositivi fisici reali. Per connettere un dispositivo reale alla soluzione, vedere l'esercitazione [Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto][lnk-connect-rm].

La procedura seguente illustra come aggiungere un dispositivo simulato alla soluzione:

1.  Tornare all'elenco dei dispositivi.

2.  Fare clic su **+ Aggiungi dispositivo** nell'angolo in basso a sinistra per aggiungere un dispositivo.

    ![Aggiungere un dispositivo alla soluzione preconfigurata][img-adddevice]

3.  Fare clic su **Aggiungi nuovo** nel riquadro **Dispositivo simulato**.

    ![Impostare i dettagli del nuovo dispositivo nel dashboard][img-addnew]
    
    Oltre a creare un nuovo dispositivo simulato, è anche possibile aggiungere un dispositivo fisico se si sceglie di creare un **dispositivo personalizzato**. Per altre informazioni sulla connessione di dispositivi fisici alla soluzione, vedere [Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto IoT Suite][lnk-connect-rm].

4.  Selezionare **Definire l'ID dispositivo** e aggiungere un nome di ID dispositivo univoco, ad esempio **mydevice\_01**.

5.  Fare clic su **Crea**.

    ![Salvare un nuovo dispositivo][img-definedevice]

6. Nel passaggio 3 della procedura **Aggiungi un dispositivo simulato** fare clic su **Operazione completata** per tornare all'elenco dei dispositivi.

7. Verificare che lo stato del dispositivo nell'elenco sia **In esecuzione**.

    ![Visualizzare il nuovo dispositivo nell'elenco dei dispositivi][img-runningnew]

8. È anche possibile visualizzare la telemetria simulata dal nuovo dispositivo nel dashboard:

    ![Visualizzare i dati di telemetria dal nuovo dispositivo][img-runningnew-2]

## Modificare i metadati del dispositivo

Quando un dispositivo si connette per la prima volta alla soluzione, invia i propri metadati alla soluzione. Quando si modificano i metadati del dispositivo nel dashboard della soluzione, i nuovi valori dei metadati vengono inviati al dispositivo e archiviati nel database DocumentDB della soluzione. Per altre informazioni, vedere [Registro delle identità dei dispositivi e DocumentDB][lnk-devicemetadata].

1.  Tornare all'elenco dei dispositivi.

2.  Selezionare il nuovo dispositivo in **Elenco dei dispositivi** e quindi fare clic su **Modifica** per modificare **Proprietà dispositivo**:

    ![Modificare i metadati del dispositivo][img-editdevice]

3. Scorrere verso il basso e modificare i valori di latitudine e longitudine. Fare quindi clic su **Salva le modifiche al Registro di sistema del dispositivo**.

    ![Modificare i metadati del dispositivo][img-editdevice2]

4. Tornare al dashboard e notare che la posizione del dispositivo sulla mappa è cambiata:

    ![Modificare i metadati del dispositivo][img-editdevice3]

## Aggiungere una regola per il nuovo dispositivo

Non sono presenti regole per il nuovo dispositivo appena aggiunto. In questa sezione si aggiunge una regola che attiva un avviso quando la temperatura segnalata dal nuovo dispositivo supera i 47 gradi. Prima di iniziare, si noti che la cronologia della telemetria per il nuovo dispositivo nel dashboard indica che la temperatura del dispositivo non supera mai i 45 gradi.

1.  Tornare all'elenco dei dispositivi.

2.  Selezionare il nuovo dispositivo in **Elenco dei dispositivi** e quindi fare clic su **Aggiungi regola** per aggiungere una regola per il dispositivo.

3. Creare una regola che usa **Temperature** come campo dati e **AlarmTemp** come output quando la temperatura supera i 47 gradi:

    ![Aggiungere una regola per il dispositivo][img-adddevicerule]

4. Fare clic su **Salva e visualizza regole** per salvare le modifiche.

5.  Fare clic su **Comandi** nel riquadro dei dettagli del nuovo dispositivo.

    ![Aggiungere una regola per il dispositivo][img-adddevicerule2]

6.  Selezionare **ChangeSetPointTemp** nell'elenco dei comandi e impostare **SetPointTemp** su 45. Fare quindi clic su **Invia comando**:

    ![Aggiungere una regola per il dispositivo][img-adddevicerule3]

7.  Tornare al dashboard della soluzione. Poco dopo verrà visualizzata una nuova voce nel riquadro **Cronologia avvisi** quando la temperatura segnalata dal nuovo dispositivo supera la soglia di 47 gradi:

    ![Aggiungere una regola per il dispositivo][img-adddevicerule4]

8. È possibile rivedere e modificare tutte le regole nella pagina **Regole** del dashboard:

    ![Elenco delle regole per il dispositivo][img-rules]

9. È possibile rivedere e modificare tutte le azioni che possono essere eseguite in risposta a una regola nella pagina **Azioni** del dashboard:

    ![Elenco delle azioni per il dispositivo][img-actions]

> [AZURE.NOTE] È possibile definire azioni che possono inviare un messaggio di posta elettronica o un SMS in risposta a una regola oppure eseguire un'integrazione con un sistema line-of-business tramite un'[app per la logica][lnk-logic-apps]. Per altre informazioni, vedere [Connettere l'app per la logica alla soluzione preconfigurata per il monitoraggio remoto Azure IoT Suite][lnk-logicapptutorial].

## Altre funzionalità

Tramite il portale della soluzione è possibile cercare i dispositivi con caratteristiche specifiche, ad esempio un numero di modello:

![Cercare un dispositivo][img-search]

È possibile disabilitare un dispositivo e dopo aver disabilitato è possibile rimuoverlo:

![Disabilitare e rimuovere un dispositivo][img-disable]

## Dietro le quinte

Quando si distribuisce una soluzione preconfigurata, il processo di distribuzione crea più risorse nella sottoscrizione di Azure selezionata. È possibile visualizzare queste risorse nel [portale][lnk-portal] di Azure. Il processo di distribuzione crea un **gruppo di risorse** con un nome basato sul nome scelto per la soluzione preconfigurata:

![Soluzione preconfigurata nel portale di Azure][img-portal]

È possibile visualizzare le impostazioni di ogni risorsa selezionandola nell'elenco di risorse nel gruppo di risorse.

È anche possibile visualizzare il codice sorgente per la soluzione preconfigurata. Il codice sorgente della soluzione preconfigurata per il monitoraggio remoto si trova nel repository GitHub [azure-iot-remote-monitoring][lnk-rmgithub]\:

- La cartella **DeviceAdministration** contiene il codice sorgente per il dashboard.
- La cartella **Simulator** contiene il codice sorgente per il dispositivo simulato.
- La cartella **EventProcessor** contiene il codice sorgente per il processo back-end che gestisce la telemetria in ingresso.

Al termine, è possibile eliminare la soluzione preconfigurata dalla sottoscrizione di Azure nel sito [azureiotsuite.com][lnk-azureiotsuite]. Questo sito consente di eliminare facilmente tutte le risorse di cui è stato effettuato il provisioning quando si è creata la soluzione preconfigurata.

> [AZURE.NOTE] Per assicurarsi di eliminare tutti gli elementi correlati alla soluzione preconfigurata, eliminarli dal sito [azureiotsuite.com][lnk-azureiotsuite] e non limitarsi a eliminare il gruppo di risorse nel portale.

## Passaggi successivi

Dopo aver distribuito una soluzione preconfigurata è possibile proseguire con l'introduzione a IoT Suite vedendo gli articoli seguenti:

- [Procedura dettagliata della soluzione preconfigurata per il monitoraggio remoto][lnk-rm-walkthrough]
- [Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto][lnk-connect-rm]
- [Autorizzazioni per il sito azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-search]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_07.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md

<!---HONumber=AcomDC_0817_2016-->