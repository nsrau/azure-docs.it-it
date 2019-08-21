---
title: Usare la portale di Azure per accedere ai dati BLOB o della coda-archiviazione di Azure
description: Quando si accede ai dati di BLOB o di Accodamento usando il portale di Azure, il portale esegue richieste ad archiviazione di Azure dietro le quinte. Queste richieste ad archiviazione di Azure possono essere autenticate e autorizzate usando l'account Azure AD o la chiave di accesso dell'account di archiviazione.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: abbd436e5d1c88c53af95fd8ba9add20fa67c8e4
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640886"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Usare la portale di Azure per accedere ai dati di BLOB o di Accodamento

Quando si accede ai dati di BLOB o di Accodamento usando il [portale di Azure](https://portal.azure.com), il portale esegue richieste ad archiviazione di Azure dietro le quinte. È possibile autorizzare una richiesta ad archiviazione di Azure usando l'account Azure AD o la chiave di accesso dell'account di archiviazione. Il portale indica il metodo usato e consente di spostarsi tra i due se si dispone delle autorizzazioni appropriate.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Autorizzazioni necessarie per accedere ai dati BLOB o di Accodamento

A seconda di come si vuole autorizzare l'accesso ai dati di BLOB o di Accodamento nella portale di Azure, sono necessarie autorizzazioni specifiche. Nella maggior parte dei casi, queste autorizzazioni vengono fornite tramite il controllo degli accessi in base al ruolo (RBAC). Per ulteriori informazioni su RBAC, vedere [che cos'è il controllo degli accessi in base al ruolo (RBAC)?](../../role-based-access-control/overview.md).

### <a name="account-access-key"></a>Chiave di accesso dell'account

Per accedere ai dati BLOB e di Accodamento con la chiave di accesso dell'account, è necessario che sia stato assegnato un ruolo RBAC che includa l'azione RBAC **Microsoft. storage/storageAccounts/listkeys/Action**. Questo ruolo RBAC può essere un ruolo predefinito o personalizzato. I ruoli predefiniti che supportano **Microsoft. storage/storageAccounts/listkeys/Action** includono:

