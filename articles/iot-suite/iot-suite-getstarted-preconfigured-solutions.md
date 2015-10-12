<properties
	pageTitle="Esercitazione introduttiva per le soluzioni preconfigurate per Microsoft Azure IoT Suite | Microsoft Azure"
	description="Seguire questa esercitazione per apprendere come distribuire una soluzione preconfigurata di Azure IoT Suite."
	services=""
	documentationCenter=".net"
	authors="aguilaaj"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/08/2015"
     ms.author="araguila"/>

# Iniziare a usare le soluzioni preconfigurate IoT

## Introduzione

Le soluzioni preconfigurate di Azure IoT Suite combinano diversi servizi di Azure IoT per esemplificare una soluzione end-to-end che soddisfa uno scenario aziendale di Internet delle cose.

Questa esercitazione mostra come effettuare il provisioning di una soluzione preconfigurata per il monitoraggio remoto e come visualizzare le funzionalità di base della soluzione preconfigurata per il monitoraggio remoto.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

-   Una sottoscrizione di Azure attiva.

    Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][].

## Effettuare il provisioning di una soluzione preconfigurata per il monitoraggio remoto

1.  Accedere a <https://azureiotsuite.com> e fare clic su **+** per creare una nuova soluzione.

2.  Selezionare **Remote Monitoring** come tipo di soluzione.

3.  Digitare un nome in **Solution Name** per la soluzione preconfigurata per il monitoraggio remoto.

4.  Convalidare i valori di **Region** e **Subscription** per cui effettuare il provisioning di questa soluzione.

5.  Fare clic su **Create Solution**.

## Visualizzare il dashboard della soluzione per il monitoraggio remoto

1.  Al termine del provisioning, quando il riquadro della soluzione preconfigurata indica **Ready**, fare clic su **Launch** per aprire il dashboard della soluzione per il monitoraggio remoto in una nuova scheda.

2.  Per impostazione predefinita, il **Dashboard** è selezionato nel menu a sinistra. Questo è il dashboard della soluzione.

## Visualizzare l'elenco di dispositivi per la soluzione

1.  Fare clic su **Devices** nel menu a sinistra per passare all'elenco di dispositivi per questa soluzione.

2.  Verranno visualizzati 4 dispositivi simulati di cui è stato effettuato il provisioning.

3.  Fare clic su un **singolo dispositivo** nell'elenco per visualizzare i dettagli associati a tale dispositivo.

## Inviare un comando a un dispositivo

1.  Fare clic su **Send Command** nel riquadro dei dettagli del dispositivo simulato selezionato.

2.  Selezionare **PingDevice** dall'elenco a discesa dei comandi.

3.  Fare clic su **Send Command**.

4.  Visualizzare lo stato del comando che appare nella cronologia comandi.

## Aggiungere un nuovo dispositivo simulato

1.  Fare clic su **←** (freccia indietro) per tornare all'elenco di dispositivi.

2.  Fare clic su **+ Add A Device** in basso a sinistra per aggiungere un nuovo dispositivo.

3.  Fare clic su **Add New** per **Simulated Device**.

4.  Selezionare **Let me define my own Device ID** e aggiungere un nome di ID dispositivo univoco.

5.  Fare clic su **Crea**.

6.  Fare clic su **←** (freccia indietro) per tornare all'elenco di dispositivi.

7.  Verificare che lo stato del dispositivo nell'elenco sia **Running**.

## Visualizzare e modificare le regole per la soluzione

1.  Si noti la tabella **Alarm History** in **Solution Dashboard**.

2.  Questi avvisi vengono attivati da un output di regola **AlarmTemp** specificato in **Rules**.

3.  Fare clic su **Rules** nel menu a sinistra per passare alle regole per questa soluzione.

4.  Al momento del provisioning verrà visualizzata 1 regola già abilitata.

5.  Fare clic sulla **regola** nell'elenco di regole per visualizzare le proprietà della regola associate.

6.  Fare clic su **Edit** nel riquadro delle proprietà della regola.

7.  Impostare il valore di **Threshold** su 30 e lasciare invariate tutte le altre proprietà.

8.  Fare clic su **Save and View Rules**.

9.  Tornare alla tabella **Alarm History** in **Solution Dashboard** e osservare la modifica nel trigger come conseguenza dell'aggiornamento della regola.

## Passaggi successivi

Dopo avere compilato una soluzione preconfigurata funzionante, è possibile passare agli scenari seguenti:

-   Guida alla personalizzazione di soluzioni preconfigurate

-   Panoramica della suite IoT

[versione di valutazione gratuita di Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F%20target=

<!---HONumber=Oct15_HO1-->