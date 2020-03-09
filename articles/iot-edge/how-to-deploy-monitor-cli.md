---
title: Distribuire moduli su larga scala usando l'interfaccia della riga di comando di Azure-Azure IoT Edge
description: Usare l'estensione IoT dell'interfaccia della riga di comando di Azure per creare distribuzioni automatiche per i gruppi di dispositivi IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9152b38a0155b610f39f7de239bcc377ad96be5d
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78893261"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Distribuire e monitorare i moduli di IoT Edge su larga scala tramite l'interfaccia della riga di comando di Azure

Creare una **IOT Edge distribuzione automatica** usando l'interfaccia della riga di comando di Azure per gestire le distribuzioni in corso per molti dispositivi contemporaneamente. Le distribuzioni automatiche per IoT Edge fanno parte della funzionalità di [gestione automatica dei dispositivi](/azure/iot-hub/iot-hub-automatic-device-management) dell'hub Internet. Le distribuzioni sono processi dinamici che consentono di distribuire più moduli a più dispositivi, tenere traccia dello stato e dell'integrità dei moduli e apportare modifiche quando necessario.

Per altre informazioni, vedere [comprendere IOT Edge distribuzioni automatiche per singoli dispositivi o su larga scala](module-deployment-monitoring.md).

In questo articolo vengono configurate l'interfaccia della riga di comando di Azure e l'estensione IoT. Si apprenderà quindi come distribuire i moduli in un set di dispositivi IoT Edge e monitorare lo stato di avanzamento usando i comandi dell'interfaccia della riga di comando disponibili.

