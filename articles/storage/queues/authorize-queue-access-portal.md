---
title: Scegliere come autorizzare l'accesso ai dati della coda nel portale di Azure
titleSuffix: Azure Storage
description: Quando si accede ai dati della coda usando il portale di Azure, il portale esegue richieste ad archiviazione di Azure dietro le quinte. Queste richieste ad archiviazione di Azure possono essere autenticate e autorizzate usando l'account Azure AD o la chiave di accesso dell'account di archiviazione.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/08/2020
ms.author: tamram
ms.reviewer: ozguns
ms.subservice: queues
ms.custom: contperfq1
ms.openlocfilehash: 2593f1b7ea4cfabe0243fe6f830d718896e68473
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715518"
---
# <a name="choose-how-to-authorize-access-to-queue-data-in-the-azure-portal"></a>Scegliere come autorizzare l'accesso ai dati della coda nel portale di Azure

Quando si accede ai dati della coda usando il [portale di Azure](https://portal.azure.com), il portale esegue richieste ad archiviazione di Azure dietro le quinte. È possibile autorizzare una richiesta ad archiviazione di Azure usando l'account Azure AD o la chiave di accesso dell'account di archiviazione. Il portale indica il metodo usato e consente di spostarsi tra i due se si dispone delle autorizzazioni appropriate.  

## <a name="permissions-needed-to-access-queue-data"></a>Autorizzazioni necessarie per accedere ai dati della coda

A seconda di come si desidera autorizzare l'accesso ai dati della coda nel portale di Azure, saranno necessarie autorizzazioni specifiche. Nella maggior parte dei casi, queste autorizzazioni vengono fornite tramite il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). Per ulteriori informazioni sul controllo degli accessi in base al ruolo di Azure, vedere informazioni sul [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md)di Azure.

### <a name="use-the-account-access-key"></a>Usare la chiave di accesso dell'account

Per accedere ai dati della coda con la chiave di accesso dell'account, è necessario che sia assegnato un ruolo di Azure che includa l'azione RBAC di Azure **Microsoft. storage/storageAccounts/listkeys/Action**. Questo ruolo di Azure può essere un ruolo predefinito o personalizzato. I ruoli predefiniti che supportano **Microsoft. storage/storageAccounts/listkeys/Action** includono:

- Ruolo [proprietario](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager
- Ruolo [collaboratore](../../role-based-access-control/built-in-roles.md#contributor) Azure Resource Manager
- Il ruolo [collaboratore account di archiviazione](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Quando si tenta di accedere ai dati della coda nel portale di Azure, il portale verifica prima di tutto se è stato assegnato un ruolo con **Microsoft. storage/storageAccounts/listkeys/Action**. Se è stato assegnato un ruolo con questa azione, il portale usa la chiave dell'account per accedere ai dati della coda. Se a questa azione non è stato assegnato un ruolo, il portale tenta di accedere ai dati tramite l'account Azure AD.

> [!NOTE]
> L'amministratore e il coamministratore del servizio ruoli di amministratore della sottoscrizione classica includono l'equivalente del ruolo [proprietario](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager. Il ruolo **proprietario** include tutte le azioni, tra cui **Microsoft. storage/storageAccounts/listkeys/Action**, in modo che un utente con uno di questi ruoli amministrativi possa accedere anche ai dati della coda con la chiave dell'account. Per altre informazioni, vedere [Ruoli di amministratore sottoscrizione classico, ruoli di Azure e ruoli di amministratore di Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Usare l'account Azure AD

Per accedere ai dati della coda dalla portale di Azure utilizzando l'account Azure AD, è necessario che entrambe le istruzioni seguenti siano vere:

- Il ruolo di [lettore](../../role-based-access-control/built-in-roles.md#reader) Azure Resource Manager è stato assegnato come minimo all'ambito del livello dell'account di archiviazione o superiore. Il ruolo **Reader** concede le autorizzazioni più limitate, ma è accettabile anche un altro Azure Resource Manager ruolo che concede l'accesso alle risorse di gestione degli account di archiviazione.
- È stato assegnato un ruolo predefinito o personalizzato che consente di accedere ai dati della coda.

Per consentire all'utente di visualizzare le risorse di gestione degli account di archiviazione nell'portale di Azure, è necessaria l'assegnazione del ruolo **lettore** o un'altra assegnazione di ruolo Azure Resource Manager. I ruoli di Azure che concedono l'accesso ai dati della coda non concedono l'accesso alle risorse di gestione degli account di archiviazione. Per accedere ai dati della coda nel portale, l'utente deve disporre delle autorizzazioni per spostarsi tra le risorse dell'account di archiviazione. Per altre informazioni su questo requisito, vedere [assegnare il ruolo lettore per l'accesso al portale](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

I ruoli predefiniti che supportano l'accesso ai dati della coda includono:

- [Collaboratore dati della coda di archiviazione](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): autorizzazioni di lettura/scrittura/eliminazione per le code.
- [Lettore dati della coda di archiviazione](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): autorizzazioni di sola lettura per le code.

I ruoli personalizzati possono supportare diverse combinazioni delle stesse autorizzazioni fornite dai ruoli predefiniti. Per altre informazioni sulla creazione di ruoli personalizzati di Azure, vedere [ruoli personalizzati di Azure](../../role-based-access-control/custom-roles.md) e [comprendere le definizioni dei ruoli per le risorse di Azure](../../role-based-access-control/role-definitions.md).

L'elenco delle code con un ruolo di amministratore della sottoscrizione classico non è supportato. Per elencare le code, è necessario che un utente abbia assegnato loro il ruolo di **lettore** di Azure Resource Manager, il ruolo di **lettore dati della coda di archiviazione** o il ruolo di **collaboratore dati della coda di archiviazione** .

> [!IMPORTANT]
> La versione di anteprima di Storage Explorer nel portale di Azure non supporta l'utilizzo di Azure AD credenziali per visualizzare e modificare i dati della coda. Storage Explorer nella portale di Azure usa sempre le chiavi dell'account per accedere ai dati. Per usare Storage Explorer nel portale di Azure, è necessario disporre di un ruolo che includa **Microsoft. storage/storageAccounts/listkeys/Action**.

## <a name="navigate-to-queues-in-the-azure-portal"></a>Passa alle code nella portale di Azure

Per visualizzare i dati della coda nel portale, passare alla **Panoramica** dell'account di archiviazione e fare clic sui collegamenti per le **Code**. In alternativa, è possibile passare alle sezioni **servizio di Accodamento** nel menu.

:::image type="content" source="media/authorize-queue-access-portal/queue-access-portal.png" alt-text="Screenshot che illustra come passare ai dati della coda nel portale di Azure":::

## <a name="determine-the-current-authentication-method"></a>Determinare il metodo di autenticazione corrente

Quando si passa a una coda, il portale di Azure indica se si sta usando la chiave di accesso dell'account o l'account Azure AD per l'autenticazione.

### <a name="authenticate-with-the-account-access-key"></a>Eseguire l'autenticazione con la chiave di accesso dell'account

Se si esegue l'autenticazione usando la chiave di accesso dell'account, verrà visualizzata la **chiave di accesso** specificata come metodo di autenticazione nel portale:

:::image type="content" source="media/authorize-queue-access-portal/auth-method-access-key.png" alt-text="Screenshot che illustra come passare ai dati della coda nel portale di Azure":::

Per passare a utilizzando l'account Azure AD, fare clic sul collegamento evidenziato nell'immagine. Se si dispone delle autorizzazioni appropriate tramite i ruoli di Azure assegnati, sarà possibile procedere. Tuttavia, se non si dispone delle autorizzazioni appropriate, verrà visualizzato un messaggio di errore simile al seguente:

:::image type="content" source="media/authorize-queue-access-portal/auth-error-azure-ad.png" alt-text="Screenshot che illustra come passare ai dati della coda nel portale di Azure":::

Si noti che nell'elenco non viene visualizzata alcuna coda se l'account Azure AD non dispone delle autorizzazioni per visualizzarli. Fare clic sul collegamento **passa a chiave di accesso** per usare di nuovo la chiave di accesso per l'autenticazione.

### <a name="authenticate-with-your-azure-ad-account"></a>Eseguire l'autenticazione con l'account di Azure AD

Se si esegue l'autenticazione usando l'account Azure AD, viene visualizzato **Azure ad account utente** specificato come metodo di autenticazione nel portale:

:::image type="content" source="media/authorize-queue-access-portal/auth-method-azure-ad.png" alt-text="Screenshot che illustra come passare ai dati della coda nel portale di Azure":::

Per passare all'uso della chiave di accesso dell'account, fare clic sul collegamento evidenziato nell'immagine. Se si ha accesso alla chiave dell'account, sarà possibile continuare. Tuttavia, se non si dispone dell'accesso alla chiave dell'account, il portale di Azure Visualizza un messaggio di errore.

Se non si dispone dell'accesso alle chiavi dell'account, le code non sono elencate nel portale. Fare clic sul collegamento **passa a Azure ad account utente** per usare di nuovo l'account Azure ad per l'autenticazione.

## <a name="next-steps"></a>Passaggi successivi

- [Autenticare l'accesso a BLOB e code di Azure usando Azure Active Directory](../common/storage-auth-aad.md)
- [Usare il portale di Azure per assegnare un ruolo di Azure per l'accesso ai dati BLOB e di Accodamento](../common/storage-auth-aad-rbac-portal.md)
- [Usare l'interfaccia della riga di comando di Azure per assegnare un ruolo di Azure per l'accesso ai dati BLOB e della coda](../common/storage-auth-aad-rbac-cli.md)
- [Usare il modulo Azure PowerShell per assegnare un ruolo di Azure per l'accesso ai dati BLOB e di Accodamento](../common/storage-auth-aad-rbac-powershell.md)
