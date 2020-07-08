---
title: Aggiungere archiviazione personalizzata (contenitore di Windows)
description: Informazioni su come aggiungere una condivisione di rete personalizzata in un contenitore Windows personalizzato nel servizio app Azure. Condividi i file tra le app, Gestisci il contenuto statico in remoto e accedi localmente e così via.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77120678"
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

## <a name="limitations"></a>Limitazioni

- Archiviazione di Azure nei contenitori di Windows è **in anteprima** e **non è supportata** per gli **scenari di produzione**.
- Archiviazione di Azure nei contenitori di Windows supporta il montaggio di **contenitori di file di Azure** (lettura/scrittura).
- Archiviazione di Azure nei contenitori di Windows **non** è attualmente supportata per gli scenari di Bring your own code nei piani di servizio app di Windows.
- Archiviazione di Azure nei contenitori di Windows **non supporta** l'uso della configurazione del **firewall di archiviazione** a causa di limitazioni dell'infrastruttura.
- Archiviazione di Azure nei contenitori di Windows consente **di specificare fino a cinque punti di** montaggio per ogni app.
- L'archiviazione di Azure montata in un'app non è accessibile tramite gli endpoint FTP/FTPs del servizio app. Usare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
- Archiviazione di Azure viene fatturato in modo indipendente e **non incluso** nell'app Web. Scopri di più sui [prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage).

## <a name="link-storage-to-your-web-app-preview"></a>Collegare la risorsa di archiviazione all'app Web (anteprima)

 Per montare una condivisione File di Azure in una directory nell'app del servizio app, usare il [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) comando. Il tipo di archiviazione deve essere risorsa.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

È consigliabile eseguire questa operazione per tutte le altre directory che si desidera collegare a una condivisione File di Azure.

## <a name="verify"></a>Verifica

Una volta che una condivisione di File di Azure è collegata a un'app Web, è possibile verificarla eseguendo il comando seguente:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire la migrazione di un'app ASP.NET al servizio app Azure usando un contenitore Windows (anteprima)](app-service-web-tutorial-windows-containers-custom-fonts.md).
