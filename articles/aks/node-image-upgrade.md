---
title: Aggiornare le immagini del nodo di Azure Kubernetes Service (AKS)
description: Informazioni su come aggiornare le immagini nei nodi del cluster AKS e nei pool di nodi.
author: laurenhughes
ms.author: lahugh
ms.service: container-service
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 040f4378e01c3696b9a74bfcc27230503828f19a
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87562788"
---
# <a name="preview---azure-kubernetes-service-aks-node-image-upgrades"></a>Anteprima: aggiornamenti dell'immagine del nodo del servizio Azure Kubernetes (AKS)

AKS supporta l'aggiornamento delle immagini in un nodo, in modo da essere aggiornati con gli aggiornamenti più recenti del sistema operativo e del runtime. AKS fornisce una nuova immagine alla settimana con gli aggiornamenti più recenti, quindi è vantaggioso aggiornare regolarmente le immagini del nodo per le funzionalità più recenti, incluse le patch di Linux o Windows. Questo articolo illustra come aggiornare le immagini dei nodi del cluster AKS e come aggiornare le immagini del pool di nodi senza aggiornare la versione di Kubernetes.

Se si è interessati a conoscere le immagini più recenti fornite da AKS, vedere le [Note sulla versione di AKS](https://github.com/Azure/AKS/releases) per altri dettagli.

Per informazioni sull'aggiornamento della versione di Kubernetes per il cluster, vedere [aggiornare un cluster AKS][upgrade-cluster].

## <a name="register-the-node-image-upgrade-preview-feature"></a>Registrare la funzionalità di anteprima dell'aggiornamento dell'immagine del nodo

Per usare la funzionalità di aggiornamento dell'immagine del nodo durante il periodo di anteprima, è necessario registrare la funzionalità.

```azurecli
# Register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "NodeImageUpgradePreview"
```

Per il completamento della registrazione, verranno necessari alcuni minuti. Usare il comando seguente per verificare che la funzionalità sia registrata:

```azurecli
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/NodeImageUpgradePreview')].{Name:name,State:properties.state}"
```

Durante l'anteprima, è necessaria l'estensione dell'interfaccia della riga di comando *AKS-Preview* per usare l'aggiornamento dell'immagine del nodo. Usare il comando [AZ Extension Add][az-extension-add] , quindi verificare la presenza di eventuali aggiornamenti disponibili usando il comando [AZ Extension Update][az-extension-update] :

```azurecli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

Quando lo stato diventa Registrato, aggiornare la registrazione del provider di risorse `Microsoft.ContainerService` usando il comando [ az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register):

```azurecli
az provider register --namespace Microsoft.ContainerService
```  

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Aggiornare tutti i nodi in tutti i pool di nodi

L'aggiornamento dell'immagine del nodo viene eseguito con `az aks upgrade` . Per aggiornare l'immagine del nodo, utilizzare il comando seguente:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Durante l'aggiornamento, controllare lo stato delle immagini del nodo con il `kubectl` comando seguente per ottenere le etichette e filtrare le informazioni sull'immagine del nodo corrente:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Al termine dell'aggiornamento, usare `az aks show` per ottenere i dettagli del pool di nodi aggiornati. L'immagine del nodo corrente viene visualizzata nella `nodeImageVersion` Proprietà.

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Aggiornare un pool di nodi specifico

L'aggiornamento dell'immagine in un pool di nodi è simile all'aggiornamento dell'immagine in un cluster.

Per aggiornare l'immagine del sistema operativo del pool di nodi senza eseguire l'aggiornamento di un cluster Kubernetes, usare l' `--node-image-only` opzione nell'esempio seguente:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Durante l'aggiornamento, controllare lo stato delle immagini del nodo con il `kubectl` comando seguente per ottenere le etichette e filtrare le informazioni sull'immagine del nodo corrente:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Al termine dell'aggiornamento, usare `az aks nodepool show` per ottenere i dettagli del pool di nodi aggiornati. L'immagine del nodo corrente viene visualizzata nella `nodeImageVersion` Proprietà.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Aggiornare le immagini del nodo con picchi di nodo

Per velocizzare il processo di aggiornamento dell'immagine del nodo, è possibile aggiornare le immagini del nodo usando un valore di aumento del nodo personalizzabile. Per impostazione predefinita, AKS usa un nodo aggiuntivo per configurare gli aggiornamenti.

Se si vuole aumentare la velocità degli aggiornamenti, usare il `--max-surge` valore per configurare il numero di nodi da usare per gli aggiornamenti in modo che vengano completati più velocemente. Per altre informazioni sui compromessi delle diverse `--max-surge` Impostazioni, vedere Personalizzare l' [aggiornamento dell'aumento dei nodi][max-surge].

Il seguente comando imposta il valore di aumento massimo per l'esecuzione di un aggiornamento dell'immagine del nodo:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Durante l'aggiornamento, controllare lo stato delle immagini del nodo con il `kubectl` comando seguente per ottenere le etichette e filtrare le informazioni sull'immagine del nodo corrente:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Usare `az aks nodepool show` per ottenere i dettagli del pool di nodi aggiornati. L'immagine del nodo corrente viene visualizzata nella `nodeImageVersion` Proprietà.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere le [Note sulla versione di AKS](https://github.com/Azure/AKS/releases) per informazioni sulle immagini del nodo più recenti.
- Informazioni su come aggiornare la versione di Kubernetes con l' [aggiornamento di un cluster AKS][upgrade-cluster].
- [Applicare gli aggiornamenti di sicurezza e kernel ai nodi Linux in Azure Kubernetes Service (AKS)][security-update]
- Altre informazioni su più pool di nodi e su come aggiornare i pool di nodi con [creare e gestire pool][use-multiple-node-pools]di nodi multipli.

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[security-update]: node-updates-kured.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade-preview
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
