---
title: Eliminare le risorse immagineDelete image resources
description: Dettagli su come gestire in modo efficace le dimensioni del Registro di sistema eliminando i dati dell'immagine del contenitore usando i comandi dell'interfaccia della riga di comando di Azure.Details on how to effectively manage registry size by deleting container image data using Azure CLI commands.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 449a1c09bf88e3e0e0aeca4d3b687371d2a6b91a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78403344"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Eliminare le immagini del contenitore nel Registro di sistema del contenitore di Azure usando l'interfaccia della riga di comando di AzureDelete container images in Azure Container Registry using the

Per mantenere le dimensioni del Registro Azure Container, è consigliabile eliminare periodicamente i dati di immagini non aggiornati. Alcune immagini di contenitori distribuite in produzione potrebbero richiedere un'archiviazione a lungo termine, mentre altre possono solitamente essere eliminate prima. Ad esempio, in uno scenario di compilazione e test automatici, il registro può riempirsi velocemente di immagini che non verranno mai distribuite, pertanto potrà essere svuotato subito dopo aver compilato la build ed effettuato correttamente i test.

Poiché è possibile eliminare i dati di immagini in modi diversi, è importante comprendere l'impatto di ciascun tipo di eliminazione sull'utilizzo dello spazio di archiviazione. Questo articolo illustra diversi metodi per eliminare i dati immagine:This article covers several methods for deleting image data:

* Eliminare un [repository](#delete-repository): elimina tutte le immagini e tutti i livelli univoci all'interno del repository.
* Eliminare in base ai [tag](#delete-by-tag): elimina un'immagine, il tag, tutti i livelli univoci a cui l'immagine fa riferimento e tutti gli altri tag a essa associati.
* Eliminare in base all'[hash di manifesto](#delete-by-manifest-digest): elimina un'immagine, tutti i livelli univoci a cui fa riferimento l'immagine e tutti i tag a essa associati.

Vengono forniti script di esempio per automatizzare le operazioni di eliminazione.

Per un'introduzione a questi concetti, vedere [Informazioni su registri, repository e immagini](container-registry-concepts.md).

## <a name="delete-repository"></a>Eliminare un repository

L'eliminazione di un repository elimina anche tutte le immagini al suo interno, inclusi tag, livelli univoci e manifesti. Quando si elimina un repository, si ripristina lo spazio di archiviazione utilizzato dalle immagini che fanno riferimento a livelli univoci in tale repository.

Il comando seguente dell'interfaccia della riga di comando di Azure elimina il repository "acr-helloworld" e tutti i tag e i manifesti al suo interno. Se i livelli a cui fanno riferimento i manifesti eliminati non fanno riferimento ad altre immagini nel Registro di sistema, vengono eliminati anche i relativi dati di livello, ripristinando lo spazio di archiviazione.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Eliminare in base ai tag

È possibile eliminare immagini singole da un repository specificando il nome del repository e il tag nell'operazione di eliminazione. Quando si elimina in base ai tag, si recupera lo spazio di archiviazione usato dai livelli univoci dell'immagine, ossia quelli non condivisi da altre immagini nel registro.

Per eliminare in base ai tag, usare il comando [az acr repository delete][az-acr-repository-delete] e specificare il nome dell'immagine nel parametro `--image`. Vengono eliminati tutti i livelli univoci dell'immagine e gli altri tag a essa associati.

Ad esempio, questa è l'eliminazione dell'immagine "acr-helloworld:latest" dal registro "myregistry":

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> L'eliminazione *in base ai tag* non deve essere confuso con l'eliminazione (rimozione) di un tag. È possibile eliminare un tag con il comando dell'interfaccia della riga di comando di Azure [az acr repository untag][az-acr-repository-untag]. Nessuno spazio viene liberato quando si annulla il tag di un'immagine perché i dati del [manifesto](container-registry-concepts.md#manifest) e del livello rimangono nel Registro di sistema. Viene eliminato solo il riferimento al tag.

## <a name="delete-by-manifest-digest"></a>Eliminare in base all'hash di manifesto

È possibile associare un [hash di manifesto](container-registry-concepts.md#manifest-digest) a uno o più tag oppure a nessuno. Quando si elimina in base all'hash, vengono eliminati tutti i tag a cui fa riferimento il manifesto, così come i dati dei livelli univoci dell'immagine. I dati dei livelli condivisi non vengono eliminati.

Per eliminare in base all'hash, elencare prima gli hash di manifesto nel repository che contengono le immagini da eliminare. Ad esempio:

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
  }
]
```

Specificare quindi l'hash da eliminare nel comando [az acr repository delete][az-acr-repository-delete]. Il formato del comando è:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Ad esempio, per eliminare l'ultimo manifesto elencato nell'output precedente (con il tag "v2"):

```azurecli
az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
```

```output
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): 
```

L'immagine `acr-helloworld:v2` viene eliminata dal Registro di sistema, così come tutti i dati dei livelli univoci per tale immagine. Se un manifesto è associato a più tag, vengono eliminati anche tutti i tag associati.

## <a name="delete-digests-by-timestamp"></a>Eliminare i digest in base al timestampDelete digests by timestamp

Per mantenere le dimensioni di un repository o di un Registro di sistema, potrebbe essere necessario eliminare periodicamente i digest del manifesto anteriori a una determinata data.

Il comando CLI di Azure seguente elenca tutto il digest del manifesto in un repository precedente a un timestamp specificato, in ordine crescente. Sostituire `<acrName>` e `<repositoryName>` con i valori appropriati all'ambiente. Il timestamp può essere un'espressione data-ora completa o una data, come in questo esempio.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Dopo aver identificato i digest del manifesto non aggiornato, è possibile eseguire lo script Bash seguente per eliminare i digest del manifesto precedenti a un timestamp specificato. e richiede l'interfaccia della riga di comando di Azure e **xargs**. Per impostazione predefinita, lo script non esegue alcuna operazione di eliminazione. Modificare il valore `ENABLE_DELETE` in `true` per abilitare l'eliminazione delle immagini.

> [!WARNING]
> Utilizzare lo script di esempio seguente con cautela: i dati dell'immagine eliminati sono UNRECOVERABLE. Se si dispone di sistemi che eseguono il pull delle immagini in base al digest del manifesto (anziché al nome dell'immagine), non è consigliabile eseguire questi script. L'eliminazione dei digest del manifesto impedirà a tali sistemi di estrarre le immagini dal Registro di sistema. Invece di eseguire il pull tramite manifesto, adottare uno schema di *assegnazione di tag univoci*, una [procedura consigliata](container-registry-image-tag-version.md). 

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

Come accennato nella sezione [Hash di manifesto](container-registry-concepts.md#manifest-digest), il push di un'immagine modificata con un tag esistente **rimuove i tag** dell'immagine del push precedente, dando vita a un'immagine orfana. Il manifesto dell'immagine del push precedente, così come i dati dei relativi livelli, rimane nel registro. Considerare la sequenza di eventi seguente:

1. Eseguire il push dell'immagine *acr-helloworld* con il tag **latest**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Controllare i manifesti per il repository *acr-helloworld*:

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   
   ```
   
   ```output
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

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   ```
   
   ```output
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

Come si può vedere nell'output dell'ultimo passaggio della sequenza, ora è presente un manifesto orfano la cui `"tags"` proprietà è un elenco vuoto. Questo manifesto è ancora presente nel registro, insieme a tutti i dati dei livelli univoci a cui fa riferimento. **Per eliminare le immagini orfane e i relativi dati di livelli, è necessario eliminarle tramite l'hash di manifesto**.

## <a name="delete-all-untagged-images"></a>Eliminare tutte le immagini senza tag

È possibile elencare tutte le immagini senza tag nel repository usando il comando seguente dell'interfaccia della riga di comando di Azure. Sostituire `<acrName>` e `<repositoryName>` con i valori appropriati all'ambiente.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Utilizzando questo comando in uno script, è possibile eliminare tutte le immagini senza tag in un repository.

> [!WARNING]
> Usare lo script di esempio seguente con cautela: i dati di un'immagine eliminata NON POSSONO ESSERE RIPRISTINATI. Se si dispone di sistemi che eseguono il pull delle immagini in base al digest del manifesto (anziché al nome dell'immagine), non è consigliabile eseguire questi script. L'eliminazione delle immagini senza tag impedirà a tali sistemi di eseguire il pull delle immagini dal registro. Invece di eseguire il pull tramite manifesto, adottare uno schema di *assegnazione di tag univoci*, una [procedura consigliata](container-registry-image-tag-version.md).

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
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
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
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```


## <a name="automatically-purge-tags-and-manifests-preview"></a>Ripulire automaticamente tag e manifesti (anteprima)

In alternativa agli script comandi dell'interfaccia della riga di comando di Azure, eseguire un'attività ACR su richiesta o pianificata per eliminare tutti i tag precedenti a una determinata durata o che corrispondono a un filtro dei nomi specificato. Per altre informazioni, vedere Eliminare automaticamente le [immagini da un registro contenitori](container-registry-auto-purge.md)di Azure.For more information, see Automatically purge images from an Azure container registry .

Facoltativamente, impostare un criterio di [conservazione](container-registry-retention-policy.md) per ogni Registro di sistema, per gestire i manifesti senza tag. Quando si abilitaunisco criterio di conservazione, i manifesti delle immagini nel Registro di sistema a cui non sono associati tag e i dati del livello sottostante vengono eliminati automaticamente dopo un determinato periodo.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'archiviazione delle immagini in Registro Azure Container, vedere [Archiviazione di immagini del contenitore in Registro Azure Container](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
