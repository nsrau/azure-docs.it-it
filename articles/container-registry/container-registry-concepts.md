---
title: Informazioni sui repository & le immagini
description: Introduzione ai concetti chiave di registri contenitori, repository e immagini di contenitori di Azure.Introduction to key concepts of Azure container registries, repositories, and container images.
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: ea6e2577d3eee91626dd613617a0b79e4ff3d6a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247059"
---
# <a name="about-registries-repositories-and-images"></a>Informazioni su registri, archivi e immagini

In questo articolo vengono presentati i concetti chiave di registri contenitore, repository e immagini di contenitori e gli elementi correlati. 

## <a name="registry"></a>Registro

Un *registro* contenitori è un servizio che archivia e distribuisce immagini del contenitore. Docker Hub è un registro dei contenitori pubblici che supporta la comunità open source e funge da catalogo generale di immagini. Il Registro di sistema contenitore di Azure offre agli utenti il controllo diretto delle immagini, con autenticazione integrata, [replica geografica](container-registry-geo-replication.md) che supporta la distribuzione e l'affidabilità globali per le distribuzioni di chiusura della rete, la configurazione della rete virtuale e del [firewall,](container-registry-vnet.md)il blocco dei [tag](container-registry-image-lock.md)e molte altre funzionalità avanzate. 

Oltre alle immagini del contenitore Docker, il Registro di sistema contenitore di Azure supporta [gli elementi](container-registry-image-formats.md) di contenuto correlati, inclusi i formati di immagine OCI (Open Container Initiative).

## <a name="content-addressable-elements-of-an-artifact"></a>Elementi indirizzabili al contenuto di un elemento

L'indirizzo di un elemento in un registro contenitori di Azure include gli elementi seguenti. 

`[loginUrl]/[namespace]/[artifact:][tag]`

* **loginUrl** - Il nome completo dell'host del Registro di sistema. L'host del Registro di sistema in un registro contenitori di Azure è nel formato *myregistry*.azurecr.io (tutte minuscole). È necessario specificare loginUrl quando si usa Docker o altri strumenti client per eseguire il pull o il push di elementi in un Registro di sistema del contenitore di Azure.You must specify the loginUrl when using Docker or other client tools to pull or push artifacts to an Azure container registry. 
* **namespace** - Raggruppamento logico delimitato da barre di immagini o elementi correlati, ad esempio per un gruppo di lavoro o un'app
* **artefatto** - Il nome di un repository per una particolare immagine o elemento
* **tag** - Una versione specifica di un'immagine o di un elemento memorizzato in un repository


Ad esempio, il nome completo di un'immagine in un registro contenitore di Azure potrebbe essere simile al seguente:For example, the full name of an image in an Azure container registry might look like:

*myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2*

Vedere le sezioni seguenti per informazioni dettagliate su questi elementi.

## <a name="repository-name"></a>Nome del repository

I registri contenitori gestiscono *archivi,* raccolte di immagini contenitore o altri elementi con lo stesso nome, ma tag diversi. Ad esempio, le tre immagini seguenti si trovano nel repository "acr-helloworld":


- *acr-helloworld: più recente*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

