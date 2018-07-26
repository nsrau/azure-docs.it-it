---
title: Distribuire e monitorare i moduli per Azure IoT Edge (CLI) | Microsoft Docs
description: Gestire i moduli eseguiti su dispositivi perimetrali
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 07/25/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b90c26eaa36c906dda904106b104c3dbf04a55ce
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257981"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Distribuire e monitorare i moduli di IoT Edge su larga scala tramite l'interfaccia della riga di comando di Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

Azure IoT Edge consente di spostare le attività di analisi sul perimetro e offre un'interfaccia cloud per gestire e monitorare i dispositivi IoT Edge senza dover accedere fisicamente a ciascuno di essi. La possibilità di gestire in remoto i dispositivi è sempre più importante con il progressivo diffondersi di soluzioni IoT (Internet delle cose) sempre più estese e complesse. Azure IoT Edge è progettato per supportare gli obiettivi aziendali, indipendentemente dal numero di dispositivi aggiunti.

È possibile gestire singoli dispositivi e distribuire i moduli in tali dispositivi uno alla volta. Tuttavia, per apportare modifiche ai dispositivi su larga scala, è possibile creare una **distribuzione automatica IoT Edge**, che rientra nella gestione automatica dei dispositivi nell'hub IoT. Le distribuzioni sono processi dinamici che consentono di distribuire più moduli in più dispositivi contemporaneamente, di tenere traccia dello stato e dell'integrità dei moduli, nonché di apportare modifiche all'occorrenza. 

In questo articolo vengono configurate l'interfaccia della riga di comando di Azure 2.0 e l'estensione IoT. Vengono quindi fornite informazioni su come distribuire moduli in un set di dispositivi IoT Edge usando i comandi disponibili dell'interfaccia della riga di comando.

## <a name="cli-prerequisites"></a>Prerequisiti dell'interfaccia della riga di comando

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) nella sottoscrizione di Azure. 
* [Dispositivi IoT Edge](how-to-register-device-cli.md) con il runtime IoT Edge installato.
* [Interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) nell'ambiente. La versione dell'interfaccia della riga di comando di Azure 2.0 deve essere 2.0.24 o successiva. Usare `az –-version` per la convalida. Questa versione supporta i comandi dell'estensione az e introduce il framework dei comandi Knack. 
* L'[estensione IoT per l'interfaccia della riga di comando di Azure 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configurare un manifesto della distribuzione

Un manifesto della distribuzione è un documento JSON contenente la descrizione dei moduli da distribuire, dei flussi di dati esistenti tra i moduli e delle proprietà desiderate dei moduli gemelli. Per altre informazioni sul funzionamento e sulla modalità di creazione dei manifesti della distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli di IoT Edge](module-composition.md).

Per distribuire i moduli tramite l'interfaccia della riga di comando di Azure 2.0, salvare il manifesto della distribuzione a livello locale come file con estensione txt. Il percorso del file verrà usato nella sezione successiva quando si eseguirà il comando per applicare la configurazione al dispositivo. 

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
                   "registryCredentials": {
                     "registryName": {
                       "username": "",
                       "password": "",
                       "address": ""
                     }
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
                     "createOptions": "{}"
                   }
                 }
               },
               "modules": {
                 "tempSensor": {
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
                   "route": "FROM /* INTO $upstream"
               },
               "storeAndForwardConfiguration": {
                 "timeToLiveSecs": 7200
               }
             }
           },
           "tempSensor": {
             "properties.desired": {}
           }
         }
       }
   }
   ```


## <a name="identify-devices-using-tags"></a>Identificare i dispositivi tramite tag

Prima di poter creare una distribuzione, è necessario essere in grado di specificare i dispositivi a cui la si vuole applicare. Azure IoT Edge identifica i dispositivi tramite **tag** nel dispositivo gemello. Ogni dispositivo può avere più tag ed è possibile definirli in qualsiasi modo risulti appropriato per una soluzione specifica. Ad esempio, il responsabile di un complesso di edifici intelligenti potrebbe aggiungere a un dispositivo i tag seguenti:

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

Per altre informazioni sui dispositivi gemelli e i tag, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT][lnk-device-twin].

## <a name="create-a-deployment"></a>Creare una distribuzione

Per distribuire moduli nei dispositivi di destinazione, si crea una distribuzione costituita dall'apposito manifesto e da altri parametri. 

Usare il comando seguente per creare una distribuzione:

   ```cli
   az iot edge deployment create --deployment-id [deployment id] --labels [labels] --content [file path] --hub-name [hub name] --target-condition [target query] --priority [int]
   ```

* **--deployment-id**: nome della distribuzione che verrà creata nell'hub IoT. Assegnare alla distribuzione un nome univoco contenente al massimo 128 lettere minuscole. Evitare gli spazi e i seguenti caratteri non validi: `& ^ [ ] { } \ | " < > /`.
* **--labels**: aggiungere etichette per tenere traccia delle distribuzioni. Le etichette sono coppie nome-valore che descrivono la distribuzione. Ad esempio, `HostPlatform, Linux` o `Version, 3.0.1`
* **--content**: percorso file del manifesto della distribuzione JSON. 
* **--hub-name**: nome dell'hub IoT in cui verrà creata la distribuzione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.
* **--target-condition**: immettere una condizione di destinazione per determinare i dispositivi di destinazione di questa distribuzione. La condizione è basata sui tag o sulle proprietà segnalate dei dispositivi gemelli e deve corrispondere al formato di espressione. Ad esempio, `tags.environment='test'` o `properties.reported.devicemodel='4000x'`. 
* **--priority**: numero intero positivo. Nel caso in cui due o più distribuzioni abbiano lo stesso dispositivo di destinazione, verrà applicata quella con valore di priorità più alto.

