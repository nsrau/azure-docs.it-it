---
title: Configurare l'archiviazione con File di Azure
description: Come configurare e connettersi a File di Azure nel contenitore di Windows nel servizio app.
author: msangapu-msft
manager: gwallace
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 2c12bf45c033fea185d976f1e9d644183407b5ac
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297217"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Configurare File di Azure in un contenitore di Windows nel servizio app

> [!NOTE]
> Questo articolo si applica ai contenitori di Windows personalizzati. Per eseguire la distribuzione nel servizio app in _Linux_, vedere [gestire il contenuto da archiviazione di Azure](./containers/how-to-serve-content-from-azure-storage.md).
>

Questa guida illustra come accedere ad archiviazione di Azure nei contenitori di Windows. Sono supportate solo le condivisioni [file di Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) e [i file Premium](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) . Usare le condivisioni di File di Azure in questa procedura. I vantaggi includono la protezione e la portabilità del contenuto, l'accesso a più app e molteplici metodi di trasferimento.

## <a name="prerequisites"></a>Prerequisiti

- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) (2.0.46 o successiva)
- [Un'app contenitore di Windows esistente nel servizio app Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Creare una condivisione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Caricare file in una condivisione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> File di Azure è un archivio non predefinito e fatturato separatamente, non incluso nell'app Web. Non supporta l'uso della configurazione del firewall a causa delle limitazioni dell'infrastruttura.
>

## <a name="link-storage-to-your-web-app-preview"></a>Collegare la risorsa di archiviazione all'app Web (anteprima)

 Per montare una condivisione file di Azure in una directory nell'app del servizio app, usare il [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) comando. Il tipo di archiviazione deve essere risorsa.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

È consigliabile eseguire questa operazione per tutte le altre directory che si desidera collegare a una condivisione File di Azure.

## <a name="verify"></a>Verificare

Una volta che una condivisione di File di Azure è collegata a un'app Web, è possibile verificarla eseguendo il comando seguente:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>Passaggi successivi

- [Eseguire la migrazione di un'app ASP.NET al servizio app Azure usando un contenitore Windows (anteprima)](app-service-web-tutorial-windows-containers-custom-fonts.md).
