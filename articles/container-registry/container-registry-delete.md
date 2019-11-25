---
title: Delete image resources
description: Details on how to effectively manage registry size by deleting container image data using Azure CLI commands.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 8d20bf2be1d472855c3e67dd79ea1725c152e3d2
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455278"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Delete container images in Azure Container Registry using the Azure CLI

Per mantenere le dimensioni del Registro Azure Container, è consigliabile eliminare periodicamente i dati di immagini non aggiornati. Alcune immagini di contenitori distribuite in produzione potrebbero richiedere un'archiviazione a lungo termine, mentre altre possono solitamente essere eliminate prima. Ad esempio, in uno scenario di compilazione e test automatici, il registro può riempirsi velocemente di immagini che non verranno mai distribuite, pertanto potrà essere svuotato subito dopo aver compilato la build ed effettuato correttamente i test.

Poiché è possibile eliminare i dati di immagini in modi diversi, è importante comprendere l'impatto di ciascun tipo di eliminazione sull'utilizzo dello spazio di archiviazione. This article covers several methods for deleting image data:

* Eliminare un [repository](#delete-repository): elimina tutte le immagini e tutti i livelli univoci all'interno del repository.
* Eliminare in base ai [tag](#delete-by-tag): elimina un'immagine, il tag, tutti i livelli univoci a cui l'immagine fa riferimento e tutti gli altri tag a essa associati.
* Eliminare in base all'[hash di manifesto](#delete-by-manifest-digest): elimina un'immagine, tutti i livelli univoci a cui fa riferimento l'immagine e tutti i tag a essa associati.

Sample scripts are provided to help automate delete operations.

For an introduction to these concepts, see [About registries, repositories, and images](container-registry-concepts.md).

## <a name="delete-repository"></a>Eliminare un repository

L'eliminazione di un repository elimina anche tutte le immagini al suo interno, inclusi tag, livelli univoci e manifesti. When you delete a repository, you recover the storage space used by the images that reference unique layers in that repository.

Il comando seguente dell'interfaccia della riga di comando di Azure elimina il repository "acr-helloworld" e tutti i tag e i manifesti al suo interno. If layers referenced by the deleted manifests are not referenced by any other images in the registry, their layer data is also deleted, recovering the storage space.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Eliminare in base ai tag

È possibile eliminare immagini singole da un repository specificando il nome del repository e il tag nell'operazione di eliminazione. Quando si elimina in base ai tag, si recupera lo spazio di archiviazione usato dai livelli univoci dell'immagine, ossia quelli non condivisi da altre immagini nel registro.

To delete by tag, use [az acr repository delete][az-acr-repository-delete] and specify the image name in the `--image` parameter. Vengono eliminati tutti i livelli univoci dell'immagine e gli altri tag a essa associati.

Ad esempio, questa è l'eliminazione dell'immagine "acr-helloworld:latest" dal registro "myregistry":

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> L'eliminazione *in base ai tag* non deve essere confuso con l'eliminazione (rimozione) di un tag. You can delete a tag with the Azure CLI command [az acr repository untag][az-acr-repository-untag]. No space is freed when you untag an image because its [manifest](container-registry-concepts.md#manifest) and layer data remain in the registry. Viene eliminato solo il riferimento al tag.

## <a name="delete-by-manifest-digest"></a>Eliminare in base all'hash di manifesto

È possibile associare un [hash di manifesto](container-registry-concepts.md#manifest-digest) a uno o più tag oppure a nessuno. Quando si elimina in base all'hash, vengono eliminati tutti i tag a cui fa riferimento il manifesto, così come i dati dei livelli univoci dell'immagine. I dati dei livelli condivisi non vengono eliminati.

Per eliminare in base all'hash, elencare prima gli hash di manifesto nel repository che contengono le immagini da eliminare. ad esempio:

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

Next, specify the digest you wish to delete in the [az acr repository delete][az-acr-repository-delete] command. Il formato del comando è:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Ad esempio, per eliminare l'ultimo manifesto elencato nell'output precedente (con il tag "v2"):

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

The `acr-helloworld:v2` image is deleted from the registry, as is any layer data unique to that image. Se un manifesto è associato a più tag, vengono eliminati anche tutti i tag associati.

## <a name="delete-digests-by-timestamp"></a>Delete digests by timestamp

To maintain the size of a repository or registry, you might need to periodically delete manifest digests older than a certain date.

The following Azure CLI command lists all manifest digest in a repository older than a specified timestamp, in ascending order. Sostituire `<acrName>` e `<repositoryName>` con i valori appropriati all'ambiente. The timestamp could be a full date-time expression or a date, as in this example.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

After identifying stale manifest digests, you can run the following Bash script to delete manifest digests older than a specified timestamp. e richiede l'interfaccia della riga di comando di Azure e **xargs**. Per impostazione predefinita, lo script non esegue alcuna operazione di eliminazione. Modificare il valore `ENABLE_DELETE` in `true` per abilitare l'eliminazione delle immagini.

> [!WARNING]
> Use the following sample script with caution--deleted image data is UNRECOVERABLE. If you have systems that pull images by manifest digest (as opposed to image name), you should not run these scripts. Deleting the manifest digests will prevent those systems from pulling the images from your registry. Instead of pulling by manifest, consider adopting a *unique tagging* scheme, a [recommended best practice](container-registry-image-tag-version.md). 

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

As you can see in the output of the last step in the sequence, there is now an orphaned manifest whose `"tags"` property is an empty list. Questo manifesto è ancora presente nel registro, insieme a tutti i dati dei livelli univoci a cui fa riferimento. **Per eliminare le immagini orfane e i relativi dati di livelli, è necessario eliminarle tramite l'hash di manifesto**.

## <a name="delete-all-untagged-images"></a>Eliminare tutte le immagini senza tag

È possibile elencare tutte le immagini senza tag nel repository usando il comando seguente dell'interfaccia della riga di comando di Azure. Sostituire `<acrName>` e `<repositoryName>` con i valori appropriati all'ambiente.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Using this command in a script, you can delete all untagged images in a repository.

> [!WARNING]
> Usare lo script di esempio seguente con cautela: i dati di un'immagine eliminata NON POSSONO ESSERE RIPRISTINATI. If you have systems that pull images by manifest digest (as opposed to image name), you should not run these scripts. L'eliminazione delle immagini senza tag impedirà a tali sistemi di eseguire il pull delle immagini dal registro. Instead of pulling by manifest, consider adopting a *unique tagging* scheme, a [recommended best practice](container-registry-image-tag-version.md).

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

As an alternative to scripting Azure CLI commands, run an on-demand or scheduled ACR task to delete all tags that are older than a certain duration or match a specified name filter. For more information, see [Automatically purge images from an Azure container registry](container-registry-auto-purge.md).

Optionally set a [retention policy](container-registry-retention-policy.md) for each registry, to manage untagged manifests. When you enable a retention policy, image manifests in the registry that don't have any associated tags, and the underlying layer data, are automatically deleted after a set period.

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
