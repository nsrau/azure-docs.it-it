---
title: Scegliere come autorizzare l'accesso ai dati BLOB nel portale di Azure
titleSuffix: Azure Storage
description: Quando si accede ai dati BLOB usando il portale di Azure, il portale effettua richieste ad archiviazione di Azure dietro le quinte. Queste richieste ad archiviazione di Azure possono essere autenticate e autorizzate usando l'account Azure AD o la chiave di accesso dell'account di archiviazione.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/08/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: contperfq1
ms.openlocfilehash: 6b02f017e08b224f05c372aab834c5dec0cc077f
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90020335"
---
# <a name="choose-how-to-authorize-access-to-blob-data-in-the-azure-portal"></a>Scegliere come autorizzare l'accesso ai dati BLOB nel portale di Azure

Quando si accede ai dati BLOB usando il [portale di Azure](https://portal.azure.com), il portale effettua richieste ad archiviazione di Azure dietro le quinte. È possibile autorizzare una richiesta ad archiviazione di Azure usando l'account Azure AD o la chiave di accesso dell'account di archiviazione. Il portale indica il metodo usato e consente di spostarsi tra i due se si dispone delle autorizzazioni appropriate.  

È anche possibile specificare come autorizzare una singola operazione di caricamento di BLOB nel portale di Azure. Per impostazione predefinita, il portale usa qualsiasi metodo già usato per autorizzare un'operazione di caricamento di BLOB, ma è possibile modificare questa impostazione quando si carica un BLOB.

## <a name="permissions-needed-to-access-blob-data"></a>Autorizzazioni necessarie per accedere ai dati BLOB

A seconda di come si vuole autorizzare l'accesso ai dati BLOB nel portale di Azure, saranno necessarie autorizzazioni specifiche. Nella maggior parte dei casi, queste autorizzazioni vengono fornite tramite il controllo degli accessi in base al ruolo (RBAC). Per altre informazioni su RBAC, vedere informazioni sul [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md).

### <a name="use-the-account-access-key"></a>Usare la chiave di accesso dell'account

Per accedere ai dati BLOB con la chiave di accesso dell'account, è necessario disporre di un ruolo di Azure assegnato che includa l'azione RBAC **Microsoft. storage/storageAccounts/listkeys/Action**. Questo ruolo di Azure può essere un ruolo predefinito o personalizzato. I ruoli predefiniti che supportano **Microsoft. storage/storageAccounts/listkeys/Action** includono:

- Ruolo [proprietario](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager
- Ruolo [collaboratore](../../role-based-access-control/built-in-roles.md#contributor) Azure Resource Manager
- Il ruolo [collaboratore account di archiviazione](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Quando si tenta di accedere ai dati BLOB nel portale di Azure, il portale verifica prima di tutto se è stato assegnato un ruolo con **Microsoft. storage/storageAccounts/listkeys/Action**. Se è stato assegnato un ruolo con questa azione, il portale usa la chiave dell'account per accedere ai dati BLOB. Se a questa azione non è stato assegnato un ruolo, il portale tenta di accedere ai dati tramite l'account Azure AD.

> [!NOTE]
> L'amministratore e il coamministratore del servizio ruoli di amministratore della sottoscrizione classica includono l'equivalente del ruolo [proprietario](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager. Il ruolo **proprietario** include tutte le azioni, tra cui **Microsoft. storage/storageAccounts/listkeys/Action**, in modo che un utente con uno di questi ruoli amministrativi possa accedere anche ai dati BLOB con la chiave dell'account. Per altre informazioni, vedere [Ruoli di amministratore sottoscrizione classico, ruoli di Azure e ruoli di amministratore di Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Usare l'account Azure AD

Per accedere ai dati BLOB dalla portale di Azure usando l'account Azure AD, entrambe le istruzioni seguenti devono essere vere per l'utente:

- Il ruolo di [lettore](../../role-based-access-control/built-in-roles.md#reader) Azure Resource Manager è stato assegnato come minimo all'ambito del livello dell'account di archiviazione o superiore. Il ruolo **Reader** concede le autorizzazioni più limitate, ma è accettabile anche un altro Azure Resource Manager ruolo che concede l'accesso alle risorse di gestione degli account di archiviazione.
- È stato assegnato un ruolo predefinito o personalizzato che fornisce l'accesso ai dati BLOB.

Per consentire all'utente di visualizzare le risorse di gestione degli account di archiviazione nell'portale di Azure, è necessaria l'assegnazione del ruolo **lettore** o un'altra assegnazione di ruolo Azure Resource Manager. I ruoli di Azure che concedono l'accesso ai dati BLOB non concedono l'accesso alle risorse di gestione degli account di archiviazione. Per accedere ai dati BLOB nel portale, l'utente deve disporre delle autorizzazioni per spostarsi tra le risorse dell'account di archiviazione. Per altre informazioni su questo requisito, vedere [assegnare il ruolo lettore per l'accesso al portale](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

I ruoli predefiniti che supportano l'accesso ai dati BLOB includono:

- [Proprietario dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): per il controllo di accesso POSIX per Azure Data Lake storage Gen2.
- [Collaboratore dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): autorizzazioni di lettura/scrittura/eliminazione per i BLOB.
- [Lettore dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): autorizzazioni di sola lettura per i BLOB.

I ruoli personalizzati possono supportare diverse combinazioni delle stesse autorizzazioni fornite dai ruoli predefiniti. Per altre informazioni sulla creazione di ruoli personalizzati di Azure, vedere [ruoli personalizzati di Azure](../../role-based-access-control/custom-roles.md) e [comprendere le definizioni dei ruoli per le risorse di Azure](../../role-based-access-control/role-definitions.md).

> [!IMPORTANT]
> La versione di anteprima di Storage Explorer nel portale di Azure non supporta l'utilizzo di Azure AD credenziali per visualizzare e modificare i dati BLOB. Storage Explorer nella portale di Azure usa sempre le chiavi dell'account per accedere ai dati. Per usare Storage Explorer nel portale di Azure, è necessario disporre di un ruolo che includa **Microsoft. storage/storageAccounts/listkeys/Action**.

## <a name="navigate-to-blobs-in-the-azure-portal"></a>Passare a BLOB nel portale di Azure

Per visualizzare i dati BLOB nel portale, passare alla **Panoramica** dell'account di archiviazione e fare clic sui collegamenti per i **BLOB**. In alternativa, è possibile passare alla sezione **servizio BLOB** nel menu.

:::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Screenshot che illustra come passare ai dati BLOB nel portale di Azure":::

## <a name="determine-the-current-authentication-method"></a>Determinare il metodo di autenticazione corrente

Quando si passa a un contenitore, il portale di Azure indica se si sta usando la chiave di accesso dell'account o l'account Azure AD per l'autenticazione.

### <a name="authenticate-with-the-account-access-key"></a>Eseguire l'autenticazione con la chiave di accesso dell'account

Se si esegue l'autenticazione usando la chiave di accesso dell'account, verrà visualizzata la **chiave di accesso** specificata come metodo di autenticazione nel portale:

:::image type="content" source="media/authorize-blob-access-portal/auth-method-access-key.png" alt-text="Screenshot che mostra l'utente che accede attualmente ai contenitori con la chiave dell'account":::

Per passare a utilizzando l'account Azure AD, fare clic sul collegamento evidenziato nell'immagine. Se si dispone delle autorizzazioni appropriate tramite i ruoli di Azure assegnati, sarà possibile procedere. Tuttavia, se non si dispone delle autorizzazioni appropriate, verrà visualizzato un messaggio di errore simile al seguente:

:::image type="content" source="media/authorize-blob-access-portal/auth-error-azure-ad.png" alt-text="Errore visualizzato se Azure AD account non supporta l'accesso":::

Si noti che nell'elenco non sono presenti BLOB se l'account Azure AD non dispone delle autorizzazioni per visualizzarli. Fare clic sul collegamento **passa a chiave di accesso** per usare di nuovo la chiave di accesso per l'autenticazione.

### <a name="authenticate-with-your-azure-ad-account"></a>Eseguire l'autenticazione con l'account di Azure AD

Se si esegue l'autenticazione usando l'account Azure AD, viene visualizzato **Azure ad account utente** specificato come metodo di autenticazione nel portale:

:::image type="content" source="media/authorize-blob-access-portal/auth-method-azure-ad.png" alt-text="Screenshot che mostra l'utente che accede attualmente ai contenitori con Azure AD account":::

Per passare all'uso della chiave di accesso dell'account, fare clic sul collegamento evidenziato nell'immagine. Se si ha accesso alla chiave dell'account, sarà possibile continuare. Tuttavia, se non si dispone dell'accesso alla chiave dell'account, verrà visualizzato un messaggio di errore simile al seguente:

:::image type="content" source="media/authorize-blob-access-portal/auth-error-access-key.png" alt-text="Errore visualizzato se non si ha accesso alla chiave dell'account":::

Si noti che nell'elenco non sono presenti BLOB se non si ha accesso alle chiavi dell'account. Fare clic sul collegamento **passa a Azure ad account utente** per usare di nuovo l'account Azure ad per l'autenticazione.

## <a name="specify-how-to-authorize-a-blob-upload-operation"></a>Specificare come autorizzare un'operazione di caricamento BLOB

Quando si carica un BLOB dalla portale di Azure, è possibile specificare se autenticare e autorizzare tale operazione con la chiave di accesso dell'account o con le credenziali Azure AD. Per impostazione predefinita, il portale usa il metodo di autenticazione corrente, come illustrato in [determinare il metodo di autenticazione corrente](#determine-the-current-authentication-method).

Per specificare come autorizzare un'operazione di caricamento di BLOB, attenersi alla procedura seguente:

1. Nella portale di Azure passare al contenitore in cui si vuole caricare un BLOB.
1. Selezionare il pulsante **Carica**.
1. Espandere la sezione **Avanzate** per visualizzare le proprietà avanzate per il BLOB.
1. Nel campo **tipo di autenticazione** , indicare se si desidera autorizzare l'operazione di caricamento utilizzando l'account Azure ad o la chiave di accesso dell'account, come illustrato nella figura seguente:

    :::image type="content" source="media/authorize-blob-access-portal/auth-blob-upload.png" alt-text="Screenshot che illustra come modificare il metodo di autorizzazione per il caricamento di BLOB":::

## <a name="next-steps"></a>Passaggi successivi

- [Autenticare l'accesso a BLOB e code di Azure usando Azure Active Directory](../common/storage-auth-aad.md)
- [Concedi l'accesso a contenitori e code di Azure con RBAC nel portale di Azure](../common/storage-auth-aad-rbac-portal.md)
- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo tramite l'interfaccia della riga di comando di Azure](../common/storage-auth-aad-rbac-cli.md)
- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo tramite PowerShell](../common/storage-auth-aad-rbac-powershell.md)
