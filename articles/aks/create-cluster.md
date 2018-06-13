---
title: Creare un cluster Azure Kubernetes Service (AKS)
description: Creare un cluster AKS con l'interfaccia della riga di comando o con il portale di Azure.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 00672b6272ce9c775621e519c327c0b8368bc220
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33935070"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>Creare un cluster Azure Kubernetes Service (AKS)

Un cluster di Azure Kubernetes Service (AKS) può essere creato con l'interfaccia della riga di comando di Azure o con il portale di Azure.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Usare il comando [az aks create][az-aks-create] per creare il cluster AKS.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

Con il comando `az aks create` sono disponibili le opzioni seguenti.

| Argomento | DESCRIZIONE | Obbligatoria |
|---|---|:---:|
| `--name``-n` | Nome della risorsa per il cluster gestito. | Sì |
| `--resource-group``-g` | Nome del gruppo di risorse di Azure Kubernetes Service. | Sì |
| `--admin-username``-u` | Nome utente delle macchine virtuali Linux.  Impostazione predefinita: azureuser. | no |
| ` --client-secret` | Segreto associato all'entità servizio. | no |
| `--dns-name-prefix``-p` | Prefisso DNS per l'indirizzo IP pubblico del cluster. | no |
| `--generate-ssh-keys` | Generare i file delle chiavi pubblica e privata SSH se mancanti. | no |
| `--kubernetes-version``-k` | Versione di Kubernetes da usare per creare il cluster, ad esempio '1.7.9' o '1.8.2'.  Impostazione predefinita: 1.7.7. | no |
| `--no-wait` | Indica che non è necessario attendere il termine dell'operazione a esecuzione prolungata. | no |
| `--node-count``-c` | Numero predefinito di nodi per i pool di nodi.  Impostazione predefinita: 3. | no |
| `--node-osdisk-size` | Dimensione di osDisk in GB per la macchina virtuale del pool di nodi. | no |
| `--node-vm-size``-s` | Dimensione della macchina virtuale.  Impostazione predefinita: Standard_D1_v2. | no |
| `--service-principal` | Entità servizio usata per l'autenticazione del cluster. | no |
| `--ssh-key-value` | Valore del file di chiave SSH o percorso del file di chiave.  Impostazione predefinita: ~/.ssh/id_rsa.pub. | no |
| `--tags` | Tag separati da spazi in formato 'chiave[=valore]'. Usare '' per cancellare tag esistenti. | no |

## <a name="azure-portal"></a>Portale di Azure

Per istruzioni sulla distribuzione di un cluster AKS con il portale di Azure, vedere la[guida introduttiva del portale Azure][aks-portal-quickstart] su Azure Kubernetes Service (AKS).

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
