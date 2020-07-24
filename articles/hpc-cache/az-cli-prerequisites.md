---
title: Prerequisiti dell'interfaccia della riga di comando di Azure per cache HPC di Azure
description: Procedura di installazione prima di poter usare l'interfaccia della riga di comando di Azure per creare o modificare una cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 7d0844e699c012d987c23a75e2b0874005cf535a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099246"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Configurare l'interfaccia della riga di comando di Azure per cache HPC di Azure

Seguire questi passaggi per preparare l'ambiente prima di usare l'interfaccia della riga di comando di Azure per creare o gestire una cache HPC di Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-azure-cli"></a>Installare l'interfaccia da riga di comando di Azure

La cache HPC di Azure richiede la versione 2,7 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione e le librerie dipendenti installate. Per installare o eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

```azurecli-interactive
az --version
```

### <a name="install-the-azure-hpc-cache-extension"></a>Installare l'estensione della cache HPC di Azure

Le funzioni della cache HPC di Azure non fanno parte della codebase principale, quindi è necessario installare anche il riferimento all'estensione. Seguire le istruzioni riportate di seguito.

1. Accedi

   Accedere con il comando [AZ login](/cli/azure/reference-index#az-login) se si usa una versione installata localmente dell'interfaccia della riga di comando.

    ```azurecli
    az login
    ```

    Seguire le istruzioni visualizzate nel terminale per completare il processo di autenticazione.

   > [!TIP]
   > Se si dispone di più sottoscrizioni, è necessario sceglierne una. Selezionarla quando si avvia una sessione di Cloud Shell nel portale di Azure o seguire le istruzioni [riportate in Introduzione all'interfaccia](/cli/azure/get-started-with-azure-cli#sign-in) della riga di comando di Azure per impostare la sottoscrizione dalla riga di comando.

2. Installare l'estensione dell'interfaccia della riga di comando di Azure

   Le funzioni della cache HPC di Azure vengono fornite come estensione dell'interfaccia della riga di comando di Azure, ma non fanno ancora parte del pacchetto CLI principale. È necessario installare il riferimento all'estensione prima di poterlo usare.

   Estensioni dell'interfaccia della riga di comando di Azure consente di accedere ai comandi sperimentali e non definitivi. Per altre informazioni sulle estensioni, tra cui l'aggiornamento e la disinstallazione, vedere [usare le estensioni con l'interfaccia](/cli/azure/azure-cli-extensions-overview)della riga di comando di Azure.

   Installare l'estensione per la cache HPC di Azure eseguendo il comando seguente:

    ```azurecli-interactive
    az extension add --name hpc-cache
   ```

## <a name="set-default-resource-group-optional"></a>Imposta il gruppo di risorse predefinito (facoltativo)

Per la maggior parte dei comandi della cache HPC è necessario passare il gruppo di risorse della cache. È possibile impostare il gruppo di risorse predefinito usando il comando [AZ Configure](/cli/azure/reference-index#az-configure).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver installato l'estensione dell'interfaccia della riga di comando di Azure ed eseguito l'accesso, è possibile usare l'interfaccia della riga di comando di Azure per creare e gestire i sistemi

* [Creare una cache HPC di Azure](hpc-cache-create.md)
* [Documentazione dell'interfaccia della riga di comando di Azure HPC-cache](/cli/azure/ext/hpc-cache/hpc-cache)
