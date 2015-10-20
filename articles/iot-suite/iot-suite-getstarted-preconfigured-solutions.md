<properties
	pageTitle="Introduzione alle soluzioni preconfigurate IoT | Microsoft Azure"
	description="Seguire questa esercitazione per apprendere come distribuire una soluzione preconfigurata di Azure IoT Suite."
	services=""
	documentationCenter=""
	authors="aguilaaj"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="araguila"/>

# Esercitazione: Introduzione alle soluzioni preconfigurate IoT

## Introduzione

Le soluzioni preconfigurate di Azure IoT Suite combinano diversi servizi di Azure IoT per esemplificare una soluzione end-to-end che soddisfa uno scenario aziendale di Internet delle cose.

Questa esercitazione illustra come eseguire il provisioning di una soluzione preconfigurata per il **monitoraggio remoto** e come visualizzarne le funzionalità di base.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

-   Una sottoscrizione di Azure attiva.

    Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][].

## Eseguire il provisioning di una soluzione preconfigurata per il monitoraggio remoto

1.  Accedere a https://www.azureiotsuite.com e fare clic su **+** per creare una nuova soluzione.

2.  Selezionare **Monitoraggio remoto** come tipo di soluzione.

3.  Specificare un **nome** per la soluzione preconfigurata per il monitoraggio remoto.

4.  Convalidare i valori di **area** e **sottoscrizione** in cui eseguire il provisioning di questa soluzione.

5.  Fare clic su **Crea soluzione**.

## Visualizzare il dashboard della soluzione per il monitoraggio remoto

1.  Al termine del provisioning, quando il riquadro della soluzione preconfigurata indica **Pronto**, fare clic su **Avvia** per aprire il dashboard della soluzione per il monitoraggio remoto in una nuova scheda.

2.  Per impostazione predefinita, il **Dashboard** è selezionato nel menu a sinistra. Questo è il dashboard della soluzione.

## Visualizzare l'elenco di dispositivi per la soluzione

1.  Fare clic su **Dispositivi** nel menu a sinistra per passare all'elenco di dispositivi per questa soluzione.

2.  Verranno visualizzati 4 dispositivi simulati di cui è stato eseguito il provisioning.

3.  Fare clic su un **singolo dispositivo** nell'elenco per visualizzare i dettagli associati a tale dispositivo.

## Inviare un comando a un dispositivo

1.  Fare clic su **Invia comando** nel riquadro dei dettagli del dispositivo simulato selezionato.

2.  Selezionare **PingDevice** dall'elenco dei comandi.

3.  Fare clic su **Invia comando**.

4.  Visualizzare lo stato del comando nella cronologia comandi.

## Aggiungere un nuovo dispositivo simulato

1.  Fare clic su **←** (freccia indietro) per tornare all'elenco di dispositivi.

2.  Fare clic su **+ Aggiungi dispositivo** in basso a sinistra per aggiungere un nuovo dispositivo.

3.  Fare clic su **Aggiungi nuovo** in **Dispositivo simulato**.

4.  Selezionare **Definire l'ID dispositivo** e aggiungere un nome di ID dispositivo univoco.

5.  Fare clic su **Crea**.

6.  Fare clic su **←** (freccia indietro) per tornare all'elenco di dispositivi.

7.  Verificare che lo stato del dispositivo nell'elenco sia **In esecuzione**.

## Visualizzare e modificare le regole per la soluzione

1.  Si noti la tabella **Cronologia avvisi** in **Dashboard soluzione**.

2.  Questi avvisi vengono attivati da un output di regola **AlarmTemp** specificato in **Regole**.

3.  Fare clic su **Regole** nel menu a sinistra per passare alle regole per questa soluzione.

4.  Al momento del provisioning verrà visualizzata una regola già abilitata.

5.  Fare clic sulla **regola** nell'elenco di regole per visualizzare le proprietà associate alla regola.

6.  Fare clic su **Modifica** nel riquadro delle proprietà della regola.

7.  Impostare il valore **Soglia** su 30 e lasciare invariate tutte le altre proprietà.

8.  Fare clic su **Salva e visualizza regole**.

9.  Tornare alla tabella **Cronologia avvisi** in **Dashboard soluzione** e osservare la modifica nel trigger come conseguenza dell'aggiornamento della regola.

## Passaggi successivi

Dopo avere compilato una soluzione preconfigurata funzionante, è possibile passare agli scenari seguenti:

-   [Guida alla personalizzazione di soluzioni preconfigurate][]

-   [Panoramica della suite IoT][]

[versione di valutazione gratuita di Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F%20target=

[Guida alla personalizzazione di soluzioni preconfigurate]: https://azure.microsoft.com/documentation/articles/iot-suite-guidance-on-customizing-preconfigured-solutions/
[Panoramica della suite IoT]: https://azure.microsoft.com/documentation/articles/iot-suite-overview/

<!---HONumber=Oct15_HO3-->