I nomi dei repository possono anche includere [spazi dei nomi](container-registry-best-practices.md#repository-namespaces), Gli spazi dei nomi consentono di raggruppare le immagini utilizzando nomi di repository delimitati da barra, ad esempio:Namespaces allow you to group images using slash-delimited repository names, for example:

- *marketing/campagna10-18/web:v2*
- *marketing/campaign10-18/api:v3*
- *marketing/campaign10-18/email-sender:v2*
- *rendimento/invio di prodotti/web:20180604*
- *prodotto-rendimento/legacy-integrator:20180715*

## <a name="image"></a>Immagine

Un'immagine contenitore o un altro elemento all'interno di un registro è associato a uno o più tag, ha uno o più livelli ed è identificato da un manifesto. Comprendere in che modo questi componenti sono correlati tra loro può aiutare a gestire il Registro di sistema in modo efficace.

### <a name="tag"></a>Tag

Il *tag* per un'immagine o un altro elemento ne specifica la versione. A un singolo elemento all'interno di un repository possono essere assegnati uno o più tag e può anche essere "senza tag". Ovvero, è possibile eliminare tutti i tag da un'immagine, mentre i dati dell'immagine (i relativi livelli) rimangono nel Registro di sistema.

Il nome di un'immagine è definito dal repository (o da repository e spazio dei nomi) e da un tag. È possibile eseguire il push e il pull di un'immagine specificandone il nome nella relativa operazione.

La modalità di tag delle immagini del contenitore è guidata dagli scenari per svilupparle o distribuirle. Ad esempio, i tag stable sono consigliati per la gestione delle immagini di base e tag univoci per la distribuzione delle immagini. Per ulteriori informazioni, consultate [Consigli per l'assegnazione di tag e il controllo delle versioni delle immagini dei contenitori.](container-registry-image-tag-version.md)

### <a name="layer"></a>Livello

Le immagini contenitore sono costituite da uno o più *livelli,* ognuno dei quali corrisponde a una riga nel file Docker che definisce l'immagine. Le immagini in un registro condividono i livelli comuni, aumentando così l'efficienza di archiviazione. Ad esempio, numerose immagini in repository diversi potrebbero condividere lo stesso livello di base Alpine Linux, ma ne verrà archiviata una sola copia nel registro.

La condivisione dei livelli ne ottimizza anche la distribuzione ai nodi, in quanto più immagini condivideranno i livelli comuni. Ad esempio, se un'immagine già presente in un nodo include il livello Alpine Linux come base, il pull successivo di un'immagine diversa che fa riferimento al medesimo livello non trasferirà nuovamente il livello al nodo, ma farà invece riferimento a quello già presente.

Per garantire l'isolamento sicuro e la protezione da potenziali manipolazioni dei layer, i layer non vengono condivisi tra i registri.

### <a name="manifest"></a>manifesto

Ogni immagine contenitore o elemento inserito in un registro contenitori è associato a un *manifesto*. generato dal registro quando l'immagine viene inserita. Il manifesto identifica in modo univoco l'immagine e ne specifica i livelli. È possibile elencare i manifesti per un repository con il comando dell'interfaccia della riga di comando di Azure [az acr repository show-manifests][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Ad esempio, elencare i manifesti per il repository "acr-helloworld":

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
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

I manifesti sono identificati da un hash SHA-256 univoco, l'*hash di manifesto*. Ogni immagine o elemento, indipendentemente dal fatto che sia contrassegnato o meno, viene identificato dal digest. Il valore dell'hash è univoco anche se i dati dei livelli dell'immagine sono identici a quelli di un'altra. Questo meccanismo consente quindi di eseguire ripetutamente il push di immagini con tag identici in un registro. Ad esempio, è possibile eseguire più volte il push di `myimage:latest` nel registro senza errori poiché ogni immagine viene identificata dal relativo hash univoco.

È possibile eseguire il pull di un'immagine da un registro specificandone l'hash nell'operazione. Alcuni sistemi sono configurati per eseguire il pull tramite hash perché in questo modo viene garantita la versione dell'immagine in questione, anche se in seguito viene eseguito il push nel registro di un'immagine con gli stessi tag.

Ad esempio, estrarre un'immagine dal repository "acr-helloworld" dal digest del manifesto:For example, pull an image from the "acr-helloworld" repository by manifest digest:

`docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`

> [!IMPORTANT]
> Se si esegue ripetutamente il push di immagini modificate con tag identici, è possibile che vengano create immagini orfane, ovvero prive di tag, che occupano comunque spazio nel registro. Le immagini senza tag non vengono visualizzate nell'interfaccia della riga di comando o nel portale di Azure quando si elencano o si visualizzano le immagini in base ai tag. Tuttavia, i loro livelli sono comunque presenti e occupano spazio nel registro. L'eliminazione di un'immagine senza tag libera lo spazio del Registro di sistema quando il manifesto è l'unico o l'ultimo, che punta a un livello specifico. Per informazioni su come liberare spazio usato dalle immagini senza tag, vedere [Eliminare immagini contenitore nel Registro di](container-registry-delete.md)sistema contenitori di Azure.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni [sull'archiviazione delle immagini](container-registry-storage.md) e sui formati di contenuto supportati in Registro di sistema contenitori di Azure.Learn more about image storage and supported content [formats](container-registry-image-formats.md) in Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


