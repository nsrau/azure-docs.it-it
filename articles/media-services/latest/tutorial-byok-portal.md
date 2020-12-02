---
title: Usare chiavi gestite dal cliente o BYOK nel portale di Azure con Servizi multimediali
description: In questa esercitazione viene illustrato come usare il portale di Azure per abilitare le chiavi gestite dal cliente o BYOK (Bring Your Own Key) con un account di archiviazione di Servizi multimediali di Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: 003b8e066a6161baedbc70e9becbca23566813ef
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "96013254"
---
# <a name="tutorial-use-the-azure-portal-to-use-customer-managed-keys-or-byok-with-media-services"></a>Esercitazione: Usare chiavi gestite dal cliente o BYOK nel portale di Azure con Servizi multimediali

Con l'API 2020-05-01 è possibile usare una chiave RSA gestita dal cliente con un account di Servizi multimediali di Azure che ha un'identità gestita dal sistema. Questa esercitazione illustra i passaggi da eseguire nel portale di Azure.

I servizi usati sono:

- Archiviazione di Azure
- Insieme di credenziali chiave di Azure
- Servizi multimediali di Azure

Questa esercitazione illustra come usare il portale di Azure per:

> [!div class="checklist"]
> - Creare un gruppo di risorse.
> - Creare un account di archiviazione con un'identità gestita dal sistema.
> - Creare un account di Servizi multimediali con un'identità gestita dal sistema.
> - Creare un insieme di credenziali delle chiavi in cui archiviare una chiave RSA gestita dal cliente.

## <a name="prerequisites"></a>Prerequisiti

Una sottoscrizione di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account per una versione di prova gratuita](https://azure.microsoft.com/free/).

## <a name="system-managed-keys"></a>Chiavi gestite dal sistema

<!-- Create a resource group -->
[!INCLUDE [create a resource group in the portal](./includes/task-create-resource-group-portal.md)]

> [!IMPORTANT]
> Per i passaggi seguenti di creazione dell'account di archiviazione, selezionare l'opzione Chiave gestita dal sistema nelle impostazioni Avanzate.

<!-- Create a media services account -->

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

<!-- Create a key vault -->

[!INCLUDE [create a key vaultl](./includes/task-create-key-vault-portal.md)]

<!-- Enable CMK BYOK on the account -->
[!INCLUDE [enable CMK](./includes/task-enable-cmk-byok-portal.md)]

> [!IMPORTANT]
> Per i passaggi seguenti di crittografia dell'archiviazione, selezionare l'opzione **Chiave gestita dal sistema**.

<!-- Set encryption for storage account -->
[!INCLUDE [Set encryption for storage account](./includes/task-set-storage-encryption-portal.md)]

## <a name="change-the-key"></a>Modificare la chiave

Servizi multimediali rileva automaticamente quando la chiave viene modificata. FACOLTATIVO: Per testare questo processo, creare un'altra versione della stessa chiave. Servizi multimediali dovrebbe rilevare che la chiave è stata cambiata.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare le risorse create e *si vuole quindi interromperne la fatturazione*, eliminarle.

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come:
> [!div class="nextstepaction"]
> [Codificare un file remoto basato su URL ed eseguire lo streaming del video con REST](stream-files-tutorial-with-rest.md)
