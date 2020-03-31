---
title: Come installare IoT Edge su Kubernetes Documenti Microsoft
description: Informazioni su come installare IoT Edge su Kubernetes utilizzando un ambiente cluster di sviluppo locale
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b2068c3944f9e7616b0666c7bafcafc68ee0cd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471286"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Come installare IoT Edge su Kubernetes (anteprima)

IoT Edge può integrarsi con Kubernetes utilizzandolo come un livello di infrastruttura resiliente e altamente disponibile. Ecco dove questo supporto si inserisce in una soluzione IoT Edge di alto livello:

![k8s introduzione](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Un buon modello mentale per questa integrazione è quello di pensare a Kubernetes come un altro ambiente operativo Applicazioni IoT Edge possono essere eseguiti in aggiunta a Linux e Windows.

## <a name="architecture"></a>Architecture 
In Kubernetes, IoT Edge fornisce *la definizione* delle risorse personalizzate (CRD) per le distribuzioni dei carichi di lavoro perimetrali. L'agente Edge IoT assume il ruolo di *controller CRD* che riconcilia lo stato desiderato gestito dal cloud con lo stato del cluster locale.

La durata del modulo è gestita dall'utilità di pianificazione Kubernetes, che mantiene la disponibilità dei moduli e sceglie il loro posizionamento. IoT Edge gestisce la piattaforma dell'applicazione perimetrale in esecuzione in alto, riconciliando continuamente lo stato desiderato specificato nell'hub IoT con lo stato nel cluster perimetrale. Il modello di applicazione è ancora il modello familiare basato su moduli e percorsi IoT Edge. Il controller dell'agente perimetrale IoT esegue la conversione *automatica* del modello di applicazione IoT Edge nei costrutti nativi di Kubernetes come pod, distribuzioni, servizi e così via.

Di seguito è riportato un diagramma dell'architettura di alto livello:Here is a high-level architecture diagram:

![kubernetes arco](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Ogni componente della distribuzione perimetrale ha come ambito uno spazio dei nomi Kubernetes specifico per il dispositivo, che consente di condividere le stesse risorse del cluster tra più dispositivi perimetrali e le relative distribuzioni.

>[!NOTE]
>IoT Edge su Kubernetes è in [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="tutorials-and-references"></a>Esercitazioni e riferimenti 

Per ulteriori informazioni, consulta il mini-sito dei documenti di [anteprima di IoT su Kubernetes](https://aka.ms/edgek8sdoc) per ulteriori informazioni, tra cui esercitazioni approfondite e riferimenti.
