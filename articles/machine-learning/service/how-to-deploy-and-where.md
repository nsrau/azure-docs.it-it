---
title: Dove distribuire i modelli con il servizio Azure Machine Learning | Microsoft Docs
description: Informazioni sulle diverse modalità con cui è possibile distribuire i modelli nell'ambiente di produzione usando il servizio di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/29/2018
ms.openlocfilehash: 97ac405db3de4fa2c6f1173f813eafd41a5361ad
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209446"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Distribuire modelli con il servizio di Azure Machine Learning

Il servizio di Azure Machine Learning offre diversi modi per distribuire il modello con training. In questo documento viene descritto come distribuire il modello come servizio Web nel cloud di Azure o nei dispositivi periferici IoT.

È possibile distribuire i modelli per le destinazioni di calcolo seguenti:

- [Istanze di contenitore di Azure (ACI)](#aci): distribuzione rapida. Soluzione ideale per lo sviluppo o il test.
- [Servizio Kubernetes di Azure (AKS)](#aks): soluzione ideale per distribuzioni di produzione su vasta scala. Fornisce la scalabilità automatica e tempi di risposta rapidi.
- [Azure IoT Edge](#iotedge): distribuzione di modelli nei dispositivi IoT. Inferenza nel dispositivo.
- [Field Programmable Gate Array (FPGA)](#fpga): latenza estremamente bassa per inferenze in tempo reale.

Nella parte restante del documento verrà illustrata in modo dettagliato ognuna di queste opzioni.

## <a id="aci"></a>Istanze di contenitore di Azure

Usare istanze di contenitore di Azure per distribuire i modelli come un endpoint API REST se una o più delle condizioni seguenti sono vere:
- È necessario distribuire e convalidare rapidamente il modello. La distribuzione di ACI viene completata in meno di 5 minuti.
- Si desidera distribuire il modello in un ambiente di sviluppo o di test. ACI consente di distribuire 20 gruppi di contenitori per ogni sottoscrizione. Per altre informazioni, consultare il documento [Quote e aree disponibili per istanze di contenitore di Azure](https://docs.microsoft.com/azure/container-instances/container-instances-quotas).

Per altre informazioni, consultare il documento [Distribuire un modello a istanze di contenitore di Azure](how-to-deploy-to-aci.md).

## <a id="aks"></a>Servizio Kubernetes di Azure

Per scenari di produzione su vasta scala, usare il servizio Kubernetes di Azure (AKS). È possibile usare un cluster AKS esistente o crearne uno nuovo tramite l'SDK di Azure Machine Learning, l'interfaccia della riga di comando o il portale di Azure.

La creazione di un cluster AKS si esegue una sola volta per l'area di lavoro. È possibile riutilizzare questo cluster per più distribuzioni. Se si elimina il cluster, per eseguire la distribuzione successiva sarà necessario creare un nuovo cluster.

Il servizio Kubernetes di Azure offre le funzionalità seguenti:

* Scalabilità automatica
* Registrazione
* Raccolta di dati del modello
* Tempi di risposta rapidi per i servizi Web

Il processo di creazione di un cluster AKS richiede circa 20 minuti.

Per altre informazioni, consultare il documento [Distribuire un modello nel servizio Kubernetes di Azure](how-to-deploy-to-aks.md).

## <a id="iotedge"></a>Azure IoT Edge

Con i dispositivi IoT, l'assegnazione dei punteggi nel dispositivo è più veloce rispetto all'invio dei dati nel cloud per l'assegnazione dei punteggi. Con Azure IoT Edge, è possibile ospitare il modello in dispositivi periferici. Distribuire il modello in IoT Edge se sono necessarie una o più delle seguenti funzionalità:
- Gestire le attività prioritarie in locale, anche senza una connessione al cloud
- Lavorare con dati generati che sono troppo grandi per eseguire rapidamente il pull dal cloud
- Abilitare l'elaborazione in tempo reale grazie all'intelligenza all'interno o in prossimità di dispositivi locali
- Soddisfare requisiti relativi alla privacy dei dati 

Per altre informazioni, consultare il documento [Distribuire su Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).

Per altre informazioni sul servizio di IoT Edge, consultare la [documentazione di Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).


## <a id="fpga"></a>Field Programmable Gate Array (FPGA)

I Modelli con accelerazione hardware alimentati dal Project Brainwave consentono di ottenere una latenza estremamente bassa per le richieste di inferenza in tempo reale. Project Brainwave accelera reti neurali profonde (DNN) distribuite in field-programmable gate array nel cloud di Azure. I DNN comunemente usati sono disponibili come featurizers per il trasferimento induttivo o sottoposti a training personalizzabile con pesi formati a partire dai propri dati.

Per altre informazioni, consultare il documento [Distribuire su FPGA](how-to-deploy-fpga-web-service.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come distribuire un modello in una destinazione di calcolo specifico, consultare i documenti seguenti:

* [Distribuire un modello a istanze di contenitore di Azure](how-to-deploy-to-aci.md)
* [Distribuire un modello in servizio Kubernetes di Azure](how-to-deploy-to-aks.md)
* [Distribuire un modello in Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)
* [Distribuire un modello in FPGA](how-to-deploy-fpga-web-service.md)
