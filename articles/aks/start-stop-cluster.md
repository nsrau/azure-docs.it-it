---
title: Avviare e arrestare un servizio Azure Kubernetes (AKS)
description: Informazioni su come arrestare o avviare un cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 09/18/2020
author: palma21
ms.openlocfilehash: a743a6c30d5ce8bcaf275bf1a658f8343de4d4fb
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937880"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster-preview"></a>Arrestare e avviare un cluster Azure Kubernetes Service (AKS) (anteprima)

È possibile che i carichi di lavoro AKS non debbano essere eseguiti in modo continuo, ad esempio un cluster di sviluppo usato solo durante l'orario di ufficio. Ciò comporta tempi in cui il cluster del servizio Kubernetes di Azure (AKS) potrebbe essere inattivo, in esecuzione non più dei componenti di sistema. È possibile ridurre il footprint del cluster [scalando tutti i `User` pool di nodi a 0](scale-cluster.md#scale-user-node-pools-to-0), ma il [ `System` pool](use-system-pools.md) è ancora necessario per eseguire i componenti di sistema mentre il cluster è in esecuzione. Per ottimizzare ulteriormente i costi durante questi periodi, è possibile disattivare completamente (arrestare) il cluster. Questa azione arresterà completamente il piano di controllo e i nodi dell'agente, consentendo di risparmiare su tutti i costi di calcolo, mantenendo al tempo stesso tutti gli oggetti e lo stato del cluster archiviati per il riavvio. In questo modo è possibile prelevare il proprio punto di partenza dopo un week-end o fare in modo che il cluster sia in esecuzione solo quando si eseguono i processi batch.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

### <a name="install-the-aks-preview-azure-cli"></a>Installare l'interfaccia della riga di comando di `aks-preview` Azure 

È anche necessaria l'estensione dell'interfaccia della riga di comando di Azure *AKS-Preview* versione 0.4.64 o successiva. Installare l'estensione dell'interfaccia della riga di comando di Azure *AKS-Preview* usando il comando [AZ Extension Add][az-extension-add] . In alternativa, installare gli eventuali aggiornamenti disponibili usando il comando [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

> [!WARNING]
> Lo stato del cluster di un cluster AKS arrestato viene mantenuto per un massimo di 12 mesi. Se il cluster viene arrestato per più di 12 mesi, non è possibile recuperare lo stato del cluster. Per ulteriori informazioni, vedere i [criteri di supporto di AKS](support-policies.md).
> È possibile avviare o eliminare solo un cluster AKS interrotto. Per eseguire qualsiasi operazione come la scala o l'aggiornamento, avviare innanzitutto il cluster.


### <a name="register-the-startstoppreview-preview-feature"></a>Registrare la `StartStopPreview` funzionalità di anteprima

Per usare la funzionalità avvia/arresta cluster, è necessario abilitare il `StartStopPreview` flag funzionalità per la sottoscrizione.

Registrare il `StartStopPreview` flag funzionalità usando il comando [AZ feature Register][az-feature-register] , come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "StartStopPreview"
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. Verificare lo stato della registrazione usando il comando [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/StartStopPreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft. servizio contenitore* usando il comando [AZ provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="stop-an-aks-cluster"></a>Arrestare un cluster AKS

È possibile usare il `az aks stop` comando per arrestare i nodi e il piano di controllo di un cluster AKS in esecuzione. Nell'esempio seguente viene arrestato un cluster denominato *myAKSCluster*:

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

È possibile verificare quando il cluster viene arrestato usando il comando [AZ AKS Show] [AZ-AKS-Show] e confermando che la `powerState` Mostra come nell' `Stopped` output seguente:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Stopped"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Se il `provisioningState` Mostra `Stopping` che indica che il cluster non è stato ancora arrestato completamente.

> [!IMPORTANT]
> Se si usano i [budget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) per l'interruzione del Pod, l'operazione di arresto può richiedere più tempo perché il completamento del processo di svuotamento potrebbe richiedere più tempo.


## <a name="start-an-aks-cluster"></a>Avviare un cluster AKS

È possibile usare il `az aks start` comando per avviare i nodi del cluster AKS e il piano di controllo interrotti. Il cluster viene riavviato con lo stato del piano di controllo precedente e il numero di nodi agente.  
Nell'esempio seguente viene avviato un cluster denominato *myAKSCluster*:

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

È possibile verificare quando il cluster è stato avviato usando il comando [AZ AKS Show] [AZ-AKS-Show] e confermare che il `powerState` Mostra `Running` come nell'output seguente:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Running"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Se il `provisioningState` Mostra `Starting` che indica che il cluster non è ancora stato completamente avviato.


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come ridimensionare i `User` pool in 0, vedere [ridimensionare i `User` pool a 0](scale-cluster.md#scale-user-node-pools-to-0).
- Per informazioni su come risparmiare sui costi usando le istanze di spot, vedere [aggiungere un pool di nodi spot ad AKS](spot-node-pool.md).
- Per altre informazioni sui criteri di supporto di AKS, vedere [criteri di supporto AKS](support-policies.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli&preserve-view=true
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register&preserve-view=true
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true