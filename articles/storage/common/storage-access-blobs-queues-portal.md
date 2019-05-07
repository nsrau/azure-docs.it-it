---
title: Usare il portale di Azure per accedere ai blob o Accoda dati - archiviazione di Azure
description: Quando si accedere ai blob o i dati della coda usando il portale di Azure, il portale apporta le richieste ad archiviazione di Azure dietro le quinte. Queste richieste ad archiviazione di Azure possono essere autenticate e autorizzate tramite account di Azure AD o la chiave di accesso di account di archiviazione.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 00f34fa9a1932aebd467163e0ed7441c993387df
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154000"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Usare il portale di Azure per accedere ai dati di accodamento o blob

Quando si accede usando dati di code o blob di [portale di Azure](https://portal.azure.com), il portale apporta le richieste ad archiviazione di Azure dietro le quinte. Queste richieste ad archiviazione di Azure possono essere autenticate e autorizzate tramite account di Azure AD o la chiave di accesso di account di archiviazione. Il portale indica quale metodo di autenticazione si usa e consente di passare tra i due oggetti, se si dispone delle autorizzazioni appropriate.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Autorizzazioni necessarie per accedere ai dati di accodamento o blob

A seconda del modo in cui si desidera eseguire l'autenticazione di accedere ai dati blob o una coda nel portale di Azure, è necessario disporre di autorizzazioni specifiche. Nella maggior parte dei casi, queste autorizzazioni vengono concesse tramite il controllo di accesso basato sui ruoli (RBAC). Per altre informazioni su RBAC, vedere [che cos'è il controllo di accesso basato sui ruoli (RBAC)?](../../role-based-access-control/overview.md).

### <a name="account-access-key"></a>Chiave di accesso account

Per accedere a dati blob e coda con il tasto di scelta account, è necessario disporre di un ruolo RBAC assegnato all'utente che include l'azione di RBAC **Microsoft.Storage/storageAccounts/listkeys/action**. Questo ruolo RBAC può essere incorporato o un ruolo personalizzato. Ruoli predefiniti che supportano **Microsoft.Storage/storageAccounts/listkeys/action** includono:

