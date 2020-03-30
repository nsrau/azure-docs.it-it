---
title: Usare il portale di Azure per accedere ai dati BLOB o codaUse the Azure portal to access blob or queue data
titleSuffix: Azure Storage
description: Quando si accede ai dati BLOB o coda tramite il portale di Azure, il portale effettua richieste ad Archiviazione di Azure sotto le coperte. Queste richieste ad Archiviazione di Azure possono essere autenticate e autorizzate usando l'account Azure AD o la chiave di accesso dell'account di archiviazione.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 602be49ef0c60274f1cd016c4f8e870cf033ec7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75866903"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Usare il portale di Azure per accedere ai dati BLOB o codaUse the Azure portal to access blob or queue data

Quando si accede ai dati BLOB o coda tramite il portale di [Azure,](https://portal.azure.com)il portale effettua richieste ad Archiviazione di Azure sotto le coperte. Una richiesta ad Archiviazione di Azure può essere autorizzata usando l'account Azure AD o la chiave di accesso dell'account di archiviazione. Il portale indica il metodo in uso e consente di passare da uno dei due metodi se si dispone delle autorizzazioni appropriate.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Autorizzazioni necessarie per accedere ai dati BLOB o coda

A seconda di come si vuole autorizzare l'accesso ai dati BLOB o coda nel portale di Azure, sono necessarie autorizzazioni specifiche. Nella maggior parte dei casi, queste autorizzazioni vengono fornite tramite il controllo degli accessi in base al ruolo. Per ulteriori informazioni sul controllo degli accessi in base al ruolo, vedere [Che cos'è](../../role-based-access-control/overview.md)il controllo degli accessi in base al ruolo?

### <a name="account-access-key"></a>Chiave di accesso dell'account

Per accedere ai dati BLOB e coda con la chiave di accesso dell'account, è necessario disporre di un ruolo RBAC assegnato che include l'azione RBAC **Microsoft.Storage/storageAccounts/listkeys/action**. Questo ruolo RBAC può essere un ruolo predefinito o personalizzato. I ruoli predefiniti che supportano **Microsoft.Storage/storageAccounts/listkeys/action** includono:Built-in roles that support Microsoft.Storage/storageAccounts/listkeys/action include:

