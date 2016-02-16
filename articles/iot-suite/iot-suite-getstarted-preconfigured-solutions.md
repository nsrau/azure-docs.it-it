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
     ms.date="12/03/2015"
     ms.author="dobett"/>

# Esercitazione: Introduzione alle soluzioni preconfigurate IoT

## Introduzione

Le [soluzioni preconfigurate ][lnk-preconfigured-solutions] di Azure IoT Suite combinano più servizi IoT di Azure per fornire soluzioni end-to-end che implementano scenari aziendali IoT comuni.

Questa esercitazione illustra come eseguire il provisioning della soluzione preconfigurata di *monitoraggio remoto* e ne descrive le funzionalità di base.

Per completare l'esercitazione, è necessaria una sottoscrizione di Azure attiva.

> [AZURE.NOTE]  Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk_free_trial].

## Effettuare il provisioning della soluzione preconfigurata di monitoraggio remoto

1.  Accedere a [azureiotsuite.com][lnk-azureiotsuite] con le credenziali dell'account Azure e fare clic su **+** per creare una nuova soluzione.

2.  Fare clic su **Seleziona** nel riquadro **Monitoraggio remoto**.

3.  Immettere un valore in **Nome soluzione** per la soluzione preconfigurata di monitoraggio remoto.

4.  Selezionare l'**Area** e la **Sottoscrizione** da usare per effettuare il provisioning della soluzione.

5.  Fare clic su **Crea soluzione** per iniziare il processo di provisioning. In genere l'esecuzione del processo richiede alcuni minuti.

## Visualizzare il dashboard della soluzione di monitoraggio remoto

1.  Al termine del provisioning quando il riquadro della soluzione preconfigurata indica **Pronto**, fare clic su **Avvia** per aprire il portale della soluzione di monitoraggio remoto in una nuova scheda.

    ![][img-launch-solution]

2.  Per impostazione predefinita, il portale della soluzione visualizza il *dashboard della soluzione*. È possibile selezionare altre viste usando il menu a sinistra.

    ![][img-dashboard]

## Visualizzare l'elenco di dispositivi per la soluzione

1.  Fare clic su **Dispositivi** nel menu a sinistra per passare all'*elenco dei dispositivi* per questa soluzione.

    ![][img-devicelist]

2.  L'elenco di dispositivi mostra che sono presenti quattro dispositivi simulati creati dal processo di provisioning.

3.  Fare clic su un dispositivo nell'elenco dei dispositivi per visualizzarne i dettagli.

    ![][img-devicedetails]

## Inviare un comando a un dispositivo

1.  Fare clic su **Comandi** nel riquadro dei dettagli del dispositivo selezionato.

    ![][img-devicecommands]

2.  Selezionare **PingDevice** dall'elenco dei comandi.

3.  Fare clic su **Invia comando**.

4.  È possibile visualizzare lo stato del comando nella cronologia dei comandi.

    ![][img-pingcommand]

## Aggiungere un nuovo dispositivo simulato

1.  Tornare all'elenco dei dispositivi.

2.  Fare clic su **+ Aggiungi dispositivo** nell'angolo in basso a sinistra per aggiungere un nuovo dispositivo.

    ![][img-adddevice]

3.  Fare clic su **Aggiungi nuovo** nel riquadro **Dispositivo simulato**.

    ![][img-addnew]

4.  Selezionare **Definire l'ID dispositivo** e aggiungere un nome di ID dispositivo univoco, ad esempio **mydevice\_01**.

5.  Fare clic su **Crea**.

    ![][img-definedevice]

6. Nel passaggio 3 della procedura **Aggiungi un dispositivo simulato** fare clic su **Operazione completata** per tornare all'elenco dei dispositivi.

7.  Verificare che lo stato del dispositivo nell'elenco sia **In esecuzione**.

    ![][img-runningnew]

## Visualizzare e modificare le regole per la soluzione

1.  Tornare al dashboard della soluzione e visualizzare il riquadro **Cronologia avvisi**.

    ![][img-alarmhistory]

2.  La regola **AlarmTemp** attiva questi avvisi.

3.  Fare clic su **Regole** nel menu a sinistra per visualizzare le regole per questa soluzione.

    ![][img-rules]

4.  La soluzione preconfigurata effettua il provisioning di due regole.

5.  Fare clic sulla regola **Temperature** nell'elenco di regole per visualizzarne le proprietà.

6.  Fare clic su **Modifica** nel riquadro delle proprietà della regola.

    ![][img-displayrule]

7.  Impostare il valore **Soglia** su 30 e lasciare invariate tutte le altre proprietà.

8.  Fare clic su **Salva e visualizza regole**.

    ![][img-editrule]

9.  Tornare alla tabella **Cronologia avvisi** in **Dashboard soluzione** e osservare la modifica nel comportamento come conseguenza della regola aggiornata.

    ![][img-newhistory]
    
Al termine, è possibile eliminare la soluzione preconfigurata dalla sottoscrizione di Azure nel sito [azureiotsuite.com][lnk-azureiotsuite], che consente di eliminare facilmente tutte le risorse di cui è stato effettuato il provisioning quando è stata creata la soluzione preconfigurata.

## Passaggi successivi

Dopo avere compilato una soluzione preconfigurata funzionante, è possibile passare agli scenari seguenti:

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
[img-alarmhistory]: media/iot-suite-getstarted-preconfigured-solutions/alarmhistory.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-displayrule]: media/iot-suite-getstarted-preconfigured-solutions/displayrule.png
[img-editrule]: media/iot-suite-getstarted-preconfigured-solutions/editrule.png
[img-newhistory]: media/iot-suite-getstarted-preconfigured-solutions/newhistory.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md

<!---HONumber=AcomDC_0211_2016-->