## <a name="cli-prerequisites"></a>Prerequisiti dell'interfaccia della riga di comando

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) nella sottoscrizione di Azure.
* [Dispositivi IoT Edge](how-to-register-device.md#prerequisites-for-the-azure-cli) con il runtime IoT Edge installato.
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) nell'ambiente in uso. Come minimo, la versione dell'interfaccia della riga di comando di Azure deve essere 2.0.70 o successiva. Usare il comando `az --version` per verificare. Questa versione supporta i comandi dell'estensione az e introduce il framework dei comandi Knack.
* [Estensione IoT per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configurare un manifesto della distribuzione

Un manifesto della distribuzione è un documento JSON contenente la descrizione dei moduli da distribuire, dei flussi di dati esistenti tra i moduli e delle proprietà desiderate dei moduli gemelli. Per ulteriori informazioni, vedere informazioni [su come distribuire moduli e definire route in IOT Edge](module-composition.md).

Per distribuire i moduli tramite l'interfaccia della riga di comando di Azure, salvare il manifesto della distribuzione a livello locale come file con estensione txt. Usare il percorso del file nella sezione successiva quando si esegue il comando per applicare la configurazione al dispositivo.

Di seguito è riportato un esempio di manifesto della distribuzione di base con un solo modulo:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

## <a name="layered-deployment"></a>Distribuzione a più livelli

Le distribuzioni sovrapposte sono un tipo di distribuzione automatica che può essere sovrapposta l'una all'altra. Per altre informazioni sulle distribuzioni a più livelli, vedere informazioni sulle [distribuzioni automatiche IOT Edge per singoli dispositivi o su larga scala](module-deployment-monitoring.md).

Le distribuzioni a più livelli possono essere create e gestite con l'interfaccia della riga di comando di Azure come qualsiasi distribuzione automatica, con poche differenze. Una volta creata una distribuzione a più livelli, la stessa interfaccia della riga di comando di Azure funziona per le distribuzioni su più livelli, come qualsiasi altra distribuzione. Per creare una distribuzione a più livelli, aggiungere il flag `--layered` al comando create.

La seconda differenza consiste nella costruzione del manifesto della distribuzione. Sebbene la distribuzione automatica standard debba contenere i moduli di runtime di sistema oltre ai moduli utente, le distribuzioni a più livelli possono contenere solo moduli utente. Al contrario, le distribuzioni a più livelli richiedono una distribuzione automatica standard anche in un dispositivo, per fornire i componenti necessari di ogni dispositivo IoT Edge, come i moduli di runtime di sistema.

Ecco un manifesto di distribuzione di base su più livelli con un modulo come esempio:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": ""
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

Nell'esempio precedente è stata illustrata un'impostazione di distribuzione su più livelli del `properties.desired` per un modulo. Se questa distribuzione a più livelli è destinata a un dispositivo in cui è già stato applicato lo stesso modulo, sovrascrive eventuali proprietà desiderate esistenti. Per aggiornare, anziché sovrascrivere, le proprietà desiderate, è possibile definire una nuova sottosezione. Ad esempio:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Per altre informazioni sulla configurazione dei moduli gemelli nelle distribuzioni a più livelli, vedere distribuzione a più [livelli](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>Identificare i dispositivi tramite tag

Prima di poter creare una distribuzione, è necessario essere in grado di specificare i dispositivi a cui la si vuole applicare. Azure IoT Edge identifica i dispositivi tramite **tag** nel dispositivo gemello. Ogni dispositivo può avere più tag definiti in qualsiasi modo appropriato per la soluzione. Ad esempio, il responsabile di un complesso di edifici intelligenti potrebbe aggiungere a un dispositivo i tag seguenti:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Per altre informazioni sui dispositivi gemelli e i tag, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Creare una distribuzione

Per distribuire moduli nei dispositivi di destinazione, si crea una distribuzione costituita dall'apposito manifesto e da altri parametri.

Usare il comando [AZ all Edge Deployment create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) per creare una distribuzione:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Usare lo stesso comando con il flag `--layered` per creare un deploymet a più livelli.

Il comando Deployment create accetta i parametri seguenti:

* **--Layered** -un flag facoltativo per identificare la distribuzione come una distribuzione a più livelli.
* **--deployment-id**: nome della distribuzione che verrà creata nell'hub IoT. Assegnare alla distribuzione un nome univoco contenente al massimo 128 lettere minuscole. Evitare gli spazi e i seguenti caratteri non validi: `& ^ [ ] { } \ | " < > /`. Parametro obbligatorio.
* **--content**: percorso file del manifesto della distribuzione JSON. Parametro obbligatorio.
* **--hub-name**: nome dell'hub IoT in cui verrà creata la distribuzione. L'hub deve trovarsi nella sottoscrizione corrente. Modificare la sottoscrizione corrente con il comando `az account set -s [subscription name]`.
* **--labels**: aggiungere etichette per tenere traccia delle distribuzioni. Le etichette sono coppie nome-valore che descrivono la distribuzione. Le etichette richiedono la formattazione JSON per nomi e valori. Ad esempio, usare `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition**: immettere una condizione di destinazione per determinare i dispositivi di destinazione di questa distribuzione.La condizione è basata sui tag del dispositivo gemello o sulle proprietà segnalate dal dispositivo gemello e deve corrispondere al formato dell'espressione.Ad esempio, `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--priority**: numero intero positivo. Nel caso in cui due o più distribuzioni abbiano lo stesso dispositivo di destinazione, verrà applicata quella con valore di priorità più alto.
* **--metrics** : consente di creare metriche che eseguono query sulle proprietà segnalate da edgeHub per tenere traccia dello stato di una distribuzione. Le metriche accettano input JSON o FilePath. Ad esempio: `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

## <a name="monitor-a-deployment"></a>Monitorare una distribuzione

Usare il comando [AZ all Edge Deployment Show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) per visualizzare i dettagli di una singola distribuzione:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Il comando Deployment Show accetta i parametri seguenti:

* **--deployment-id**: nome della distribuzione esistente nell'hub IoT. Parametro obbligatorio.
* **--hub-name**: nome dell'hub IoT in cui si trova la distribuzione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

Ispezionare la distribuzione nella finestra di comando.La proprietà **metrica** elenca un conteggio per ogni metrica valutata da ogni hub:

* **targetedCount**: metrica di sistema che specifica il numero di dispositivi gemelli presenti nell'hub IoT che corrispondono alla condizione di destinazione.
* **appliedCount**: metrica di sistema che specifica il numero di dispositivi ai cui moduli gemelli nell'hub IoT è stato applicato il contenuto della distribuzione.
* **reportedSuccessfulCount** : metrica del dispositivo che specifica il numero di dispositivi IOT Edge nella distribuzione che segnalano l'esito positivo del runtime del client IOT Edge.
* **reportedFailedCount** : metrica del dispositivo che specifica il numero di dispositivi IOT Edge nell'errore di segnalazione della distribuzione dal runtime del client IOT Edge.

È possibile visualizzare un elenco di ID dispositivo o oggetti per ogni metrica usando il comando [AZ Internet Edge Deployment Show-Metric](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) :

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

Il comando Deployment Show-Metric accetta i parametri seguenti:

* **--deployment-id**: nome della distribuzione esistente nell'hub IoT.
* **--Metric-ID** : nome della metrica per cui si vuole visualizzare l'elenco di ID dispositivo, ad esempio `reportedFailedCount`.
* **--hub-name**: nome dell'hub IoT in cui si trova la distribuzione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

## <a name="modify-a-deployment"></a>Modificare una distribuzione

Quando si modifica una distribuzione, le modifiche vengono replicate immediatamente in tutti i dispositivi di destinazione.

Se si aggiorna la condizione di destinazione, vengono eseguiti gli aggiornamenti seguenti:

* Se un dispositivo non soddisfa la condizione di destinazione precedente, ma soddisfa la nuova condizione di destinazione e questa distribuzione ha la priorità più alta per il dispositivo, la distribuzione viene applicata al dispositivo.
* Se un dispositivo che esegue la distribuzione non soddisfa più la condizione di destinazione, disinstalla questa distribuzione e riceve la distribuzione successiva nell'ordine di priorità.
* Se un dispositivo che esegue la distribuzione non soddisfa più la condizione di destinazione e non soddisfa la condizione di destinazione di tutte le altre distribuzioni, nel dispositivo non viene apportata alcuna modifica. Il dispositivo continua a eseguire i moduli corrente nello stato corrente, ma non è più gestito come parte di questa distribuzione. Quando soddisfa la condizione di destinazione di qualsiasi altra distribuzione, disinstalla questa distribuzione e riceve quella nuova.

Non è possibile aggiornare il contenuto di una distribuzione, che include i moduli e le route definiti nel manifesto della distribuzione. Se si desidera aggiornare il contenuto di una distribuzione, è necessario creare una nuova distribuzione destinata agli stessi dispositivi con una priorità più alta. È possibile modificare determinate proprietà di un modulo esistente, tra cui la condizione di destinazione, le etichette, le metriche e la priorità.

Usare il comando [AZ all Edge Deployment Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) per aggiornare una distribuzione:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Il comando di aggiornamento della distribuzione accetta i parametri seguenti:

* **--deployment-id**: nome della distribuzione esistente nell'hub IoT.
* **--hub-name**: nome dell'hub IoT in cui si trova la distribuzione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.
* **--set**: aggiorna una proprietà nella distribuzione. È possibile aggiornare le proprietà seguenti:
  * targetCondition, ad esempio `targetCondition=tags.location.state='Oregon'`
  * Etichette
  * priority
* **--Add** -aggiunge una nuova proprietà alla distribuzione, incluse le condizioni di destinazione o le etichette.
* **--Remove** : rimuove una proprietà esistente, incluse le condizioni di destinazione o le etichette.

## <a name="delete-a-deployment"></a>Eliminare una distribuzione

Quando si elimina una distribuzione, tutti i dispositivi ricevono la distribuzione successiva nell'ordine di priorità. Se i dispositivi non soddisfano la condizione di destinazione di qualsiasi altra distribuzione, i moduli non vengono rimossi in seguito all'eliminazione della distribuzione.

Usare il comando [AZ all Edge Deployment Delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) per eliminare una distribuzione:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

Il comando Deployment Delete accetta i parametri seguenti:

* **--deployment-id**: nome della distribuzione esistente nell'hub IoT.
* **--hub-name**: nome dell'hub IoT in cui si trova la distribuzione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [distribuzione di moduli per IOT Edge dispositivi](module-deployment-monitoring.md).
