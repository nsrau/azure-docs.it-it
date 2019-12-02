---
title: Proprietà dei dispositivi gemelli dell'agente e del modulo Hub-Azure IoT Edge
description: Esaminare le proprietà specifiche e i rispettivi valori per i moduli gemelli edgeAgent ed edgeHub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 731c51894126a6de75c9fc25e4e7bdb3dfa4dd03
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665798"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Proprietà dei moduli gemelli "agente di IoT Edge" e "hub di IoT Edge"

L'agente di IoT Edge e l'hub di IoT Edge sono due moduli che costituiscono il runtime di IoT Edge. Per altre informazioni sulle operazioni eseguite da ciascun modulo, vedere [Informazioni sul runtime di Azure IoT Edge e la relativa architettura](iot-edge-runtime.md). 

Questo articolo descrive le proprietà desiderate e quelle segnalate dei moduli gemelli del runtime. Per altre informazioni su come distribuire i moduli nei dispositivi IoT Edge, vedere [informazioni su come distribuire moduli e definire route in IOT Edge](module-composition.md).

Un modulo gemello include: 

* **Proprietà desiderate**. Il back-end della soluzione può impostare le proprietà desiderate, che possono essere lette dal modulo. Il modulo può inoltre ricevere notifiche delle modifiche apportate alle proprietà desiderate. Le proprietà desiderate vengono usate insieme alle proprietà segnalate per sincronizzare la configurazione o le condizioni del modulo.

* **Proprietà segnalate**. Il modulo può impostare le proprietà segnalate e il back-end della soluzione può leggere ed eseguire query su di essi. Le proprietà segnalate vengono usate insieme alle proprietà desiderate per sincronizzare la configurazione o le condizioni del modulo. 

## <a name="edgeagent-desired-properties"></a>Proprietà desiderate di EdgeAgent

Il dispositivo gemello del modulo per l'agente di IoT Edge è denominato `$edgeAgent` e coordina le comunicazioni tra l'agente di IoT Edge in esecuzione su un dispositivo e l'hub IoT. Le proprietà desiderate vengono impostate durante l'applicazione di un manifesto della distribuzione in un dispositivo specifico nell'ambito di una distribuzione di un singolo dispositivo o su larga scala. 

| Proprietà | Description | Obbligatoria |
| -------- | ----------- | -------- |
| schemaVersion | Deve essere "1.0" | SÌ |
| runtime.type | Deve essere "docker" | SÌ |
| runtime.settings.minDockerVersion | Impostata sulla versione minima di Docker richiesta da questo manifesto della distribuzione | SÌ |
| runtime.settings.loggingOptions | Un file JSON in formato stringa contenente le opzioni di registrazione per il contenitore dell'agente di IoT Edge. [Opzioni di registrazione di Docker](https://docs.docker.com/engine/admin/logging/overview/) | No |
| runtime.settings.registryCredentials<br>.{registryId}.username | Nome utente del registro contenitori. Per Registro Azure Container, il nome utente corrisponde in genere al nome del registro.<br><br> Le credenziali del registro sono necessarie per tutte le immagini di modulo che non sono pubbliche. | No |
| runtime.settings.registryCredentials<br>.{registryId}.password | Password del registro contenitori. | No |
| runtime.settings.registryCredentials<br>.{registryId}.address | Indirizzo del registro contenitori. Per Registro Azure Container, l'indirizzo è in genere *{nome registro}.azurecr.io*. | No |  
| systemModules.edgeAgent.type | Deve essere "docker" | SÌ |
| systemModules.edgeAgent.settings.image | URI dell'immagine dell'agente di IoT Edge. Al momento, l'agente di IoT Edge non è in grado di aggiornarsi automaticamente. | SÌ |
| systemModules.edgeAgent.settings<br>.createOptions | Un file JSON in formato stringa contenente le opzioni per la creazione del contenitore dell'agente di IoT Edge. [Opzioni di creazione di Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeAgent.configuration.id | ID della distribuzione che ha distribuito questo modulo. | L'hub IoT imposta questa proprietà quando il manifesto viene applicato tramite una distribuzione. Non fa parte di un manifesto della distribuzione. |
| systemModules.edgeHub.type | Deve essere "docker" | SÌ |
| systemModules.edgeHub.status | Deve essere "running" | SÌ |
| systemModules.edgeHub.restartPolicy | Deve essere "always" | SÌ |
| systemModules.edgeHub.settings.image | URI dell'immagine dell'hub di IoT Edge. | SÌ |
| systemModules.edgeHub.settings<br>.createOptions | Un file JSON in formato stringa contenente le opzioni per la creazione del contenitore dell'hub di IoT Edge. [Opzioni di creazione di Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeHub.configuration.id | ID della distribuzione che ha distribuito questo modulo. | L'hub IoT imposta questa proprietà quando il manifesto viene applicato tramite una distribuzione. Non fa parte di un manifesto della distribuzione. |
| modules.{moduleId}.version | Una stringa definita dall'utente che rappresenta la versione di questo modulo. | SÌ |
| modules.{moduleId}.type | Deve essere "docker" | SÌ |
| modules.{moduleId}.status | {"running" \| "stopped"} | SÌ |
| modules.{moduleId}.restartPolicy | {"Never" \| "on-failure" \| "on-unhealthy" \| "Always"} | SÌ |
| moduli. {moduleId}. imagePullPolicy | {"on-create" \| "Never"} | No |
| modules.{moduleId}.settings.image | URI dell'immagine del modulo. | SÌ |
| modules.{moduleId}.settings.createOptions | Un file JSON in formato stringa contenente le opzioni per la creazione del contenitore del modulo. [Opzioni di creazione di Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| modules.{moduleId}.configuration.id | ID della distribuzione che ha distribuito questo modulo. | L'hub IoT imposta questa proprietà quando il manifesto viene applicato tramite una distribuzione. Non fa parte di un manifesto della distribuzione. |

## <a name="edgeagent-reported-properties"></a>Proprietà segnalate di EdgeAgent

Le proprietà segnalate dell'agente di IoT Edge includono tre tipi principali di informazioni:

1. Lo stato dell'applicazione delle proprietà desiderate visualizzate per ultime;
2. Lo stato dei moduli attualmente in esecuzione nel dispositivo, come segnalato dall'agente di IoT Edge;
3. Una copia delle proprietà desiderate attualmente in esecuzione nel dispositivo.

Queste ultime informazioni, una copia delle proprietà desiderate correnti, sono utili per indicare se il dispositivo ha applicato le proprietà desiderate più recenti o se è ancora in esecuzione un manifesto di distribuzione precedente.

> [!NOTE]
> Le proprietà segnalate dell'agente di IoT Edge sono utili in quanto possono essere sottoposte a query con il [linguaggio di query dell'hub IoT](../iot-hub/iot-hub-devguide-query-language.md) per esaminare lo stato delle distribuzioni su larga scala. Per altre informazioni su come usare le proprietà dell'agente di IoT Edge relative allo stato, vedere [Informazioni sulle distribuzioni IoT Edge per singoli dispositivi o su vasta scala](module-deployment-monitoring.md).

