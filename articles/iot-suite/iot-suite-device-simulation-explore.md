---
title: Introduzione alla soluzione Simulazione dispositivi - Azure | Microsoft Docs
description: "La soluzione di simulazione IoT Suite è uno strumento che può essere usato per facilitare sviluppo e test di una soluzione IoT. Il servizio di simulazione è un'offerta autonoma utilizzabile in combinazione con altre soluzioni preconfigurate o con soluzioni personalizzate."
services: iot device simulation
suite: iot-suite
author: troyhopwood
manager: timlt
ms.author: troyhop
ms.service: iot-suite
ms.date: 12/15/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 25b9d1c7debe0d98a87b7d0b47dea6ab6f36c8af
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="device-simulation-walkthrough"></a>Procedura dettagliata per Simulazione dispositivi

Simulazione dispositivi di Azure IoT è uno strumento che può essere usato per facilitare sviluppo e test di una soluzione IoT. Simulazione dispositivi è un'offerta autonoma utilizzabile in combinazione con altre soluzioni preconfigurate o con soluzioni personalizzate.

Questa esercitazione illustra alcune delle funzionalità di Simulazione dispositivi. Viene illustrato il funzionamento e come usare questo strumento per testare le proprie soluzioni IoT.

In questa esercitazione si apprenderà come:

>[!div class="checklist"]
> * Configurare una simulazione
> * Avvio e arresto di una simulazione
> * Visualizzare le metriche di telemetria

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessaria un'istanza distribuita di Simulazione dispositivi di Azure IoT nella sottoscrizione di Azure.

Se la soluzione Simulazione dispositivi non è stata ancora distribuita, è necessario completare l'esercitazione [Distribuire la soluzione Simulazione dispositivi di Azure IoT](iot-suite-device-simulation-explore.md).

## <a name="configuring-device-simulation"></a>Configurazione di Simulazione dispositivi

È possibile configurare ed eseguire Simulazione dispositivi completamente dall'interno del dashboard. Aprire il dashboard dalla pagina [Soluzioni di cui è stato effettuato il provisioning](https://www.azureiotsuite.com/) di IoT Suite. Fare clic su **Avvia** per la nuova distribuzione di Simulazione dispositivi.

### <a name="target-iot-hub"></a>Hub IoT di destinazione

È possibile usare Simulazione dispositivi con un hub IoT di cui è già stato effettuato il provisioning o con qualsiasi altro hub IoT:

![Hub IoT di destinazione](media/iot-suite-device-simulation-explore/targethub.png)

> [!NOTE]
> L'opzione per usare un hub IoT di cui è già stato effettuato il preprovisioning è disponibile solo se si è scelto di creare un nuovo hub IoT durante la distribuzione di Simulazione dispositivi. Se non è disponibile un hub IoT, è sempre possibile crearne uno nuovo dal [portale di Azure](https://portal.azure.com).

