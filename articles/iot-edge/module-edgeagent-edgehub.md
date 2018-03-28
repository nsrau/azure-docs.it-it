---
title: Informazioni di riferimento su EdgeAgent ed EdgeHub per Azure IoT | Microsoft Docs
description: Esaminare le proprietà specifiche e i rispettivi valori per i moduli gemelli edgeAgent ed edgeHub
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 17c97c6f233c9861ac42abc0a1f11089bb938e7c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="properties-of-the-edge-agent-and-edge-hub-module-twins"></a>Proprietà dei moduli gemelli "agente di Edge" e "hub di Edge"

L'agente di Edge e l'hub di Edge sono due moduli che costituiscono il runtime di IoT Edge. Per altre informazioni sulle operazioni eseguite da ciascun modulo, vedere [Informazioni sul runtime di Azure IoT Edge e la relativa architettura]](iot-edge-runtime.md). 

Questo articolo descrive le proprietà desiderate e quelle segnalate dei moduli gemelli del runtime. Per altre informazioni su come distribuire i moduli nei dispositivi IoT Edge, vedere [Deployment and monitoring][lnk-deploy] (Distribuzione e monitoraggio).

## <a name="edgeagent-desired-properties"></a>Proprietà desiderate di EdgeAgent

Il dispositivo gemello del modulo per l'agente Edge è denominato `$edgeAgent` e coordina le comunicazioni tra l'agente Edge in esecuzione su un dispositivo e l'hub IoT. Le proprietà desiderate vengono impostate durante l'applicazione di un manifesto della distribuzione in un dispositivo specifico nell'ambito di una distribuzione di un singolo dispositivo o su larga scala. 

| Proprietà | DESCRIZIONE | Obbligatoria |
| -------- | ----------- | -------- |
| schemaVersion | Deve essere "1.0" | Sì |
| runtime.type | Deve essere "docker" | Sì |
| runtime.settings.minDockerVersion | Impostata sulla versione minima di Docker richiesta da questo manifesto della distribuzione | Sì |
| runtime.settings.loggingOptions | Un file JSON in formato stringa contenente le opzioni di registrazione per il contenitore dell'agente Edge. [Opzioni di registrazione di Docker][lnk-docker-logging-options] | No  |
| systemModules.edgeAgent.type | Deve essere "docker" | Sì |
| systemModules.edgeAgent.settings.image | URI dell'immagine dell'agente Edge. Attualmente, l'agente Edge non è in grado di aggiornarsi automaticamente. | Sì |
| systemModules.edgeAgent.settings.createOptions | Un file JSON in formato stringa contenente le opzioni per la creazione del contenitore dell'agente Edge. [Opzioni di creazione di Docker][lnk-docker-create-options] | No  |
| systemModules.edgeAgent.configuration.id | ID della distribuzione che ha distribuito questo modulo. | Impostato dall'hub IoT quando viene applicato questo manifesto usando una distribuzione. Non fa parte di un manifesto della distribuzione. |
| systemModules.edgeHub.type | Deve essere "docker" | Sì |
| systemModules.edgeHub.status | Deve essere "running" | Sì |
| systemModules.edgeHub.restartPolicy | Deve essere "always" | Sì |
| systemModules.edgeHub.settings.image | URI dell'immagine dell'hub Edge. | Sì |
| systemModules.edgeHub.settings.createOptions | Un file JSON in formato stringa contenente le opzioni per la creazione del contenitore dell'hub Edge. [Opzioni di creazione di Docker][lnk-docker-create-options] | No  |
| systemModules.edgeHub.configuration.id | ID della distribuzione che ha distribuito questo modulo. | Impostato dall'hub IoT quando viene applicato questo manifesto usando una distribuzione. Non fa parte di un manifesto della distribuzione. |
| modules.{moduleId}.version | Una stringa definita dall'utente che rappresenta la versione di questo modulo. | Sì |
| modules.{moduleId}.type | Deve essere "docker" | Sì |
| modules.{moduleId}.restartPolicy | {"never" \| "on-failed" \| "on-unhealthy" \| "always"} | Sì |
| modules.{moduleId}.settings.image | URI dell'immagine del modulo. | Sì |
| modules.{moduleId}.settings.createOptions | Un file JSON in formato stringa contenente le opzioni per la creazione del contenitore del modulo. [Opzioni di creazione di Docker][lnk-docker-create-options] | No  |
| modules.{moduleId}.configuration.id | ID della distribuzione che ha distribuito questo modulo. | Impostato dall'hub IoT quando viene applicato questo manifesto usando una distribuzione. Non fa parte di un manifesto della distribuzione. |

## <a name="edgeagent-reported-properties"></a>Proprietà segnalate di EdgeAgent

Le proprietà segnalate dell'agente Edge includono tre tipi di informazioni principali:

1. Lo stato dell'applicazione delle proprietà desiderate visualizzate per ultime;
2. Lo stato dei moduli attualmente in esecuzione nel dispositivo, come segnalato dall'agente Edge;
3. Una copia delle proprietà desiderate attualmente in esecuzione nel dispositivo.

Quest'ultima informazione è utile nel caso in cui le proprietà desiderate più recenti non vengano applicate correttamente dal runtime e il dispositivo esegua ancora un manifesto della distribuzione precedente.

