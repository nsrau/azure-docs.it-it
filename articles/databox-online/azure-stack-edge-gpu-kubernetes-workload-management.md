---
title: Informazioni sulla gestione dei carichi di lavoro Kubernetes nel dispositivo Azure Stack Edge Pro | Microsoft Docs
description: Descrive il modo in cui i carichi di lavoro di Kubernetes possono essere gestiti sul dispositivo Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: d9e0da9e24a0bd32047d029879c4f0e110dc0c16
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320796"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-pro-device"></a>Gestione del carico di lavoro Kubernetes sul dispositivo Azure Stack Edge Pro

Nel dispositivo Azure Stack Edge Pro viene creato un cluster Kubernetes quando si configura il ruolo di calcolo. Una volta creato il cluster Kubernetes, le applicazioni in contenitori possono essere distribuite nel cluster Kubernetes in pod. Esistono diversi modi per distribuire i carichi di lavoro nel cluster Kubernetes. 

Questo articolo descrive i vari metodi che possono essere usati per distribuire i carichi di lavoro nel dispositivo Azure Stack Edge Pro.

## <a name="workload-types"></a>Tipi di carico di lavoro

I due tipi comuni di carichi di lavoro che è possibile distribuire nel dispositivo Azure Stack Edge Pro sono applicazioni senza stato o applicazioni con stato.

- **Le applicazioni** senza stato non conservano il proprio stato e non salvano i dati nell'archivio permanente. Tutti i dati utente e sessione rimangono con il client. Alcuni esempi di applicazioni senza stato includono front-end Web come nginx e altre applicazioni Web.

    È possibile creare una distribuzione di Kubernetes per distribuire un'applicazione senza stato nel cluster. 

- **Le applicazioni con stato** richiedono il salvataggio dello stato. Le applicazioni con stato usano un archivio permanente, ad esempio volumi persistenti, per salvare i dati per l'uso da parte del server o da altri utenti. Esempi di applicazioni con stato includono database come [Azure SQL Edge](../azure-sql-edge/overview.md) e MongoDB.

    È possibile creare una distribuzione di Kubernetes per distribuire un'applicazione con stato. 

## <a name="deployment-flow"></a>Flusso di distribuzione

Per distribuire le applicazioni in un dispositivo Azure Stack Edge Pro, attenersi alla procedura seguente: 
 
1. **Configurare l'accesso**: prima di tutto, si userà il spazio di PowerShell per creare un utente, creare uno spazio dei nomi e concedere agli utenti l'accesso a tale spazio dei nomi.
2. **Configurare l'archiviazione**: successivamente, si userà la risorsa Azure stack Edge nel portale di Azure per creare volumi permanenti usando il provisioning statico o dinamico per le applicazioni con stato che verrà distribuito.
3. **Configurare la rete**: Infine, si useranno i servizi per esporre le applicazioni all'esterno e all'interno del cluster Kubernetes.
 
## <a name="deployment-types"></a>Tipi distribuzione

Esistono tre modi principali per distribuire i carichi di lavoro. Ognuna di queste metodologie di distribuzione consente di connettersi a uno spazio dei nomi distinto nel dispositivo e quindi di distribuire applicazioni con o senza stato.

![Distribuzione del carico di lavoro Kubernetes](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Distribuzione locale**: questa distribuzione è tramite lo strumento di accesso da riga di comando, ad esempio, `kubectl` che consente di distribuire Kubernetes `yamls` . È possibile accedere al cluster Kubernetes in Azure Stack Edge Pro tramite un `kubeconfig` file. Per altre informazioni, vedere [accedere a un cluster Kubernetes tramite kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- **Distribuzione di IOT Edge**: questa operazione viene eseguita tramite IOT Edge, che si connette all'hub Azure. Ci si connette al cluster Kubernetes nel dispositivo Azure Stack Edge Pro tramite lo `iotedge` spazio dei nomi. Gli agenti IoT Edge distribuiti in questo spazio dei nomi sono responsabili della connettività ad Azure. Si applica la `IoT Edge deployment.json` configurazione usando ci/CD di Azure DevOps. La gestione dello spazio dei nomi e IoT Edge viene eseguita tramite l'operatore cloud.

- **Distribuzione Kubernetes abilitata per Azure Arc**: Azure Arc Enabled Kubernetes è uno strumento di gestione ibrido che consente di distribuire applicazioni nei cluster Kubernetes. Connettersi al cluster Kubernetes nel dispositivo Azure Stack Edge Pro tramite il `azure-arc namespace` . Gli agenti distribuiti in questo spazio dei nomi sono responsabili della connettività ad Azure. La configurazione della distribuzione viene applicata tramite la gestione della configurazione basata su GitOps. 
    
    Azure Arc Enabled Kubernetes consente anche di usare monitoraggio di Azure per i contenitori per visualizzare e monitorare il cluster. Per altre informazioni, vedere [che cos'è Azure Arc Enabled Kubernetes?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview).

## <a name="choose-the-deployment-type"></a>Scegliere il tipo di distribuzione

Durante la distribuzione delle applicazioni, tenere presenti le seguenti informazioni:

- **Tipi singoli o multipli**: è possibile scegliere una singola opzione di distribuzione o una combinazione di opzioni di distribuzione diverse.
- **Cloud rispetto alla lingua locale**: a seconda delle applicazioni, è possibile scegliere la distribuzione locale tramite kubectl o la distribuzione cloud tramite IOT Edge e Azure Arc. 
    - Quando si sceglie una distribuzione locale, si è limitati alla rete in cui viene distribuito il dispositivo Azure Stack Edge Pro.
    - Se si dispone di un agente cloud che è possibile distribuire, è necessario distribuire l'operatore cloud e usare la gestione cloud.
- Internet delle cose e **Azure Arc**: la scelta della distribuzione dipende anche dallo scopo dello scenario del prodotto. Se si distribuiscono applicazioni o contenitori con una maggiore integrazione con l'ecosistema Internet delle cose o degli altri, selezionare IoT Edge per distribuire le applicazioni. Se si dispone di distribuzioni Kubernetes esistenti, Azure Arc è la scelta migliore.


## <a name="next-steps"></a>Passaggi successivi

Per distribuire localmente un'app tramite kubectl, vedere:

- [Distribuire un'applicazione senza stato nel Azure stack Edge Pro tramite kubectl](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).

Per distribuire un'app tramite IoT Edge, vedere:

- [Distribuire un modulo di esempio in Azure stack Edge Pro tramite IOT Edge](azure-stack-edge-gpu-deploy-sample-module.md).

Per distribuire un'app tramite Azure Arc, vedere:

- [Distribuire un'applicazione con Azure Arc](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).
