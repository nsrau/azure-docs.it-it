---
title: Eliminare le risorse immagine in Registro Azure Container
description: Informazioni dettagliate su come gestire in modo efficace le dimensioni del registro eliminando i dati di immagini del contenitore.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 04/04/2019
ms.author: danlep
ms.openlocfilehash: 1e496002c869c5d2c072773d37ed5fd5d4a5841e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60430801"
---
# <a name="delete-container-images-in-azure-container-registry"></a>Eliminare le immagini del contenitore in Registro Azure Container

Per mantenere le dimensioni del registro contenitori di Azure, è consigliabile eliminare periodicamente i dati di immagini non aggiornati. Alcune immagini di contenitori distribuite in produzione potrebbero richiedere un'archiviazione a lungo termine, mentre altre possono solitamente essere eliminate prima. Ad esempio, in uno scenario di compilazione e test automatici, il registro può riempirsi velocemente di immagini che non verranno mai distribuite, pertanto potrà essere svuotato subito dopo aver compilato la build ed effettuato correttamente i test.

Poiché è possibile eliminare i dati di immagini in modi diversi, è importante comprendere l'impatto di ciascun tipo di eliminazione sull'utilizzo dello spazio di archiviazione. Prima di tutto, l'articolo descrive i componenti di un registro Docker e le immagini del contenitore, quindi illustra vari modi per eliminare i dati di immagini. Vengono forniti esempi di script per automatizzare le operazioni di eliminazione.

## <a name="registry"></a>Registro

Un *registro* contenitori è un servizio che archivia e distribuisce immagini del contenitore. Docker Hub è un registro contenitori di Docker pubblico, mentre Registro Azure Container fornisce registri contenitori di Docker privati in Azure.

## <a name="repository"></a>Repository

I registri contenitori gestiscono *repository*, ovvero raccolte di immagini del contenitore con lo stesso nome ma tag differenti. Ad esempio, le tre immagini seguenti si trovano nel repository "acr-helloworld":

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

## <a name="components-of-an-image"></a>Componenti di un immagine

Un'immagine del contenitore all'interno di un registro è associata a uno o più tag, ha uno o più livelli e viene identificata da un manifesto. Comprendere l'interazione tra questi elementi può aiutare a individuare il metodo migliore per liberare spazio nel registro.

### <a name="tag"></a>Tag

Il *tag* di un'immagine ne specifica la versione. A una singola immagine all'interno di un repository possono essere assegnati uno o più tag, che possono anche essere eliminati. Vale a dire, è possibile eliminare tutti i tag da un'immagine, anche se i dati dell'immagine (relativi livelli) rimangono nel Registro di sistema.

Il nome di un'immagine è definito dal repository (o da repository e spazio dei nomi) e da un tag. È possibile eseguire il push e il pull di un'immagine specificandone il nome nella relativa operazione.

In un registro privato, ad esempio il Registro Azure Container, il nome dell'immagine include anche il nome completo dell'host del registro. L'host del Registro di sistema per le immagini nel registro contenitori di AZURE è nel formato *acrname.azurecr.io* (tutte lettere minuscole). Ad esempio, il nome completo dell'immagine del primo nello spazio dei nomi "marketing" nella sezione precedente sarebbe:

```
myregistry.azurecr.io/marketing/campaign10-18/web:v2
```

