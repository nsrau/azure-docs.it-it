---
title: Configurazione del cluster nei servizi Azure Kubernetes (AKS)
description: Informazioni su come configurare un cluster nel servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fe5ce13d9db8f2bc2231f87de7e602e63d239bfa
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725147"
---
# <a name="configure-an-aks-cluster"></a>Configurare un cluster del servizio Azure Kubernetes

Per creare un cluster AKS, potrebbe essere necessario personalizzare la configurazione del cluster in base alle esigenze. Questo articolo introduce alcune opzioni per la personalizzazione del cluster AKS.

## <a name="os-configuration-preview"></a>Configurazione del sistema operativo (anteprima)

AKS supporta ora Ubuntu 18.04 come sistema operativo dei nodi nell'anteprima. Durante il periodo di anteprima sono disponibili sia Ubuntu 16.04 sia Ubuntu 18.04.

Devono essere installate le risorse seguenti:

- Interfaccia della riga di comando di Azure, versione 2.2.0 o successiva
- Estensione aks-preview 0.4.35

Per installare l'estensione aks-preview 0.4.35 o una versione successiva, usare i comandi seguenti dell'interfaccia della riga di comando di Azure:

```azurecli
az extension add --name aks-preview
az extension list
```

Registrare la funzionalità `UseCustomizedUbuntuPreview`:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Potrebbero essere necessari alcuni minuti prima che lo stato venga visualizzato come **Registrato**. È possibile controllare lo stato di registrazione con il comando [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list):

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Quando lo stato diventa Registrato, aggiornare la registrazione del provider di risorse `Microsoft.ContainerService` usando il comando [ az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register):

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="new-clusters"></a>Nuovi cluster

Configurare il cluster per l'uso di Ubuntu 18.04 quando viene creato il cluster. Usare il flag `--aks-custom-headers` per impostare Ubuntu 18.04 come sistema operativo predefinito.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Se si vuole creare un normale cluster Ubuntu 16.04, è possibile omettere il tag `--aks-custom-headers` personalizzato.

### <a name="existing-clusters"></a>Cluster esistenti

Configurare un nuovo pool di nodi per usare Ubuntu 18.04. Usare il flag `--aks-custom-headers` per impostare Ubuntu 18.04 come sistema operativo predefinito per il pool di nodi.

```azure-cli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Se si vuole creare un normale pool di nodi Ubuntu 16.04, è possibile omettere il tag `--aks-custom-headers` personalizzato.


## <a name="custom-resource-group-name"></a>Nome del gruppo di risorse personalizzato

Quando si distribuisce un cluster del servizio Kubernetes di Azure in Azure, viene creato un secondo gruppo di risorse per i nodi di lavoro. Per impostazione predefinita, AKS assegna un nome al gruppo di risorse del nodo `MC_resourcegroupname_clustername_location`, ma è anche possibile specificare un nome personalizzato.

Per specificare un nome per il gruppo di risorse, installare la versione 0.3.2 o una versione successiva dell'estensione aks-preview dell'interfaccia della riga di comando di Azure. Nell'interfaccia della riga di comando di Azure, usare il parametro `--node-resource-group` del comando `az aks create` per specificare un nome personalizzato per il gruppo di risorse. Se si usa un modello di Azure Resource Manager per distribuire un cluster AKS, è possibile definire il nome del gruppo di risorse usando la proprietà `nodeResourceGroup`.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Il gruppo di risorse secondario viene creato automaticamente dal provider di risorse Azure nella propria sottoscrizione. Quando viene creato il cluster, è possibile specificare solo il nome del gruppo di risorse personalizzato. 

Quando si lavora con il gruppo di risorse del nodo, tenere presente che non è possibile:

- Specificare un gruppo di risorse esistente come gruppo di risorse del nodo.
- Specificare una sottoscrizione diversa come gruppo di risorse del nodo.
- Modificare il nome del gruppo di risorse del nodo dopo la creazione del cluster.
- Specificare i nomi delle risorse gestite nel gruppo di risorse del nodo.
- Modificare o eliminare i tag creati da Azure delle risorse gestite all'interno del gruppo di risorse del nodo.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare `Kured` per [applicare gli aggiornamenti di sicurezza e del kernel ai nodi Linux](node-updates-kured.md) nel cluster.
- Per informazioni su come aggiornare il cluster alla versione più recente di Kubernetes, vedere [Aggiornare un cluster del servizio Azure Kubernetes](upgrade-cluster.md).
- Per trovare le risposte ad alcune domande comuni su AKS, vedere l'elenco delle [domande frequenti su AKS](faq.md).