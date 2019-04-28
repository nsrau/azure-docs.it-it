---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 10/24/2018
ms.author: cephalin
ms.openlocfilehash: e4bc749047bbf0d55fc60a699ac956421775d5b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710017"
---
Nel [portale di Azure](https://portal.azure.com) fare clic su **Gruppi di risorse** > **cloud-shell-storage-\<your_region>** > **\<storage_account_name>**.

![Trovare l'account di archiviazione di Cloud Shell](../articles/app-service/media/app-service-deploy-zip/upload-choose-storage-account.png)

Nella pagina **Panoramica** dell'account di archiviazione selezionare **File**.

Selezionare la condivisione file generata automaticamente e quindi fare clic su **Carica**. La condivisione file viene montata in Cloud Shell come `clouddrive`.

![Trovare il pulsante Carica](../articles/app-service/media/app-service-deploy-zip/upload-select-button.png)

Fare clic sul selettore di file e selezionare il file ZIP, quindi fare clic su **Carica**. 

In Cloud Shell usare `ls` per verificare che il file ZIP caricato sia visualizzato nella condivisione `clouddrive` predefinita.

```azurecli-interactive
ls clouddrive
```