- Ruolo Proprietario di Azure Resource ManagerThe Azure Resource Manager [Owner](../../role-based-access-control/built-in-roles.md#owner) role
- Ruolo [Collaboratore](../../role-based-access-control/built-in-roles.md#contributor) di Azure Resource ManagerThe Azure Resource Manager Contributor role
- Ruolo [Collaboratore account di archiviazioneThe Storage Account Contributor](../../role-based-access-control/built-in-roles.md#storage-account-contributor) role

Quando si tenta di accedere ai dati BLOB o coda nel portale di Azure, il portale controlla innanzitutto se è stato assegnato un ruolo con **Microsoft.Storage/storageAccounts/listkeys/action**. Se è stato assegnato un ruolo con questa azione, il portale usa la chiave account per l'accesso ai dati BLOB e coda. Se non è stato assegnato un ruolo con questa azione, il portale tenta di accedere ai dati usando l'account Azure AD.

> [!NOTE]
> I ruoli di amministratore della sottoscrizione classica Amministratore del servizio e Coamministratore includono l'equivalente del ruolo Proprietario di Azure Resource Manager.The classic subscription administrator roles Service Administrator and Co-Administrator include the equivalent of the Azure Resource Manager [Owner](../../role-based-access-control/built-in-roles.md#owner) role. Il ruolo **Proprietario** include tutte le azioni, incluse **Microsoft.Storage/storageAccounts/listkeys/action**, in modo che un utente con uno di questi ruoli amministrativi possa accedere anche ai dati BLOB e coda con la chiave dell'account. Per ulteriori informazioni, vedere Ruoli di [amministratore della sottoscrizione classica](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="azure-ad-account"></a>Account Azure AD

Per accedere ai dati BLOB o coda dal portale di Azure usando l'account Azure AD, entrambe le istruzioni seguenti devono essere vere:

- Il ruolo [Lettore](../../role-based-access-control/built-in-roles.md#reader) di Azure Resource Manager è stato assegnato almeno al minimo, al livello dell'account di archiviazione o superiore. Il ruolo **Lettore** concede le autorizzazioni più limitate, ma è accettabile anche un altro ruolo di Azure Resource Manager che concede l'accesso alle risorse di gestione degli account di archiviazione.
- È stato assegnato un ruolo predefinito o personalizzato che fornisce l'accesso ai dati BLOB o della coda.

L'assegnazione di ruolo Lettore o un'altra assegnazione di ruolo di Azure Resource Manager è necessaria in modo che l'utente possa visualizzare ed esplorare le risorse di gestione degli account di archiviazione nel portale di Azure.The **Reader** role assignment or another Azure Resource Manager role assignment is necessary so that the user can view and navigate storage account management resources in the Azure portal. I ruoli RBAC che concedono l'accesso ai dati BLOB o coda non concedono l'accesso alle risorse di gestione degli account di archiviazione. Per accedere ai dati BLOB o coda nel portale, l'utente deve disporre delle autorizzazioni per esplorare le risorse dell'account di archiviazione. Per ulteriori informazioni su questo requisito, vedere [Assegnare il ruolo Lettore per l'accesso al portale.](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)

I ruoli predefiniti che supportano l'accesso ai dati blob o coda includono:The built-in roles that support access to your blob or queue data include:

- [Storage Blob Data Owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Per il controllo di accesso POSIX per Azure Data Lake Storage Gen2.
- [Collaboratore dati di archiviazione:](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)autorizzazioni di lettura/scrittura/eliminazione per i BLOB.
- [Lettore di dati BLOB di archiviazione:](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)autorizzazioni di sola lettura per i BLOB.
- [Storage Queue Data Contributor :](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor)autorizzazioni di lettura/scrittura/eliminazione per le code.
- [Lettore dati coda di](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader)archiviazione: autorizzazioni di sola lettura per le code.
    
I ruoli personalizzati possono supportare diverse combinazioni delle stesse autorizzazioni fornite dai ruoli predefiniti. Per altre informazioni sulla creazione di ruoli RBAC personalizzati, vedere Ruoli personalizzati per le risorse di Azure e Informazioni sulle definizioni dei ruoli per le risorse di Azure.For more information about creating custom RBAC roles, see [Custom roles for Azure resources](../../role-based-access-control/custom-roles.md) and Understand role definitions for Azure [resources.](../../role-based-access-control/role-definitions.md)

L'elenco delle code con un ruolo di amministratore della sottoscrizione classica non è supportato. Per elencare le code, è necessario che un utente abbia assegnato loro il ruolo **Lettore** risorse di Azure, il ruolo **Lettore dati coda di archiviazione** o il ruolo **Collaboratore dati coda di archiviazione.**

> [!IMPORTANT]
> La versione di anteprima di Storage Explorer nel portale di Azure non supporta l'uso delle credenziali di Azure AD per visualizzare e modificare i dati BLOB o coda. Storage Explorer nel portale di Azure usa sempre le chiavi dell'account per accedere ai dati. Per usare Storage Explorer nel portale di Azure, è necessario disporre di un ruolo che includa **Microsoft.Storage/storageAccounts/listkeys/action**.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Passare a BLOB o code nel portale

Per visualizzare i dati BLOB o delle code nel portale, passare alla **Panoramica** dell'account di archiviazione e fare clic sui collegamenti per **BLOB** o **code.** In alternativa, è possibile passare alle sezioni **Servizio BLOB** e Servizio **di coda** nel menu. 

![Passare ai dati BLOB o coda nel portale di AzureNavigate to blob or queue data in the Azure portal](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Determinare il metodo di autenticazione corrente

Quando si passa a un contenitore o a una coda, il portale di Azure indica se si usa attualmente la chiave di accesso dell'account o l'account Azure AD per l'autenticazione.

Gli esempi in questa sezione mostrano l'accesso a un contenitore e ai relativi BLOB, ma il portale visualizza lo stesso messaggio quando si accede a una coda e ai relativi messaggi o si elencano le code.

### <a name="account-access-key"></a>Chiave di accesso dell'account

Se si esegue l'autenticazione utilizzando la chiave di accesso dell'account, nel portale verrà visualizzato Chiave di **accesso** specificata come metodo di autenticazione:

![Accesso attualmente ai dati del contenitore con la chiave dell'account](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Per passare all'uso dell'account Azure AD, fare clic sul collegamento evidenziato nell'immagine. Se si dispone delle autorizzazioni appropriate tramite i ruoli RBAC assegnati all'utente, sarà possibile procedere. Tuttavia, se non si dispone delle autorizzazioni corrette, verrà visualizzato un messaggio di errore simile al seguente:

![Errore visualizzato se l'account Azure AD non supporta l'accesso](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Si noti che nessun BLOB viene visualizzato nell'elenco se l'account Azure AD non dispone delle autorizzazioni per visualizzarli. Fare clic sul collegamento **Passa per accedere alla chiave** per utilizzare nuovamente la chiave di accesso per l'autenticazione.

### <a name="azure-ad-account"></a>Account Azure AD

Se si esegue l'autenticazione usando l'account Azure AD, verrà visualizzato **L'account utente** di Azure AD specificato come metodo di autenticazione nel portale:If you are authenticating using your Azure AD account, you'll see Azure AD User Account specified as the authentication method in the portal:

![Accesso corrente ai dati del contenitore con l'account Azure ADCurrently accessing container data with Azure AD account](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Per passare all'utilizzo del tasto di scelta dell'account, fare clic sul collegamento evidenziato nell'immagine. Se hai accesso alla chiave dell'account, allora sarai in grado di procedere. Tuttavia, se non si dispone dell'accesso alla chiave dell'account, verrà visualizzato un messaggio di errore simile al seguente:

![Errore visualizzato se non si dispone dell'accesso alla chiave dell'account](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Si noti che nessun BLOB viene visualizzato nell'elenco se non si ha accesso alle chiavi dell'account. Fare clic sul collegamento **Passa all'account utente** di Azure AD per usare nuovamente l'account Azure AD per l'autenticazione.

## <a name="next-steps"></a>Passaggi successivi

- [Autenticare l'accesso ai BLOB e alle code di Azure usando Azure Active DirectoryAuthenticate access to Azure blobs and queues using Azure Active Directory](storage-auth-aad.md)
- [Concedere l'accesso ai contenitori e alle code di Azure con il controllo degli accessi in base al ruolo nel portale di AzureGrant access to Azure containers and queues with RBAC in the Azure portal](storage-auth-aad-rbac-portal.md)
- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo tramite l'interfaccia della riga di comando di Azure](storage-auth-aad-rbac-cli.md)
- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo tramite PowerShell](storage-auth-aad-rbac-powershell.md)
