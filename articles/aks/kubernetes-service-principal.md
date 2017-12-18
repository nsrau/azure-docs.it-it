---
title: "Entità servizio per il cluster Kubernetes di Azure"
description: "Creare e gestire un'entità servizio di Azure Active Directory per un cluster Kubernetes nel servizio contenitore di Azure"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: get-started-article
ms.date: 11/30/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 23d59d37e25775f67d01813bbf53d150f1973622
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="service-principals-with-azure-container-service-aks"></a>Entità servizio con il servizio contenitore di Azure

Un cluster del servizio contenitore di Azure richiede un'[entità servizio di Azure Active Directory][aad-service-principal] per l'interazione con le API di Azure. L'entità servizio è necessaria per la gestione dinamica di risorse quali le [route definite dall'utente][user-defined-routes] e [Azure Load Balancer di livello 4][azure-load-balancer-overview].

Questo articolo descrive diverse opzioni per la configurazione di un'entità servizio per il cluster Kubernetes nel servizio contenitore di Azure.

## <a name="before-you-begin"></a>Prima di iniziare


Per creare un'entità servizio di Azure AD, sono necessarie le autorizzazioni per registrare un'applicazione con il tenant di Azure AD e per assegnare l'applicazione a un ruolo nella sottoscrizione. In mancanza di queste autorizzazioni, potrebbe essere necessario chiedere all'amministratore di Azure AD o della sottoscrizione di assegnarle oppure di creare in anticipo un'entità servizio per il cluster Kubernetes.

È anche necessario che sia installata e configurata l'interfaccia della riga di comando di Azure versione 2.0.21 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="create-sp-with-aks-cluster"></a>Creare l'entità servizio con il cluster del servizio contenitore di Azure

Quando si distribuisce un cluster del servizio contenitore di Azure con il comando `az aks create`, è possibile scegliere di generare automaticamente un'entità servizio.

Nell'esempio seguente viene creato un cluster del servizio contenitore di Azure e poiché non è specificata un'entità servizio, ne viene creata una per il cluster. Per completare questa operazione, l'account deve avere i diritti appropriati per la creazione di un'entità servizio.

```azurecli
az aks create --name myK8SCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>Usare un'entità servizio esistente

È possibile usare un'entità servizio di Azure AD esistente oppure crearne in anticipo una da usare con un cluster del servizio contenitore di Azure. Questa operazione è utile quando si distribuisce un cluster dal portale di Azure, in cui è necessario fornire informazioni sull'entità servizio. Quando si usa un'entità servizio esistente, il segreto client deve essere configurato come password.

## <a name="pre-create-a-new-sp"></a>Creare in anticipo una nuova entità servizio

Per creare l'entità servizio con l'interfaccia della riga di comando di Azure, usare il comando [az ad sp create-for-rbac][az-ad-sp-create].

```azurecli
az ad sp create-for-rbac --skip-assignment
```

L'output è simile al seguente. Prendere nota dei valori di `appId` e `password`. Questi valori vengono usati durante la creazione di un cluster del servizio contenitore di Azure.

```json
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>Usare un'entità servizio esistente

Quando si usa un'entità servizio già creata, specificare `appId` e `password` come valori di argomento nel comando `az aks create`.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myK8SCluster --service-principal <appId> --client-secret <password>
```

Se si distribuisce un cluster del servizio contenitore di Azure tramite il portale di Azure, immettere il valore di `appId` nel campo **Service principal client ID** (ID client dell'entità servizio) e il valore di `password` nel campo **Service principal client secret** (Segreto client dell'entità servizio) nel modulo di configurazione del cluster del servizio contenitore di Azure.

![Immagine del passaggio ad Azure Vote](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>Considerazioni aggiuntive

Quando si usano entità servizio del servizio contenitore di Azure e di Azure AD, ricordare le considerazioni seguenti.

* L'entità servizio per Kubernetes fa parte della configurazione del cluster. Non usare tuttavia l'identità per distribuire il cluster.
* Ogni entità servizio è associata a un'applicazione Azure AD. L'entità servizio per un cluster Kubernetes può essere associata a qualsiasi nome applicazione Azure AD valido, ad esempio `https://www.contoso.org/example`. L'URL per l'applicazione non deve essere necessariamente un endpoint reale.
* Quando si specifica l'**ID client** dell'entità servizio, è possibile usare il valore di `appId`, come illustrato in questo articolo, o il valore `name` corrispondente dell'entità servizio, ad esempio `https://www.contoso.org/example`.
* Nelle macchine virtuali master e del nodo nel cluster Kubernetes le credenziali dell'entità servizio sono archiviate nel file `/etc/kubernetes/azure.json`.
* Quando si usa il comando `az aks create` per generare automaticamente l'entità servizio, le credenziali dell'entità servizio vengono scritte nel file `~/.azure/acsServicePrincipal.json` nel computer usato per eseguire il comando.
* Quando si usa il comando `az aks create` per generare automaticamente l'entità servizio, questa può anche eseguire l'autenticazione con un [registro contenitori di Azure][acr-into] creato nella stessa sottoscrizione.
* Quando si elimina un cluster del servizio contenitore di Azure creato da `az aks create`, l'entità servizio creata automaticamente non verrà eliminata. È possibile usare `az ad sp delete --id $clientID` per eliminarla.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle entità servizio di Azure Active Directory, vedere la documentazione delle applicazioni di Azure AD.

> [!div class="nextstepaction"]
> [Oggetti applicazione e oggetti entità servizio][service-principal]

<!-- LINKS - internal -->
[aad-service-principal]: ../active-directory/develop/active-directory-application-objects.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]: ../active-directory/develop/active-directory-application-objects.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md