Per una discussione sulle procedure consigliate per il tag di immagine, vedere il post di blog [Docker Tagging: Best practices for tagging and versioning docker images][tagging-best-practices] (Assegnazione tag di Docker: procedure consigliate per l'assegnazione di tag e il controllo delle versioni delle immagini Docker) su MSDN.

### <a name="layer"></a>Livello

Le immagini sono costituite da uno o più *livelli*, ognuno corrispondente a una riga del Dockerfile che definisce l'immagine. Le immagini in un registro condividono i livelli comuni, aumentando così l'efficienza di archiviazione. Ad esempio, numerose immagini in repository diversi potrebbero condividere lo stesso livello di base Alpine Linux, ma ne verrà archiviata una sola copia nel registro.

La condivisione dei livelli ne ottimizza anche la distribuzione ai nodi, in quanto più immagini condivideranno i livelli comuni. Ad esempio, se un'immagine già presente in un nodo include il livello Alpine Linux come base, il pull successivo di un'immagine diversa che fa riferimento al medesimo livello non trasferirà nuovamente il livello al nodo, ma farà invece riferimento a quello già presente.

### <a name="manifest"></a>Manifesto

A ogni immagine del contenitore inserita in un registro contenitori viene associato un *manifesto* generato dal registro quando l'immagine viene inserita. Il manifesto identifica in modo univoco l'immagine e ne specifica i livelli. È possibile elencare i manifesti per un repository con il comando dell'interfaccia della riga di comando di Azure [az acr repository show-manifests][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Ad esempio, questo è l'elenco degli hash di manifesto per il repository "acr-helloworld":

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

Il manifesto presentato in questo argomento è diverso dal manifesto dell'immagine visualizzare nel portale di Azure o con il comando [docker manifest inspect][docker-manifest-inspect]. Nella sezione seguente, l'espressione "hash di manifesto" si riferisce all'hash generato dall'operazione push, non a *config.digest* nel manifesto dell'immagine. È possibile eseguire il pull ed eliminare le immagini tramite l'**hash di manifesto**, non config.digest. L'immagine seguente illustra i due tipi di hash.

![Hash di manifesto e config.digest nel portale di Azure][manifest-digest]

### <a name="manifest-digest"></a>Hash di manifesto

I manifesti sono identificati da un hash SHA-256 univoco, l'*hash di manifesto*. Ciascuna immagine, con o senza tag, viene identificata da un proprio hash. Il valore dell'hash è univoco anche se i dati dei livelli dell'immagine sono identici a quelli di un'altra. Questo meccanismo consente quindi di eseguire ripetutamente il push di immagini con tag identici in un registro. Ad esempio, è possibile eseguire più volte il push di `myimage:latest` nel registro senza errori poiché ogni immagine viene identificata dal relativo hash univoco.

È possibile eseguire il pull di un'immagine da un registro specificandone l'hash nell'operazione. Alcuni sistemi sono configurati per eseguire il pull tramite hash perché in questo modo viene garantita la versione dell'immagine in questione, anche se in seguito viene eseguito il push nel registro di un'immagine con gli stessi tag.

Ad esempio, questo è il pull di un'immagine dal repository "acr-helloworld" tramite l'hash di manifesto:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Se si esegue ripetutamente il push di immagini modificate con tag identici, è possibile che vengano create immagini orfane, ovvero prive di tag, che occupano comunque spazio nel registro. Le immagini senza tag non vengono visualizzate nell'interfaccia della riga di comando o nel portale di Azure quando si elencano o si visualizzano le immagini in base ai tag. Tuttavia, i loro livelli sono comunque presenti e occupano spazio nel registro. La sezione [Eliminare immagini senza tag](#delete-untagged-images) di questo articolo illustra come liberare lo spazio usato dalle immagini senza tag.

## <a name="delete-image-data"></a>Eliminare i dati di immagini

Esistono vari modi per eliminare i dati di immagini dal registro contenitori:

* Eliminare un [repository](#delete-repository): elimina tutte le immagini e tutti i livelli univoci all'interno del repository.
* Eliminare in base ai [tag](#delete-by-tag): elimina un'immagine, il tag, tutti i livelli univoci a cui l'immagine fa riferimento e tutti gli altri tag a essa associati.
* Eliminare in base al [digest del manifesto](#delete-by-manifest-digest): elimina un'immagine, tutti i livelli univoci a cui fa riferimento l'immagine e tutti i tag a essa associati.

## <a name="delete-repository"></a>Eliminare un repository

L'eliminazione di un repository elimina anche tutte le immagini al suo interno, inclusi tag, livelli univoci e manifesti. Quando si elimina un repository, si recupera lo spazio di archiviazione usato dalle immagini in precedenza al suo interno.

Il comando seguente dell'interfaccia della riga di comando di Azure elimina il repository "acr-helloworld" e tutti i tag e i manifesti al suo interno. Se i livelli a cui fanno riferimento i manifesti eliminati non sono associati ad altre immagini nel registro, i relativi dati verranno eliminati.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Eliminare in base ai tag

È possibile eliminare immagini singole da un repository specificando il nome del repository e il tag nell'operazione di eliminazione. Quando si elimina in base ai tag, si recupera lo spazio di archiviazione usato dai livelli univoci dell'immagine, ossia quelli non condivisi da altre immagini nel registro.

Per eliminare in base ai tag, usare il comando [az acr repository delete][az-acr-repository-delete] e specificare il nome dell'immagine nel parametro `--image`. Vengono eliminati tutti i livelli univoci dell'immagine e gli altri tag a essa associati.

Ad esempio, questa è l'eliminazione dell'immagine "acr-helloworld:latest" dal registro "myregistry":

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> L'eliminazione *in base ai tag* non deve essere confuso con l'eliminazione (rimozione) di un tag. È possibile eliminare un tag con il comando dell'interfaccia della riga di comando di Azure [az acr repository untag][az-acr-repository-untag]. Spazio non viene liberato quando rimuovere il tag da un'immagine poiché relativi [manifesto](#manifest) e livello dati rimangano nel Registro di sistema. Viene eliminato solo il riferimento al tag.

## <a name="delete-by-manifest-digest"></a>Eliminare in base all'hash di manifesto

È possibile associare un [hash di manifesto](#manifest-digest) a uno o più tag oppure a nessuno. Quando si elimina in base all'hash, vengono eliminati tutti i tag a cui fa riferimento il manifesto, così come i dati dei livelli univoci dell'immagine. I dati dei livelli condivisi non vengono eliminati.

Per eliminare in base all'hash, elencare prima gli hash di manifesto nel repository che contengono le immagini da eliminare. Ad esempio:

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
  }
]
```

Specificare quindi l'hash da eliminare nel comando [az acr repository delete][az-acr-repository-delete]. Il formato del comando è:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Ad esempio, per eliminare l'ultimo manifesto elencato nell'output precedente (con il tag "v2"):

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

Il `acr-helloworld:v2` immagine viene eliminata dal Registro di sistema, come i dati di livello univoci per tale immagine. Se un manifesto è associato a più tag, vengono eliminati anche tutti i tag associati.

### <a name="list-digests-by-timestamp"></a>Digest di elenco dal timestamp

Per mantenere le dimensioni di un repository o del Registro di sistema, potrebbe essere necessario eliminare periodicamente i digest di manifesto precedente rispetto a una determinata data.

Il comando di Azure Elenca tutti i digest di manifesto in un repository antecedente a un timestamp specificato, in ordine crescente. Sostituire `<acrName>` e `<repositoryName>` con i valori appropriati all'ambiente. Il timestamp può essere un'espressione di data e ora completa o una data, come in questo esempio.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

### <a name="delete-digests-by-timestamp"></a>Eliminazione di digest di base al timestamp

Dopo aver individuato i digest di manifesto non aggiornati, è possibile eseguire lo script Bash seguente per eliminare manifesto digest antecedenti a un timestamp specificato. e richiede l'interfaccia della riga di comando di Azure e **xargs**. Per impostazione predefinita, lo script non esegue alcuna operazione di eliminazione. Modificare il valore `ENABLE_DELETE` in `true` per abilitare l'eliminazione delle immagini.

> [!WARNING]
> Usare il seguente script di esempio con cautela: i dati immagine eliminato è irreversibile. Se si dispone di sistemi che pull delle immagini dal manifesto del digest (anziché nome immagine), è consigliabile non eseguire questi script. L'eliminazione di digest di manifesto, potrà eseguire il pull di immagini dal Registro di sistema di tali sistemi. Invece di eseguire il pull tramite manifesto, adottare uno schema di *assegnazione di tag univoci*, una [procedura consigliata][tagging-best-practices]. 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>Eliminare le immagini senza tag

Come accennato nella sezione [Hash di manifesto](#manifest-digest), il push di un'immagine modificata con un tag esistente **rimuove i tag** dell'immagine del push precedente, dando vita a un'immagine orfana. Il manifesto dell'immagine del push precedente, così come i dati dei relativi livelli, rimane nel registro. Considerare la sequenza di eventi seguente:

1. Eseguire il push dell'immagine *acr-helloworld* con il tag **latest**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Controllare i manifesti per il repository *acr-helloworld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. Modificare il Dockerfile *acr-helloworld*
1. Eseguire il push dell'immagine *acr-helloworld* con il tag **latest**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Controllare i manifesti per il repository *acr-helloworld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

Come può vedere nell'output dell'ultimo passaggio della sequenza, è ora disponibile un orfano manifesto cui `"tags"` proprietà è un elenco vuoto. Questo manifesto è ancora presente nel registro, insieme a tutti i dati dei livelli univoci a cui fa riferimento. **Per eliminare le immagini orfane e i relativi dati di livelli, è necessario eliminarle tramite l'hash di manifesto**.

### <a name="list-untagged-images"></a>Elencare le immagini senza tag

È possibile elencare tutte le immagini senza tag nel repository usando il comando seguente dell'interfaccia della riga di comando di Azure. Sostituire `<acrName>` e `<repositoryName>` con i valori appropriati all'ambiente.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

### <a name="delete-all-untagged-images"></a>Eliminare tutte le immagini senza tag

> [!WARNING]
> Usare lo script di esempio seguente con cautela: i dati di un'immagine eliminata NON POSSONO ESSERE RIPRISTINATI. Se si dispone di sistemi che pull delle immagini dal manifesto del digest (anziché nome immagine), è consigliabile non eseguire questi script. L'eliminazione delle immagini senza tag impedirà a tali sistemi di eseguire il pull delle immagini dal registro. Invece di eseguire il pull tramite manifesto, adottare uno schema di *assegnazione di tag univoci*, una [procedura consigliata][tagging-best-practices].

**Interfaccia della riga di comando di Azure in Bash**

Lo script Bash seguente elimina tutte le immagini senza tag da un repository e richiede l'interfaccia della riga di comando di Azure e **xargs**. Per impostazione predefinita, lo script non esegue alcuna operazione di eliminazione. Modificare il valore `ENABLE_DELETE` in `true` per abilitare l'eliminazione delle immagini.

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted. Set ENABLE_DELETE=true to enable image deletion."
fi
```

**Interfaccia della riga di comando di Azure in PowerShell**

Lo script PowerShell seguente elimina tutte le immagini senza tag da un repository e richiede PowerShell e l'interfaccia della riga di comando di Azure. Per impostazione predefinita, lo script non esegue alcuna operazione di eliminazione. Modificare il valore `$enableDelete` in `$TRUE` per abilitare l'eliminazione delle immagini.

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted. Set `$enableDelete = `$TRUE to enable image deletion."
}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'archiviazione delle immagini in Registro Azure Container, vedere [Archiviazione di immagini del contenitore in Registro Azure Container](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com
[tagging-best-practices]: https://blogs.msdn.microsoft.com/stevelasker/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
