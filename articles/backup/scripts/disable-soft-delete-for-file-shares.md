---
title: Esempio di script - Disabilitare l'eliminazione temporanea per la condivisione file
description: Informazioni su come usare uno script per disabilitare l'eliminazione temporanea per le condivisioni file in un account di archiviazione.
ms.topic: sample
ms.date: 02/02/2020
ms.openlocfilehash: 172a7bd19ffd07ca7900a457c447362343e5f185
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84121284"
---
# <a name="disable-soft-delete-for-file-shares-in-a-storage-account"></a>Disabilitare l'eliminazione temporanea per le condivisioni file in un account di archiviazione

Questo documento illustra la procedura per disabilitare l'eliminazione temporanea per le condivisioni file in un account di archiviazione.

A tale scopo, seguire questa procedura:

1. Installare ARMClient. Per informazioni sull'installazione, visitare [questo collegamento](https://github.com/projectkudu/ARMClient).

2. Salvare i due file del corpo della richiesta seguenti in una cartella nel computer.

    ```json
    rqbody-enableSoftDelete.json

    {
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled":true,
        "days": 14
        }
    },
    "cors": {
        "corsRules": []
    }
    }

    rqbody-disableSoftDelete.json

    {
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled":false,
        "days": 0
        }
    },
    "cors": {
        "corsRules": []
    }
    }
    ```

3. Tenere a portata di mano l'ID dell'account di archiviazione di Azure Resource Manager (ARM). Ad esempio: `/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare/providers/Microsoft.Storage/storageAccounts/inquirytest`

4. Per accedere con le proprie credenziali eseguire **armclient login**.

5. Ottenere le proprietà di eliminazione temporanea correnti delle condivisioni file nell'account di archiviazione.

    L'operazione GET seguente recupera le proprietà di eliminazione temporanea per le condivisioni file nell'account *inquirytest*:

    ```cmd
    armclient get /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01
    ```

    ```output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/de
    fault",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "cors": {
        "corsRules": []
        },
        "shareDeleteRetentionPolicy": {
        "enabled": true,
        "days": 14
        }
    }
    }
    ```

6. Disabilitare l'eliminazione temporanea per le condivisioni file nell'account di archiviazione.

    L'operazione PUT seguente disabilita le proprietà di eliminazione temporanea per le condivisioni file nell'account *inquirytest*:

    ```cmd
    armclient put /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01 .\rqbody-disableSoftDelete.json
    ```

    ```Output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/de
    fault",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled": false,
        "days": 0
        }
    }
    }
    ```

7. Per riabilitare l'eliminazione temporanea, usare l'esempio seguente.

    L'operazione PUT seguente abilita le proprietà di eliminazione temporanea per le condivisioni file nell'account "inquirytest".

    ```cmd
    armclient put /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01 .\rqbody-EnableSoftDelete.json
    ```

    ```Output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled": true,
        "days": 14
        }
    }
    }
    ```
