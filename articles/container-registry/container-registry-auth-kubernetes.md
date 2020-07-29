---
title: Eseguire l'autenticazione dal cluster Kubernetes
description: Informazioni su come fornire un cluster Kubernetes con accesso alle immagini nel registro contenitori di Azure creando un segreto di pull usando un'entità servizio
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 05/28/2020
ms.openlocfilehash: fbf5dfd68b823b600b11cad3643e5d4004b85ff5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84309816"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Eseguire il pull delle immagini da un registro contenitori di Azure a un cluster Kubernetes

È possibile usare un registro contenitori di Azure come origine di immagini del contenitore con qualsiasi cluster Kubernetes, inclusi i cluster Kubernetes "locali", ad esempio [minikube](https://minikube.sigs.k8s.io/) e [Kind](https://kind.sigs.k8s.io/). Questo articolo illustra come creare un segreto pull di Kubernetes basato su un'entità servizio Azure Active Directory. Usare quindi il segreto per estrarre le immagini da un registro contenitori di Azure in una distribuzione di Kubernetes.

> [!TIP]
> Se si usa il servizio gestito di [Azure Kubernetes](../aks/intro-kubernetes.md), è anche possibile [integrare il cluster](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) con un registro contenitori di Azure di destinazione per i pull di immagini. 

Questo articolo presuppone che sia già stato creato un registro contenitori di Azure privato. È anche necessario avere un cluster Kubernetes in esecuzione e accessibile tramite lo `kubectl` strumento da riga di comando.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

Se non si salva o si ricorda la password dell'entità servizio, è possibile reimpostarla con il comando [AZ ad SP Credential Reset][az-ad-sp-credential-reset] :

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

Questo comando restituisce una nuova password valida per l'entità servizio.

## <a name="create-an-image-pull-secret"></a>Creare un segreto di pull immagine

Kubernetes usa un *segreto di pull immagine* per archiviare le informazioni necessarie per l'autenticazione nel registro. Per creare il segreto di pull per un registro contenitori di Azure, è necessario specificare l'ID dell'entità servizio, la password e l'URL del registro di sistema. 

Creare un segreto di pull immagine con il `kubectl` comando seguente:

```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```
dove:

| valore | Descrizione |
| :--- | :--- |
| `secret-name` | Nome del segreto di pull dell'immagine, ad esempio *ACR-Secret* |
| `namespace` | Spazio dei nomi Kubernetes in cui inserire il segreto <br/> Necessaria solo se si desidera inserire il segreto in uno spazio dei nomi diverso dallo spazio dei nomi predefinito |
| `container-registry-name` | Nome del registro contenitori di Azure, ad esempio, *Registro* di sistema<br/><br/>`--docker-server`È il nome completo del server di accesso del registro di sistema  |
| `service-principal-ID` | ID dell'entità servizio che verrà usata da Kubernetes per accedere al registro |
| `service-principal-password` | Password dell'entità servizio |

## <a name="use-the-image-pull-secret"></a>Usa il segreto di pull dell'immagine

Una volta creato il segreto di pull dell'immagine, è possibile usarlo per creare pod e distribuzioni Kubernetes. Consente di specificare il nome del segreto `imagePullSecrets` in nel file di distribuzione. Ad esempio:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: myregistry.azurecr.io/my-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

Nell'esempio precedente, `my-awesome-app:v1` è il nome dell'immagine da estrarre dal registro contenitori di Azure e `acr-secret` è il nome del segreto di pull creato per accedere al registro di sistema. Quando si distribuisce il Pod, Kubernetes esegue automaticamente il pull dell'immagine dal registro di sistema, se non è già presente nel cluster.


## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'uso delle entità servizio e di Azure Container Registry, vedere [autenticazione container Registry di Azure con entità servizio](container-registry-auth-service-principal.md)
* Scopri di più sui segreti di pull delle immagini nella [documentazione di Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset