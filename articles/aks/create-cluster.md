---
title: Creare un cluster Azure Kubernetes Service (AKS)
description: Creare un cluster AKS con l'interfaccia della riga di comando o con il portale di Azure.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 304f3807a70179e4aab2ede80dc08a1aa85a2e51
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098907"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>Creare un cluster Azure Kubernetes Service (AKS)

Un cluster di Azure Kubernetes Service (AKS) può essere creato con l'interfaccia della riga di comando di Azure o con il portale di Azure.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Usare il comando [az aks create][az-aks-create] per creare il cluster AKS.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

Con il comando `az aks create` sono disponibili le opzioni seguenti. Per altre informazioni su ognuno di questi argomenti, vedere [Riferimento all'interfaccia della riga di comando di Azure][az-aks-create] per il servizio Kubernetes di Azure.

| Argomento | DESCRIZIONE | Obbligatoria |
|---|---|:---:|
| `--name``-n` | Nome della risorsa per il cluster gestito. | Sì |
| `--resource-group``-g` | Nome del gruppo di risorse di Azure Kubernetes Service. | Sì |
| `--admin-username``-u` | Nome utente delle macchine virtuali Linux.  Impostazione predefinita: azureuser. | no |
| `--aad-client-app-id` | (ANTEPRIMA) L'ID di un'applicazione client di Azure Active Directory di tipo "Nativa". | no |
| `--aad-server-app-id` | (ANTEPRIMA) L'ID di un'applicazione server di Azure Active Directory di tipo "App Web/API". | no |
| `--aad-server-app-secret` | (ANTEPRIMA) Il segreto di un'applicazione server di Azure Active Directory. | no |
| `--aad-tenant-id` | (ANTEPRIMA) L'ID di un tenant di Azure Active Directory. | no |
| `--admin-username``-u` | Account utente da creare nelle macchine virtuali del nodo per l'accesso SSH.  Impostazione predefinita: azureuser. | no |
| ` --client-secret` | Segreto associato all'entità servizio. | no |
| `--dns-name-prefix``-p` | Prefisso DNS per l'indirizzo IP pubblico del cluster. | no |
| `--dns-service-ip` | Indirizzo IP assegnato al servizio DNS di Kubernetes. | no |
| `--docker-bridge-address` | Indirizzo IP e netmask assegnati al bridge Docker. | no |
| `--enable-addons``-a` | Abilitare i componenti aggiuntivi Kubernetes in un elenco delimitato da virgole. | no |
| `--enable-rbac``-r` | Abilitare il controllo degli accessi in base al ruolo di Kubernetes. | no |
| `--generate-ssh-keys` | Generare i file delle chiavi pubblica e privata SSH se mancanti. | no |
| `--kubernetes-version``-k` | Versione di Kubernetes da usare per creare il cluster, ad esempio '1.7.9' o '1.9.6'. | no |
| `--locaton``-l` | Posizione per il gruppo di risorse create automaticamente. | no |
| `--max-pods``-m` | Numero massimo di pod distribuibili in un nodo. | no |
| `--network-plugin` | Plug-in di rete Kubernetes da usare. | no |
| `--no-ssh-key``-x` | Non usare o creare una chiave SSH locale. | no |
| `--no-wait` | Indica che non è necessario attendere il termine dell'operazione a esecuzione prolungata. | no |
| `--node-count``-c` | Numero predefinito di nodi per i pool di nodi.  Impostazione predefinita: 3. | no |
| `--node-osdisk-size` | Dimensione di osDisk in GB per la macchina virtuale del pool di nodi. | no |
| `--node-vm-size``-s` | Dimensione della macchina virtuale.  Impostazione predefinita: Standard_D1_v2. | no |
| `--pod-cidr` | Intervallo IP in notazione CIDR da cui assegnare gli indirizzi IP dei pod quando si usa kubenet. | no |
| `--service-cidr` | Intervallo IP in notazione CIDR da cui assegnare gli indirizzi IP dei cluster del servizio. | no |
| `--service-principal` | Entità servizio usata per l'autenticazione del cluster. | no |
| `--ssh-key-value` | Valore del file di chiave SSH o percorso del file di chiave.  Impostazione predefinita: ~/.ssh/id_rsa.pub. | no |
| `--tags` | Tag separati da spazi in formato 'chiave[=valore]'. Usare '' per cancellare tag esistenti. | no |
| `--vnet-subnet-id` | ID di una subnet di una rete virtuale esistente in cui si vuole distribuire il cluster. | no |
| `--workspace-resource-id` | ID risorsa di un'area di lavoro di Log Analytics esistente da usare per l'archiviazione dei dati di monitoraggio. | no |

## <a name="azure-portal"></a>Portale di Azure

Per istruzioni sulla distribuzione di un cluster AKS con il portale di Azure, vedere la[guida introduttiva del portale Azure][aks-portal-quickstart] su Azure Kubernetes Service (AKS).

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
