---
title: Comprendere la gestione del carico di lavoro Kubernetes nel dispositivo Azure Stack Edge | Microsoft Docs
description: Descrive il modo in cui i carichi di lavoro di Kubernetes possono essere gestiti sul dispositivo Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 38c46bdcce64f726b3a7ddf74e0cfd10a14ba663
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268032"
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

## <a name="deployment-flow"></a>Flusso di distribuzione

Per distribuire le applicazioni in un dispositivo Azure Stack Edge, attenersi alla procedura seguente: 
 
1. **Configurare l'accesso**: prima di tutto, si userà il spazio di PowerShell per creare un utente, creare uno spazio dei nomi e concedere agli utenti l'accesso a tale spazio dei nomi.
2. **Configurare l'archiviazione**: successivamente, si userà la risorsa Azure stack Edge nel portale di Azure per creare volumi permanenti usando il provisioning statico o dinamico per le applicazioni con stato che verrà distribuito.
3. **Configurare la rete**: Infine, si useranno i servizi per esporre le applicazioni all'esterno e all'interno del cluster Kubernetes.
 
## <a name="deployment-types"></a>Tipi distribuzione

Esistono tre modi principali per distribuire i carichi di lavoro. Ognuna di queste metodologie di distribuzione consente di connettersi a uno spazio dei nomi distinto nel dispositivo e quindi di distribuire applicazioni con o senza stato.

![Distribuzione del carico di lavoro Kubernetes](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Distribuzione locale**: si tratta dello strumento di accesso da riga di comando, ad esempio, `kubectl` che consente di distribuire Kubernetes `yamls` . È possibile connettersi al cluster Kubernetes nel Azure Stack Edge creato usando il `kubeconfig` file. Per altre informazioni, vedere [accedere a un cluster Kubernetes tramite kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- **Distribuzione di IOT Edge**: questa operazione viene eseguita tramite IOT Edge, che si connette all'hub Azure. Ci si connette al cluster Kubernetes nel dispositivo Azure Stack Edge tramite lo `iotedge` spazio dei nomi. Gli agenti IoT Edge distribuiti in questo spazio dei nomi sono responsabili della connettività ad Azure. Si applica la `IoT Edge deployment.json` configurazione usando ci/CD di Azure DevOps. La gestione dello spazio dei nomi e IoT Edge viene eseguita tramite l'operatore cloud.

- **Distribuzione di Azure/Arc**: Azure Arc è uno strumento di gestione ibrido che consente di distribuire applicazioni nei cluster Kubernetes. Connettere il cluster Kubernetes nel dispositivo Azure Stack Edge tramite il `azure-arc namespace` . Gli agenti vengono distribuiti in questo spazio dei nomi responsabile della connettività ad Azure. La configurazione della distribuzione viene applicata tramite la gestione della configurazione basata su GitOps. Azure ARC consente inoltre di usare monitoraggio di Azure per i contenitori per visualizzare e monitorare i cluster. Per altre informazioni, vedere [che cos'è Azure-Arc Enabled Kubernetes?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview).

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
