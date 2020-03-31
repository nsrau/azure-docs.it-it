---
title: Servire il contenuto da Archiviazione di Azure ai contenitori LinuxServe content from Azure Storage to Linux containers
description: Informazioni su come collegare una condivisione di rete personalizzata al contenitore Linux nel servizio app di Azure.Learn how to attach custom network share to your Linux container in Azure App Service. Condividi i file tra le app, gestisci contenuti statici in remoto e accedi in locale, ecc.
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
> Questo articolo si applica ai contenitori Linux.This article applies to Linux containers. Per eseguire la distribuzione in contenitori Windows personalizzati, vedere Configurare i file di Azure in un contenitore di [Windows nel servizio app.](../configure-connect-to-azure-storage.md) Archiviazione di Azure nel servizio app in Linux è una funzionalità di **anteprima.** Questa funzionalità non è **supportata per gli scenari**di produzione.
>

Questa guida illustra come collegare Archiviazione di Azure al servizio app in Linux.This guide shows how to attach Azure Storage to App Service on Linux. I vantaggi includono contenuto protetto, portabilità del contenuto, archiviazione permanente, accesso a più app e più metodi di trasferimento.

## <a name="prerequisites"></a>Prerequisiti

- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) (2.0.46 o successiva)
- Un servizio app esistente [nell'app Linux.](https://docs.microsoft.com/azure/app-service/containers/)
- Un [account di archiviazione di AzureAn Azure Storage Account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- Una [condivisione file e una directory di Azure](../../storage/files/storage-how-to-use-files-cli.md).


## <a name="limitations-of-azure-storage-with-app-service"></a>Limitazioni di Archiviazione di Azure con il servizio appLimitations of Azure Storage with App Service

- Archiviazione di Azure con il servizio app è **in anteprima** per il servizio app in Linux e l'app Web per i contenitori. Non è **supportato** per gli scenari di **produzione.**
- Archiviazione di Azure con il servizio app supporta il montaggio di **contenitori di file** di Azure (lettura/scrittura) e **contenitori BLOB** di Azure (sola lettura)Azure Storage with App Service supports mounting Azure Files containers (Read/Write) and Azure Blob containers (Read Only)
- Archiviazione di Azure con il servizio app **non supporta** l'uso della configurazione di Firewall **di archiviazione** a causa delle limitazioni dell'infrastruttura.
- Archiviazione di Azure con il servizio app consente di specificare **fino a cinque** punti di montaggio per app.
- Archiviazione di Azure montata su un'app non è accessibile tramite gli endpoint FTP/FTP del servizio app. Usare [Esplora archivi di Azure](https://azure.microsoft.com/features/storage-explorer/).
- Archiviazione di Azure non è **incluso** nell'app Web e fatturato separatamente. Altre informazioni sui [prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage).Learn more about Azure Storage pricing .

> [!WARNING]
> Le configurazioni del servizio app che usano Archiviazione BLOB di Azure diventeranno di sola lettura in feb 2020.App Service configurations using Azure Blob Storage will become Read only in Feb 2020. [Scopri di più](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Configurare l'app con Archiviazione di AzureConfigure your app with Azure Storage

Dopo aver creato [l'account di Archiviazione di Azure,](#prerequisites)la condivisione file e la directory, è ora possibile configurare l'app con Archiviazione di Azure.Once you've created your Azure Storage account, file share and directory , you can now configure your app with Azure Storage.

Per montare un account di archiviazione in una [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) directory nell'app del servizio app, usare il comando. Il tipo di archiviazione può essere AzureBlob o AzureFiles. AzureFiles viene usato in questo esempio.


> [!CAUTION]
> La directory specificata come percorso di montaggio nell'app Web deve essere vuota. Qualsiasi contenuto memorizzato in questa directory verrà eliminato quando viene aggiunto un montaggio esterno. Se si esegue la migrazione dei file di un'app esistente, creare un copia di backup dell'app e del relativo contenuto prima di iniziare.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

È consigliabile eseguire questa operazione per tutte le altre directory che si vogliono collegare a un account di archiviazione.

## <a name="verify-azure-storage-link-to-the-web-app"></a>Verificare il collegamento Archiviazione di Azure all'app WebVerify Azure Storage link to the web app

Dopo aver collegato un contenitore di archiviazione a un'app Web, è possibile verificare il collegamento eseguendo il comando seguente:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Usare Archiviazione di Azure in Docker ComposeUse Azure Storage in Docker Compose

Archiviazione di Azure può essere montata con app multi-contenitore usando custom-id.Azure Storage can be mounted with multi-container apps using the custom-id. Per visualizzare il nome dell'ID personalizzato, eseguire [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

Nel file *docker-compose.yml,* `volumes` mappare `custom-id`l'opzione su . Ad esempio:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le app Web nel servizio app](../configure-common.md)di Azure .

