---
title: Ruotare i certificati nel servizio Azure Kubernetes (AKS)Rotate certificates in Azure Kubernetes Service (AKS)
description: Informazioni su come ruotare i certificati in un cluster di servizio Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: 00dcef4ae0f04fc7f550859238ae8c7e1ad19384
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549061"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Ruotare i certificati nel servizio Azure Kubernetes (AKS)Rotate certificates in Azure Kubernetes Service (AKS)

Il servizio Azure Kubernetes (AKS) usa i certificati per l'autenticazione con molti dei relativi componenti. Periodicamente, potrebbe essere necessario ruotare tali certificati per motivi di sicurezza o criteri. Ad esempio, è possibile disporre di un criterio per ruotare tutti i certificati ogni 90 giorni.

In questo articolo viene illustrato come ruotare i certificati nel cluster AKS.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.77 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][azure-cli-install]you need to install or upgrade, see Install Azure CLI.

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>Certificati AKS, autorità di certificazione e account di servizio

AKS genera e utilizza i certificati, le Autorità di certificazione e gli account di servizio seguenti:

* Il server API AKS crea un'autorità di certificazione (CA) denominata CA cluster.
* Il server API dispone di una CA cluster, che firma i certificati per la comunicazione unidirezionale dal server API a kubelets.
* Ogni kubelet crea anche una richiesta di firma del certificato (CSR), firmata dalla CA del cluster, per la comunicazione dal kubelet al server API.
* L'archivio dei valori della chiave e così via dispone di un certificato firmato dalla CA del cluster per la comunicazione dal server API e così via.
* L'archivio di valori di chiave e così via crea una CA che firma i certificati per autenticare e autorizzare la replica dei dati tra le repliche etcd nel cluster AKS.
* L'aggregatore di API utilizza la CA del cluster per emettere certificati per la comunicazione con altre API. L'aggregatore API può anche disporre di una propria CA per l'emissione di tali certificati, ma attualmente utilizza la CA cluster.
* Ogni nodo utilizza un token dell'account di servizio (SA), firmato dalla CA del cluster.
* Il `kubectl` client dispone di un certificato per la comunicazione con il cluster AKS.

> [!NOTE]
> I cluster AKS creati prima di marzo 2019 dispongono di certificati che scadono dopo due anni. Qualsiasi cluster creato dopo marzo 2019 o qualsiasi cluster con i certificati ruotati dispongono di certificati CA cluster che scadono dopo 30 anni. Tutti gli altri certificati scadono dopo due anni. Per verificare quando è `kubectl get nodes` stato creato il cluster, utilizzare per visualizzare l'Età dei pool di nodi. *Age*
> 
> Inoltre, è possibile controllare la data di scadenza del certificato del cluster. Ad esempio, il comando seguente visualizza i dettagli del certificato per il cluster *myAKSCluster.*
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d > my-cert.crt
> openssl x509 -in my-cert.crt -text
> ```

## <a name="rotate-your-cluster-certificates"></a>Ruotare i certificati del cluster

> [!WARNING]
> La rotazione dei `az aks rotate-certs` certificati tramite può causare fino a 30 minuti di inattività per il cluster AKS.

Usare [az aks get-credentials][az-aks-get-credentials] per accedere al cluster AKS. Questo comando scarica e `kubectl` configura anche il certificato client nel computer locale.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Utilizzare `az aks rotate-certs` per ruotare tutti i certificati, le CA e le CA del cluster.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Il completamento potrebbe richiedere `az aks rotate-certs` fino a 30 minuti. Se il comando ha esito negativo prima del completamento, utilizzare `az aks show` per verificare che lo stato del cluster sia Rotazione *certificato*. Se il cluster è in stato `az aks rotate-certs` di errore, eseguire nuovamente per ruotare nuovamente i certificati.

Verificare che i certificati precedenti non `kubectl` siano più validi eseguendo un comando. Poiché non sono stati `kubectl`aggiornati i certificati utilizzati da , verrà visualizzato un errore.  Ad esempio:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Aggiornare il `kubectl` certificato `az aks get-credentials`utilizzato da eseguendo .

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Verificare che i certificati `kubectl` siano stati aggiornati eseguendo un comando che ora avrà esito positivo. Ad esempio:

```console
kubectl get no
```

> [!NOTE]
> Se si dispone di servizi che vengono eseguiti in cima ad AKS, ad esempio [Azure Dev Spaces,][dev-spaces]potrebbe essere necessario aggiornare anche i [certificati correlati a tali servizi.][dev-spaces-rotate]

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come ruotare automaticamente i certificati, le CA e le CA del cluster. Per altre informazioni sulle procedure consigliate per la sicurezza di AKS, vedere Procedure consigliate per la sicurezza e gli aggiornamenti del [cluster.][aks-best-practices-security-upgrades]


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
