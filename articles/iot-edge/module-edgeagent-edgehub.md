---
title: Proprietà dei gemelli del modulo agente e del modulo hub - Azure IoT Edge
description: Esaminare le proprietà specifiche e i rispettivi valori per i moduli gemelli edgeAgent ed edgeHub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f2d6603c264c9da3f2700f460a8c61b24681fac6
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546196"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Proprietà dei moduli gemelli "agente di IoT Edge" e "hub di IoT Edge"

L'agente di IoT Edge e l'hub di IoT Edge sono due moduli che costituiscono il runtime di IoT Edge. Per altre informazioni sulle responsabilità di ogni modulo di runtime, vedere Informazioni sul runtime di [Azure IoT Edge e](iot-edge-runtime.md)sulla relativa architettura.

Questo articolo descrive le proprietà desiderate e quelle segnalate dei moduli gemelli del runtime. Per ulteriori informazioni su come distribuire i moduli nei dispositivi IoT Edge, vedere [Informazioni su come distribuire moduli e stabilire route in IoT Edge](module-composition.md).

Un modulo gemello include:

* **Proprietà desiderate**. Il back-end della soluzione può impostare le proprietà desiderate e il modulo può leggerle. Il modulo può anche ricevere notifiche delle modifiche nelle proprietà desiderate. Le proprietà desiderate vengono utilizzate insieme alle proprietà segnalate per sincronizzare la configurazione o le condizioni del modulo.

* **Proprietà segnalate**. Il modulo può impostare le proprietà segnalate e il back-end della soluzione può leggerle ed eseguire query su di esse. Le proprietà segnalate vengono utilizzate insieme alle proprietà desiderate per sincronizzare la configurazione o le condizioni del modulo.

## <a name="edgeagent-desired-properties"></a>Proprietà desiderate di EdgeAgent

Il dispositivo gemello del modulo per l'agente di IoT Edge è denominato `$edgeAgent` e coordina le comunicazioni tra l'agente di IoT Edge in esecuzione su un dispositivo e l'hub IoT. Le proprietà desiderate vengono impostate durante l'applicazione di un manifesto della distribuzione in un dispositivo specifico nell'ambito di una distribuzione di un singolo dispositivo o su larga scala.

