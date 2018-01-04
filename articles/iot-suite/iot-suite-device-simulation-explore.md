---
title: Iniziare con la soluzione - simulazione del dispositivo di Azure | Documenti Microsoft
description: "La soluzione di simulazione IoT Suite è uno strumento che può essere usato per facilitare lo sviluppo e test di una soluzione IoT. Il servizio di simulazione è un computer autonomo che può essere utilizzato in combinazione con altre soluzioni preconfigurate o con soluzioni personalizzate di offerta."
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="device-simulation-walkthrough"></a>Procedura dettagliata di simulazione di dispositivo

Simulazione del dispositivo IoT Azure è uno strumento che può essere usato per facilitare lo sviluppo e test di una soluzione IoT. Simulazione del dispositivo è un computer autonomo offerta che è possibile utilizzare in combinazione con altre soluzioni preconfigurate o con soluzioni personalizzate.

In questa esercitazione illustra alcune delle funzionalità della simulazione del dispositivo. Illustrato il funzionamento e consente di utilizzarlo per testare le proprie soluzioni IoT.

In questa esercitazione si apprenderà come:

>[!div class="checklist"]
> * Configurare una simulazione
> * Avvio e arresto di una simulazione
> * Visualizza le metriche di telemetria

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario un'istanza distribuita di simulazione del dispositivo IoT Azure nella sottoscrizione di Azure.

Se non è stato ancora distribuito simulazione del dispositivo, è necessario completare il [distribuire simulazione del dispositivo IoT Azure](iot-suite-device-simulation-explore.md) esercitazione.

## <a name="configuring-device-simulation"></a>Configurazione di simulazione del dispositivo

È possibile configurare ed eseguire simulazione del dispositivo da completamente all'interno del dashboard. Aprire il dashboard da IoT Suite [il provisioning delle soluzioni](https://www.azureiotsuite.com/) pagina. Fare clic su **avviare** sotto la nuova distribuzione di simulazione del dispositivo.

### <a name="target-iot-hub"></a>Hub IoT di destinazione

È possibile utilizzare simulazione del dispositivo con un hub IoT pre-provisioning o con qualsiasi altro hub IoT:

![Hub IoT di destinazione](media/iot-suite-device-simulation-explore/targethub.png)

> [!NOTE]
> L'opzione per usare un IoT Hub pre-provisioning è disponibile solo se si sceglie di creare un nuovo IoT Hub per la distribuzione di simulazione del dispositivo. Se non si dispone di un hub IoT, è sempre possibile crearne uno nuovo dal [portale di Azure](https://portal.azure.com).

