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
ms.openlocfilehash: 7f3627a79cad6833b5fb20f3c829c1e3bcbd9c3e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457337"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Come installare IoT Edge in Kubernetes (anteprima)

IoT Edge può essere integrato con Kubernetes usandolo come livello infrastruttura resiliente e a disponibilità elevata. Viene registrato un IoT Edge *definizione di risorsa personalizzata* (CRD) con il server dell'API Kubernetes. Fornisce inoltre un *operatore* (IOT Edge Agent) che riconcilia lo stato desiderato gestito dal cloud con lo stato del cluster locale. 

La durata del modulo viene gestita dall'utilità di pianificazione di Kubernetes, che mantiene la disponibilità dei moduli e sceglie la selezione host. IoT Edge gestisce la piattaforma applicativa perimetrale in esecuzione in primo piano, riconciliando continuamente lo stato desiderato specificato nell'hub Internet con lo stato nel cluster perimetrale. Il modello di applicazione Edge è ancora il modello familiare basato su moduli e route IoT Edge. L'operatore IoT Edge Agent esegue la conversione *automatica* ai costrutti nativi Kubernetes come Pod, distribuzioni, servizi e così via.

Di seguito è riportato un diagramma dell'architettura di alto livello:

![kubernetes Arch](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Ogni componente della distribuzione perimetrale ha come ambito uno spazio dei nomi Kubernetes specifico per il dispositivo, rendendo possibile la condivisione delle stesse risorse cluster tra più dispositivi perimetrali e le relative distribuzioni.

>[!NOTE]
>IoT Edge in Kubernetes è in [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="install-locally-for-a-quick-test-environment"></a>Installare localmente per un ambiente di testing rapido

### <a name="prerequisites"></a>prerequisiti

* Kubernetes 1,10 o versione successiva. Se non si dispone di una configurazione cluster esistente, è possibile usare [Minikube](https://kubernetes.io/docs/setup/minikube/) per un ambiente cluster locale. 

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide), gestione pacchetti Kubernetes.

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) per la visualizzazione e l'interazione con il cluster.

### <a name="setup-steps"></a>Procedura di configurazione

1. Avviare **Minikube**

    ``` shell
    minikube start
    ```

1. Inizializzare il componente server Helm *(timone*) nel cluster

    ``` shell
    helm init
    ```

1. Aggiungere IoT Edge repository e aggiornare l'installazione di Helm

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Creare un hub](../iot-hub/iot-hub-create-through-portal.md)tutto, [registrare un dispositivo IOT Edge](how-to-register-device.md)e prendere nota della relativa stringa di connessione.

1. Installare iotedged e IoT Edge Agent nel cluster

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

    Negli spazi dei nomi del cluster ne viene visualizzato uno per il dispositivo IoT Edge dopo la convenzione *msiot-\<iothub-name >-\<edgedevice-name >* . L'agente IoT Edge e i pod iotedged devono essere operativi in questo spazio dei nomi.

1. Aggiungere un modulo di sensore di temperatura simulato seguendo i passaggi descritti nella sezione [distribuire un modulo](quickstart-linux.md#deploy-a-module) della Guida introduttiva. IoT Edge gestione dei moduli viene eseguita dal portale dell'hub Internet come qualsiasi altro dispositivo IoT Edge. Non è consigliabile apportare modifiche locali alla configurazione del modulo tramite gli strumenti Kubernetes perché potrebbero essere sovrascritti.

1. In pochi secondi, l'aggiornamento della pagina **Pod** nello spazio dei nomi del dispositivo perimetrale nel dashboard elenca l'hub IOT Edge e i pod dei sensori simulati come in esecuzione con il Pod dell'hub IOT Edge inserimento dei dati nell'hub.

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere tutte le risorse create dalla distribuzione perimetrale, usare il comando seguente con il nome usato nel passaggio 5 della sezione precedente.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>Passaggi successivi

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Distribuire come gateway perimetrale a disponibilità elevata 

Il dispositivo perimetrale in un cluster Kubernetes può essere usato come gateway Internet delle cose per i dispositivi downstream. Può essere configurato in modo da essere resiliente agli errori del nodo, garantendo così una disponibilità elevata per le distribuzioni perimetrali. Vedere questa [procedura](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) dettagliata per usare IOT Edge in questo scenario.