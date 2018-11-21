---
title: Come gestire le identità gestite assegnate dall'utente di Azure mediante REST
description: Istruzioni dettagliate su come creare, elencare ed eliminare un'identità gestita assegnata dall'utente per eseguire chiamate dell'API REST.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/26/2018
ms.author: daveba
ms.openlocfilehash: 4bf77cd34ba985dfcfa568db0543150c0510c406
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300099"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Creare, elencare ed eliminare un'identità gestita assegnata dall'utente mediante chiamate dell'API REST

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

È possibile usare le identità gestite per le risorse di Azure con i servizi di Azure per l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo spiega come creare, elencare ed eliminare un'identità gestita assegnata dall'utente usando CURL per effettuare chiamate dell'API REST.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Se si usa Windows, installare il [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about) oppure usare [Azure Cloud Shell](../../cloud-shell/overview.md) nel portale di Azure.
- Se si usa il [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about) o una [distribuzione Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [installare la console locale di interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
- Se si usa la console locale di interfaccia della riga di comando di Azure, accedere ad Azure tramite `az login` con un account associato alla sottoscrizione di Azure di cui si intende distribuire o recuperare le informazioni sull'identità gestita assegnata dall'utente.
- Recuperare un token di accesso Bearer usando `az account get-access-token` per eseguire le seguenti operazioni relative all'identità gestita assegnata dall'utente.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente 

Per creare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Per creare un'identità gestita assegnata dall'utente, usare la seguente richiesta CURL all'API di Azure Resource Manager. Sostituire i valori `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>`, `<LOCATION>` e `<ACCESS TOKEN>` con valori personalizzati:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="list-user-assigned-managed-identities"></a>Elencare le identità gestite assegnate dall'utente

Per elencare/leggere un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Operatore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-operator) o [Collaboratore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Per elencare le identità gestite assegnate dall'utente, usare la seguente richiesta CURL all'API di Azure Resource Manager. Sostituire i valori `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` e `<ACCESS TOKEN>` con valori personalizzati:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```
## <a name="delete-a-user-assigned-managed-identity"></a>Eliminare un'identità gestita assegnata dall'utente

Per eliminare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Per eliminare un'identità gestita assegnata dall'utente, usare la seguente richiesta CURL all'API di Azure Resource Manager. Sostituire i valori dei parametri `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` e `<ACCESS TOKEN>` con valori personalizzati:

> [!NOTE]
> L'eliminazione di un'identità gestita assegnata dall'utente non eliminerà il riferimento proveniente da qualunque risorsa a cui è stato assegnato. Per eliminare un'identità gestita assegnata dall'utente da una macchina virtuale mediante CURL, vedere [Rimuovere un'identità assegnata dall'utente da una macchina virtuale di Azure](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come assegnare un'identità gestita assegnata dall'utente a una VM/VMSS di Azure usando CURL, vedere [Configure managed identities for Azure resources on an Azure VM using REST API calls](qs-configure-rest-vm.md#user-assigned-managed-identity) (Configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure mediante chiamate dell'API REST) e [Configure managed identities for Azure resources on a virtual machine scale set using REST API calls](qs-configure-rest-vmss.md#user-assigned-managed-identity) (Configurare le identità gestite per le risorse di Azure in un set di scalabilità di macchine virtuali mediante chiamate dell'API REST).


