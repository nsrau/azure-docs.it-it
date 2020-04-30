---
title: Monitorare le distribuzioni di IoT Edge-Azure IoT Edge
description: Monitoraggio di alto livello, incluse le proprietà segnalate da edgeHub e edgeAgent e le metriche di distribuzione automatica.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cc7c1fd1dff85908c96e2fd7b2276df3d833e37f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82134312"
---
# <a name="monitor-iot-edge-deployments"></a>Monitorare le distribuzioni IoT Edge

Azure IoT Edge fornisce funzionalità di creazione di report che consentono di monitorare le informazioni in tempo reale sui moduli distribuiti nei dispositivi di IoT Edge. Il servizio hub Internet delle cose recupera lo stato dai dispositivi e li rende disponibili per l'operatore. Il monitoraggio è importante anche per le [distribuzioni su larga scala](module-deployment-monitoring.md) che includono distribuzioni automatiche e distribuzioni a più livelli.

I dispositivi e i moduli hanno dati simili, ad esempio la connettività, in modo che i valori vengano ottenuti in base all'ID del dispositivo o all'ID modulo.

Il servizio hub Internet delle cose raccoglie i dati segnalati da dispositivi e moduli gemelli e fornisce i conteggi dei vari Stati che i dispositivi possono avere. Il servizio hub Internet delle cose organizza questi dati in quattro gruppi di metriche:

| Type | Description |
| --- | ---|
| Servizio di | Mostra i dispositivi IoT Edge che corrispondono alla condizione di destinazione della distribuzione. |
| Applicazione | Mostra i dispositivi IoT Edge di destinazione che non sono interessati da un'altra distribuzione con priorità più elevata. |
| Segnalazione riuscita | Mostra i dispositivi IoT Edge che hanno segnalato che i moduli sono stati distribuiti correttamente. |
| Segnalazione di errori | Mostra i dispositivi IoT Edge che hanno segnalato che uno o più moduli non sono stati distribuiti correttamente. Per esaminare ulteriormente questo errore, connettersi in remoto a tali dispositivi e visualizzare i file di log. |

Il servizio hub Internet delle cose rende disponibili i dati per il monitoraggio nel portale di Azure e nell'interfaccia della riga di comando di Azure.

## <a name="monitor-a-deployment-in-the-azure-portal"></a>Monitorare una distribuzione nel portale di Azure

Per visualizzare i dettagli di una distribuzione e monitorare i dispositivi che la eseguono, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub Internet.
1. Selezionare **IOT Edge** dal menu del riquadro sinistro.
1. Selezionare la scheda **distribuzioni IOT Edge** .
1. Esaminare l'elenco delle distribuzioni.Per ogni distribuzione, è possibile visualizzare i dettagli seguenti:

    | Colonna | Descrizione |
    | --- | --- |
    | ID | Nome della distribuzione. |
    | Tipo | Il tipo di distribuzione **, ovvero la distribuzione o la** distribuzione a più **livelli**. |
    | Target Condition (Condizione di destinazione) | Tag utilizzato per definire i dispositivi di destinazione. |
    | Priorità | Numero di priorità assegnato alla distribuzione. |
    | Metriche di sistema | Il numero di dispositivi gemelli nell'hub Internet che corrispondono alla condizione di destinazione. **Applicato** specifica il numero di dispositivi per i quali è stato applicato il contenuto di distribuzione ai moduli gemelli nell'hub. |
    | Metriche del dispositivo | Il numero di dispositivi IoT Edge segnala l'esito positivo o negativo della IoT Edge runtime del client. |
    | Metriche personalizzate | Il numero di dispositivi IoT Edge che segnalano i dati per le metriche definite per la distribuzione. |
    | Data creazione | Timestamp da quando è stata creata la distribuzione. Questo timestamp viene usato per stabilire la prevalenza quando due distribuzioni hanno la stessa priorità. |

1. Selezionare la distribuzione che si vuole monitorare.  
1. Nella pagina **Dettagli distribuzione** scorrere fino alla sezione inferiore e selezionare la scheda condizione di **destinazione** . Selezionare **Visualizza** per elencare i dispositivi che corrispondono alla condizione di destinazione. È possibile modificare la condizione e anche la **priorità**. Selezionare **Salva** se sono state apportate modifiche.

   ![Visualizzare i dispositivi di destinazione per una distribuzione](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. Selezionare la scheda **metrica** . Se si sceglie una metrica dall'elenco a discesa **Seleziona metrica** , viene visualizzato un pulsante **Visualizza** per visualizzare i risultati. È anche possibile selezionare **modifica metrica** per modificare i criteri per le metriche personalizzate definite. Selezionare **Salva** se sono state apportate modifiche.

   ![Visualizzare le metriche per una distribuzione](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)


Per apportare modifiche alla distribuzione, vedere [modificare una distribuzione](how-to-deploy-at-scale.md#modify-a-deployment).

## <a name="monitor-a-deployment-with-azure-cli"></a>Monitorare una distribuzione con l'interfaccia della riga di comando di Azure

Usare il comando [az IOT Edge Deployment Show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) per visualizzare i dettagli di una singola distribuzione:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Il comando Deployment Show accetta i parametri seguenti:

* **--deployment-id**: nome della distribuzione esistente nell'hub IoT. Parametro obbligatorio.
* **--Hub-Name** -nome dell'hub Internet delle cose in cui è presente la distribuzione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

Ispezionare la distribuzione nella finestra di comando.La proprietà **metrics** visualizza un conteggio per ogni metrica valutata da ciascun hub:

* **targetedCount**: metrica di sistema che specifica il numero di dispositivi gemelli presenti nell'hub IoT che corrispondono alla condizione di destinazione.
* **appliedCount**: metrica di sistema che specifica il numero di dispositivi ai cui moduli gemelli nell'hub IoT è stato applicato il contenuto della distribuzione.
* **reportedSuccessfulCount** : metrica del dispositivo che specifica il numero di dispositivi IOT Edge nella distribuzione che segnalano l'esito positivo del runtime del client IOT Edge.
* **reportedFailedCount** : metrica del dispositivo che specifica il numero di dispositivi IOT Edge nell'errore di segnalazione della distribuzione dal runtime del client IOT Edge.

È possibile visualizzare un elenco di ID dispositivo o oggetti per ogni metrica con il comando [az IOT Edge Deployment Show-Metric](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) :

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

Il comando Deployment Show-Metric accetta i parametri seguenti:

* **--deployment-id**: nome della distribuzione esistente nell'hub IoT.
* **--Metric-ID** : nome della metrica per cui si vuole visualizzare l'elenco di ID dispositivo, ad esempio `reportedFailedCount`.
* **--Hub-Name** -nome dell'hub Internet delle cose in cui è presente la distribuzione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

Per apportare modifiche alla distribuzione, vedere [modificare una distribuzione](how-to-deploy-cli-at-scale.md#modify-a-deployment).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [comunicare con EdgeAgent usando metodi diretti predefiniti](how-to-edgeagent-direct-method.md).