| Proprietà | Descrizione | Obbligatoria |
| -------- | ----------- | -------- |
| schemaVersion | Deve essere "1.0" | Sì |
| runtime.type | Deve essere "docker" | Sì |
| runtime.settings.minDockerVersion | Impostata sulla versione minima di Docker richiesta da questo manifesto della distribuzione | Sì |
| runtime.settings.loggingOptions | Un file JSON in formato stringa contenente le opzioni di registrazione per il contenitore dell'agente di IoT Edge. [Opzioni di registrazione di Docker](https://docs.docker.com/engine/admin/logging/overview/) | No |
| runtime.settings.registryCredentials<br>.{registryId}.username | Nome utente del registro contenitori. Per Registro Azure Container, il nome utente corrisponde in genere al nome del registro.<br><br> Le credenziali del Registro di sistema sono necessarie per tutte le immagini del modulo privato. | No |
| runtime.settings.registryCredentials<br>.{registryId}.password | Password del registro contenitori. | No |
| runtime.settings.registryCredentials<br>.{registryId}.address | Indirizzo del registro contenitori. Per Registro Azure Container, l'indirizzo è in genere *{nome registro}.azurecr.io*. | No |  
| systemModules.edgeAgent.type | Deve essere "docker" | Sì |
| systemModules.edgeAgent.settings.image | URI dell'immagine dell'agente di IoT Edge. Attualmente, l'agente IoT Edge non è in grado di aggiornarsi. | Sì |
| systemModules.edgeAgent.settings<br>.createOptions | Un file JSON in formato stringa contenente le opzioni per la creazione del contenitore dell'agente di IoT Edge. [Opzioni di creazione di Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeAgent.configuration.id | ID della distribuzione che ha distribuito questo modulo. | L'hub IoT imposta questa proprietà quando il manifesto viene applicato tramite una distribuzione. Non fa parte di un manifesto della distribuzione. |
| systemModules.edgeHub.type | Deve essere "docker" | Sì |
| systemModules.edgeHub.status | Deve essere "running" | Sì |
| systemModules.edgeHub.restartPolicy | Deve essere "always" | Sì |
| systemModules.edgeHub.settings.image | URI dell'immagine dell'hub di IoT Edge. | Sì |
| systemModules.edgeHub.settings<br>.createOptions | Un file JSON in formato stringa contenente le opzioni per la creazione del contenitore dell'hub di IoT Edge. [Opzioni di creazione di Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeHub.configuration.id | ID della distribuzione che ha distribuito questo modulo. | L'hub IoT imposta questa proprietà quando il manifesto viene applicato tramite una distribuzione. Non fa parte di un manifesto della distribuzione. |
| modules.{moduleId}.version | Una stringa definita dall'utente che rappresenta la versione di questo modulo. | Sì |
| modules.{moduleId}.type | Deve essere "docker" | Sì |
| modules.{moduleId}.status | {"running" \| "stopped"} | Sì |
| modules.{moduleId}.restartPolicy | "mai" \| "in caso \| di errore" "on-non integro" \| "sempre" | Sì |
| Moduli. "moduleId".imagePullPolicy (informazioni in stato informazione) | "on-create" \| "mai" | No |
| Moduli. .env (IDmodulo) | Elenco di variabili di ambiente da passare al modulo. Prende il formato`"<name>": {"value": "<value>"}` | No |
| modules.{moduleId}.settings.image | URI dell'immagine del modulo. | Sì |
| modules.{moduleId}.settings.createOptions | Un file JSON in formato stringa contenente le opzioni per la creazione del contenitore del modulo. [Opzioni di creazione di Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| modules.{moduleId}.configuration.id | ID della distribuzione che ha distribuito questo modulo. | L'hub IoT imposta questa proprietà quando il manifesto viene applicato tramite una distribuzione. Non fa parte di un manifesto della distribuzione. |

## <a name="edgeagent-reported-properties"></a>Proprietà segnalate di EdgeAgent

Le proprietà segnalate dell'agente di IoT Edge includono tre tipi principali di informazioni:

1. Lo stato dell'applicazione delle proprietà desiderate visualizzate per ultime;
2. Lo stato dei moduli attualmente in esecuzione nel dispositivo, come segnalato dall'agente di IoT Edge;
3. Una copia delle proprietà desiderate attualmente in esecuzione nel dispositivo.

La copia delle proprietà desiderate correnti è utile per indicare se il dispositivo ha applicato la distribuzione più recente o è ancora in esecuzione un manifesto di distribuzione precedente.

> [!NOTE]
> Le proprietà segnalate dell'agente di IoT Edge sono utili in quanto possono essere sottoposte a query con il [linguaggio di query dell'hub IoT](../iot-hub/iot-hub-devguide-query-language.md) per esaminare lo stato delle distribuzioni su larga scala. Per altre informazioni su come usare le proprietà dell'agente di IoT Edge relative allo stato, vedere [Informazioni sulle distribuzioni IoT Edge per singoli dispositivi o su vasta scala](module-deployment-monitoring.md).

La tabella seguente non include le informazioni copiate dalle proprietà desiderate.

