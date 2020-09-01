---
title: Comprendere la gestione del carico di lavoro Kubernetes nel dispositivo Azure Stack Edge | Microsoft Docs
description: Descrive il modo in cui i carichi di lavoro di Kubernetes possono essere gestiti sul dispositivo Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: alkohli
ms.openlocfilehash: 21845b51fdd108221d5e1bce50e953b79084d17d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89085361"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-device"></a>Gestione del carico di lavoro Kubernetes sul dispositivo Azure Stack Edge

Nel dispositivo Azure Stack Edge viene creato un cluster Kubernetes quando si configura il ruolo di calcolo. Una volta creato il cluster Kubernetes, le applicazioni in contenitori possono essere distribuite nel cluster Kubernetes in pod. Esistono diversi modi per distribuire i carichi di lavoro nel cluster Kubernetes. 

Questo articolo descrive i vari metodi che possono essere usati per distribuire i carichi di lavoro nel dispositivo Azure Stack Edge.

## <a name="workload-types"></a>Tipi di carico di lavoro

I due tipi comuni di carichi di lavoro che è possibile distribuire nel dispositivo Azure Stack Edge sono applicazioni senza stato o applicazioni con stato.

- **Le applicazioni** senza stato non conservano il proprio stato e non salvano i dati nell'archivio permanente. Tutti i dati utente e sessione rimangono con il client. Alcuni esempi di applicazioni senza stato includono front-end Web come nginx e altre applicazioni Web.

    È possibile creare una distribuzione di Kubernetes per distribuire un'applicazione senza stato nel cluster. 

- **Le applicazioni con stato** richiedono il salvataggio dello stato. Le applicazioni con stato usano un archivio permanente, ad esempio volumi persistenti, per salvare i dati per l'uso da parte del server o da altri utenti. Esempi di applicazioni con stato includono database come MongoDB.

    È possibile creare una distribuzione di Kubernetes per distribuire un'applicazione con stato. 

## <a name="namespaces-types"></a>Tipi di spazi dei nomi

Le risorse Kubernetes, ad esempio pod e distribuzioni, sono raggruppate logicamente in uno spazio dei nomi. Questi raggruppamenti consentono di dividere logicamente un cluster Kubernetes e di limitare l'accesso per creare, visualizzare o gestire le risorse. Gli utenti possono interagire solo con le risorse all'interno degli spazi dei nomi assegnati.

Gli spazi dei nomi sono destinati all'uso in ambienti con molti utenti distribuiti in più team o progetti. Per i cluster con pochi o dieci utenti, non è necessario creare o considerare gli spazi dei nomi. Iniziare a usare gli spazi dei nomi quando sono necessarie le funzionalità che forniscono.

Per altre informazioni, vedere [Spazi dei nomi di Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).


Il dispositivo Azure Stack Edge presenta gli spazi dei nomi seguenti:

- **Spazio dei nomi System** : questo spazio dei nomi è il punto in cui si trovano le risorse principali, ad esempio le funzionalità di rete come DNS e proxy o il dashboard Kubernetes. In genere non si distribuiscono le proprie applicazioni in questo spazio dei nomi. Usare questo spazio dei nomi per eseguire il debug di eventuali problemi del cluster Kubernetes. 

    Nel dispositivo sono presenti più spazi dei nomi di sistema e i nomi corrispondenti a questi spazi dei nomi di sistema sono riservati. Di seguito è riportato un elenco degli spazi dei nomi di sistema riservati: 
    - Kube-sistema
    - metallb-sistema
    - DBE-spazio dei nomi
    - default
    - kubernetes-dashboard
    - default
    - Kube-node-lease
    - Kube-pubblico
    - iotedge
    - Azure-Arc

    Assicurarsi di non usare nomi riservati per gli spazi dei nomi utente creati. 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **Spazio dei nomi utente** : questi spazi dei nomi che è possibile creare tramite **kubectl** per distribuire localmente le applicazioni.
 
- **IOT Edge spazio dei nomi** : è possibile connettersi a questo `iotedge` spazio dei nomi per distribuire le applicazioni tramite IOT Edge.

