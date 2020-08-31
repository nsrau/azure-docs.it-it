---
title: Informazioni sulla rete Kubernetes nel dispositivo Azure Stack Edge | Microsoft Docs
description: Descrive il funzionamento della rete Kubernetes in un dispositivo Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: alkohli
ms.openlocfilehash: 8394ddbc4247eb992532fb11d06d8f5432edd1c7
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89085421"
---
# <a name="kubernetes-networking-in-your-azure-stack-edge-device"></a>Rete Kubernetes nel dispositivo Azure Stack Edge

Nel dispositivo Azure Stack Edge viene creato un cluster Kubernetes quando si configura il ruolo di calcolo. Una volta creato il cluster Kubernetes, le applicazioni in contenitori possono essere distribuite nel cluster Kubernetes in pod. Esistono diversi modi per usare la rete per i Pod nel cluster Kubernetes. 

Questo articolo descrive la rete in un cluster Kubernetes in generale e in particolare nel contesto del dispositivo Azure Stack Edge. 

## <a name="networking-requirements"></a>Requisiti di rete

Di seguito è riportato un esempio di una tipica app a 2 livelli distribuita nel cluster Kubernetes.

- L'app include un front-end del server Web e un'applicazione di database nel back-end. 
- A ogni pod viene assegnato un indirizzo IP, ma questi indirizzi IP possono cambiare al riavvio e al failover del Pod. 
- Ogni app è costituita da più POD ed è necessario bilanciare il carico del traffico tra tutte le repliche pod. 

![Requisiti di rete Kubernetes](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1.png)

Lo scenario precedente comporta i requisiti di rete seguenti:

 - È necessario che l'applicazione esterna riconnessa sia accessibile da un utente dell'applicazione all'esterno del cluster Kubernetes tramite un nome o un indirizzo IP. 
 - Le applicazioni all'interno del cluster Kubernetes, ad esempio front-end e i pod back-end, devono essere in grado di comunicare tra loro.

Per risolvere le esigenze precedenti, viene introdotto un servizio Kubernetes. 


## <a name="networking-services"></a>Servizi di rete

Esistono due tipi di servizi Kubernetes: 

- **Servizio IP del cluster** : considerare come fornire un endpoint costante per i pod dell'applicazione. Non è possibile accedere a qualsiasi Pod associato a questi servizi dall'esterno del cluster Kubernetes. L'indirizzo IP usato con questi servizi deriva dallo spazio degli indirizzi nella rete privata. 
- **Bilanciamento del carico IP** , ad esempio il servizio IP del cluster, ma l'IP associato deriva dalla rete esterna ed è possibile accedervi dall'esterno del cluster Kubernetes.


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->


## <a name="kubernetes-networking-on-azure-stack-edge"></a>Rete Kubernetes su Azure Stack Edge

Calice, Metallb e DNS core sono tutti componenti installati per la rete in Azure Stack Edge. 

- **Calice** assegna un indirizzo IP da un intervallo IP privato a ogni pod e configura la rete per questi Pod, in modo che il pod in un nodo possa comunicare con il pod in un altro nodo. 
- **Metallb** viene eseguito in un pod nel cluster e assegna un indirizzo IP ai servizi di tipo Load Balancer. Gli indirizzi IP del servizio di bilanciamento del carico vengono scelti dall'intervallo di indirizzi IP del servizio fornito tramite l'interfaccia utente locale. 
- **DNS di base** : questo componente aggiuntivo configura i record DNS per il mapping del nome del servizio all'indirizzo IP del cluster.

Gli indirizzi IP usati per i nodi Kubernetes e i servizi esterni vengono forniti tramite la pagina **rete di calcolo** nell'interfaccia utente locale del dispositivo.

![Assegnazione IP Kubernetes nell'interfaccia utente locale](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

L'assegnazione IP è per:

- IP del **nodo Kubernetes**: questo intervallo IP viene usato per i nodi master e di lavoro Kubernetes. Questi indirizzi IP vengono usati quando i nodi Kubernetes comunicano tra loro.

- **Indirizzi IP del servizio esterno Kubernetes**: questo intervallo IP viene usato per i servizi esterni (noti anche come servizi Load Balancer) esposti all'esterno del cluster Kubernetes.  


## <a name="next-steps"></a>Passaggi successivi

Per configurare la rete Kubernetes in Azure Stack Edge, vedere:

- [Esporre un'applicazione senza stato esternamente all'Azure stack Edge tramite IOT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).

- [Esporre un'applicazione senza stato esternamente all'Azure stack Edge tramite kuebctl](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).
