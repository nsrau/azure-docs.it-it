---
title: Aggiungere spazio di archiviazione personalizzato (contenitore di Windows)Add custom storage (Windows container)
description: Informazioni su come collegare una condivisione di rete personalizzata in un contenitore di Windows personalizzato nel servizio app di Azure.Learn how to attach custom network share in a custom Windows container in Azure App Service. Condividi i file tra le app, gestisci contenuti statici in remoto e accedi in locale, ecc.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120678"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Configurare i file di Azure in un contenitore di Windows nel servizio appConfigure Azure Files in a Windows Container on App Service

> [!NOTE]
> Questo articolo si applica ai contenitori di Windows personalizzati. Per eseguire la distribuzione al servizio app in _Linux,_ vedere [Servire il contenuto da Archiviazione di Azure](./containers/how-to-serve-content-from-azure-storage.md).
>

Questa guida illustra come accedere ad Archiviazione di Azure nei contenitori di Windows.This guide shows how to access Azure Storage in Windows Containers. Sono supportate solo [le condivisioni file](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) di Azure e le [condivisioni file Premium.Only](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) Azure Files Shares and Premium Files Shares are supported. In questa procedura vengono usate le condivisioni file di Azure.You use Azure Files Shares in this how-to. I vantaggi includono la protezione e la portabilità del contenuto, l'accesso a più app e molteplici metodi di trasferimento.

## <a name="prerequisites"></a>Prerequisiti

- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) (2.0.46 o successiva)
- [An existing Windows Container app in Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Creare una condivisione file di AzureCreate Azure file share](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Caricare file nella condivisione file di AzureUpload files to Azure File share](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> File di Azure è l'archiviazione non predefinita e fatturata separatamente, non inclusa nell'app Web.Azure Files is non-default storage and billed separately, not included with the web app. Non supporta l'utilizzo della configurazione di Firewall a causa di limitazioni dell'infrastruttura.
>

## <a name="limitations"></a>Limitazioni

- Archiviazione di Azure nei contenitori di Windows è **in anteprima** e non **è supportata** per gli scenari di **produzione.**
- Archiviazione di Azure nei contenitori di Windows supporta solo il montaggio di **contenitori di File** di Azure (lettura/scrittura).
- Archiviazione di Azure nei contenitori di Windows non è attualmente **supportata** per portare i propri scenari di codice nei piani del servizio app di Windows.Azure Storage in Windows containers is currently not supported for bring your own code scenarios on Windows App Service plans.
- Archiviazione di Azure nei contenitori di Windows **non supporta** l'uso della configurazione di Firewall **di archiviazione** a causa delle limitazioni dell'infrastruttura.
- Archiviazione di Azure nei contenitori di Windows consente di specificare **fino a cinque** punti di montaggio per app.
- Archiviazione di Azure montata su un'app non è accessibile tramite gli endpoint FTP/FTP del servizio app. Usare [Esplora archivi di Azure](https://azure.microsoft.com/features/storage-explorer/).
- Archiviazione di Azure viene fatturata in modo indipendente e **non inclusa** nell'app Web. Altre informazioni sui [prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage).Learn more about Azure Storage pricing .

## <a name="link-storage-to-your-web-app-preview"></a>Collegare la risorsa di archiviazione all'app Web (anteprima)

 Per montare una condivisione file di Azure in [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) una directory nell'app del servizio app, usare il comando. Il tipo di archiviazione deve essere AzureFiles.Storage Type must be AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

È consigliabile eseguire questa operazione per tutte le altre directory che si desidera collegare a una condivisione File di Azure.You should do this for any other directories you want to be linked to an Azure Files share.

## <a name="verify"></a>Verifica

Dopo aver collegato una condivisione File di Azure a un'app Web, è possibile verificarlo eseguendo il comando seguente:Once an Azure Files share is linked to a web app, you can verify this by running the following command:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>Passaggi successivi

- Eseguire la migrazione di un'app ASP.NET al servizio app di [Azure usando un contenitore di Windows (anteprima).](app-service-web-tutorial-windows-containers-custom-fonts.md)
