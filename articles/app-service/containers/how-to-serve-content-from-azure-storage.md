---
title: Gestire il contenuto da archiviazione di Azure a contenitori Linux
description: Informazioni su come aggiungere una condivisione di rete personalizzata al contenitore Linux nel servizio app Azure. Condividi i file tra le app, Gestisci il contenuto statico in remoto e accedi localmente e così via.
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 79a4e423f7a2b6570234c958ac833cdf5c6a75e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297918"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Rendere disponibile contenuto di Archiviazione di Azure nel servizio app in Linux

> [!NOTE]
> Questo articolo si applica ai contenitori Linux. Per eseguire la distribuzione in contenitori Windows personalizzati, vedere [configurare file di Azure in un contenitore di Windows nel servizio app](../configure-connect-to-azure-storage.md). Archiviazione di Azure nel servizio app in Linux è una funzionalità di **Anteprima** . Questa funzionalità **non è supportata per gli scenari di produzione**.
>

Questa guida illustra come aggiungere archiviazione di Azure al servizio app in Linux. I vantaggi includono contenuto protetto, portabilità del contenuto, archiviazione persistente, accesso a più app e più metodi di trasferimento.

## <a name="prerequisites"></a>Prerequisiti

- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) (2.0.46 o successiva)
- Un [servizio app esistente nell'app Linux](https://docs.microsoft.com/azure/app-service/containers/).
- Un [account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- Una [condivisione file e una directory di Azure](../../storage/files/storage-how-to-use-files-cli.md).


## <a name="limitations-of-azure-storage-with-app-service"></a>Limitazioni di archiviazione di Azure con il servizio app

- Archiviazione di Azure con il servizio app è **in anteprima** per il servizio app in Linux e app Web per contenitori. Non è **supportata** per gli **scenari di produzione**.
- Archiviazione di Azure con il servizio app supporta il montaggio di **contenitori di file di Azure** (lettura/scrittura) e **contenitori BLOB di Azure** (sola lettura)
- Archiviazione di Azure con il servizio app **non supporta** l'uso della configurazione del **firewall di archiviazione** a causa di limitazioni dell'infrastruttura.
- Archiviazione di Azure con il servizio app consente **di specificare fino a cinque punti di** montaggio per ogni app.
- L'archiviazione di Azure montata in un'app non è accessibile tramite gli endpoint FTP/FTPs del servizio app. Usare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
- Archiviazione di Azure **non è incluso** nell'app Web e fatturato separatamente. Scopri di più sui [prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage).

> [!WARNING]
> Le configurazioni del servizio app che usano l'archiviazione BLOB di Azure diventeranno di sola lettura nel 2020 febbraio. [Altre informazioni](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Configurare l'app con archiviazione di Azure

Dopo aver creato l' [account di archiviazione di Azure, la condivisione file e la directory](#prerequisites), è ora possibile configurare l'app con archiviazione di Azure.

Per montare un account di archiviazione in una directory nell'app del servizio app, usare il [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) comando. Il tipo di archiviazione può essere AzureBlob o AzureFiles. In questo esempio viene usato risorsa.


> [!CAUTION]
> La directory specificata come percorso di montaggio nell'app Web deve essere vuota. Eventuali contenuti archiviati in questa directory verranno eliminati quando verrà aggiunto un montaggio esterno. Se si esegue la migrazione dei file di un'app esistente, creare un copia di backup dell'app e del relativo contenuto prima di iniziare.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

È consigliabile eseguire questa operazione per tutte le altre directory che si vogliono collegare a un account di archiviazione.

## <a name="verify-azure-storage-link-to-the-web-app"></a>Verificare il collegamento di archiviazione di Azure all'app Web

Dopo aver collegato un contenitore di archiviazione a un'app Web, è possibile verificare il collegamento eseguendo il comando seguente:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Usare archiviazione di Azure in Docker Compose

Archiviazione di Azure può essere montata con app multicontenitore usando l'ID personalizzato. Per visualizzare il nome dell'ID personalizzato, eseguire [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

Nel file *Docker-compose. yml* mappare l' `volumes` opzione a `custom-id`. Ad esempio:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le app Web nel servizio app Azure](../configure-common.md).

