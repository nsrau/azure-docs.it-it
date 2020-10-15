---
title: Aggiungere archiviazione di Azure (contenitore)
description: Informazioni su come alleghi una condivisione di rete personalizzata in un'app in contenitori nel servizio app Azure. Condividi i file tra le app, Gestisci il contenuto statico in remoto e accedi localmente e così via.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: d435a33ba45daf2c8a6a42e51c3e0d58f3abc23b
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057757"
---
# <a name="access-azure-storage-as-a-network-share-from-a-container-in-app-service"></a>Accedere ad Archiviazione di Azure come una condivisione di rete da un contenitore nel servizio app

::: zone pivot="container-windows"

Questa guida illustra come aggiungere i file di archiviazione di Azure come condivisione di rete a un contenitore di Windows nel servizio app. Sono supportate solo le condivisioni [file di Azure](../storage/files/storage-how-to-use-files-cli.md) e [i file Premium](../storage/files/storage-how-to-create-premium-fileshare.md) . I vantaggi includono la protezione e la portabilità del contenuto, l'accesso a più app e molteplici metodi di trasferimento.

::: zone-end

::: zone pivot="container-linux"

Questa guida illustra come aggiungere archiviazione di Azure a un servizio app contenitore Linux. I vantaggi includono contenuto protetto, portabilità del contenuto, archiviazione persistente, accesso a più app e più metodi di trasferimento.

::: zone-end

## <a name="prerequisites"></a>Prerequisiti

::: zone pivot="container-windows"

- [Un'app contenitore di Windows esistente nel servizio app Azure](quickstart-custom-container.md)
- [Creare una condivisione file di Azure](../storage/files/storage-how-to-use-files-cli.md)
- [Caricare file in una condivisione file di Azure](../storage/files/storage-files-deployment-guide.md)

::: zone-end

::: zone pivot="container-linux"

- Un [servizio app esistente nell'app Linux](index.yml).
- Un [account di archiviazione di Azure](../storage/common/storage-account-create.md?tabs=azure-cli)
- Una [condivisione file e una directory di Azure](../storage/files/storage-how-to-use-files-cli.md).

::: zone-end

> [!NOTE]
> File di Azure è un archivio non predefinito e fatturato separatamente, non incluso nell'app Web. Non supporta l'uso della configurazione del firewall a causa delle limitazioni dell'infrastruttura.
>

## <a name="limitations"></a>Limitazioni

::: zone pivot="container-windows"

- Archiviazione di Azure nel servizio app è **in anteprima** e **non è supportata** per gli scenari di **produzione**.
- Archiviazione di Azure nel servizio app non è attualmente **supportata** per scenari Bring your own code (app di Windows non in contenitori).
- Archiviazione di Azure nel servizio app **non supporta** l'uso della configurazione del **firewall di archiviazione** a causa di limitazioni dell'infrastruttura.
- Archiviazione di Azure con il servizio app consente **di specificare fino a cinque punti di** montaggio per ogni app.
- L'archiviazione di Azure montata in un'app non è accessibile tramite gli endpoint FTP/FTPs del servizio app. Usare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

::: zone pivot="container-linux"

- Archiviazione di Azure nel servizio app è **in anteprima** per il servizio app in Linux e app Web per contenitori. Non è **supportata** per gli **scenari di produzione**.
- Archiviazione di Azure nel servizio app supporta il montaggio di **contenitori di file di Azure** (lettura/scrittura) e **contenitori BLOB di Azure** (sola lettura)
- Archiviazione di Azure nel servizio app consente **di specificare fino a cinque punti di** montaggio per ogni app.
- L'archiviazione di Azure montata in un'app non è accessibile tramite gli endpoint FTP/FTPs del servizio app. Usare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

## <a name="link-storage-to-your-app"></a>Collegare lo spazio di archiviazione all'app

::: zone pivot="container-windows"

Dopo aver creato l' [account di archiviazione di Azure, la condivisione file e la directory](#prerequisites), è ora possibile configurare l'app con archiviazione di Azure.

Per montare una condivisione File di Azure in una directory nell'app del servizio app, usare il [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) comando. Il tipo di archiviazione deve essere risorsa.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

È consigliabile eseguire questa operazione per tutte le altre directory che si desidera collegare a una condivisione File di Azure.

::: zone-end

::: zone pivot="container-linux"

Dopo aver creato l' [account di archiviazione di Azure, la condivisione file e la directory](#prerequisites), è ora possibile configurare l'app con archiviazione di Azure.

Per montare un account di archiviazione in una directory nell'app del servizio app, usare il [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) comando. Il tipo di archiviazione può essere AzureBlob o AzureFiles. In questo esempio viene usato risorsa. L'impostazione del percorso di montaggio corrisponde alla cartella all'interno del contenitore che si vuole montare nell'archiviazione di Azure. Impostando il valore su'/', l'intero contenitore viene montato in archiviazione di Azure.


> [!CAUTION]
> La directory specificata come percorso di montaggio nell'app Web deve essere vuota. Eventuali contenuti archiviati in questa directory verranno eliminati quando verrà aggiunto un montaggio esterno. Se si esegue la migrazione dei file di un'app esistente, creare un copia di backup dell'app e del relativo contenuto prima di iniziare.
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

È consigliabile eseguire questa operazione per tutte le altre directory che si vogliono collegare a un account di archiviazione.

::: zone-end

## <a name="verify-linked-storage"></a>Verifica archiviazione collegata

Una volta collegata la condivisione all'app, è possibile verificarla eseguendo il comando seguente:

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>Passaggi successivi

::: zone pivot="container-windows"

- [Eseguire la migrazione del software personalizzato al servizio app Azure usando un contenitore personalizzato](tutorial-custom-container.md?pivots=container-windows).

::: zone-end

::: zone pivot="container-linux"

- [Configurare un contenitore personalizzato](configure-custom-container.md?pivots=platform-linux).

::: zone-end