> [!NOTE]
> Le proprietà segnalate dell'agente Edge sono utili in quanto possono essere sottoposte a query con il [linguaggio di query dell'hub IoT][lnk-iothub-query] per esaminare lo stato delle distribuzioni su larga scala. Per altre informazioni su come usare questa funzionalità, fare riferimento a [Distribuzioni][lnk-deploy].

La tabella seguente non include le informazioni copiate dalle proprietà desiderate.

| Proprietà | DESCRIZIONE |
| -------- | ----------- |
| lastDesiredVersion | Questo integer si riferisce all'ultima versione delle proprietà desiderate elaborate dall'agente di Edge. |
| lastDesiredStatus.code | Questo è il codice di stato che fa riferimento alle ultime proprietà desiderate visualizzate dall'agente Edge. Valori consentiti: `200` Success, `400` Invalid configuration, `412` Invalid schema version, `417` the desired properties are empty, `500` Failed |
| lastDesiredStatus.description | Descrizione di testo dello stato |
| deviceHealth | `healthy` se lo stato di runtime di tutti i moduli è `running` o `stopped`, altrimenti `unhealthy` |
| configurationHealth.{deploymentId}.health | `healthy` se lo stato di runtime di tutti i moduli impostati da {deploymentId} della distribuzione è `running` o `stopped`, altrimenti `unhealthy` |
| runtime.platform.OS | Segnalazione del sistema operativo in esecuzione nel dispositivo |
| runtime.platform.architecture | Segnalazione dell'architettura della CPU nel dispositivo |
| systemModules.edgeAgent.runtimeStatus | Stato segnalato dell'agente Edge: {"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | Descrizione di testo dello stato segnalato dell'agente Edge. |
| systemModules.edgeHub.runtimeStatus | Stato corrente dell'hub Edge: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | Descrizione di testo dello stato corrente dell'hub Edge se unhealthy. |
| systemModules.edgeHub.exitCode | Se è stata chiusa, il codice di uscita segnalato dal contenitore dell'hub Edge |
| systemModules.edgeHub.startTimeUtc | Ora dell'ultimo avvio dell'hub Edge |
| systemModules.edgeHub.lastExitTimeUtc | Ora dell'ultima chiusura dell'hub Edge |
| systemModules.edgeHub.lastRestartTimeUtc | Ora dell'ultimo riavvio dell'hub Edge |
| systemModules.edgeHub.restartCount | Numero di tentativi di riavvio del modulo nell'ambito dei criteri di riavvio. |
| modules.{moduleId}.runtimeStatus | Stato corrente del modulo: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| modules.{moduleId}.statusDescription | Descrizione di testo dello stato corrente del modulo se unhealthy. |
| modules.{moduleId}.exitCode | Se è stata chiusa, il codice di uscita segnalato dal contenitore del modulo |
| modules.{moduleId}.startTimeUtc | Ora dell'ultimo avvio del modulo |
| modules.{moduleId}.lastExitTimeUtc | Ora dell'ultima chiusura del modulo |
| modules.{moduleId}.lastRestartTimeUtc | Ora dell'ultimo riavvio del modulo |
| modules.{moduleId}.restartCount | Numero di tentativi di riavvio del modulo nell'ambito dei criteri di riavvio. |

## <a name="edgehub-desired-properties"></a>Proprietà desiderate di EdgeHub

Il dispositivo gemello del modulo per l'hub Edge è denominato `$edgeHub` e coordina le comunicazioni tra l'hub Edge in esecuzione su un dispositivo e l'hub IoT. Le proprietà desiderate vengono impostate durante l'applicazione di un manifesto della distribuzione in un dispositivo specifico nell'ambito di una distribuzione di un singolo dispositivo o su larga scala. 

| Proprietà | DESCRIZIONE | Obbligatoria nel manifesto della distribuzione |
| -------- | ----------- | -------- |
| schemaVersion | Deve essere "1.0" | Sì |
| routes.{routeName} | Stringa che rappresenta una route dell'hub Edge. | L'elemento `routes` può essere presente, ma vuoto. |
| storeAndForwardConfiguration.timeToLiveSecs | Durata di conservazione dei messaggi in secondi da parte dell'hub di Edge in caso di endpoint di routing disconnessi, ad esempio disconnessi dall'hub IoT o dal modulo locale | Sì |

## <a name="edgehub-reported-properties"></a>Proprietà segnalate di EdgeHub

| Proprietà | DESCRIZIONE |
| -------- | ----------- |
| lastDesiredVersion | Questo integer si riferisce all'ultima versione delle proprietà desiderate elaborate dall'hub di Edge. |
| lastDesiredStatus.code | Questo è il codice di stato che fa riferimento alle ultime proprietà desiderate visualizzate dall'hub Edge. Valori consentiti: `200` Success, `400` Invalid configuration, `500` Failed |
| lastDesiredStatus.description | Descrizione di testo dello stato |
| clients.{device or module identity}.status | Stato di connettività del dispositivo o del modulo. Valori possibili: {"connected" \| "disconnected"}. Solo le identità del modulo possono essere in stato disconnected. I dispositivi downstream che si connettono all'hub Edge vengono visualizzati solo se lo stato è connected. |
| clients.{device or module identity}.lastConnectTime | Ultima ora di connessione del dispositivo o del modulo |
| clients.{device or module identity}.lastDisconnectTime | Ultima ora di disconnessione del dispositivo o del modulo |

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare queste proprietà per generare i manifesti di distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli IoT Edge](module-composition.md).

<!--links -->
[lnk-deploy]: module-deployment-monitoring.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
