---
title: Eseguire l'autenticazione dal cluster KubernetesAuthenticate from Kubernetes cluster
description: Informazioni su come fornire a un cluster Kubernetes l'accesso alle immagini nel Registro di sistema del contenitore di Azure creando un segreto pull usando un'entità servizio
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 02/10/2020
ms.openlocfilehash: 0608ca0e0e53acf2f19910a7f1107dacf67d4e61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77154894"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Eseguire il pull di immagini da un registro contenitori di Azure a un cluster KubernetesPull images from an Azure container registry to a Kubernetes cluster

È possibile usare un registro contenitori di Azure come origine delle immagini del contenitore con qualsiasi cluster Kubernetes, inclusi i cluster Kubernetes "locali", ad esempio [minikube](https://minikube.sigs.k8s.io/) e [kind](https://kind.sigs.k8s.io/). Questo articolo illustra come creare un segreto di pull Kubernetes basato su un'entità servizio azure Active Directory.This article shows how to create a Kubernetes pull secret based on an Azure Active Directory service principal. Quindi, usare il segreto per estrarre le immagini da un registro contenitori di Azure in una distribuzione di Kubernetes.Then, use the secret to pull images from an Azure container registry in a Kubernetes deployment.

> [!TIP]
> Se si usa il [servizio Azure Kubernetes](../aks/intro-kubernetes.md)gestito, è anche possibile [integrare il cluster](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) con un registro del contenitore di Azure di destinazione per il pull delle immagini. 

Questo articolo presuppone che sia già stato creato un registro contenitore di Azure privato. È inoltre necessario disporre di un cluster Kubernetes in esecuzione e accessibile tramite lo strumento da `kubectl` riga di comando.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

Se non si salva o si ricorda la password dell'entità servizio, è possibile reimpostarla con il comando az ad sp per la [reimpostazione delle credenziali:][az-ad-sp-credential-reset]

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

Questo comando restituisce una nuova password valida per l'entità servizio.

## <a name="create-an-image-pull-secret"></a>Creare un segreto di pull dell'immagineCreate an image pull secret

Kubernetes utilizza un *segreto* di pull immagine per memorizzare le informazioni necessarie per l'autenticazione al registro. Per creare il segreto di pull per un registro del contenitore di Azure, specificare l'ID dell'entità servizio, la password e l'URL del Registro di sistema. 

Creare un segreto di `kubectl` pull dell'immagine con il comando seguente:Create an image pull secret with the following command:

```console
kubectl create secret docker-registry <secret-name> \
  --namespace <namespace> \
  --docker-server=https://<container-registry-name>.azurecr.io \
  --docker-username=<service-principal-ID> \
  --docker-password=<service-principal-password>
```
dove:

| valore | Descrizione |
| :--- | :--- |
| `secret-name` | Nome del segreto di pull dell'immagine, ad esempio *acr-secret* |
| `namespace` | Kubernetes spazio dei nomi per mettere il segreto in <br/> Necessario solo se si desidera inserire il segreto in uno spazio dei nomi diverso dallo spazio dei nomi predefinito |
| `container-registry-name` | Nome del Registro di sistema del contenitore di AzureName of your Azure container registry |
| `service-principal-ID` | ID dell'entità servizio che verrà utilizzata da Kubernetes per accedere al Registro di sistema |
| `service-principal-password` | Password dell'entità servizioService principal password |

## <a name="use-the-image-pull-secret"></a>Usare il segreto di pull dell'immagineUse the image pull secret

Dopo aver creato il segreto di pull dell'immagine, puoi usarlo per creare pod e distribuzioni Kubernetes. Specificare il nome `imagePullSecrets` del segreto nel file di distribuzione. Ad esempio:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: your-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: your-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

Nell'esempio precedente `your-awesome-app:v1` è il nome dell'immagine da estrarre dal `acr-secret` Registro di sistema del contenitore di Azure ed è il nome del segreto di pull creato per accedere al Registro di sistema. Quando si distribuisce il pod, Kubernetes estrae automaticamente l'immagine dal Registro di sistema, se non è già presente nel cluster.


## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'uso delle entità servizio e del Registro di sistema del contenitore di Azure, vedere Autenticazione del Registro di sistema del contenitore di Azure con entità servizioFor more about working with service principals and Azure Container Registry, see [Azure Container Registry authentication with service principals](container-registry-auth-service-principal.md)
* Ulteriori informazioni sui segreti di image pull sono riportati nella documentazione di [Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset