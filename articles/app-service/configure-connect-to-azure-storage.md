---
title: Configurare l'archiviazione con file di Azure
description: Come configurare e connettersi ai file di Azure nel contenitore di Windows nel servizio App.
author: msangapu-msft
manager: gwallace
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu-msft
ms.openlocfilehash: ea6555e8459b8f372a73d7f943e9a96523d4c791
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789046"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Configurare i file di Azure in un contenitore di Windows nel servizio App

> [!NOTE]
> Questo articolo si applica ai contenitori di Windows personalizzati. Per la distribuzione nel servizio App in _Linux_, vedere [rendere disponibile contenuto da archiviazione di Azure](./containers/how-to-serve-content-from-azure-storage.md).
>

Questa guida illustra come accedere ad archiviazione di Azure nei contenitori di Windows. Solo [condivisioni di file di Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) e [condivisioni file Premium](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) sono supportati. In questa procedura si usano le condivisioni file di Azure. I vantaggi includono la protezione e la portabilità del contenuto, l'accesso a più app e molteplici metodi di trasferimento.

## <a name="prerequisites"></a>Prerequisiti

- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) (2.0.46 o successiva)
- [Un'app contenitore di Windows esistente nel servizio App di Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Crea condivisione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Caricare file in una condivisione File di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> File di Azure è un'archiviazione non predefinito e fatturati separatamente, non inclusi con l'app web. Non supporta tramite configurazione del Firewall a causa delle limitazioni dell'infrastruttura.
>

## <a name="link-storage-to-your-web-app-preview"></a>Collegare la risorsa di archiviazione all'app Web (anteprima)

 Per montare una condivisione file di Azure in una directory nell'App del servizio app, si utilizza il [ `az webapp config storage-account add` ](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) comando. Tipo di archiviazione deve essere una risorsa.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

È necessario eseguire questa operazione per tutte le altre directory a cui che si desidera essere collegato a un file di Azure condiviso.

## <a name="verify"></a>Verificare

Dopo aver collegata una condivisione file di Azure a un'app web, è possibile verificarlo eseguendo il comando seguente:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>Passaggi successivi

- [Eseguire la migrazione di un'app ASP.NET nel servizio App di Azure con un contenitore di Windows (anteprima)](app-service-web-tutorial-windows-containers-custom-fonts.md).