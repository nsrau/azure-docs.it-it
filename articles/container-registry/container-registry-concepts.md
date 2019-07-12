---
title: Su repository e immagini nel registro contenitori di Azure
description: Introduzione ai concetti chiave di registri contenitori di Azure, i repository e le immagini del contenitore.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/01/2019
ms.author: danlep
ms.openlocfilehash: a14f0a2a86c5e4922fcddf3c92d48c6dfb1497a3
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800328"
---
# <a name="about-registries-repositories-and-images"></a>Sui registri e repository di immagini

Questo articolo illustra i concetti chiave di registri contenitori, i repository e le immagini del contenitore e gli elementi correlati. 

## <a name="registry"></a>Registro

Un *registro* contenitori è un servizio che archivia e distribuisce immagini del contenitore. Docker Hub è un registro contenitori pubblici che supporta la community open source e funge da un catalogo generale delle immagini. Registro contenitori di Azure offre agli utenti con un controllo diretto delle proprie immagini, con l'autenticazione integrata [replica geografica](container-registry-geo-replication.md) supporto per le distribuzioni con prossimità di rete, distribuzione globale e l'affidabilità [ configurazione di firewall e della rete virtuale](container-registry-vnet.md), [tag blocco](container-registry-image-lock.md), e molte altre funzionalità avanzate. 

Oltre alle immagini del contenitore Docker, registro contenitori di Azure supporta correlati [degli elementi di contenuto](container-registry-image-formats.md) tra formati di immagine iniziativa OCI (Open Container Initiative).

## <a name="content-addressable-elements-of-an-artifact"></a>Elementi indirizzabili di contenuto di un elemento

L'indirizzo di un elemento in un registro contenitori di Azure include gli elementi seguenti. 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** -specificare il nome completo dell'host del Registro di sistema. L'host del Registro di sistema in Registro contenitori di Azure è nel formato *myregistry*. azurecr.io (tutte lettere minuscole). È necessario specificare il loginUrl quando si usa Docker o altri strumenti client per gli artefatti di pull o push a un'istanza di registro contenitori di Azure. 
* **spazio dei nomi** - delimitato da barra raggruppamento logico di immagini correlate o degli artefatti - ad esempio, per un gruppo di lavoro o un'app
* **elemento** -il nome di un repository per un particolare immagine o un artefatto
* **tag** -una versione specifica di un'immagine o un elemento archiviato in un repository


Ad esempio, il nome completo di un'immagine in Registro contenitori di Azure potrebbe essere simile:

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

Vedere le sezioni seguenti per informazioni dettagliate su questi elementi.

## <a name="repository-name"></a>Nome del repository

Gestire i registri per contenitori *repository*, raccolte di immagini del contenitore o altri elementi con lo stesso nome ma con tag diversi. Ad esempio, le tre immagini seguenti si trovano nel repository "acr-helloworld":

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

I nomi dei repository possono anche includere [spazi dei nomi](container-registry-best-practices.md#repository-namespaces), Gli spazi dei nomi consentono di raggruppare le immagini usando nomi di inoltro repository delimitato da barra, ad esempio:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="image"></a>Image

Un'immagine del contenitore o un altro elemento all'interno di un registro di sistema è associato a uno o più tag, ha uno o più livelli e viene identificato da un manifesto. Comprendere come questi componenti interagiscono tra di loro può aiutarti a gestire il Registro di sistema in modo efficace.

### <a name="tag"></a>Tag

Il *tag* per un'immagine o un altro elemento specifica la versione. Un singolo elemento all'interno di un repository può essere assegnato uno o più tag e potrebbe anche essere "tag". Vale a dire, è possibile eliminare tutti i tag da un'immagine, anche se i dati dell'immagine (relativi livelli) rimangono nel Registro di sistema.

Il nome di un'immagine è definito dal repository (o da repository e spazio dei nomi) e da un tag. È possibile eseguire il push e il pull di un'immagine specificandone il nome nella relativa operazione.

Come è contrassegnare le immagini del contenitore è condotta dagli scenari per sviluppare o distribuirli. Ad esempio, tag stabile sono consigliati per la gestione di immagini di base e i tag univoci per la distribuzione di immagini. Per altre informazioni, vedere [raccomandazioni per l'assegnazione di tag e controllo delle versioni delle immagini del contenitore](container-registry-image-tag-version.md).

### <a name="layer"></a>Livello

Le immagini del contenitore sono costituite da uno o più *livelli*, ognuno corrispondente a una riga del documento Dockerfile che definisce l'immagine. Le immagini in un registro condividono i livelli comuni, aumentando così l'efficienza di archiviazione. Ad esempio, numerose immagini in repository diversi potrebbero condividere lo stesso livello di base Alpine Linux, ma ne verrà archiviata una sola copia nel registro.

La condivisione dei livelli ne ottimizza anche la distribuzione ai nodi, in quanto più immagini condivideranno i livelli comuni. Ad esempio, se un'immagine già presente in un nodo include il livello Alpine Linux come base, il pull successivo di un'immagine diversa che fa riferimento al medesimo livello non trasferirà nuovamente il livello al nodo, ma farà invece riferimento a quello già presente.

Per fornire un isolamento sicuro e la protezione da potenziali manipolazione del livello, i livelli non sono condivise tra i registri.

### <a name="manifest"></a>Manifesto

Ogni immagine del contenitore o dell'elemento inserito in un registro contenitori è associato un *manifesto*. generato dal registro quando l'immagine viene inserita. Il manifesto identifica in modo univoco l'immagine e ne specifica i livelli. È possibile elencare i manifesti per un repository con il comando di Azure CLI [az acr repository show-manifesti][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Ad esempio, il manifesto dell'elenco esegue il digest per il repository "acr-helloworld":

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

I manifesti sono identificati da un hash SHA-256 univoco, l'*hash di manifesto*. Ogni immagine o un artefatto, se contrassegnati o non - è identificato dal relativo digest. Il valore dell'hash è univoco anche se i dati dei livelli dell'immagine sono identici a quelli di un'altra. Questo meccanismo consente quindi di eseguire ripetutamente il push di immagini con tag identici in un registro. Ad esempio, è possibile eseguire più volte il push di `myimage:latest` nel registro senza errori poiché ogni immagine viene identificata dal relativo hash univoco.

È possibile eseguire il pull di un'immagine da un registro specificandone l'hash nell'operazione. Alcuni sistemi sono configurati per eseguire il pull tramite hash perché in questo modo viene garantita la versione dell'immagine in questione, anche se in seguito viene eseguito il push nel registro di un'immagine con gli stessi tag.

Eseguire il pull, ad esempio, un'immagine dal repository "acr-helloworld" dal manifesto del digest:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Se si esegue ripetutamente il push di immagini modificate con tag identici, è possibile che vengano create immagini orfane, ovvero prive di tag, che occupano comunque spazio nel registro. Le immagini senza tag non vengono visualizzate nell'interfaccia della riga di comando o nel portale di Azure quando si elencano o si visualizzano le immagini in base ai tag. Tuttavia, i loro livelli sono comunque presenti e occupano spazio nel registro. Per informazioni su come aumentare lo spazio utilizzato dalle immagini senza tag, vedere [eliminare le immagini del contenitore in Registro contenitori di Azure](container-registry-delete.md).


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [archiviazione di immagini](container-registry-storage.md) e [formati di contenuto supportati](container-registry-image-formats.md) in Registro contenitori di Azure.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


