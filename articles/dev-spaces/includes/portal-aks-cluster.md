---
title: File di inclusione
description: File di inclusione
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 05736495d0d4a0c3a5072d29ad27801b6d4a7241
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967868"
---
## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Creare un cluster Kubernetes abilitato per Azure Dev Spaces

1. Accedere al portale di Azure all'indirizzo http://portal.azure.com.
1. Scegliere **Crea una risorsa** > cercare **Kubernetes** > selezionare **Kubernetes Service** > **Crea**.

   Completare i passaggi seguenti in ogni intestazione del modulo per la creazione di un cluster del servizio contenitore di Azure.

    - **PROJECT DETAILS** (DETTAGLI PROGETTO): selezionare una sottoscrizione di Azure e un gruppo di risorse di Azure nuovo o esistente.
    - **CLUSTER DETAILS** (DETTAGLI CLUSTER): immettere nome, area (attualmente è possibile scegliere Stati Uniti orientali, Stati Uniti centrali, Europa occidentale, Stati Uniti occidentali 2, Canada centrale o Canada orientale), versione e prefisso del nome DNS per il cluster nel servizio contenitore di Azure.
    - **SCALE** (SCALABILITÀ): selezionare le dimensioni della macchina virtuale per i nodi agente AKS e il numero di nodi. Se si sta iniziando a usare Azure Dev Spaces, un nodo è sufficiente per esplorare tutte le funzionalità. Il numero di nodi può essere modificato facilmente in qualsiasi momento dopo la distribuzione del cluster. Le dimensioni della macchina virtuale non possono essere modificate dopo la creazione del cluster AKS. Tuttavia, se è necessario aumentare le prestazioni, dopo la distribuzione del cluster AKS è possibile facilmente creare un nuovo cluster AKS con macchine virtuali di dimensioni maggiori e usare Dev Spaces per la ridistribuzione nel cluster più grande.

   Assicurarsi di scegliere Kubernetes versione 1.10.3 o successive.

   ![Impostazioni di configurazione di Kubernetes](../media/common/Kubernetes-Create-Cluster-2.PNG)

   Al termine dell'operazione, selezionare **Next: Networking** (Avanti: Rete).

1. Assicurarsi che il routing dell'applicazione Http sia abilitato.

   ![Abilitare il routing dell'applicazione HTTP](../media/common/Kubernetes-Create-Cluster-3.PNG)

    > [!IMPORTANT]
    > È necessario assicurarsi di abilitare il routing dell'applicazione Http quando si crea il cluster AKS. Non è possibile modificare questa impostazione in un secondo momento.

1. Scegliere l'impostazione desiderata per il controllo degli accessi in base al ruolo. Azure Dev Spaces supporta i cluster con il controllo degli accessi in base al ruolo abilitato o disabilitato.

    ![Impostazione del controllo degli accessi in base al ruolo](../media/common/k8s-RBAC.PNG)

1. Al termine dell'operazione, selezionare **Review + create** (Esamina + crea) e quindi **Create** (Crea).