- **Spazio dei nomi di Azure Arc** : è possibile connettersi a questo `azure-arc` spazio dei nomi per distribuire le applicazioni tramite Azure Arc.

 
## <a name="deployment-types"></a>Tipi distribuzione

Esistono tre modi principali per distribuire i carichi di lavoro. Ognuna di queste metodologie di distribuzione consente di connettersi a uno spazio dei nomi distinto nel dispositivo e quindi di distribuire applicazioni con o senza stato.

![Distribuzione del carico di lavoro Kubernetes](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Distribuzione locale**: si tratta dello strumento di accesso da riga di comando, ad esempio, `kubectl` che consente di distribuire K8 `yamls` . Ci si connette al cluster K8 sul Azure Stack Edge creato usando il `kubeconfig` file. Per altre informazioni, vedere [accedere a un cluster Kubernetes tramite kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- **Distribuzione di IOT Edge**: questa operazione viene eseguita tramite IOT Edge, che si connette all'hub Azure. Ci si connette al cluster K8 sul dispositivo Azure Stack Edge tramite lo `iotedge` spazio dei nomi. Gli agenti IoT Edge distribuiti in questo spazio dei nomi sono responsabili della connettività ad Azure. Si applica la `IoT Edge deployment.json` configurazione usando ci/CD di Azure DevOps. La gestione dello spazio dei nomi e IoT Edge viene eseguita tramite l'operatore cloud.

- **Distribuzione di Azure/Arc**: Azure Arc è uno strumento di gestione ibrido che consente di distribuire applicazioni nei cluster K8. Connettere il cluster K8 sul dispositivo Azure Stack Edge tramite il `azure-arc namespace` .  Gli agenti vengono distribuiti in questo spazio dei nomi responsabile della connettività ad Azure. La configurazione della distribuzione viene applicata tramite la gestione della configurazione basata su GitOps. Azure ARC consente inoltre di usare monitoraggio di Azure per i contenitori per visualizzare e monitorare i cluster. Per altre informazioni, vedere [che cos'è Azure-Arc Enabled Kubernetes?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview).

## <a name="choose-the-deployment-type"></a>Scegliere il tipo di distribuzione

Durante la distribuzione delle applicazioni, tenere presenti le seguenti informazioni:

- **Tipi singoli o multipli**: è possibile scegliere una singola opzione di distribuzione o una combinazione di opzioni di distribuzione diverse.
- **Cloud rispetto alla lingua locale**: a seconda delle applicazioni, è possibile scegliere la distribuzione locale tramite kubectl o la distribuzione cloud tramite IOT Edge e Azure Arc. 
    - La distribuzione locale è più adatta per gli scenari di sviluppo. Quando si sceglie una distribuzione locale, si è limitati alla rete in cui viene distribuito il dispositivo Azure Stack Edge.
    - Se si dispone di un agente cloud che è possibile distribuire, è necessario distribuire l'operatore cloud e usare la gestione cloud.
- Internet delle cose e **Azure Arc**: la scelta della distribuzione dipende anche dallo scopo dello scenario del prodotto. Se si distribuiscono applicazioni o contenitori con una maggiore integrazione con l'ecosistema Internet delle cose o degli altri, è consigliabile scegliere il IoT Edge modo per distribuire le applicazioni. Se si dispone di distribuzioni Kubernetes esistenti, Azure Arc è la scelta migliore.


## <a name="next-steps"></a>Passaggi successivi

Per distribuire localmente un'app tramite kubectl, vedere:

- [Distribuire un'applicazione senza stato nel Azure stack Edge tramite kubectl](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).

Per distribuire un'app tramite IoT Edge, vedere:

- [Distribuire un modulo di esempio in Azure stack Edge tramite IOT Edge](azure-stack-edge-gpu-deploy-sample-module.md).

Per distribuire un'app tramite Azure Arc, vedere:

- [Distribuire un'applicazione con Azure Arc](azure-stack-edge-gpu-deploy-sample-module.md).