La tabella seguente non include le informazioni copiate dalle proprietà desiderate.

| Proprietà | Description |
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
| modules.{moduleId}.exitCode | Il codice di uscita segnalato dal contenitore del modulo se il contenitore viene chiuso |
| modules.{moduleId}.startTimeUtc | Ora dell'ultimo avvio del modulo |
| modules.{moduleId}.lastExitTimeUtc | Ora dell'ultima chiusura del modulo |
| modules.{moduleId}.lastRestartTimeUtc | Ora dell'ultimo riavvio del modulo |
| modules.{moduleId}.restartCount | Numero di tentativi di riavvio del modulo nell'ambito dei criteri di riavvio. |

## <a name="edgehub-desired-properties"></a>Proprietà desiderate di EdgeHub

Il dispositivo gemello del modulo per l'hub di IoT Edge è denominato `$edgeHub` e coordina le comunicazioni tra l'hub di IoT Edge in esecuzione su un dispositivo e l'hub IoT. Le proprietà desiderate vengono impostate durante l'applicazione di un manifesto della distribuzione in un dispositivo specifico nell'ambito di una distribuzione di un singolo dispositivo o su larga scala. 

| Proprietà | Description | Obbligatoria nel manifesto della distribuzione |
| -------- | ----------- | -------- |
| schemaVersion | Deve essere "1.0" | SÌ |
| routes.{routeName} | Stringa che rappresenta una route dell'hub di IoT Edge. Per altre informazioni, vedere [Declare Routes](module-composition.md#declare-routes). | L'elemento `routes` può essere presente, ma vuoto. |
| storeAndForwardConfiguration.timeToLiveSecs | Tempo in secondi durante il quale l'hub IoT Edge mantiene i messaggi in caso di disconnessione dagli endpoint di routing, sia che si tratti di un modulo locale o dell'hub. Il valore può essere qualsiasi numero intero positivo. | SÌ |

## <a name="edgehub-reported-properties"></a>Proprietà segnalate di EdgeHub

| Proprietà | Description |
| -------- | ----------- |
| lastDesiredVersion | Questo integer si riferisce all'ultima versione delle proprietà desiderate elaborate dall'hub di IoT Edge. |
| lastDesiredStatus.code | Codice di stato che fa riferimento alle ultime proprietà desiderate visualizzate dall'hub IoT Edge. Valori consentiti: `200` Success, `400` Invalid configuration, `500` Failed |
| lastDesiredStatus.description | Descrizione del testo dello stato. |
| clients.{device or moduleId}.status | Stato di connettività del dispositivo o del modulo. Valori possibili: {"connected" \| "disconnected"}. Solo le identità del modulo possono essere in stato disconnected. I dispositivi downstream che si connettono all'hub di IoT Edge vengono visualizzati solo quando lo stato è connected. |
| clients.{device or moduleId}.lastConnectTime | Ora dell'ultima connessione del dispositivo o del modulo. |
| clients.{device or moduleId}.lastDisconnectTime | Ora dell'ultima disconnessione del dispositivo o del modulo. |

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare queste proprietà per generare i manifesti di distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli IoT Edge](module-composition.md).
