---
title: Nodi di confidential computing nel servizio Azure Kubernetes - Anteprima pubblica
description: Nodi di confidential computing nel servizio Azure Kubernetes
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: ae3090689f9999c9ea6aa65447dadbdd7b0b2026
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90998465"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service-public-preview"></a>Nodi di confidential computing nel servizio Azure Kubernetes (anteprima pubblica)

Il [confidential computing di Azure](overview.md) consente di proteggere i dati sensibili durante l'uso. Le infrastrutture sottostanti proteggono questi dati da altre applicazioni, amministratori e provider di servizi cloud. 

## <a name="overview"></a>Panoramica

Il servizio Azure Kubernetes supporta l'aggiunta di [nodi di confidential computing DCsv2](confidential-computing-enclaves.md) in Intel SGX. Questi nodi eseguono carichi di lavoro sensibili in un ambiente TEE (Trusted Execution Environment) consentendo al codice a livello di utente di allocare aree di memoria private. Queste aree di memoria private sono dette enclave. Le enclave sono progettate per proteggere il codice e i dati dai processi in esecuzione con privilegi più elevati. Il modello di esecuzione SGX rimuove i livelli intermedi del sistema operativo guest e dell'hypervisor. Questo consente di eseguire applicazioni contenitore direttamente sulla CPU, mantenendo al tempo stesso il blocco speciale di memoria crittografato. 


![Panoramica dei nodi SGX](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>Funzionalità dei nodi di confidential computing del servizio Azure Kubernetes

- Isolamento dei contenitori basato su hardware e a livello di processo tramite l'ambiente TEE (Trusted Execution Environment) SGX 
- Cluster di pool di nodi eterogenei (una combinazione di pool di nodi di confidential computing e non)
- Pianificazione dei pod basata sulla memoria EPC (Encrypted Page Cache)
- Driver DCAP SGX preinstallato
- Patch Intel FSGS preinstallata
- Supporto della scalabilità automatica orizzontale dei pod e della scalabilità automatica dei cluster in base all'utilizzo della CPU
- Supporto dell'attestazione out-of-process tramite il DaemonSet del servizio Azure Kubernetes
- Supporto dei contenitori Linux tramite i nodi di lavoro di VM Ubuntu 18.04 Gen 2

## <a name="aks-provided-daemon-sets"></a>DaemonSet forniti dal servizio Azure Kubernetes

#### <a name="sgx-device-plugin"></a>Plug-in del dispositivo SGX <a id="sgx-plugin"></a>

Il plug-in del dispositivo SGX implementa l'interfaccia del plug-in del dispositivo Kubernetes per la memoria EPC. In realtà, con questo plug-in la memoria EPC diventa un tipo di risorsa aggiuntivo in Kubernetes. Gli utenti possono specificare dei limiti per questa risorsa esattamente come per altre risorse. Oltre alla funzione di pianificazione, il plug-in del dispositivo consente di assegnare le autorizzazioni di driver di dispositivo SGX ai contenitori di carichi di lavoro riservati. Un'implementazione di esempio della distribuzione basata sulla memoria EPC (`kubernetes.azure.com/sgx_epc_mem_in_MiB`) è disponibile [qui](https://github.com/azure-samples/confidential-computing/blob/main/containersamples/helloworld/helloworld.yaml)

#### <a name="sgx-quote-helper-service"></a>Servizio helper di citazioni SGX <a id="sgx-quote"></a>

Le applicazioni enclave che eseguono l'attestazione remota devono generare una CITAZIONE. La CITAZIONE fornisce la prova crittografica dell'identità e dello stato dell'applicazione, oltre all'ambiente in cui l'enclave è in esecuzione. La generazione di CITAZIONI si basa su alcuni componenti software attendibili di Intel, che fanno parte dei componenti software della piattaforma SGX (PSW/DCAP). Questo PSW viene fornito come DaemonSet che viene eseguito per nodo. Può essere sfruttato per richiedere la CITAZIONE dell'attestazione dalle app enclave. L'uso del servizio Azure Kubernetes consentirà di gestire meglio la compatibilità tra il componente PSW e altri componenti SW nell'host. Altre informazioni sull'utilizzo e sulle funzionalità di questo componente sono disponibili [qui](confidential-nodes-out-of-proc-attestation.md).

## <a name="programming--application-models"></a>Programmazione e modelli applicativi

### <a name="confidential-containers"></a>Contenitori riservati

I [contenitori riservati](confidential-containers.md) eseguono programmi esistenti e la maggior parte dei **linguaggi di programmazione più diffusi** (Python, Node, Java e così via), insieme alle dipendenze di librerie esistenti, senza alcuna modifica o ricompilazione del codice sorgente. Questo modello è quello che consente di ottenere più velocemente la riservatezza abilitata tramite progetti open source e partner di Azure. Le immagini del contenitore preparate per l'esecuzione nelle enclave sicure sono dette contenitori riservati.

### <a name="enclave-aware-containers"></a>Contenitori con riconoscimento dell'enclave

Il servizio Azure Kubernetes supporta le applicazioni programmate per l'esecuzione su nodi di confidential computing e per utilizzare **set di istruzioni speciali** rese disponibili tramite gli SDK e i framework. Questo modello applicativo fornisce il maggior controllo sulle applicazioni con la più bassa TCB (Trusted Computing Base). Altre informazioni sui contenitori con riconoscimento dell'enclave sono disponibili [qui](enclave-aware-containers.md).

## <a name="next-steps"></a>Passaggi successivi

[Distribuire un cluster del servizio Azure Kubernetes con nodi di confidential computing](./confidential-nodes-aks-get-started.md)

[Esempi di contenitori riservati](https://github.com/Azure-Samples/confidential-container-samples)

[Elenco di SKU DCsv2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series)

<!-- LINKS - external -->
[Azure Attestation]: https://docs.microsoft.com/en-us/azure/attestation/


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions