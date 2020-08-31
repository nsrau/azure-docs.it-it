---
title: Panoramica del cluster Kubernetes nel dispositivo Microsoft Azure Stack Edge | Microsoft Docs
description: Descrive il modo in cui Kubernetes viene implementato nel dispositivo Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: b85586a431a20102035e253537fc45c8a8a54796
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89085400"
---
# <a name="kubernetes-on-your-azure-stack-edge-device"></a>Kubernetes sul dispositivo Azure Stack Edge

Kubernetes è una piattaforma open source diffusa per orchestrare le applicazioni in contenitori. Questo articolo fornisce una panoramica di Kubernetes e descrive il funzionamento di Kubernetes sul dispositivo Azure Stack Edge. 

## <a name="about-kubernetes"></a>Informazioni su Kubernetes 

Kubernetes fornisce una piattaforma semplice e affidabile per gestire le applicazioni basate su contenitori e i componenti di rete e di archiviazione associati. Puoi creare, distribuire e ridimensionare rapidamente app in contenitori con Kubernetes.

Come piattaforma aperta, è possibile usare Kubernetes per creare applicazioni con il linguaggio di programmazione preferito, le librerie del sistema operativo o il bus di messaggistica. Per pianificare e distribuire le versioni, Kubernetes è in grado di integrarsi con gli strumenti di integrazione continua e recapito continuo esistenti.

Per ulteriori informazioni, vedere [funzionamento di Kubernetes](https://www.youtube.com/watch?v=q1PcAawa4Bg&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=2&t=0s).

## <a name="kubernetes-on-azure-stack-edge"></a>Kubernetes su Azure Stack Edge

Sul dispositivo Azure Stack Edge è possibile creare un cluster Kubernetes configurando il calcolo. Quando il ruolo di calcolo è configurato, il cluster Kubernetes, inclusi i nodi master e worker, viene distribuito e configurato per l'utente. Questo cluster viene quindi usato per la distribuzione del carico di lavoro tramite `kubectl` , IOT Edge o Azure Arc.

Il dispositivo Azure Stack Edge è disponibile come configurazione a 1 nodo che costituisce il cluster di infrastruttura. Il cluster Kubernetes è separato dal cluster di infrastruttura e viene distribuito nel cluster di infrastruttura. Il cluster di infrastruttura fornisce l'archiviazione permanente per il dispositivo Azure Stack Edge mentre il cluster Kubernetes è responsabile esclusivamente dell'orchestrazione dell'applicazione. 

Il cluster Kubernetes in questo caso ha un nodo master e un nodo di lavoro. I nodi Kubernetes in un cluster sono macchine virtuali che eseguono le applicazioni e i flussi di lavoro cloud. 

Il nodo master Kubernetes è responsabile della gestione dello stato desiderato per il cluster. Il nodo master controlla anche il nodo del ruolo di lavoro che a sua volta esegue le applicazioni incluse in contenitori. 

Il diagramma seguente illustra l'implementazione di Kubernetes in un dispositivo Azure Stack Edge a 1 nodo. Il dispositivo a 1 nodo non è a disponibilità elevata e se il singolo nodo ha esito negativo, il dispositivo diventa inattivo. Anche il cluster Kubernetes diventa inattivo.

![Architettura Kubernetes per un dispositivo Azure Stack Edge a 1 nodo](media/azure-stack-edge-gpu-kubernetes-overview/kubernetes-architecture-1-node.png)

Per altre informazioni sull'architettura del cluster Kubernetes, vedere [concetti di base di Kubernetes](https://kubernetes.io/docs/concepts/architecture/).


<!--The Kubernetes cluster control plane components make global decisions about the cluster. The control plane has:

- *kubeapiserver* that is the front end of the Kubernetes API and exposes the API.
- *etcd* that is a highly available key value store that backs up all the Kubernetes cluster data.
- *kube-scheduler* that makes scheduling decisions.
- *kube-controller-manager* that runs controller processes such as those for node controllers, replications controllers, endpoint controllers, and service account and token controllers. -->

## <a name="storage-volume-provisioning"></a>Provisioning del volume di archiviazione

Per supportare i carichi di lavoro dell'applicazione, è possibile montare i volumi di archiviazione per i dati persistenti nelle condivisioni del dispositivo Azure Stack Edge. È possibile usare volumi sia statici che dinamici. 

Per altre informazioni, vedere Opzioni di provisioning dell'archiviazione per le applicazioni in [archiviazione Kubernetes per il dispositivo Azure stack Edge](azure-stack-edge-gpu-kubernetes-storage.md).

## <a name="networking"></a>Rete

La rete Kubernetes consente di configurare la comunicazione all'interno della rete Kubernetes, incluse le funzionalità di rete da contenitore a contenitore, rete da Pod a Pod, rete da Pod a servizio e rete da Internet a servizio. Per altre informazioni, vedere il modello di rete in [rete Kubernetes per il dispositivo Azure stack Edge](azure-stack-edge-gpu-kubernetes-networking.md).

## <a name="updates"></a>Aggiornamenti

Man mano che le nuove versioni di Kubernetes diventano disponibili, il cluster può essere aggiornato usando gli aggiornamenti standard disponibili per il dispositivo Azure Stack Edge. Per istruzioni su come eseguire l'aggiornamento, vedere [applicare gli aggiornamenti per il Azure stack Edge](azure-stack-edge-gpu-install-update.md).

## <a name="access-monitoring"></a>Accesso, monitoraggio

Il cluster Kubernetes nel dispositivo Azure Stack Edge consente il controllo degli accessi in base al ruolo (RBAC). Per altre informazioni, vedere [controllo degli accessi in base al ruolo per il cluster Kubernetes nel dispositivo Azure stack Edge](azure-stack-edge-gpu-kubernetes-rbac.md).

È anche possibile monitorare l'integrità del cluster e delle risorse tramite il dashboard di Kubernetes. Sono disponibili anche i log del contenitore. Per altre informazioni, vedere [usare il dashboard di Kubernetes per monitorare l'integrità del cluster Kubernetes sul dispositivo Azure stack Edge](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).

