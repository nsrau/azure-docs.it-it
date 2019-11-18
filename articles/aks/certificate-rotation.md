---
title: Ruotare i certificati in Azure Kubernetes Service (AKS)
description: Informazioni su come ruotare i certificati in un cluster Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: 00d8546cb20d12c5f1a94bdcababa04a77c73133
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134411"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Ruotare i certificati in Azure Kubernetes Service (AKS)

Il servizio Azure Kubernetes (AKS) USA i certificati per l'autenticazione con molti dei suoi componenti. Periodicamente, potrebbe essere necessario ruotare tali certificati per motivi di sicurezza o di criteri. Ad esempio, è possibile avere un criterio per ruotare tutti i certificati ogni 90 giorni.

Questo articolo illustra come ruotare i certificati nel cluster AKS.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede la versione 2.0.76 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].


### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga comando di aks-preview

Per usare questa funzionalità, è necessaria l'estensione dell'interfaccia della riga di comando *AKS-Preview* 0.4.21 o versione successiva. Installare l'estensione dell'interfaccia della riga di comando di Azure *AKS-Preview* usando il comando [AZ Extension Add][az-extension-add] , quindi verificare la disponibilità di eventuali aggiornamenti tramite il comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>Certificati AKS, autorità di certificazione e account del servizio

AKS genera e usa i certificati, le autorità di certificazione e gli account di servizio seguenti:

* Il server dell'API AKS crea un'autorità di certificazione (CA) denominata CA del cluster.
* Il server API dispone di una CA del cluster, che firma i certificati per la comunicazione unidirezionale dal server API a kubelets.
* Ogni kubelet crea anche una richiesta di firma del certificato (CSR), firmata dalla CA del cluster, per la comunicazione dalla kubelet al server API.
* L'archivio del valore della chiave ETCD dispone di un certificato firmato dalla CA del cluster per la comunicazione da ETCD al server API.
* L'archivio del valore della chiave ETCD crea una CA che firma i certificati per autenticare e autorizzare la replica dei dati tra le repliche ETCD nel cluster AKS.
* L'aggregatore di API usa la CA del cluster per emettere certificati per la comunicazione con altre API, ad esempio Open Service Broker per Azure. L'aggregatore di API può anche avere una propria autorità di certificazione per emettere tali certificati, ma attualmente usa la CA del cluster.
* Ogni nodo utilizza un token dell'account di servizio (SA), firmato dalla CA del cluster.
* Il client `kubectl` dispone di un certificato per la comunicazione con il cluster AKS.

> [!NOTE]
> I cluster AKS creati prima del 2019 marzo hanno certificati che scadono dopo due anni. Tutti i cluster creati dopo il 2019 marzo o qualsiasi cluster con i relativi certificati ruotati hanno certificati che scadono dopo 30 anni.

## <a name="rotate-your-cluster-certificates"></a>Ruotare i certificati del cluster

> [!WARNING]
> La rotazione dei certificati con `az aks rotate-certs` può causare fino a 30 minuti di tempo di inattività per il cluster AKS.

Usare [AZ AKS Get-credentials][az-aks-get-credentials] per accedere al cluster AKS. Questo comando consente inoltre di scaricare e configurare il certificato client di `kubectl` nel computer locale.

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Usare `az aks rotate-certs` per ruotare tutti i certificati, CA e SAs nel cluster.

```console
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Potrebbero essere necessari fino a 30 minuti per il completamento del `az aks rotate-certs`. Se il comando ha esito negativo prima del completamento, usare `az aks show` per verificare che lo stato del cluster sia *rotazione del certificato*. Se il cluster si trova in uno stato di errore, rieseguire `az aks rotate-certs` per ruotare nuovamente i certificati.

Verificare che i certificati precedenti non siano più validi eseguendo un comando `kubectl`. Poiché i certificati utilizzati da `kubectl`non sono stati aggiornati, verrà visualizzato un errore.  Ad esempio:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Aggiornare il certificato usato da `kubectl` eseguendo `az aks get-credentials`.

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Verificare che i certificati siano stati aggiornati eseguendo un comando `kubectl`, che ora avrà esito positivo. Ad esempio:

```console
kubectl get no
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come ruotare automaticamente i certificati, le autorità di certificazione e le associazioni di sicurezza del cluster. Per altre informazioni sulle procedure consigliate per la sicurezza di AKS, vedere procedure consigliate [per la sicurezza e gli aggiornamenti del cluster in Azure Kubernetes Service (AKS)][aks-best-practices-security-upgrades] .


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
