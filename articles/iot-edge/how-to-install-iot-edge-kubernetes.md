---
title: Come installare IoT Edge in Kubernetes | Microsoft Docs
description: Informazioni su come installare IoT Edge in Kubernetes usando un ambiente cluster di sviluppo locale
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 66aca7be9a2df93d846d7e78bc64c93279afc2d1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160696"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Come installare IoT Edge in Kubernetes (anteprima)

IoT Edge può integrarsi con Kubernetes per utilizzarlo come un livello di infrastruttura a disponibilità elevata resilienti. Registra un IoT Edge *definizione di risorsa personalizzata* (CRD) con il Server API Kubernetes. Fornisce anche un *operatore* (agente di IoT Edge) che risolve le differenze tra cloud gestiti dello stato desiderato con lo stato del cluster locale. 

Durata di modulo viene gestita dall'utilità di pianificazione di Kubernetes, che gestisce la disponibilità di modulo e sceglie la loro posizione. IoT Edge gestisce la piattaforma applicativa di edge in esecuzione in primo piano, riconciliazione continuamente lo stato desiderato specificato nell'IoT Hub con lo stato nel cluster di edge. Il modello di applicazione edge è ancora il noto modello basato su route e i moduli di IoT Edge. L'operatore agente IoT Edge esegue *automatica* costruisce la traduzione in nativi di Kubernetes, ad esempio i POD, distribuzioni, servizi e così via.

Ecco un diagramma di architettura di alto livello:

![architettura di kubernetes](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Ogni componente della distribuzione edge ha come ambito uno spazio dei nomi Kubernetes specifico del dispositivo, rendendo possibile condividere le stesse risorse del cluster tra più dispositivi edge e le distribuzioni.

>[!NOTE]
>IoT Edge su Kubernetes è nel [versione di anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="install-locally-for-a-quick-test-environment"></a>Installare in locale per un ambiente di test rapida

### <a name="prerequisites"></a>Prerequisiti

* Kubernetes 1.10 o versione successiva. Se non si dispone di una configurazione cluster esistente, è possibile usare [Minikube](https://kubernetes.io/docs/setup/minikube/) per un ambiente cluster locale. 

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide), la gestione di pacchetti di Kubernetes.

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) per visualizzare e interagire con il cluster.

### <a name="setup-steps"></a>Procedura di configurazione

1. Start **Minikube**

    ``` shell
    minikube start
    ```

1. Inizializzare il **Helm** componente server (*tiller*) nel cluster

    ``` shell
    helm init
    ```

1. Aggiungere il repository di IoT Edge e aggiornare l'installazione di helm

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Creare un IoT Hub](../iot-hub/iot-hub-create-through-portal.md), [registrare un dispositivo IoT Edge](how-to-register-device-portal.md)e annotare la stringa di connessione.

1. Installare iotedged e l'agente di IoT Edge nel cluster

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```
1. Aprire il dashboard di Kubernetes nel browser

    ```shell
    minikube dashboard
    ```

    Negli spazi dei nomi del cluster, verrà visualizzato uno per il dispositivo IoT Edge che seguono la convenzione *msiot -\<iothub-name >-\<edgedevice-name >*. I POD iotedged e dell'agente di IoT Edge devono essere in esecuzione in questo spazio dei nomi.

1. Aggiungere un modulo di sensore di temperatura simulati usando la procedura nel [distribuire un modulo](quickstart-linux.md#deploy-a-module) sezione della Guida rapida. Gestione dei moduli IoT Edge viene eseguita dal portale dell'IoT Hub come qualsiasi altro dispositivo di IoT Edge. Apportare le modifiche locali alla configurazione del modulo tramite strumenti di Kubernetes non è consigliata perché essi avrebbero potuto venire sovrascritti.

1. In pochi secondi, l'aggiornamento di **POD** pagina nello spazio dei nomi dispositivo edge nel dashboard elencherà hub di IoT Edge e i POD di sensore simulato come in esecuzione con l'hub di IoT Edge pod inserimento di dati nell'IoT Hub.

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere tutte le risorse create dalla distribuzione edge, usare il comando seguente con il nome usato nel passaggio 5 della sezione precedente.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>Passaggi successivi

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Distribuire un gateway edge a disponibilità elevata 

Il dispositivo edge in un cluster Kubernetes è utilizzabile come gateway IoT per i dispositivi downstream. Può essere configurato per essere resilienti agli errori di nodo in modo da fornire un'elevata disponibilità per le distribuzioni edge. Vedere questo [dettagliata](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) usare IoT Edge in questo scenario.