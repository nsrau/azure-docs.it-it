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
     ms.date="03/02/2016"
     ms.author="dobett"/>

# Esercitazione: Introduzione alle soluzioni preconfigurate IoT

## Introduzione

Le [soluzioni preconfigurate ][lnk-preconfigured-solutions] di Azure IoT Suite combinano più servizi IoT di Azure per fornire soluzioni end-to-end che implementano scenari aziendali IoT comuni.

Questa esercitazione illustra come eseguire il provisioning della soluzione preconfigurata di *monitoraggio remoto* e ne descrive le funzionalità di base.

Per completare l'esercitazione, è necessaria una sottoscrizione di Azure attiva.

> [AZURE.NOTE]  Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk_free_trial].

## Effettuare il provisioning della soluzione preconfigurata di monitoraggio remoto

1.  Accedere a [azureiotsuite.com][lnk-azureiotsuite] con le credenziali dell'account Azure e fare clic su **+** per creare una nuova soluzione.

    > [AZURE.NOTE] Per informazioni aggiuntive in caso di problemi con le autorizzazioni necessarie per effettuare il provisioning di una soluzione, vedere [Autorizzazioni per il sito azureiotsuite.com][lnk-permissions].

2.  Fare clic su **Seleziona** nel riquadro **Monitoraggio remoto**.

3.  Immettere un valore in **Nome soluzione** per la soluzione preconfigurata di monitoraggio remoto.

4.  Selezionare l'**Area** e la **Sottoscrizione** da usare per effettuare il provisioning della soluzione.

5.  Fare clic su **Crea soluzione** per iniziare il processo di provisioning. In genere l'esecuzione del processo richiede alcuni minuti.

## Attendere il completamento del processo di provisioning.

1. Fare clic sul riquadro per la soluzione con stato **Provisioning**.
 
2. Notare gli stati **Provisioning** man mano che i servizi di Azure vengono distribuiti nella sottoscrizione di Azure.

3. Al termine del provisioning lo stato cambia in **Pronto**.

4. Fare clic sul riquadro per visualizzare i dettagli della soluzione nel riquadro di destra.

> [AZURE.NOTE] Se si verificano problemi con la distribuzione della soluzione preconfigurata, vedere [Autorizzazioni per il sito azureiotsuite.com][lnk-permissions] e le [domande frequenti][lnk-faq]. Se i problemi persistono, creare un ticket di servizio nel [portale][lnk-portal].

