---
title: "Esercitazione: Creare un ruolo personalizzato per le risorse di Azure tramite l'interfaccia della riga di comando di Azure"
description: Questa esercitazione descrive come creare un ruolo personalizzato per le risorse di Azure con l'interfaccia della riga di comando di Azure.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.openlocfilehash: 29b0bea851466b5173711441a5a7927c796c9926
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74419588"
---
# <a name="tutorial-create-a-custom-role-for-azure-resources-using-azure-cli"></a>Esercitazione: creare un ruolo personalizzato per le risorse di Azure tramite l'interfaccia della riga di comando di Azure

Se i [ruoli predefiniti per le risorse di Azure](built-in-roles.md) non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare ruoli personalizzati. Per questa esercitazione viene creato un ruolo personalizzato denominato Reader Support Tickets con l'interfaccia della riga di comando di Azure. Il ruolo personalizzato consente all'utente di visualizzare tutti gli elementi nel piano di gestione di una sottoscrizione e anche di aprire ticket di supporto.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un ruolo personalizzato
> * Elencare ruoli personalizzati
> * Aggiornare un ruolo personalizzato
> * Eliminare un ruolo personalizzato

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

- Avere le autorizzazioni per creare ruoli personalizzati, ad esempio [Proprietario](built-in-roles.md#owner) o [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) o [interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)

## <a name="sign-in-to-azure-cli"></a>Accedere all'interfaccia della riga di comando di Azure

Accedere all'[interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli).

## <a name="create-a-custom-role"></a>Creare un ruolo personalizzato

Il modo più semplice per creare un ruolo personalizzato è quello di iniziare con un modello JSON, aggiungere le modifiche e quindi creare un nuovo ruolo.

1. Esaminare l'elenco delle operazioni per il [provider di risorse Microsoft.Support](resource-provider-operations.md#microsoftsupport). È utile conoscere le operazioni disponibili per creare le autorizzazioni.

    | Operazione | DESCRIZIONE |
    | --- | --- |
    | Microsoft.Support/register/action | Esegue la registrazione al provider di risorse di supporto |
    | Microsoft.Support/supportTickets/read | Ottiene i dettagli del ticket di supporto (inclusi stato, gravità, dettagli di contatto e comunicazioni) oppure ottiene l'elenco dei ticket di supporto dalle diverse sottoscrizioni. |
    | Microsoft.Support/supportTickets/write | Crea o aggiorna un ticket di supporto. È possibile creare un ticket di supporto per problemi associati ai settori Tecnico, Fatturazione, Quote o Gestione della sottoscrizione. È possibile aggiornare la gravità, i dettagli di contatto e le comunicazioni dei ticket di supporto esistenti. |
    
1. Creare un nuovo file denominato **ReaderSupportRole.json**.

1. Aprire ReaderSupportRole.json in un editor e aggiungere il codice JSON seguente.

    Per informazioni sulle diverse proprietà, vedere [Ruoli personalizzati per le risorse di Azure](custom-roles.md).

    ```json
    {
      "Name": "",
      "IsCustom": true,
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId1}"
      ]
    }
    ```
    
1. Aggiungere le operazioni seguenti alla proprietà `Actions`. Queste azioni consentono all'utente di visualizzare tutti gli elementi nella sottoscrizione e di creare i ticket di supporto.

    ```
    "*/read",
    "Microsoft.Support/*"
    ```

1. Ottenere l'ID della sottoscrizione con il comando [az account list](/cli/azure/account#az-account-list).

    ```azurecli
    az account list --output table
    ```

1. In `AssignableScopes` sostituire `{subscriptionId1}` con l'ID sottoscrizione.

    È necessario aggiungere ID sottoscrizione espliciti. In caso contrario non sarà possibile importare il ruolo nella sottoscrizione.

1. Modificare le proprietà `Name` e `Description` con "Reader Support Tickets" e "View everything in the subscription and also open support tickets".

    Il file JSON deve apparire come segue:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. Per creare il nuovo ruolo personalizzato, usare il comando [az role definition create](/cli/azure/role/definition#az-role-definition-create) e specificare il file di definizione del ruolo JSON.

    ```azurecli
    az role definition create --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

    Il nuovo ruolo personalizzato è ora disponibile e può essere assegnato a utenti, gruppi o entità servizio come i ruoli predefiniti.

## <a name="list-custom-roles"></a>Elencare ruoli personalizzati

- Per elencare tutti i ruoli personalizzati, usare il comando [az role definition list](/cli/azure/role/definition#az-role-definition-list) con il parametro `--custom-role-only`.

    ```azurecli
    az role definition list --custom-role-only true
    ```
    
    ```Output
    [
      {
        "additionalProperties": {},
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ],
        "description": "View everything in the subscription and also open support tickets.",
        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
        "name": "22222222-2222-2222-2222-222222222222",
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Support/*",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Insights/diagnosticSettings/*/read"
            ],
            "additionalProperties": {},
            "dataActions": [],
            "notActions": [],
            "notDataActions": []
          }
        ],
        "roleName": "Reader Support Tickets",
        "roleType": "CustomRole",
        "type": "Microsoft.Authorization/roleDefinitions"
      }
    ]
    ```
    
    È anche possibile visualizzare il ruolo personalizzato nel portale di Azure.

    ![Screenshot del ruolo personalizzato importato nel portale di Azure](./media/tutorial-custom-role-cli/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Aggiornare un ruolo personalizzato

Per aggiornare il ruolo personalizzato, aggiornare il file JSON e quindi aggiornare il ruolo personalizzato.

1. Aprire il file ReaderSupportRole.json.

1. In `Actions` aggiungere l'operazione per creare e gestire le distribuzioni di gruppi di risorse `"Microsoft.Resources/deployments/*"`. Assicurarsi di includere una virgola dopo l'operazione precedente.

    Il file JSON aggiornato deve apparire come segue:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. Per aggiornare il ruolo personalizzato, usare il comando [az role definition update](/cli/azure/role/definition#az-role-definition-update) e specificare il file JSON aggiornato.

    ```azurecli
    az role definition update --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*",
            "Microsoft.Resources/deployments/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```
    
## <a name="delete-a-custom-role"></a>Eliminare un ruolo personalizzato

- Usare il comando [az role definition delete](/cli/azure/role/definition#az-role-definition-delete) e specificare il nome del ruolo o l'ID ruolo per eliminare il ruolo personalizzato.

    ```azurecli
    az role definition delete --name "Reader Support Tickets"
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare ruoli personalizzati per le risorse di Azure tramite l'interfaccia della riga di comando di Azure](custom-roles-cli.md)