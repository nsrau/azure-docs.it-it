---
title: Configurazione del cluster nei servizi Kubernetes di Azure (AKS)
description: Informazioni su come configurare un cluster in Azure Kubernetes Service (AKS)
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

Come parte della creazione di un cluster AKS, potrebbe essere necessario personalizzare la configurazione del cluster in base alle esigenze. Questo articolo introduce alcune opzioni per la personalizzazione del cluster AKS.

## <a name="os-configuration-preview"></a>Configurazione del sistema operativo (anteprima)

AKS supporta ora Ubuntu 18,04 come sistema operativo node (sistema operativo) in anteprima. Durante il periodo di anteprima sono disponibili sia Ubuntu 16,04 che Ubuntu 18,04.

È necessario che siano installate le risorse seguenti:

- INTERFACCIA della riga di comando di Azure, versione 2.2.0 o successiva
- Estensione 0.4.35 AKS-Preview

Per installare l'estensione AKS-Preview 0.4.35 o versione successiva, usare i seguenti comandi dell'interfaccia della riga di comando di Azure:

```azurecli
az extension add --name aks-preview
az extension list
```

Registrare la `UseCustomizedUbuntuPreview` funzionalità:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Potrebbero essere necessari alcuni minuti prima che lo stato venga visualizzato come **registrato**. È possibile controllare lo stato della registrazione usando il comando [AZ feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Quando lo stato viene visualizzato come registrato, aggiornare la registrazione del `Microsoft.ContainerService` provider di risorse usando il comando [AZ provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Configurare il cluster per l'uso di Ubuntu 18,04 quando viene creato il cluster. Usare il `--aks-custom-headers` flag per impostare Ubuntu 18,04 come sistema operativo predefinito.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Se si vuole creare un normale cluster Ubuntu 16,04, è possibile omettere il tag personalizzato `--aks-custom-headers` .

## <a name="custom-resource-group-name"></a>Nome del gruppo di risorse personalizzato

Quando si distribuisce un cluster del servizio Kubernetes di Azure in Azure, viene creato un secondo gruppo di risorse per i nodi del ruolo di lavoro. Per impostazione predefinita, AKS assegna un nome al gruppo `MC_resourcegroupname_clustername_location`di risorse del nodo, ma è anche possibile specificare un nome personalizzato.

Per specificare il nome del gruppo di risorse, installare la versione dell'estensione dell'interfaccia della riga di comando di Azure AKS-Preview 0.3.2 o versioni successive. Usando l'interfaccia della `--node-resource-group` `az aks create` riga di comando di Azure, usare il parametro del comando per specificare un nome personalizzato per il gruppo di risorse. Se si usa un modello di Azure Resource Manager per distribuire un cluster AKS, è possibile definire il nome del gruppo di risorse `nodeResourceGroup` usando la proprietà.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Il gruppo di risorse secondario viene creato automaticamente dal provider di risorse di Azure nella propria sottoscrizione. Si noti che quando viene creato il cluster, è possibile specificare solo il nome del gruppo di risorse personalizzato. 

Quando si lavora con il gruppo di risorse del nodo, tenere presente che non è possibile:

- Specificare un gruppo di risorse esistente per il gruppo di risorse del nodo.
- Specificare una sottoscrizione diversa per il gruppo di risorse del nodo.
- Modificare il nome del gruppo di risorse del nodo dopo la creazione del cluster.
- Specificare i nomi per le risorse gestite all'interno del gruppo di risorse del nodo.
- Modificare o eliminare i tag creati da Azure delle risorse gestite all'interno del gruppo di risorse del nodo.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare `Kured` per [applicare gli aggiornamenti di sicurezza e kernel ai nodi Linux](node-updates-kured.md) nel cluster.
- Per informazioni su come aggiornare il cluster alla versione più recente di Kubernetes, vedere [aggiornare un cluster di Azure Kubernetes Service (AKS)](upgrade-cluster.md) .
- Per trovare le risposte ad alcune domande comuni su AKS, vedere l'elenco delle [domande frequenti su AKS](faq.md) .