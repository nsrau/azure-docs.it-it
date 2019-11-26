---
title: Gestire le identità gestite assegnate dall'utente tramite REST-Azure AD
description: Istruzioni dettagliate su come creare, elencare ed eliminare un'identità gestita assegnata dall'utente per eseguire chiamate dell'API REST.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/26/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0b4da7f47181341fce7c5fa5e7a6d239fe3070d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224656"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Creare, elencare ed eliminare un'identità gestita assegnata dall'utente mediante chiamate dell'API REST

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

È possibile usare le identità gestite per le risorse di Azure con i servizi di Azure per l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo spiega come creare, elencare ed eliminare un'identità gestita assegnata dall'utente usando CURL per effettuare chiamate dell'API REST.

## <a name="prerequisites"></a>prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#how-does-it-work)** .
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Se si usa Windows, installare il [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about) oppure usare [Azure Cloud Shell](../../cloud-shell/overview.md) nel portale di Azure.
- Se si usa il [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about) o una [distribuzione Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [installare la console locale di interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
- Se si usa la console locale di interfaccia della riga di comando di Azure, accedere ad Azure tramite `az login` con un account associato alla sottoscrizione di Azure di cui si intende distribuire o recuperare le informazioni sull'identità gestita assegnata dall'utente.
- Recuperare un token di accesso Bearer usando `az account get-access-token` per eseguire le seguenti operazioni relative all'identità gestita assegnata dall'utente.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente 

Per creare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**Intestazioni della richiesta**

|Intestazione della richiesta  |DESCRIZIONE  |
|---------|---------|
|*Content-Type*     | Obbligatorio. Impostare su `application/json`.        |
|*Autorizzazione*     | Obbligatorio. Impostare su un token di accesso `Bearer` valido.        |

**Corpo della richiesta**

|Nome  |DESCRIZIONE  |
|---------|---------|
|location     | Obbligatorio. Percorso della risorsa.        |

## <a name="list-user-assigned-managed-identities"></a>Elencare le identità gestite assegnate dall'utente

Per elencare/leggere un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Operatore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-operator) o [Collaboratore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Intestazione della richiesta  |DESCRIZIONE  |
|---------|---------|
|*Content-Type*     | Obbligatorio. Impostare su `application/json`.        |
|*Autorizzazione*     | Obbligatorio. Impostare su un token di accesso `Bearer` valido.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminare un'identità gestita assegnata dall'utente

Per eliminare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

> [!NOTE]
> L'eliminazione di un'identità gestita assegnata dall'utente non eliminerà il riferimento proveniente da qualunque risorsa a cui è stato assegnato. Per rimuovere un'identità gestita assegnata dall'utente da una macchina virtuale mediante CURL, vedere [Rimuovere un'identità gestita assegnata dall'utente da una macchina virtuale](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Intestazione della richiesta  |DESCRIZIONE  |
|---------|---------|
|*Content-Type*     | Obbligatorio. Impostare su `application/json`.        |
|*Autorizzazione*     | Obbligatorio. Impostare su un token di accesso `Bearer` valido.        |

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come assegnare un'identità gestita assegnata dall'utente a una VM/VMSS di Azure usando CURL, vedere [Configure managed identities for Azure resources on an Azure VM using REST API calls](qs-configure-rest-vm.md#user-assigned-managed-identity) (Configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure mediante chiamate dell'API REST) e [Configure managed identities for Azure resources on a virtual machine scale set using REST API calls](qs-configure-rest-vmss.md#user-assigned-managed-identity) (Configurare le identità gestite per le risorse di Azure in un set di scalabilità di macchine virtuali mediante chiamate dell'API REST).