Per impostare come destinazione un hub IoT specifico, fornire il **iothubowner** stringa di connessione. È possibile ottenere la stringa di connessione dal [portale di Azure](https://portal.azure.com):

1. Nella pagina Configurazione IoT Hub nel portale di Azure, fare clic su **criteri di accesso condiviso**.
1. Fare clic su **iothubowner**.
1. Copiare sia la chiave primaria o secondaria.
1. Incollare questo valore nella casella di testo in IoT Hub di destinazione.

![Hub IoT di destinazione](media/iot-suite-device-simulation-explore/connectionstring.png)

### <a name="device-model"></a>Modello del dispositivo

Il modello del dispositivo consente di scegliere il tipo di dispositivo per simulare. È possibile scegliere uno dei modelli di dispositivo preconfigurata o definire un elenco di sensori per un modello di dispositivo personalizzata:

![Modello del dispositivo](media/iot-suite-device-simulation-explore/devicemodel.png)

#### <a name="pre-configured-device-models"></a>Modelli di dispositivo preconfigurato

Simulazione del dispositivo fornisce tre modelli di dispositivi configurati in precedenza. Modelli di dispositivo per Chillers ascensori e camion sono disponibili.

Modelli di dispositivo preconfigurata che includono più con una frequenza di telemetria predeterminato. Non è possibile personalizzare la frequenza di telemetria per questi dispositivi.

Nella tabella seguente mostra un elenco delle configurazioni per ogni modello di dispositivo configurati in precedenza:

| Modello del dispositivo | Sensore | Unità | Frequenza di telemetria
| -------------| ------ | -----| --------------------|
| Chiller (Refrigeratore) | Umidità | % | 5 secondi |
| | pressure | psig | 5 secondi |
| | Temperatura | F | 5 secondi |
| Elevator (Montacarichi) | floor | | 5 secondi |
| | resistenza alle vibrazioni | MM | 5 secondi |
| | Temperatura | F | 5 secondi |
| Truck (Veicolo) | Latitude | | 3 secondi |
| | Longitudine | | 3 secondi |
| | Velocità | m/h | 5 secondi |
| | cargotemperature | F | 5 secondi |

#### <a name="custom-device-model"></a>Modello di dispositivo personalizzato

Modelli di dispositivo personalizzate consentono di sensori di modello che rappresentano meglio i propri dispositivi. Un dispositivo personalizzato può avere fino a 10 sensori personalizzati.

Quando si seleziona il tipo di modello di dispositivo personalizzata, è possibile aggiungere nuovi sensori facendo **+ Aggiungi sensore**.

![Aggiungere sensori](media/iot-suite-device-simulation-explore/customsensors.png)

Sensori personalizzati hanno le seguenti proprietà:

| Campo | DESCRIZIONE |
| ----- | ----------- |
| Nome del sensore | Un nome descrittivo per il sensore, ad esempio **temperatura** o **velocità**. |
| Comportamento | I comportamenti consentono di dati di telemetria a variare da un messaggio a altro per simulare i dati reali. **Incremento** aumenta il valore di uno in ogni messaggio inviato a partire dal valore minimo. Una volta che il valore massimo viene soddisfatta, quindi avvia su nuovamente al valore minimo. **Decremento** si comporta allo stesso modo come **incremento** ma conto alla rovescia. Il **casuali** comportamento genera un valore casuale compreso tra il valore minimo e massimi valori. |
| Valore minimo | Il numero più basso che rappresenta l'intervallo di valori accettabili. |
| Valore massimo | Il numero massimo che rappresenta l'intervallo di valori accettabili. |
| Unità | L'unità di misura per il sensore, ad esempio ° F o m/h. |

### <a name="number-of-devices"></a>Numero di dispositivi

Simulazione del dispositivo consente di simulare fino a 1.000 dispositivi.

![Numero di dispositivi](media/iot-suite-device-simulation-explore/numberofdevices.png)

### <a name="telemetry-frequency"></a>Frequenza di telemetria

Frequenza di telemetria consente di specificare la frequenza con cui i dispositivi simulati devono inviare i dati per l'hub IoT. È possibile inviare dati come spesso come ogni 10 secondi o come raramente ogni come 99 ore, 59 minuti e 59 secondi.

![Frequenza di telemetria](media/iot-suite-device-simulation-explore/frequency.png)

> [!NOTE]
> Se si utilizza un hub IoT diverso da hub IoT pre-provisioning, considerare i limiti dei messaggi per l'hub IoT di destinazione. Ad esempio, se si dispone di 1.000 dispositivi simulati l'invio di dati di telemetria ogni 10 secondi a un hub S1 si raggiunge il limite di telemetria per l'hub in più di un'ora.

### <a name="simulation-duration"></a>Durata di simulazione

È possibile scegliere di eseguire la simulazione di un determinato periodo di tempo o per l'esecuzione fino a quando non si arresta in modo esplicito. Quando si sceglie un periodo di tempo specifico, è possibile scegliere qualsiasi durata da 10 minuti fino a 99 ore, 59 minuti e 59 secondi.

![Durata di simulazione](media/iot-suite-device-simulation-explore/duration.png)

### <a name="start-and-stop-the-simulation"></a>Avviare e arrestare la simulazione

Dopo aver aggiunto tutti i dati di configurazione necessarie per il modulo, il **avviare simulazione** pulsante è abilitato. Per avviare la simulazione, fare clic su questo pulsante.

![Avvia simulazione](media/iot-suite-device-simulation-explore/start.png)

Se è stato specificato un periodo di tempo specifico per la simulazione, viene arrestato automaticamente quando viene raggiunto il tempo. È sempre possibile arrestare tempestivamente la simulazione facendo **arrestare simulazione.**

Se si sceglie di eseguire la simulazione all'infinito, quindi viene eseguito fino a quando non si fa clic su **arrestare simulazione**. È possibile chiudere il browser e tornare alla pagina della simulazione del dispositivo per arrestare la simulazione in qualsiasi momento.

![Arresta simulazione](media/iot-suite-device-simulation-explore/stop.png)

> [!NOTE]
> È possibile eseguire solo una simulazione alla volta. È necessario arrestare la simulazione attualmente in esecuzione prima di iniziare una simulazione di nuovo.
