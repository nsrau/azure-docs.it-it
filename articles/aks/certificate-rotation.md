---
title: Ruotare i certificati in Azure Kubernetes Service (AKS)
description: Informazioni su come ruotare i certificati in un cluster Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: 00dcef4ae0f04fc7f550859238ae8c7e1ad19384
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80549061"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Ruotare i certificati in Azure Kubernetes Service (AKS)

Il servizio Azure Kubernetes (AKS) USA i certificati per l'autenticazione con molti dei suoi componenti. Periodicamente, potrebbe essere necessario ruotare tali certificati per motivi di sicurezza o di criteri. Ad esempio, è possibile avere un criterio per ruotare tutti i certificati ogni 90 giorni.

Questo articolo illustra come ruotare i certificati nel cluster AKS.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede la versione 2.0.77 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>Certificati AKS, autorità di certificazione e account del servizio

AKS genera e usa i certificati, le autorità di certificazione e gli account di servizio seguenti:

* Il server dell'API AKS crea un'autorità di certificazione (CA) denominata CA del cluster.
* Il server API dispone di una CA del cluster, che firma i certificati per la comunicazione unidirezionale dal server API a kubelets.
* Ogni kubelet crea anche una richiesta di firma del certificato (CSR), firmata dalla CA del cluster, per la comunicazione dalla kubelet al server API.
* L'archivio del valore della chiave ETCD dispone di un certificato firmato dalla CA del cluster per la comunicazione da ETCD al server API.
* L'archivio del valore della chiave ETCD crea una CA che firma i certificati per autenticare e autorizzare la replica dei dati tra le repliche ETCD nel cluster AKS.
* L'aggregatore di API usa la CA del cluster per emettere certificati per la comunicazione con altre API. L'aggregatore di API può anche avere una propria autorità di certificazione per emettere tali certificati, ma attualmente usa la CA del cluster.
* Ogni nodo utilizza un token dell'account di servizio (SA), firmato dalla CA del cluster.
* Il `kubectl` client dispone di un certificato per comunicare con il cluster AKS.

> [!NOTE]
> I cluster AKS creati prima del 2019 marzo hanno certificati che scadono dopo due anni. Tutti i cluster creati dopo il 2019 marzo o qualsiasi cluster con i relativi certificati ruotati hanno certificati della CA del cluster che scadono dopo 30 anni. Tutti gli altri certificati scadono dopo due anni. Per verificare quando è stato creato il cluster, `kubectl get nodes` usare per visualizzare l' *età* dei pool di nodi.
> 
> Inoltre, è possibile controllare la data di scadenza del certificato del cluster. Ad esempio, il comando seguente Visualizza i dettagli del certificato per il cluster *myAKSCluster* .
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d > my-cert.crt
> openssl x509 -in my-cert.crt -text
> ```

## <a name="rotate-your-cluster-certificates"></a>Ruotare i certificati del cluster

> [!WARNING]
> La rotazione dei certificati `az aks rotate-certs` con può causare fino a 30 minuti di inattività per il cluster AKS.

Usare [AZ AKS Get-credentials][az-aks-get-credentials] per accedere al cluster AKS. Questo comando consente inoltre di scaricare e configurare `kubectl` il certificato client nel computer locale.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Usare `az aks rotate-certs` per ruotare tutti i certificati, CA e SAS nel cluster.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Il completamento dell' `az aks rotate-certs` operazione potrebbe richiedere fino a 30 minuti. Se il comando ha esito negativo prima `az aks show` del completamento, usare per verificare che lo stato del cluster sia la *rotazione del certificato*. Se il cluster si trova in uno stato di errore `az aks rotate-certs` , eseguire di nuovo per ruotare nuovamente i certificati.

Verificare che i certificati precedenti non siano più validi eseguendo un `kubectl` comando. Poiché non sono stati aggiornati i certificati utilizzati da `kubectl`, verrà visualizzato un errore.  Ad esempio:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Aggiornare il certificato utilizzato da `kubectl` eseguendo `az aks get-credentials`.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Verificare che i certificati siano stati aggiornati eseguendo un `kubectl` comando che ora avrà esito positivo. Ad esempio:

```console
kubectl get no
```

> [!NOTE]
> Se si dispone di servizi eseguiti su AKS, ad esempio [Azure Dev Spaces][dev-spaces], potrebbe essere necessario [aggiornare anche i certificati correlati a tali servizi][dev-spaces-rotate] .

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come ruotare automaticamente i certificati, le autorità di certificazione e le associazioni di sicurezza del cluster. Per altre informazioni sulle procedure consigliate per la sicurezza di AKS, vedere procedure consigliate [per la sicurezza e gli aggiornamenti del cluster in Azure Kubernetes Service (AKS)][aks-best-practices-security-upgrades] .


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