## <a name="monitor-a-deployment"></a>Monitorare una distribuzione

Per visualizzare il contenuto di una distribuzione, usare il comando seguente:

   ```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
   ```
* **--deployment-id**: nome della distribuzione esistente nell'hub IoT.
* **--hub-name**: nome dell'hub IoT in cui si trova la distribuzione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

Ispezionare la distribuzione nella finestra di comando. La proprietà **metrics** visualizza un conteggio per ogni metrica valutata da ciascun hub:
* **targetedCount**: metrica di sistema che specifica il numero di dispositivi gemelli presenti nell'hub IoT che corrispondono alla condizione di destinazione.
* **appliedCount**: metrica di sistema che specifica il numero di dispositivi ai cui moduli gemelli nell'hub IoT è stato applicato il contenuto della distribuzione.
* **reportedSuccessfulCount**: metrica del dispositivo che specifica il numero di dispositivi Edge presenti nella distribuzione che segnalano il completamento dell'operazione eseguita dal runtime del client di IoT Edge.
* **reportedFailedCount**: metrica del dispositivo che specifica il numero di dispositivi Edge presenti nella distribuzione che segnalano gli errori generati dal runtime del client di IoT Edge.

È possibile visualizzare un elenco di oggetti o ID dispositivo per ognuna delle metriche tramite il comando seguente:

   ```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
   ```

* **--deployment-id**: nome della distribuzione esistente nell'hub IoT.
* **--metric-id**: nome della metrica per la quale si vuole visualizzare l'elenco di ID dispositivo, ad esempio `reportedFailedCount`.
* **--hub-name**: nome dell'hub IoT in cui si trova la distribuzione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

## <a name="modify-a-deployment"></a>Modificare una distribuzione

Quando si modifica una distribuzione, le modifiche vengono replicate immediatamente in tutti i dispositivi di destinazione. 

Se si aggiorna la condizione di destinazione, vengono eseguiti gli aggiornamenti seguenti:
* Se un dispositivo non soddisfa la condizione di destinazione precedente, ma soddisfa la nuova condizione di destinazione e questa distribuzione ha la priorità più alta per il dispositivo, la distribuzione viene applicata al dispositivo. 
* Se un dispositivo che esegue la distribuzione non soddisfa più la condizione di destinazione, disinstalla questa distribuzione e riceve la distribuzione successiva nell'ordine di priorità. 
* Se un dispositivo che esegue la distribuzione non soddisfa più la condizione di destinazione e non soddisfa la condizione di destinazione di tutte le altre distribuzioni, nel dispositivo non viene apportata alcuna modifica. Il dispositivo continua a eseguire i moduli corrente nello stato corrente, ma non è più gestito come parte di questa distribuzione. Quando soddisfa la condizione di destinazione di qualsiasi altra distribuzione, disinstalla questa distribuzione e riceve quella nuova. 

Per aggiornare una distribuzione, usare il comando seguente:

   ```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
   ```
* **--deployment-id**: nome della distribuzione esistente nell'hub IoT.
* **--hub-name**: nome dell'hub IoT in cui si trova la distribuzione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.
* **--set**: aggiorna una proprietà nella distribuzione. È possibile aggiornare le proprietà seguenti:
    * targetCondition, ad esempio `targetCondition=tags.location.state='Oregon'`
    * Etichette 
    * priority


## <a name="delete-a-deployment"></a>Eliminare una distribuzione

Quando si elimina una distribuzione, tutti i dispositivi ricevono la distribuzione successiva nell'ordine di priorità. Se i dispositivi non soddisfano la condizione di destinazione di qualsiasi altra distribuzione, i moduli non vengono rimossi in seguito all'eliminazione della distribuzione. 

Per eliminare una distribuzione, usare il comando seguente:

   ```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
   ```
* **--deployment-id**: nome della distribuzione esistente nell'hub IoT.
* **--hub-name**: nome dell'hub IoT in cui si trova la distribuzione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [distribuzione di moduli nei dispositivi perimetrali][lnk-deployments].

<!-- Images -->
[1]: ./media/how-to-deploy-monitor/iot-edge-deployments.png

<!-- Links -->
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-portal]: https://portal.azure.com
[lnk-docker-create]: https://docs.docker.com/engine/reference/commandline/create/
[lnk-deployments]: module-deployment-monitoring.md

<!-- Anchor links -->
[anchor-monitor]: #monitor-a-deployment
