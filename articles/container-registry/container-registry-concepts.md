---
title: About repositories & images
description: Introduction to key concepts of Azure container registries, repositories, and container images.
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: 9de0c344b226a0b13e76c7f02977ba3c91ba2d2a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455284"
---
# <a name="about-registries-repositories-and-images"></a>About registries, repositories, and images

This article introduces the key concepts of container registries, repositories, and container images and related artifacts. 

## <a name="registry"></a>Registro

Un *registro* contenitori è un servizio che archivia e distribuisce immagini del contenitore. Docker Hub is a public container registry that supports the open source community and serves as a general catalog of images. Azure Container Registry provides users with direct control of their images, with integrated authentication, [geo-replication](container-registry-geo-replication.md) supporting global distribution and reliability for network-close deployments, [virtual network and firewall configuration](container-registry-vnet.md), [tag locking](container-registry-image-lock.md), and many other enhanced features. 

In addition to Docker container images, Azure Container Registry supports related [content artifacts](container-registry-image-formats.md) including Open Container Initiative (OCI) image formats.

## <a name="content-addressable-elements-of-an-artifact"></a>Content addressable elements of an artifact

The address of an artifact in an Azure container registry includes the following elements. 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** - The fully qualified name of the registry host. The registry host in an Azure container registry is in the format *myregistry*.azurecr.io (all lowercase). You must specify the loginUrl when using Docker or other client tools to pull or push artifacts to an Azure container registry. 
* **namespace** - Slash-delimited logical grouping of related images or artifacts - for example, for a workgroup or app
* **artifact** - The name of a repository for a particular image or artifact
* **tag** - A specific version of an image or artifact stored in a repository


For example, the full name of an image in an Azure container registry might look like:

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

See the following sections for details about these elements.

## <a name="repository-name"></a>Nome del repository

Container registries manage *repositories*, collections of container images or other artifacts with the same name, but different tags. Ad esempio, le tre immagini seguenti si trovano nel repository "acr-helloworld":

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

I nomi dei repository possono anche includere [spazi dei nomi](container-registry-best-practices.md#repository-namespaces), Namespaces allow you to group images using forward slash-delimited repository names, for example:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="image"></a>Immagine

A container image or other artifact within a registry is associated with one or more tags, has one or more layers, and is identified by a manifest. Understanding how these components relate to each other can help you manage your registry effectively.

### <a name="tag"></a>Tag

The *tag* for an image or other artifact specifies its version. A single artifact within a repository can be assigned one or many tags, and may also be "untagged." That is, you can delete all tags from an image, while the image's data (its layers) remain in the registry.

Il nome di un'immagine è definito dal repository (o da repository e spazio dei nomi) e da un tag. È possibile eseguire il push e il pull di un'immagine specificandone il nome nella relativa operazione.

How you tag container images is guided by your scenarios to develop or deploy them. For example, stable tags are recommended for maintaining your base images, and unique tags for deploying images. For more information, see [Recommendations for tagging and versioning container images](container-registry-image-tag-version.md).

### <a name="layer"></a>Livello

Container images are made up of one or more *layers*, each corresponding to a line in the Dockerfile that defines the image. Le immagini in un registro condividono i livelli comuni, aumentando così l'efficienza di archiviazione. Ad esempio, numerose immagini in repository diversi potrebbero condividere lo stesso livello di base Alpine Linux, ma ne verrà archiviata una sola copia nel registro.

La condivisione dei livelli ne ottimizza anche la distribuzione ai nodi, in quanto più immagini condivideranno i livelli comuni. Ad esempio, se un'immagine già presente in un nodo include il livello Alpine Linux come base, il pull successivo di un'immagine diversa che fa riferimento al medesimo livello non trasferirà nuovamente il livello al nodo, ma farà invece riferimento a quello già presente.

To provide secure isolation and protection from potential layer manipulation, layers are not shared across registries.

### <a name="manifest"></a>Manifesto

Each container image or artifact pushed to a container registry is associated with a *manifest*. generato dal registro quando l'immagine viene inserita. Il manifesto identifica in modo univoco l'immagine e ne specifica i livelli. You can list the manifests for a repository with the Azure CLI command [az acr repository show-manifests][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

For example, list the manifests for the "acr-helloworld" repository:

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Hash di manifesto

I manifesti sono identificati da un hash SHA-256 univoco, l'*hash di manifesto*. Each image or artifact--whether tagged or not--is identified by its digest. Il valore dell'hash è univoco anche se i dati dei livelli dell'immagine sono identici a quelli di un'altra. Questo meccanismo consente quindi di eseguire ripetutamente il push di immagini con tag identici in un registro. Ad esempio, è possibile eseguire più volte il push di `myimage:latest` nel registro senza errori poiché ogni immagine viene identificata dal relativo hash univoco.

È possibile eseguire il pull di un'immagine da un registro specificandone l'hash nell'operazione. Alcuni sistemi sono configurati per eseguire il pull tramite hash perché in questo modo viene garantita la versione dell'immagine in questione, anche se in seguito viene eseguito il push nel registro di un'immagine con gli stessi tag.

For example, pull an image from the "acr-helloworld" repository by manifest digest:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Se si esegue ripetutamente il push di immagini modificate con tag identici, è possibile che vengano create immagini orfane, ovvero prive di tag, che occupano comunque spazio nel registro. Le immagini senza tag non vengono visualizzate nell'interfaccia della riga di comando o nel portale di Azure quando si elencano o si visualizzano le immagini in base ai tag. Tuttavia, i loro livelli sono comunque presenti e occupano spazio nel registro. Deleting an untagged image frees registry space when the manifest is the only one, or the last one, pointing to a particular layer. For information about freeing space used by untagged images, see [Delete container images in Azure Container Registry](container-registry-delete.md).

## <a name="next-steps"></a>Passaggi successivi

Learn more about [image storage](container-registry-storage.md) and [supported content formats](container-registry-image-formats.md) in Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