Per impostare un hub IoT specifico come destinazione, specificare la stringa di connessione **iothubowner**. È possibile ottenere la stringa di connessione dal [portale di Azure](https://portal.azure.com):

1. Nella pagina di configurazione dell'hub IoT nel portale di Azure, fare clic su **Criteri di accesso condiviso**.
1. Fare clic su **iothubowner**.
1. Copiare la chiave primaria o la chiave secondaria.
1. Incollare questo valore nella casella di testo sotto a Target IoT Hub (Hub IoT di destinazione).

![Target IoT Hub (Hub IoT di destinazione)](media/iot-suite-device-simulation-explore/connectionstring.png)

### <a name="device-model"></a>Modello del dispositivo

Il modello del dispositivo consente di scegliere il tipo di dispositivo da simulare. È possibile scegliere uno dei modelli di dispositivo preconfigurati o definire un elenco di sensori per un modello di dispositivo personalizzato:

![Modello del dispositivo](media/iot-suite-device-simulation-explore/devicemodel.png)

#### <a name="pre-configured-device-models"></a>Modelli di dispositivo preconfigurati

Simulazione dispositivi include tre modelli di dispositivi preconfigurati. Sono disponibili modelli di dispositivo per Chillers (Refrigeratori), Elevators (Montacarichi) e Trucks (Veicoli).

I modelli di dispositivo preconfigurati includono più sensori con una frequenza di raccolta dei dati di telemetria predeterminata. Non è possibile personalizzare la frequenza di raccolta dei dati di telemetria per questi dispositivi.

La tabella seguente mostra un elenco delle configurazioni per ogni modello di dispositivo preconfigurato:

| Modello del dispositivo | Sensore | Unità | Frequenza telemetria
| -------------| ------ | -----| --------------------|
| Chiller (Refrigeratore) | umidità | % | 5 secondi |
| | pressure | psig | 5 secondi |
| | temperatura | F | 5 secondi |
| Elevator (Montacarichi) | Piano | | 5 secondi |
| | Vibrazione | MM | 5 secondi |
| | Temperatura | F | 5 secondi |
| Truck (Veicolo) | Latitude | | 3 secondi |
| | Longitudine | | 3 secondi |
| | velocità | mph | 5 secondi |
| | temperatura carico | F | 5 secondi |

#### <a name="custom-device-model"></a>Modello di dispositivo personalizzato

I modelli di dispositivo personalizzati consentono di definire sensori che rappresentano più fedelmente dispositivi specifici. Un dispositivo personalizzato può avere fino a 10 sensori personalizzati.

Quando si seleziona il tipo di modello di dispositivo personalizzato, è possibile aggiungere nuovi sensori facendo clic su **+Add sensor** (Aggiungi sensore).

![Aggiungere sensori](media/iot-suite-device-simulation-explore/customsensors.png)

Per i sensori personalizzati sono disponibili le proprietà seguenti:

| Campo | Descrizione |
| ----- | ----------- |
| Sensor Name (Nome del sensore) | Nome descrittivo per il sensore, ad esempio **temperatura** o **velocità**. |
| Behavior (Comportamento) | I comportamenti consentono di ottenere dati di telemetria variabili da un messaggio all'altro, per simulare dati reali. Il comportamento **Increment** (Incremento) consente di aumentare il valore di un'unità per ogni messaggio inviato a partire dal valore minimo. Quando viene raggiunto il valore massimo, il processo riparte dal valore minimo. Il comportamento **Decrement** (Decremento) è uguale a **increment** (Incremento), ma alla rovescia. Il comportamento **Random** (Casuale) genera un valore casuale compreso tra i valori minimo e massimo. |
| Min Value (Valore minimo) | Il numero più basso che rappresenta l'intervallo di valori accettabili. |
| Max Value (Valore massimo) | Il numero massimo che rappresenta l'intervallo di valori accettabili. |
| Unit (Unità) | Unità di misura per il sensore, ad esempio °C o km/h. |

### <a name="number-of-devices"></a>Number of devices (Numero di dispositivi)

Simulazione dispositivi consente attualmente di simulare fino a 1.000 dispositivi.

![Number of devices (Numero di dispositivi)](media/iot-suite-device-simulation-explore/numberofdevices.png)

### <a name="telemetry-frequency"></a>Telemetry frequency (Frequenza telemetria)

Questa opzione consente di specificare la frequenza con cui i dispositivi simulati devono inviare dati all'hub IoT. È possibile inviare dati con la frequenza minima di 10 secondi o raramente con la frequenza massima di 99 ore, 59 minuti e 59 secondi.

![Telemetry frequency (Frequenza telemetria)](media/iot-suite-device-simulation-explore/frequency.png)

> [!NOTE]
> Se si usa un hub IoT diverso dall'hub IoT di cui è stato effettuato il pre-provisioning, valutare la possibilità di limitare i messaggi per l'hub IoT di destinazione. Ad esempio, in presenza di 1.000 dispositivi simulati che inviano dati di telemetria ogni 10 secondi a un hub S1, il limite di telemetria per l'hub viene raggiunto in poco più di un'ora.

### <a name="simulation-duration"></a>Simulation duration (Durata simulazione)

È possibile scegliere di eseguire la simulazione per un determinato periodo di tempo o fino a quando non viene arrestata in modo esplicito. Quando si sceglie un periodo di tempo specifico, è possibile scegliere qualsiasi durata da 10 minuti fino a 99 ore, 59 minuti e 59 secondi.

![Simulation duration (Durata simulazione)](media/iot-suite-device-simulation-explore/duration.png)

### <a name="start-and-stop-the-simulation"></a>Avviare e arrestare la simulazione

Dopo aver aggiunto tutti i dati di configurazione necessari nella schermata, viene abilitato il pulsante **Start Simulation** (Avvia simulazione). Per avviare la simulazione, fare clic su questo pulsante.

![Start Simulation (Avvia simulazione)](media/iot-suite-device-simulation-explore/start.png)

Se per la simulazione viene impostata una durata specifica, verrà arrestata automaticamente una volta trascorso il tempo specificato. È sempre possibile arrestare prima la simulazione facendo clic su **Stop Simulation** (Arresta simulazione).

Se si sceglie di eseguire la simulazione all'infinito, verrà eseguita fino a quando non si fa clic su **Stop Simulation** (Arresta simulazione). È possibile chiudere il browser e tornare alla pagina Simulazione dispositivi per arrestare la simulazione in qualsiasi momento.

![Stop Simulation (Arresta simulazione)](media/iot-suite-device-simulation-explore/stop.png)

> [!NOTE]
> È possibile eseguire una sola simulazione alla volta. È necessario arrestare la simulazione in esecuzione prima di avviare una nuova simulazione.