Se ci sono dettagli importanti non elencati per la soluzione, è possibile segnalarlo e inviare suggerimenti sulle funzionalità usando il [forum di Azure IoT](https://feedback.azure.com/forums/321918-azure-iot).

## Visualizzare il dashboard della soluzione di monitoraggio remoto

Il dashboard della soluzione consente di gestire la soluzione distribuita. Ad esempio, è possibile visualizzare dati di telemetria, aggiungere dispositivi e configurare regole.

1.  Al termine del provisioning quando il riquadro della soluzione preconfigurata indica **Pronto**, fare clic su **Avvia** per aprire il portale della soluzione di monitoraggio remoto in una nuova scheda.

    ![][img-launch-solution]

2.  Per impostazione predefinita, il portale della soluzione visualizza il *dashboard della soluzione*. È possibile selezionare altre viste usando il menu a sinistra.

    ![][img-dashboard]

Il dashboard visualizza le informazioni seguenti:

- La mappa visualizza la posizione di ogni dispositivo connesso alla soluzione. Quando si esegue la soluzione per la prima volta, sono disponibili quattro dispositivi simulati. I dispositivi simulati vengono implementati come Processi Web di Azure e la soluzione usa l'API Bing Maps per tracciare le informazioni sulla mappa.
- Il pannello **Telemetry History** traccia la telemetria di umidità e temperatura da un dispositivo selezionato in tempo quasi reale e visualizza i dati aggregati, ad esempio l'umidità massima, minima e media.
- Il pannello **Alarm History** mostra gli eventi di avviso recenti relativi a quando un valore di telemetria ha superato una soglia. È possibile definire i propri avvisi oltre agli esempi creati dalla soluzione preconfigurata.

## Visualizzare l'elenco di dispositivi per la soluzione

L'elenco dei dispositivi mostra tutti i dispositivi registrati nella soluzione. Permette di visualizzare e modificare metadati dei dispositivi, aggiungere o rimuovere dispositivi e inviare comandi ai dispositivi.

1.  Fare clic su **Dispositivi** nel menu a sinistra per passare all'*elenco dei dispositivi* per questa soluzione.

    ![][img-devicelist]

2.  L'elenco di dispositivi mostra che sono presenti quattro dispositivi simulati creati dal processo di provisioning.

3.  Fare clic su un dispositivo nell'elenco dei dispositivi per visualizzarne i dettagli.

    ![][img-devicedetails]

Il pannello **Dettagli dispositivo** contiene tre sezioni:

- La sezione **Azioni** elenca le azioni che è possibile eseguire sul dispositivo. Se si disabilita il dispositivo, non sarà più consentito inviare la telemetria o ricevere comandi. Se si disabilita un dispositivo, sarà poi possibile abilitarlo di nuovo. È possibile aggiungere una regola associata al dispositivo, che attiva un avviso quando un valore della telemetria supera una soglia. È anche possibile inviare un comando a un dispositivo. Quando un dispositivo si connette per la prima volta, comunica alla soluzione i comandi a cui può rispondere.
- La sezione **Proprietà dispositivo** elenca i metadati del dispositivo. Alcuni di questi metadati provengono dal dispositivo stesso (ad esempio, il produttore), altri invece vengono generati dalla soluzione (ad esempio, l'ora di creazione). È possibile modificare i metadati del dispositivo da qui.
- La sezione **Chiavi di autenticazione** elenca le chiavi che il dispositivo può usare per l'autenticazione con la soluzione.

## Inviare un comando a un dispositivo

Il riquadro dei dettagli del dispositivo mostra tutti i comandi supportati da un dispositivo specifico e consente di inviare comandi a un dispositivo. Quando un dispositivo viene avviato per la prima volta, invia alla soluzione informazioni sui comandi che supporta.

1.  Fare clic su **Comandi** nel riquadro dei dettagli del dispositivo selezionato.

    ![][img-devicecommands]

2.  Selezionare **PingDevice** dall'elenco dei comandi.

3.  Fare clic su **Invia comando**.

4.  È possibile visualizzare lo stato del comando nella cronologia dei comandi.

    ![][img-pingcommand]

La soluzione tiene traccia dello stato di ogni comando inviato. All'inizio il risultato è **In sospeso**. Quando il dispositivo segnala che il comando è stato eseguito, il risultato viene impostato su **Operazione riuscita**.

## Aggiungere un nuovo dispositivo simulato

1.  Tornare all'elenco dei dispositivi.

2.  Fare clic su **+ Aggiungi dispositivo** nell'angolo in basso a sinistra per aggiungere un nuovo dispositivo.

    ![][img-adddevice]

3.  Fare clic su **Aggiungi nuovo** nel riquadro **Dispositivo simulato**.

    ![][img-addnew]
    
    Oltre a creare un nuovo dispositivo simulato, è anche possibile aggiungere un dispositivo fisico se si sceglie di creare un **Dispositivo personalizzato**. Per altre informazioni in proposito, vedere [Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto IoT Suite][lnk-connecting-devices].

4.  Selezionare **Definire l'ID dispositivo** e aggiungere un nome di ID dispositivo univoco, ad esempio **mydevice\_01**.

5.  Fare clic su **Crea**.

    ![][img-definedevice]

6. Nel passaggio 3 della procedura **Aggiungi un dispositivo simulato** fare clic su **Operazione completata** per tornare all'elenco dei dispositivi.

7. Verificare che lo stato del dispositivo nell'elenco sia **In esecuzione**.

    ![][img-runningnew]

8. È anche possibile visualizzare la telemetria simulata dal nuovo dispositivo nel dashboard:

    ![][img-runningnew-2]

## Modificare i metadati del dispositivo

1.  Tornare all'elenco dei dispositivi.

2.  Selezionare il nuovo dispositivo in **Elenco dei dispositivi** e quindi fare clic su **Modifica** per modificare **Proprietà dispositivo**:

    ![][img-editdevice]

3. Scorrere verso il basso e modificare i valori di latitudine e longitudine. Fare quindi clic su **Save changes to device registry**.

    ![][img-editdevice2]

4. Tornare al dashboard e notare che la posizione del dispositivo sulla mappa è cambiata:

    ![][img-editdevice3]

## Aggiungere una regola per il nuovo dispositivo

Non sono presenti regole per il nuovo dispositivo appena aggiunto. In questa sezione si aggiungerà una regola che attiva un avviso quando la temperatura segnalata dal nuovo dispositivo supera i 47 gradi. Prima di iniziare, si noti che la cronologia della telemetria per il nuovo dispositivo nel dashboard indica che la temperatura del dispositivo non supera mai i 45 gradi.

1.  Tornare all'elenco dei dispositivi.

2.  Selezionare il nuovo dispositivo in **Elenco dei dispositivi** e quindi fare clic su **Aggiungi regola** per aggiungere una nuova regola per il dispositivo.

3. Creare una regola che usa **Temperature** come campo dati e **AlarmTemp** come output quando la temperatura supera i 47 gradi:

    ![][img-adddevicerule]

4. Fare clic su **Save and View Rules** per salvare le modifiche.

5.  Fare clic su **Comandi** nel riquadro dei dettagli del nuovo dispositivo.

    ![][img-adddevicerule2]

6.  Selezionare **ChangeSetPointTemp** nell'elenco dei comandi e impostare **SetPointTemp** su 45. Fare quindi clic su **Send Command**:

    ![][img-adddevicerule3]

7.  Tornare al dashboard della soluzione. Poco dopo verrà visualizzata una nuova voce nel riquadro **Alarm History** quando la temperatura segnalata dal nuovo dispositivo supera la soglia di 47 gradi:

    ![][img-adddevicerule4]

8. È possibile rivedere e modificare tutte le regole nella pagina **Regole** del dashboard:

    ![][img-rules]

9. È possibile rivedere e modificare tutte le azioni che possono essere eseguite in risposta a una regola nella pagina **Azioni** del dashboard:

    ![][img-actions]

> [AZURE.NOTE] È possibile definire azioni che possono inviare un messaggio di posta elettronica o un SMS in risposta a una regola oppure eseguire un'integrazione con un sistema line-of-business tramite un'[app per la logica][lnk-logic-apps].

## Dietro le quinte

Quando si distribuisce una soluzione preconfigurata, il processo di distribuzione crea più risorse nella sottoscrizione di Azure selezionata. È possibile visualizzare queste risorse nel [portale][lnk-portal] di Azure. Il processo di distribuzione crea un **gruppo di risorse** con un nome basato sul nome scelto per la soluzione preconfigurata:

![][img-portal]

È possibile visualizzare le impostazioni di ogni risorsa selezionandola nell'elenco di risorse nel gruppo di risorse. Lo screenshot precedente illustra le impostazioni per l'hub IoT usato nella soluzione preconfigurata.

È anche possibile visualizzare il codice sorgente per la soluzione preconfigurata. Il codice sorgente della soluzione preconfigurata per il monitoraggio remoto si trova in [azure-iot-remote-monitoring][lnk-rmgithub]\:

- La cartella **DeviceAdministration** contiene il codice sorgente per il dashboard.
- La cartella **Simulator** contiene il codice sorgente per il dispositivo simulato.
- La cartella **EventProcessor** contiene il codice sorgente per il processo back-end che gestisce la telemetria in ingresso.

Al termine, è possibile eliminare la soluzione preconfigurata dalla sottoscrizione di Azure nel sito [azureiotsuite.com][lnk-azureiotsuite]. Questo permette di eliminare facilmente tutte le risorse di cui è stato effettuato il provisioning quando è stata creata la soluzione preconfigurata.

> [AZURE.NOTE] Per assicurarsi di eliminare tutti gli elementi correlati alla soluzione preconfigurata, eliminarli da [azureiotsuite.com][lnk-azureiotsuite] e non limitarsi a eliminare il gruppo di risorse nel portale.

## Passaggi successivi

Ora che è stata compilata una soluzione preconfigurata funzionante, è possibile passare alle procedure dettagliate seguenti:

-   [Guida alla personalizzazione di soluzioni preconfigurate][lnk-customize]
-   [Panoramica della soluzione preconfigurata di manutenzione predittiva][lnk-predictive]

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

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md
[lnk-connecting-devices]: iot-suite-connecting-devices.md
[lnk-permissions]: https://azure.microsoft.com/documentation/articles/iot-suite-permissions/
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-faq]: https://azure.microsoft.com/documentation/articles/iot-suite-faq/

<!---HONumber=AcomDC_0420_2016-->