- Azure Resource Manager [proprietario](../../role-based-access-control/built-in-roles.md#owner) ruolo
- Azure Resource Manager [collaboratore](../../role-based-access-control/built-in-roles.md#contributor) ruolo
- Il [collaboratore Account di archiviazione](../../role-based-access-control/built-in-roles.md#storage-account-contributor) ruolo

Quando si tenta di accedere ai dati di blob o una coda nel portale di Azure, nel portale viene verificato innanzitutto se è stato assegnato un ruolo con **Microsoft.Storage/storageAccounts/listkeys/action**. Se è stato assegnato un ruolo con questa azione, il portale Usa la chiave dell'account per l'accesso ai dati blob e coda. Se si have non è stato assegnato un ruolo con questa azione, il portale tenta di accedere ai dati con il proprio account Azure AD.

> [!NOTE]
> I ruoli di amministratore sottoscrizione classico coamministratore e amministratore del servizio includono l'equivalente di Azure Resource Manager [proprietario](../../role-based-access-control/built-in-roles.md#owner) ruolo. Il **proprietario** ruolo include tutte le azioni, incluse le **Microsoft.Storage/storageAccounts/listkeys/action**, in modo che un utente con uno di questi ruoli amministrativi possa accedere anche i dati blob e coda con il chiave dell'account. Per altre informazioni, vedere [ruoli amministratore sottoscrizione classico](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="azure-ad-account"></a>Account Azure AD

Per accedere ai dati di accodamento o blob dal portale di Azure con il proprio account Azure AD, entrambe le affermazioni seguenti deve essere true per l'utente:

- È stata assegnata Azure Resource Manager [lettore](../../role-based-access-control/built-in-roles.md#reader) ruolo, come minimo, hanno come ambito il livello dell'account di archiviazione o versione successiva. Il **lettore** ruolo concede le autorizzazioni più limitate, ma è accettabile anche un altro ruolo di Azure Resource Manager che concede l'accesso alle risorse di gestione di account di archiviazione.
- Si sono state assegnate di un ruolo predefinito o personalizzato che fornisce accesso ai dati di accodamento o blob.

Il **lettore** assegnazione di ruolo o un'altra assegnazione di ruolo di Azure Resource Manager è necessaria in modo che l'utente può visualizzare e spostarsi tra le risorse di gestione account di archiviazione nel portale di Azure. I ruoli RBAC che concedono l'accesso ai dati di accodamento o blob non concedono l'accesso alle risorse di gestione di account di archiviazione. Per accedere a dati blob o una coda nel portale, l'utente deve avere autorizzazioni per passare le risorse di account di archiviazione. Per altre informazioni su questo requisito, vedere [assegnare il ruolo lettore per l'accesso al portale](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

I ruoli predefiniti che supportano l'accesso ai dati di accodamento o blob includono:

- [Proprietario dei dati Blob di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Per il controllo di accesso POSIX per Azure Data Lake Storage Gen2.
- [Collaboratore ai dati Blob di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Autorizzazioni di lettura, scrittura ed eliminazione per i BLOB.
- [Lettore di dati Blob di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Autorizzazioni di sola lettura per i BLOB.
- [Collaboratore ai dati di archiviazione coda](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Autorizzazioni di lettura, scrittura ed eliminazione per le code.
- [Lettore di dati di archiviazione coda](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): Autorizzazioni di sola lettura per le code.
    
Ruoli personalizzati possono supportare diverse combinazioni delle stesse autorizzazioni fornite per i ruoli predefiniti. Per altre informazioni sulla creazione di ruoli personalizzati, vedere [ruoli personalizzati per le risorse di Azure](../../role-based-access-control/custom-roles.md) e [comprendere le definizioni di ruolo per le risorse di Azure](../../role-based-access-control/role-definitions.md).

> [!NOTE]
> Elencare le code con un ruolo di amministratore sottoscrizione classico non è supportato. Per elencare le code, un utente deve avere assegnato loro Azure Resource Manager **Reader** ruolo, il **lettore di dati della coda di archiviazione** ruolo, o la **collaboratore ai dati della coda di archiviazione** ruolo.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Passare al BLOB o code nel portale

Per visualizzare i dati di blob o una coda nel portale, passare al **Overview** per l'account di archiviazione e fare clic sui collegamenti per **BLOB** o **code**. In alternativa è possibile passare per il **servizio Blob** e **servizio di Accodamento** sezioni nel menu di scelta. 

![Passare al blob o accodamento dei dati nel portale di Azure](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Determinare il metodo di autenticazione corrente

Quando si passa a un contenitore o una coda, il portale di Azure indica se si usa attualmente la chiave di accesso di account o l'account Azure AD per l'autenticazione.

Gli esempi in questa sezione illustrano l'accesso a un contenitore e ai relativi BLOB, ma il portale Visualizza il messaggio stesso quando non si ha accesso a una coda e i relativi messaggi o elencare le code.

### <a name="account-access-key"></a>Chiave di accesso account

Se si esegue l'autenticazione usando il tasto di scelta account, si noterà **chiave di accesso** specificato come metodo di autenticazione nel portale:

![Attualmente l'accesso ai dati del contenitore con la chiave dell'account](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Per iniziare a usare Azure AD account, fare clic sul collegamento evidenziato nell'immagine. Se si dispone delle autorizzazioni appropriate tramite i ruoli RBAC assegnati all'utente, sarà possibile continuare. Tuttavia, se non si dispongono delle autorizzazioni appropriate, noterete un messaggio di errore simile a quello riportato di seguito:

![Errore riportato se account Azure AD non supporta l'accesso](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Si noti che nessun BLOB vengono visualizzati nell'elenco se l'account Azure AD non dispone delle autorizzazioni per visualizzarli. Fare clic sui **passare alla chiave di accesso** collegamento per usare la chiave di accesso per l'autenticazione di nuovo.

### <a name="azure-ad-account"></a>Account Azure AD

Se si esegue l'autenticazione con il proprio account Azure AD, si noterà **Account utente Azure AD** specificato come metodo di autenticazione nel portale:

![Attualmente l'accesso ai dati del contenitore con account Azure AD](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Per passare a usando la chiave di accesso di account, fare clic sul collegamento evidenziato nell'immagine. Se si ha accesso per la chiave dell'account, quindi sarà possibile continuare. Tuttavia, se non si dispone di accesso per la chiave dell'account, noterete un messaggio di errore simile a quello riportato di seguito:

![Errore riportato se si ha accesso alla chiave dell'account](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Si noti che nessun BLOB vengono visualizzati nell'elenco, se non hai accesso alle chiavi di account. Fare clic sui **passare all'Account utente di Azure AD** collegamento da usare all'account Azure AD per l'autenticazione di nuovo.

## <a name="next-steps"></a>Passaggi successivi

- [Autenticare l'accesso a BLOB di Azure e code con Azure Active Directory](storage-auth-aad.md)
- [Concedere l'accesso a contenitori di Azure e code con accessi nel portale di Azure](storage-auth-aad-rbac-portal.md)
- [Concedere l'accesso a dati blob e di Accodamento di Azure con RBAC tramite la CLI di Azure](storage-auth-aad-rbac-cli.md)
- [Concedere l'accesso a dati blob e di Accodamento di Azure con RBAC tramite PowerShell](storage-auth-aad-rbac-powershell.md)
