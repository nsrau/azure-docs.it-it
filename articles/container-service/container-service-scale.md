---
title: Ridimensionare un cluster del servizio contenitore di Azure | Microsoft Docs
description: "È possibile ridimensionare il numero di nodi agente in un cluster DC/OS, Docker Swarm o Kubernetes nel servizio contenitore di Azure tramite l&quot;interfaccia della riga di comando o il Portale di Azure."
services: container-service
documentationcenter: 
author: sauryadas
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: Docker, Contenitori, Micro-servizi, Mesos, Azure
ms.assetid: 4a567474-f9a2-4172-bf86-7522aa4d4d80
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: saudas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2464901d22bb91cbf396ef60f4bda6d979b578b7
ms.openlocfilehash: 022e34811de9d2023d113c43d6f85842812dfee3
ms.lasthandoff: 03/02/2017


---
# <a name="scale-agent-nodes-in-a-container-service-cluster"></a>Ridimensionare i nodi agente in un cluster del servizio contenitore
Dopo aver [distribuito un cluster del servizio contenitore di Azure](container-service-deployment.md), potrebbe essere necessario modificare il numero di nodi agente. Ad esempio, potrebbero essere necessari più agenti in modo da eseguire più applicazioni o istanze contenitore. 

È possibile modificare il numero di nodi agente in un cluster DC/OS, Docker Swarm o Kubernetes tramite il portale di Azure o l'interfaccia della riga di comando di Azure 2.0. 

## <a name="scale-with-the-azure-portal"></a>Ridimensionare con il portale di Azure

1. Nel [portale di Azure](https://portal.azure.com), passare a **Servizi contenitore** e fare clic sul servizio contenitore da modificare.
2. Nel pannello **Servizio contenitore** fare clic su **Agenti**.
3. In **Conteggio macchine virtuali** immettere il numero desiderato di nodi di agenti.

    ![Ridimensionare un pool nel portale](./media/container-service-scale/container-service-scale-portal.png)

4. Per salvare la configurazione, fare clic su **Salva**.



## <a name="scale-with-the-azure-cli-20"></a>Ridimensionare con l'interfaccia della riga di comando di Azure 2.0

Assicurarsi di avere [installato](/cli/azure/install-az-cli2) la versione più recente dell'interfaccia della riga di comando di Azure 2.0 e di avere eseguito l'accesso a un account Azure (`az login`).


### <a name="see-the-current-agent-count"></a>Visualizzare il numero di agenti corrente
Per visualizzare il numero di agenti attualmente presenti nel cluster, eseguire il comando `az acs show`. Così facendo viene mostrata la configurazione del cluster. Ad esempio, il comando seguente mostra la configurazione del servizio contenitore denominato `containerservice-myACSName` nel gruppo di risorse `myResourceGroup`:

```azurecli
az acs show -g myResourceGroup -n containerservice-myACSName
```

Il comando restituisce il numero di agenti nel valore `Count` in `AgentPoolProfiles`.


### <a name="use-the-az-acs-scale-command"></a>Usare il comando az acs scale
Per modificare il numero di nodi dell'agente, eseguire il comando `az acs scale` e indicare il **gruppo di risorse**, il **nome del servizio contenitore** e il **numero di nuovi agenti desiderato**. Se si specifica un numero più piccolo è possibile ridurre le prestazioni, mentre se si specifica un numero più grande è possibile aumentarle.

Ad esempio, per modificare il numero di agenti nel cluster precedente a 10, digitare il comando seguente:

```azurecli
azure acs scale -g myResourceGroup -n containerservice-myACSName --new-agent-count 10
```

L'interfaccia della riga di comando di Azure 2.0 restituisce una stringa JSON che rappresenta la nuova configurazione del servizio contenitore, incluso il nuovo numero di agenti.

Per ulteriori opzioni di comandi, eseguire `az acs scale --help`.


## <a name="scaling-considerations"></a>Considerazioni sulla scalabilità


* Il numero di nodi agente deve essere compreso tra 1 e 100 inclusi. 

* La quota di core può limitare il numero di nodi agente in un cluster.

* Le operazioni di scalabilità del nodo agente vengono applicate a un set di scalabilità di macchine virtuali di Azure che contiene il pool dell'agente. In un cluster DC/OS, le operazioni illustrate in questo articolo consentono di ridimensionare solo i nodi di agenti nel pool privato.

* A seconda dell'agente di orchestrazione distribuito nel cluster, è possibile ridimensionare separatamente il numero di istanze di un contenitore in esecuzione nel cluster. Ad esempio, in un cluster DC/OS, usare l'[interfaccia utente di Marathon](container-service-mesos-marathon-ui.md) per modificare il numero di istanze di un'applicazione contenitore.

* Attualmente, non è supportata la scalabilità automatica dei nodi di agenti in un cluster del servizio contenitore.





## <a name="next-steps"></a>Passaggi successivi
* Vedere [altri esempi](container-service-create-acs-cluster-cli.md) dell'uso di comandi dell'interfaccia della riga di comando di Azure 2.0 con il servizio contenitore di Azure.
* Ulteriori informazioni sui [pool di agenti DC/OS](container-service-dcos-agents.md) del servizio contenitore di Azure.


