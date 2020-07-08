---
title: Monitorare le distribuzioni IoT Edge - Azure IoT Edge
description: Monitoraggio di alto livello, incluse le proprietà segnalate da edgeHub e edgeAgent e le metriche di distribuzione automatica.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4ca22c39fcd7b488f527326e4fdeaa56b0485cb9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84220303"
---
# <a name="monitor-iot-edge-deployments"></a>Monitorare le distribuzioni IoT Edge

Azure IoT Edge fornisce funzionalità di creazione di report che consentono di monitorare le informazioni in tempo reale nei moduli distribuiti nei dispositivi IoT Edge. Il servizio hub IoT recupera le informazioni sullo stato dai dispositivi e le rende disponibili all'operatore. Il monitoraggio è importante anche per le [distribuzioni su larga scala](module-deployment-monitoring.md) che includono distribuzioni automatiche e distribuzioni a livelli.

Sia i dispositivi che i moduli hanno dati simili, ad esempio per quanto riguarda la connettività, quindi i valori vengono ottenuti in base all'ID dispositivo o all'ID modulo.

Il servizio hub IoT raccoglie i dati segnalati da dispositivi e moduli gemelli e fornisce i conteggi dei vari stati che i dispositivi possono avere. Il servizio hub IoT organizza questi dati in quattro gruppi di metriche:

| Tipo | Descrizione |
| --- | ---|
| Assegnato | Mostra i dispositivi IoT Edge che soddisfano la condizione di destinazione della distribuzione. |
| Applicato | Mostra i dispositivi IoT Edge di destinazione che non rappresentano la destinazione di un'altra distribuzione con priorità più alta. |
| Riuscite | Mostra i dispositivi IoT Edge che hanno segnalato che i moduli sono stati distribuiti correttamente. |
| Con errori | Mostra i dispositivi IoT Edge che hanno segnalato che uno o più moduli non sono stati distribuiti correttamente. Per esaminare ulteriormente questo errore, connettersi in remoto a tali dispositivi e visualizzare i file di log. |

Il servizio hub IoT rende disponibili i dati per il monitoraggio nel portale di Azure e nell'interfaccia della riga di comando di Azure.

## <a name="monitor-a-deployment-in-the-azure-portal"></a>Monitorare una distribuzione nel portale di Azure

Per visualizzare i dettagli di una distribuzione e monitorare i dispositivi che la eseguono, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
1. Selezionare **IoT Edge** nel riquadro a sinistra.
1. Selezionare la scheda **Distribuzioni di IoT Edge**.
1. Esaminare l'elenco delle distribuzioni. Per ogni distribuzione, è possibile visualizzare i dettagli seguenti:

    | Colonna | Descrizione |
    | --- | --- |
    | ID | Nome della distribuzione. |
    | Tipo | Tipo di distribuzione, ovvero **Distribuzione** o **Distribuzione a livelli**. |
    | Condizione di destinazione | Tag usato per definire i dispositivi di destinazione. |
    | Priorità | Valore di priorità assegnato alla distribuzione. |
    | Metriche di sistema | Numero di dispositivi gemelli nell'hub IoT che soddisfano la condizione di destinazione. **Applicato** specifica il numero di dispositivi ai cui moduli gemelli nell'hub IoT è stato applicato il contenuto della distribuzione. |
    | Metriche del dispositivo | Numero di dispositivi IoT Edge che segnalano il corretto completamento o la presenza di errori dal runtime client IoT Edge. |
    | Metriche personalizzate | Numero di dispositivi IoT Edge che segnalano i dati per le metriche definite per la distribuzione. |
    | Data creazione | Timestamp relativo alla creazione della distribuzione. Questo timestamp viene usato per stabilire la prevalenza quando due distribuzioni hanno la stessa priorità. |

1. Selezionare la distribuzione che si vuole monitorare.  
1. Nella pagina **Dettagli della distribuzione** scorrere fino alla sezione inferiore e selezionare la scheda **Condizione di destinazione**. Selezionare **Visualizza** per elencare i dispositivi che soddisfano la condizione di destinazione. È possibile modificare sia la condizione che il valore di **Priorità**. Se sono state apportate modifiche, selezionare **Salva**.

   ![Visualizzare i dispositivi di destinazione per una distribuzione](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. Selezionare la scheda **Metriche**. Se si sceglie una metrica nell'elenco a discesa **Seleziona metrica**, viene visualizzato un pulsante **Visualizza** che consente di visualizzare i risultati. È anche possibile selezionare **Modifica metriche** per modificare i criteri per le metriche personalizzate definite. Se sono state apportate modifiche, selezionare **Salva**.

   ![Visualizzare le metriche per una distribuzione](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)


Per apportare modifiche alla distribuzione, vedere [Modificare una distribuzione](how-to-deploy-at-scale.md#modify-a-deployment).

## <a name="monitor-a-deployment-with-azure-cli"></a>Monitorare una distribuzione con l'interfaccia della riga di comando di Azure

Usare il comando [az IoT Edge deployment show](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/edge/deployment?view=azure-cli-latest#ext-azure-iot-az-iot-edge-deployment-show) per visualizzare i dettagli di una singola distribuzione:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Il comando deployment show accetta i parametri seguenti:

* **--deployment-id** - Nome della distribuzione esistente nell'hub IoT. Parametro obbligatorio.
* **--hub-name** - Nome dell'hub IoT in cui si trova la distribuzione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

Ispezionare la distribuzione nella finestra di comando. La proprietà **metrics** visualizza un conteggio per ogni metrica valutata da ciascun hub:

* **targetedCount** - Metrica di sistema che specifica il numero di dispositivi gemelli presenti nell'hub IoT che corrispondono alla condizione di destinazione.
* **appliedCount** - Metrica di sistema che specifica il numero di dispositivi ai cui moduli gemelli nell'hub IoT è stato applicato il contenuto della distribuzione.
* **reportedSuccessfulCount** - Metrica del dispositivo che specifica il numero di dispositivi IoT Edge presenti nella distribuzione che segnalano l'esito positivo dal runtime client IoT Edge.
* **reportedFailedCount** - Metrica del dispositivo che specifica il numero di dispositivi IoT Edge presenti nella distribuzione che segnalano l'esito negativo dal runtime client IoT Edge.

È possibile visualizzare un elenco di oggetti o ID dispositivo per ognuna delle metriche tramite il comando [az IoT Edge deployment show-metric](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/edge/deployment?view=azure-cli-latest#ext-azure-iot-az-iot-edge-deployment-show-metric):

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

Il comando deployment show-metric accetta i parametri seguenti:

* **--deployment-id** - Nome della distribuzione esistente nell'hub IoT.
* **--metric-id** - Nome della metrica per cui visualizzare l'elenco di ID dispositivo, ad esempio `reportedFailedCount`.
* **--hub-name** - Nome dell'hub IoT in cui si trova la distribuzione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

Per apportare modifiche alla distribuzione, vedere [Modificare una distribuzione](how-to-deploy-cli-at-scale.md#modify-a-deployment).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [monitorare i moduli gemelli](how-to-monitor-module-twins.md), principalmente i moduli IOT Edge Agent e runtime Hub IOT Edge, per la connettività e l'integrità delle distribuzioni di IOT Edge.