- Ruolo [proprietario](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager
- Ruolo [collaboratore](../../role-based-access-control/built-in-roles.md#contributor) Azure Resource Manager
- Il ruolo [collaboratore account di archiviazione](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Quando si tenta di accedere ai dati del BLOB o della coda nel portale di Azure, il portale verifica prima di tutto se è stato assegnato un ruolo con **Microsoft. storage/storageAccounts/listkeys/Action**. Se è stato assegnato un ruolo con questa azione, il portale usa la chiave dell'account per accedere ai dati BLOB e di Accodamento. Se a questa azione non è stato assegnato un ruolo, il portale tenta di accedere ai dati tramite l'account Azure AD.

> [!NOTE]
> L'amministratore e il coamministratore del servizio ruoli di amministratore della sottoscrizione classica includono l'equivalente del ruolo [proprietario](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager. Il ruolo **proprietario** include tutte le azioni, tra cui **Microsoft. storage/storageAccounts/listkeys/Action**, in modo che un utente con uno di questi ruoli amministrativi possa accedere anche ai dati BLOB e Queue con la chiave dell'account. Per altre informazioni, vedere [ruoli di amministratore della sottoscrizione classici](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="azure-ad-account"></a>Account Azure AD

Per accedere ai dati BLOB o della coda dalla portale di Azure usando l'account Azure AD, entrambe le istruzioni seguenti devono essere vere per l'utente:

- Il ruolo di [lettore](../../role-based-access-control/built-in-roles.md#reader) Azure Resource Manager è stato assegnato come minimo all'ambito del livello dell'account di archiviazione o superiore. Il ruolo **Reader** concede le autorizzazioni più limitate, ma è accettabile anche un altro Azure Resource Manager ruolo che concede l'accesso alle risorse di gestione degli account di archiviazione.
- È stato assegnato un ruolo predefinito o personalizzato che consente di accedere ai dati di BLOB o di Accodamento.

Per consentire all'utente di visualizzare le risorse di gestione degli account di archiviazione nell'portale di Azure, è necessaria l'assegnazione del ruolo **lettore** o un'altra assegnazione di ruolo Azure Resource Manager. I ruoli RBAC che concedono l'accesso ai dati di BLOB o di Accodamento non concedono l'accesso alle risorse di gestione degli account di archiviazione. Per accedere ai dati BLOB o di accodamento nel portale, l'utente deve disporre delle autorizzazioni per spostarsi tra le risorse dell'account di archiviazione. Per altre informazioni su questo requisito, vedere [assegnare il ruolo lettore per l'accesso al portale](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

I ruoli predefiniti che supportano l'accesso ai dati BLOB o della coda includono:

- [Proprietario dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Per il controllo di accesso POSIX per Azure Data Lake Storage Gen2.
- [Collaboratore dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Autorizzazioni di lettura/scrittura/eliminazione per i BLOB.
- [Lettore dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Autorizzazioni di sola lettura per i BLOB.
- [Collaboratore dati coda di archiviazione](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Autorizzazioni di lettura/scrittura/eliminazione per le code.
- [Lettore dati della coda di archiviazione](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): Autorizzazioni di sola lettura per le code.
    
I ruoli personalizzati possono supportare diverse combinazioni delle stesse autorizzazioni fornite dai ruoli predefiniti. Per altre informazioni sulla creazione di ruoli RBAC personalizzati, vedere [ruoli personalizzati per le risorse di Azure](../../role-based-access-control/custom-roles.md) e informazioni sulle [definizioni di ruolo per le risorse di Azure](../../role-based-access-control/role-definitions.md).

> [!NOTE]
> L'elenco delle code con un ruolo di amministratore della sottoscrizione classico non è supportato. Per elencare le code, è necessario che un utente abbia assegnato loro il ruolo di **lettore** di Azure Resource Manager, il ruolo di **lettore dati della coda di archiviazione** o il ruolo di collaboratore dati della coda di **archiviazione** .

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Passare a BLOB o code nel portale

Per visualizzare i dati di BLOB o di accodamento nel portale, passare alla **Panoramica** dell'account di archiviazione e fare clic sui collegamenti per **BLOB** o **Code**. In alternativa, è possibile passare al **servizio BLOB** e **servizio di Accodamento** sezioni nel menu. 

![Passare a BLOB o dati della coda nel portale di Azure](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Determinare il metodo di autenticazione corrente

Quando si passa a un contenitore o a una coda, il portale di Azure indica se si sta usando la chiave di accesso dell'account o l'account Azure AD per l'autenticazione.

Gli esempi in questa sezione illustrano l'accesso a un contenitore e ai relativi BLOB, ma il portale Visualizza lo stesso messaggio quando si accede a una coda e ai relativi messaggi oppure si elencano le code.

### <a name="account-access-key"></a>Chiave di accesso dell'account

Se si esegue l'autenticazione usando la chiave di accesso dell'account, verrà visualizzata la **chiave di accesso** specificata come metodo di autenticazione nel portale:

![Accesso ai dati del contenitore con la chiave dell'account](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Per passare a utilizzando l'account Azure AD, fare clic sul collegamento evidenziato nell'immagine. Se si dispone delle autorizzazioni appropriate tramite i ruoli RBAC assegnati, sarà possibile procedere. Tuttavia, se non si dispone delle autorizzazioni appropriate, verrà visualizzato un messaggio di errore simile al seguente:

![Errore visualizzato se Azure AD account non supporta l'accesso](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Si noti che nell'elenco non sono presenti BLOB se l'account Azure AD non dispone delle autorizzazioni per visualizzarli. Fare clic sul collegamento **passa a chiave di accesso** per usare di nuovo la chiave di accesso per l'autenticazione.

### <a name="azure-ad-account"></a>Account Azure AD

Se si esegue l'autenticazione usando l'account Azure AD, viene visualizzato **Azure ad account utente** specificato come metodo di autenticazione nel portale:

![Accesso ai dati del contenitore con Azure AD account](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Per passare all'uso della chiave di accesso dell'account, fare clic sul collegamento evidenziato nell'immagine. Se si ha accesso alla chiave dell'account, sarà possibile continuare. Tuttavia, se non si dispone dell'accesso alla chiave dell'account, verrà visualizzato un messaggio di errore simile al seguente:

![Errore visualizzato se non si ha accesso alla chiave dell'account](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Si noti che nell'elenco non sono presenti BLOB se non si ha accesso alle chiavi dell'account. Fare clic sul collegamento **passa a Azure ad account utente** per usare di nuovo l'account Azure ad per l'autenticazione.

## <a name="next-steps"></a>Passaggi successivi

- [Autenticare l'accesso a BLOB e code di Azure usando Azure Active Directory](storage-auth-aad.md)
- [Concedi l'accesso a contenitori e code di Azure con RBAC nel portale di Azure](storage-auth-aad-rbac-portal.md)
- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo tramite l'interfaccia della riga di comando di Azure](storage-auth-aad-rbac-cli.md)
- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo tramite PowerShell](storage-auth-aad-rbac-powershell.md)
