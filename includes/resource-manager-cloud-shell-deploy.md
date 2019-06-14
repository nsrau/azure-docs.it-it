---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 7c081b3bc5f9e6273f680b24897f9aced4999afa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66129019"
---
## <a name="deploy-template-from-cloud-shell"></a>Distribuire il modello da Cloud Shell

È possibile usare [Cloud Shell](../articles/cloud-shell/overview.md) per distribuire il modello. Per distribuire un modello esterno, specificare l'URI del modello, proprio come si farebbe per qualsiasi distribuzione esterna. Per distribuire un modello locale, è innanzitutto necessario caricare il modello nell'account di archiviazione per Cloud Shell. Questa sezione descrive come caricare il modello nell'account di Cloud Shell e distribuirlo come un file locale. Per informazioni sulla configurazione di Cloud Shell per il primo utilizzo, vedere [Panoramica di Azure Cloud Shell](../articles/cloud-shell/overview.md).

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare il gruppo di risorse di Cloud Shell. Il modello del nome è `cloud-shell-storage-<region>`.

   ![Selezionare il gruppo di risorse](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Selezionare l'account di archiviazione per Cloud Shell.

   ![Selezionare l'account di archiviazione](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Selezionare **BLOB**.

   ![Selezionare BLOB](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Selezionare **+ Contenitore**.

   ![Aggiungere il contenitore](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Assegnare un nome e un livello di accesso al contenitore. Il modello di esempio in questo articolo non contiene informazioni riservate, quindi è consentito l'accesso in lettura anonimo. Selezionare **OK**.

   ![Fornire i valori del contenitore](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Selezionare il contenitore creato.

   ![Selezionare un nuovo contenitore](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Selezionare **Carica**.

   ![Caricamento del BLOB](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Trovare e caricare il modello.

   ![Caricare il file](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Al termine del caricamento, selezionare il modello.

   ![Selezionare un nuovo modello](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Copiare l'URL.

   ![Copia l'URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Aprire il prompt.

   ![Aprire Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