Monitoraggio di Azure è disponibile anche come componente aggiuntivo per raccogliere i dati di integrità da contenitori, nodi e controller. Per altre informazioni, vedere [Panoramica di monitoraggio di Azure](../azure-monitor/overview.md)

<!--## Private container registry

Kubernetes on Azure Stack Edge device allows for the private storage of your images by providing a local container registry.-->

## <a name="application-management"></a>Gestione delle applicazioni

Dopo la creazione di un cluster Kubernetes nel dispositivo Azure Stack Edge, è possibile gestire le applicazioni distribuite in questo cluster tramite uno dei metodi seguenti:

- Accesso nativo tramite `kubectl`
- IoT Edge 
- Azure Arc

Questi metodi sono descritti nelle sezioni seguenti.


### <a name="kubernetes-and-kubectl"></a>Kubernetes e kubectl

Una volta distribuito il cluster Kubernetes, è possibile gestire le applicazioni distribuite nel cluster localmente da un computer client. Usare uno strumento nativo, ad esempio *kubectl* , tramite la riga di comando per interagire con le applicazioni. 

Per altre informazioni sulla distribuzione di un cluster Kubernetes, vedere [distribuire un cluster Kubernetes nel dispositivo Azure stack Edge](azure-stack-edge-gpu-create-kubernetes-cluster.md). Per informazioni sulla gestione, vedere [usare kubectl per gestire il cluster Kubernetes nel dispositivo Azure stack Edge](azure-stack-edge-gpu-create-kubernetes-cluster.md).


### <a name="kubernetes-and-iot-edge"></a>Kubernetes e IoT Edge

Kubernetes può anche essere integrato con carichi di lavoro IoT Edge sul dispositivo Azure Stack Edge, in cui Kubernetes fornisce la scalabilità e l'ecosistema e gli ecosistemi forniscono l'ecosistema incentrato sugli elementi. Il livello Kubernetes viene usato come livello di infrastruttura per distribuire carichi di lavoro Azure IoT Edge. La durata del modulo e il bilanciamento del carico di rete vengono gestiti da Kubernetes, mentre la piattaforma applicativa perimetrale viene gestita da IoT Edge.

Per altre informazioni sulla distribuzione di applicazioni nel cluster Kubernetes tramite IoT Edge, vedere: 

- [Esporre le applicazioni senza stato nel dispositivo Azure stack Edge tramite IOT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).


### <a name="kubernetes-and-azure-arc"></a>Kubernetes e Azure Arc

Azure Arc è uno strumento di gestione ibrido che consente di distribuire applicazioni nei cluster Kubernetes. Azure ARC consente anche di usare monitoraggio di Azure per i contenitori per visualizzare e monitorare i cluster. Per altre informazioni, vedere [che cos'è Azure-Arc Enabled Kubernetes?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview). Per informazioni sui prezzi di Azure Arc, vedere [prezzi di Azure Arc](https://azure.microsoft.com/services/azure-arc/#pricing).


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull'archiviazione Kubernetes nel [dispositivo Azure stack Edge](azure-stack-edge-gpu-kubernetes-storage.md).
- Informazioni sul modello di rete Kubernetes sul [dispositivo Azure stack Edge](azure-stack-edge-gpu-kubernetes-networking.md).
- Distribuire [Azure stack Edge](azure-stack-edge-gpu-deploy-prep.md) nel portale di Azure.
