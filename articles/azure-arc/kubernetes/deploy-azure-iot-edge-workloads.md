---
title: Distribuire i carichi di lavoro di Azure IoT Edge (Anteprima)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Distribuire i carichi di lavoro di Azure IoT Edge
keywords: Kubernetes, Arc, Azure, K8s, contenitori
ms.openlocfilehash: d82c93783d80060bc3443131191b7cec32dc4878
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680763"
---
# <a name="deploy-azure-iot-edge-workloads-preview"></a>Distribuire i carichi di lavoro di Azure IoT Edge (Anteprima)

## <a name="overview"></a>Panoramica

Azure Arc e Azure IoT Edge integrano correttamente le reciproche funzionalità. Azure Arc offre meccanismi per gli operatori del cluster per configurare i componenti di base di un cluster e applicare i criteri del cluster. IoT Edge consente agli operatori di applicazioni di distribuire e gestire in modalità remota i carichi di lavoro su larga scala con utili inserimenti nel cloud e primitive di comunicazione bidirezionale. Il diagramma seguente illustra questo concetto:

![Configurazione di IoT Arc](./media/edge-arc.png)

## <a name="pre-requisites"></a>Prerequisiti

* [Registrare un dispositivo IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart-linux#register-an-iot-edge-device) e [distribuire il modulo del sensore di temperatura simulato](https://docs.microsoft.com/azure/iot-edge/quickstart-linux#deploy-a-module). Assicurarsi di prendere nota della stringa di connessione del dispositivo.

* Usare il [supporto di IoT Edge per Kubernetes](https://aka.ms/edgek8sdoc) per distribuirlo tramite l'operatore Flux di Azure Arc.

* Scaricare il file [**values.yaml**](https://github.com/Azure/iotedge/blob/master/kubernetes/charts/edge-kubernetes/values.yaml) per il grafico Helm di IoT Edge e sostituire il segnaposto **deviceConnectionString** alla fine del file con quello indicato nel passaggio 1. È possibile impostare qualsiasi altra opzione di installazione del grafico supportata in base alle necessità. Creare uno spazio dei nomi per il carico di lavoro IoT Edge e aggiungervi un segreto:

    ```
    $ kubectl create ns iotedge

    $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
    ```

    È anche possibile configurarlo in remoto usando l'[esempio di configurazione del cluster](./use-gitops-connected-cluster.md).

## <a name="connect-a-cluster"></a>Connettere un cluster

Usare l'estensione `connectedk8s` dell'interfaccia della riga di comando `az` per connettere un cluster Kubernetes ad Azure Arc:

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Creare una configurazione per IoT Edge

Repository di esempio: https://github.com/veyalla/edgearc

Questo repository punta al grafico Helm di IoT Edge e fa riferimento al segreto creato nella sezione prerequisiti.

1. Usare l'estensione `k8sconfiguration` dell'interfaccia della riga di comando `az` per creare una configurazione per collegare il cluster connesso al repository GIT:

    ```
    az k8sconfiguration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
    ```

    In uno o due minuti dovrebbero essere visualizzati i moduli del carico di lavoro IoT Edge distribuiti nello spazio dei nomi `iotedge` del cluster. È possibile visualizzare i log del pod `SimulatedTemperatureSensor` nello spazio dei nomi per vedere i valori di esempio generati. È anche possibile vedere i messaggi in arrivo all'hub IoT usando l'[estensione Toolkit dell'hub IoT di Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="cleanup"></a>Pulizia

È possibile rimuovere la configurazione usando:

```
az k8sconfiguration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Passaggi successivi

[Usare Criteri di Azure per gestire la configurazione del cluster](./use-azure-policy.md)
