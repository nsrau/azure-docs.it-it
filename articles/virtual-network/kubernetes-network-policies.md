---
title: Criteri di rete di Azure Kubernetes | Microsoft Docs
description: Informazioni sui criteri di rete di Kubernetes per proteggere il cluster Kubernetes.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 5a6da7e65a9a3e962a2df37b062792fbb990d04d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159688"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Panoramica dei criteri di rete di Azure Kubernetes

I criteri di rete consentono la micro-segmentazione per i pod, così come i gruppi di sicurezza di rete supportano la micro-segmentazione per le macchine virtuali. L'implementazione dei criteri di rete di Azure supporta la specifica standard per i criteri di rete di Kubernetes. È possibile usare le etichette per selezionare un gruppo di pod e definire un elenco di regole in ingresso e in uscita che specificano il tipo di traffico consentito da e verso questi pod. Altre informazioni sui criteri di rete di Kubernetes sono disponibili nella [documentazione di Kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Panoramica dei criteri di rete di Kubernetes](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

I criteri di rete di Azure operano in combinazione con l'interfaccia di rete dei contenitori (CNI, Container Network Interface) di Azure che rende disponibile l'integrazione della rete virtuale per i contenitori. Sono supportati solo per i nodi Linux al momento. Le implementazioni configurano le regole per le tabelle IP Linux in base ai criteri definiti per applicare il filtro del traffico.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Pianificazione della sicurezza per il cluster Kubernetes
Quando si implementa la sicurezza per il cluster, usare i gruppi di sicurezza di rete (gruppi) per filtrare il traffico nord-sud, ovvero il traffico in ingresso e in uscita dalla subnet del cluster e usare i criteri di rete Kubernetes per il traffico East-West, ovvero il traffico tra i pod in il cluster.

## <a name="using-azure-kubernetes-network-policies"></a>Uso dei criteri di rete di Azure Kubernetes
È possibile usare criteri di rete di Azure nei modi seguenti per implementare la micro-segmentazione per i pod.

### <a name="acs-engine"></a>ACS-Engine
ACS-Engine è uno strumento che genera un modello di Azure Resource Manager per la distribuzione di un cluster Kubernetes in Azure. La configurazione del cluster è specificata in un file JSON che viene passato allo strumento durante la generazione del modello. Per altre informazioni sull'intero elenco delle impostazioni di cluster supportate e le relative descrizioni, vedere Motore del servizio Microsoft Azure Container - Definizione del cluster.

Per abilitare i criteri nei cluster distribuiti tramite ACS-Engine, specificare il valore "azure" per l'impostazione networkPolicy nel file di definizione del cluster.

#### <a name="example-configuration"></a>Configurazione di esempio

La configurazione di esempio JSON seguente crea una nuova rete virtuale e una subnet, quindi distribuisce un cluster Kubernetes in tale rete con l'interfaccia di rete dei contenitori di Azure. È consigliabile usare il Blocco note per modificare il file JSON. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Creazione di un cluster Kubernetes in Azure
L'implementazione è utilizzabile per specificare i criteri di rete per i pod nei cluster Kubernetes distribuiti manualmente, senza affidarsi a strumenti come ACS-Engine. In questo caso, è prima di tutto necessario installare il plug-in CNI e abilitarlo in ogni macchina virtuale in un cluster. Per istruzioni dettagliate, vedere [Distribuire il plug-in per un cluster Kubernetes](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Dopo aver distribuito il cluster, eseguire il comando `kubectl` seguente per scaricare e applicare i criteri di rete di Azure *daemonset* nel cluster.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
La soluzione è anche open source e il codice è disponibile nel [repository Azure Container Networking](https://github.com/Azure/azure-container-networking/tree/master/npm).



## <a name="next-steps"></a>Passaggi successivi
- Informazioni sul [servizio Azure Kubernetes](../aks/intro-kubernetes.md).
-  Informazioni sulla [rete dei contenitori](container-networking-overview.md).
- [Distribuire il plug-in](deploy-container-networking.md) per i cluster Kubernetes o i contenitori Docker.
