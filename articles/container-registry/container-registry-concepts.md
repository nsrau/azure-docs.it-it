---
title: Informazioni sui repository & immagini
description: Introduzione ai concetti chiave di registri contenitori di Azure, repository e immagini del contenitore.
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: 9de0c344b226a0b13e76c7f02977ba3c91ba2d2a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455284"
---
# <a name="about-registries-repositories-and-images"></a>Informazioni sui registri, i repository e le immagini

Questo articolo presenta i concetti chiave relativi a registri contenitori, repository e immagini del contenitore ed elementi correlati. 

## <a name="registry"></a>Registro

Un *registro* contenitori è un servizio che archivia e distribuisce immagini del contenitore. Docker Hub è un registro contenitori pubblico che supporta la community open source e funge da catalogo di immagini generale. Azure Container Registry offre agli utenti il controllo diretto delle proprie immagini, con l'autenticazione integrata, la [replica geografica](container-registry-geo-replication.md) che supporta la distribuzione e l'affidabilità globali per le distribuzioni con chiusura in rete, la [configurazione della rete virtuale e del firewall](container-registry-vnet.md), il [blocco dei tag](container-registry-image-lock.md)e molte altre funzionalità avanzate. 

Oltre alle immagini del contenitore Docker, Azure Container Registry supporta gli [artefatti di contenuto](container-registry-image-formats.md) correlati, inclusi i formati di immagine OCI (Open Container Initiative).

## <a name="content-addressable-elements-of-an-artifact"></a>Elementi indirizzabili del contenuto di un artefatto

L'indirizzo di un artefatto in un registro contenitori di Azure include gli elementi seguenti. 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** : nome completo dell'host del registro di sistema. L'host del registro di sistema in un registro contenitori di Azure è nel formato *Registro*di sistema. azurecr.io (tutti minuscole). È necessario specificare loginUrl quando si usa Docker o altri strumenti client per eseguire il pull o il push di elementi in un registro contenitori di Azure. 
* **spazio dei nomi** : raggruppamento logico delimitato da barre di immagini o artefatti correlati, ad esempio per un gruppo di lavoro o un'app
* **artefatto** : il nome di un repository per un'immagine o un artefatto specifico
* **tag** : una versione specifica di un'immagine o di un artefatto archiviato in un repository


Ad esempio, il nome completo di un'immagine in un registro contenitori di Azure potrebbe essere simile al seguente:

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

Per informazioni dettagliate su questi elementi, vedere le sezioni seguenti.

## <a name="repository-name"></a>Nome del repository

I registri contenitori gestiscono *repository*, raccolte di immagini del contenitore o altri elementi con lo stesso nome, ma tag diversi. Ad esempio, le tre immagini seguenti si trovano nel repository "acr-helloworld":

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

I nomi dei repository possono anche includere [spazi dei nomi](container-registry-best-practices.md#repository-namespaces), Gli spazi dei nomi consentono di raggruppare le immagini utilizzando nomi di repository delimitati da barre, ad esempio:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="image"></a>Image

Un'immagine del contenitore o un altro artefatto all'interno di un registro è associato a uno o più tag, ha uno o più livelli ed è identificato da un manifesto. Comprendere in che modo questi componenti sono correlati tra loro possono aiutare a gestire il registro di sistema in modo efficace.

### <a name="tag"></a>Tag

Il *tag* per un'immagine o un altro artefatto ne specifica la versione. Un singolo artefatto all'interno di un repository può essere assegnato a uno o più tag e può anche essere "senza tag". Ovvero, è possibile eliminare tutti i tag da un'immagine, mentre i dati dell'immagine (i relativi livelli) rimangono nel registro di sistema.

Il nome di un'immagine è definito dal repository (o da repository e spazio dei nomi) e da un tag. È possibile eseguire il push e il pull di un'immagine specificandone il nome nella relativa operazione.

Il modo in cui vengono contrassegnate le immagini del contenitore è guidato dagli scenari per lo sviluppo o la distribuzione. Ad esempio, sono consigliati Tag stabili per la manutenzione delle immagini di base e tag univoci per la distribuzione di immagini. Per altre informazioni, vedere [consigli per l'assegnazione di tag e il controllo delle versioni delle immagini del contenitore](container-registry-image-tag-version.md).

### <a name="layer"></a>Livello

Le immagini del contenitore sono costituite da uno o più *livelli*, ognuno corrispondente a una riga nel Dockerfile che definisce l'immagine. Le immagini in un registro condividono i livelli comuni, aumentando così l'efficienza di archiviazione. Ad esempio, numerose immagini in repository diversi potrebbero condividere lo stesso livello di base Alpine Linux, ma ne verrà archiviata una sola copia nel registro.

La condivisione dei livelli ne ottimizza anche la distribuzione ai nodi, in quanto più immagini condivideranno i livelli comuni. Ad esempio, se un'immagine già presente in un nodo include il livello Alpine Linux come base, il pull successivo di un'immagine diversa che fa riferimento al medesimo livello non trasferirà nuovamente il livello al nodo, ma farà invece riferimento a quello già presente.

Per garantire l'isolamento e la protezione da potenziali manipolazioni dei livelli, i livelli non vengono condivisi tra i registri.

### <a name="manifest"></a>Manifesto

Ogni immagine del contenitore o artefatto di cui è stato eseguito il push in un registro contenitori è associato a un *manifesto*. generato dal registro quando l'immagine viene inserita. Il manifesto identifica in modo univoco l'immagine e ne specifica i livelli. È possibile elencare i manifesti per un repository con il comando dell'interfaccia della riga di comando di Azure [AZ ACR repository Show-manifests][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Ad esempio, elencare i manifesti per il repository "ACR-HelloWorld":

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

I manifesti sono identificati da un hash SHA-256 univoco, l'*hash di manifesto*. Ogni immagine o artefatto, indipendentemente dal fatto che sia contrassegnato o meno, viene identificato dal digest. Il valore dell'hash è univoco anche se i dati dei livelli dell'immagine sono identici a quelli di un'altra. Questo meccanismo consente quindi di eseguire ripetutamente il push di immagini con tag identici in un registro. Ad esempio, è possibile eseguire più volte il push di `myimage:latest` nel registro senza errori poiché ogni immagine viene identificata dal relativo hash univoco.

È possibile eseguire il pull di un'immagine da un registro specificandone l'hash nell'operazione. Alcuni sistemi sono configurati per eseguire il pull tramite hash perché in questo modo viene garantita la versione dell'immagine in questione, anche se in seguito viene eseguito il push nel registro di un'immagine con gli stessi tag.

Ad esempio, effettuare il pull di un'immagine dal repository "ACR-HelloWorld" dal digest del manifesto:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Se si esegue ripetutamente il push di immagini modificate con tag identici, è possibile che vengano create immagini orfane, ovvero prive di tag, che occupano comunque spazio nel registro. Le immagini senza tag non vengono visualizzate nell'interfaccia della riga di comando o nel portale di Azure quando si elencano o si visualizzano le immagini in base ai tag. Tuttavia, i loro livelli sono comunque presenti e occupano spazio nel registro. L'eliminazione di un'immagine senza tag libera lo spazio del registro di sistema quando il manifesto è l'unico, o l'ultimo, che punta a un livello specifico. Per informazioni su come liberare spazio usato dalle immagini non contrassegnate, vedere [eliminare immagini del contenitore in Azure container Registry](container-registry-delete.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull' [archiviazione immagini](container-registry-storage.md) e i [formati di contenuto supportati](container-registry-image-formats.md) in Azure container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


