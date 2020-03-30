---
title: Configurazione cluster nei servizi Di Azure Kubernetes (AKS)Cluster configuration in Azure Kubernetes Services (AKS)
description: Informazioni su come configurare un cluster nel servizio Azure Kubernetes (AKS)Learn how to configure a cluster in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 94f84beee2d7a76e48ac1470a0ce0b387929cc08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479163"
---
# <a name="configure-an-aks-cluster"></a>Configurare un cluster del servizio Azure Kubernetes

Come parte della creazione di un cluster AKS, potrebbe essere necessario personalizzare la configurazione del cluster in base alle proprie esigenze. In questo articolo vengono presentate alcune opzioni per la personalizzazione del cluster AKS.

## <a name="os-configuration-preview"></a>Configurazione del sistema operativo (anteprima)OS configuration (Preview)

AKS ora supporta Ubuntu 18.04 come sistema operativo del nodo (OS) in anteprima. Durante il periodo di anteprima, sono disponibili sia Ubuntu 16.04 che Ubuntu 18.04.

È necessario disporre delle seguenti risorse installate:

- Interfaccia della riga di comando di Azure, versione 2.2.0 o successiva
- L'estensione aks-preview 0.4.35

Per installare l'estensione aks-preview 0.4.35 o versione successiva, usare i comandi dell'interfaccia della riga di comando di Azure seguenti:To install the aks-preview 0.4.35 extension or later, use the following Azure CLI commands:

```azurecli
az extension add --name aks-preview
az extension list
```

Registrare `UseCustomizedUbuntuPreview` la funzione:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

La visualizzazione dello stato come **Registered**potrebbe richiedere alcuni minuti. È possibile controllare lo stato della registrazione utilizzando il comando [elenco funzionalità az:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Quando lo stato viene visualizzato come `Microsoft.ContainerService` registrato, aggiornare la registrazione del provider di risorse utilizzando il comando [az provider register:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Configurare il cluster per l'utilizzo di Ubuntu 18.04 quando viene creato il cluster. Utilizzare `--aks-custom-headers` il flag per impostare Ubuntu 18.04 come sistema operativo predefinito.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Se si desidera creare un normale cluster Ubuntu 16.04, è `--aks-custom-headers` possibile farlo omettendo il tag personalizzato.

## <a name="custom-resource-group-name"></a>Nome del gruppo di risorse personalizzato

Quando si distribuisce un cluster di servizi Azure Kubernetes in Azure, viene creato un secondo gruppo di risorse per i nodi di lavoro. Per impostazione predefinita, AKS `MC_resourcegroupname_clustername_location`chiamerà il gruppo di risorse del nodo, ma è anche possibile specificare un nome personalizzato.

Per specificare il nome del gruppo di risorse, installare l'estensione aks-preview dell'interfaccia della riga di comando di Azure versione 0.3.2 o successiva. Usando l'interfaccia della `--node-resource-group` riga `az aks create` di comando di Azure usare il parametro del comando per specificare un nome personalizzato per il gruppo di risorse. Se si usa un modello di Azure Resource Manager per distribuire un cluster `nodeResourceGroup` AKS, è possibile definire il nome del gruppo di risorse usando la proprietà.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Il gruppo di risorse secondario viene creato automaticamente dal provider di risorse di Azure nella sottoscrizione. Si noti che è possibile specificare il nome del gruppo di risorse personalizzato solo quando viene creato il cluster. 

Quando si lavora con il gruppo di risorse del nodo, tenere presente che non è possibile:As you work with the node resource group, keep in mind that you cannot:

- Specificare un gruppo di risorse esistente per il gruppo di risorse del nodo.
- Specificare una sottoscrizione diversa per il gruppo di risorse del nodo.
- Modificare il nome del gruppo di risorse del nodo dopo la creazione del cluster.
- Specificare i nomi per le risorse gestite all'interno del gruppo di risorse del nodo.
- Modificare o eliminare i tag creati da Azure delle risorse gestite all'interno del gruppo di risorse del nodo.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su `Kured` come usare per [applicare gli aggiornamenti del kernel e della sicurezza ai nodi Linux](node-updates-kured.md) nel cluster.
- Vedere Aggiornare un cluster di servizio (AKS) di Azure per informazioni su come aggiornare il cluster alla versione più recente di Kubernetes.See Upgrade an [Azure Kubernetes Service (AKS) cluster](upgrade-cluster.md) to learn how to upgrade your cluster to the latest version of Kubernetes.
- Vedere l'elenco delle [domande frequenti su AKS](faq.md) per trovare le risposte ad alcune domande aKS comuni.