| Proprietà | Descrizione |
| -------- | ----------- |
| lastDesiredVersion | Questo integer si riferisce all'ultima versione delle proprietà desiderate elaborate dall'agente di IoT Edge. |
| lastDesiredStatus.code | Questo codice di stato fa riferimento alle ultime proprietà desiderate visualizzate dall'agente IoT Edge. Valori consentiti: `200` Success, `400` Invalid configuration, `412` Invalid schema version, `417` the desired properties are empty, `500` Failed |
| lastDesiredStatus.description | Descrizione di testo dello stato |
| deviceHealth | `healthy` se lo stato di runtime di tutti i moduli è `running` o `stopped`, altrimenti `unhealthy` |
| configurationHealth.{deploymentId}.health | `healthy` se lo stato di runtime di tutti i moduli impostati da {deploymentId} della distribuzione è `running` o `stopped`, altrimenti `unhealthy` |
| runtime.platform.OS | Segnalazione del sistema operativo in esecuzione nel dispositivo |
| runtime.platform.architecture | Segnalazione dell'architettura della CPU nel dispositivo |
| systemModules.edgeAgent.runtimeStatus | Stato segnalato dell'agente di IoT Edge: {"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | Descrizione di testo dello stato segnalato dell'agente di IoT Edge. |
| systemModules.edgeHub.runtimeStatus | Stato dell'hub di IoT Edge: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | Descrizione di testo dello stato dell'hub di IoT Edge, se unhealthy. |
| systemModules.edgeHub.exitCode | Il codice di uscita segnalato dal contenitore dell'hub IoT Edge se il contenitore viene chiuso |
| systemModules.edgeHub.startTimeUtc | Ora dell'ultimo avvio dell'hub di IoT Edge |
| systemModules.edgeHub.lastExitTimeUtc | Ora dell'ultima chiusura dell'hub di IoT Edge |
| systemModules.edgeHub.lastRestartTimeUtc | Ora dell'ultimo riavvio dell'hub di IoT Edge |
| systemModules.edgeHub.restartCount | Numero di tentativi di riavvio del modulo nell'ambito dei criteri di riavvio. |
| modules.{moduleId}.runtimeStatus | Stato del modulo: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| modules.{moduleId}.statusDescription | Descrizione di testo dello stato del modulo, se unhealthy. |
| modules.{moduleId}.exitCode | Il codice di uscita segnalato dal contenitore del modulo se il contenitore |
| modules.{moduleId}.startTimeUtc | Ora dell'ultimo avvio del modulo |
| modules.{moduleId}.lastExitTimeUtc | Ora dell'ultima chiusura del modulo |
| modules.{moduleId}.lastRestartTimeUtc | Ora dell'ultimo riavvio del modulo |
| modules.{moduleId}.restartCount | Numero di tentativi di riavvio del modulo nell'ambito dei criteri di riavvio. |

## <a name="edgehub-desired-properties"></a>Proprietà desiderate di EdgeHub

Il dispositivo gemello del modulo per l'hub di IoT Edge è denominato `$edgeHub` e coordina le comunicazioni tra l'hub di IoT Edge in esecuzione su un dispositivo e l'hub IoT. Le proprietà desiderate vengono impostate durante l'applicazione di un manifesto della distribuzione in un dispositivo specifico nell'ambito di una distribuzione di un singolo dispositivo o su larga scala.

| Proprietà | Descrizione | Obbligatoria nel manifesto della distribuzione |
| -------- | ----------- | -------- |
| schemaVersion | Deve essere "1.0" | Sì |
| routes.{routeName} | Stringa che rappresenta una route dell'hub di IoT Edge. Per ulteriori informazioni, vedere [Dichiarare route](module-composition.md#declare-routes). | L'elemento `routes` può essere presente, ma vuoto. |
| storeAndForwardConfiguration.timeToLiveSecs | Tempo in secondi durante il periodo in cui l'hub IoT Edge mantiene i messaggi se disconnessi dagli endpoint di routing, sia che si tratti di un hub IoT che di un modulo locale. Il valore può essere qualsiasi numero intero positivo. | Sì |

## <a name="edgehub-reported-properties"></a>Proprietà segnalate di EdgeHub

| Proprietà | Descrizione |
| -------- | ----------- |
| lastDesiredVersion | Questo integer si riferisce all'ultima versione delle proprietà desiderate elaborate dall'hub di IoT Edge. |
| lastDesiredStatus.code | Codice di stato che fa riferimento alle ultime proprietà desiderate visualizzate dall'hub IoT Edge. Valori consentiti: `200` Success, `400` Invalid configuration, `500` Failed |
| lastDesiredStatus.description | Descrizione testuale dello stato. |
| clients.{device or moduleId}.status | Stato di connettività del dispositivo o del modulo. Valori possibili: {"connected" \| "disconnected"}. Solo le identità del modulo possono essere in stato disconnected. I dispositivi downstream che si connettono all'hub di IoT Edge vengono visualizzati solo quando lo stato è connected. |
| clients.{device or moduleId}.lastConnectTime | L'ultima volta che il dispositivo o il modulo si è collegato. |
| clients.{device or moduleId}.lastDisconnectTime | L'ultima volta che il dispositivo o il modulo si è disconnesso. |

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare queste proprietà per generare i manifesti di distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli IoT Edge](module-composition.md).
