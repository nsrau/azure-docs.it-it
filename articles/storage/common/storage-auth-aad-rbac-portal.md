---
title: Usare il portale di Azure per assegnare un ruolo Controllo degli accessi in base al ruolo per l'accesso ai datiUse the Azure portal to assign an RBAC role for data access
titleSuffix: Azure Storage
description: Informazioni su come usare il portale di Azure per assegnare autorizzazioni a un'entità di sicurezza di Azure Active Directory con controllo degli accessi in base al ruolo. Archiviazione di Azure supporta ruoli RBAC predefiniti e personalizzati per l'autenticazione tramite Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: d224bd9e9e7b1f8fc9eb45d85e78811d8642fc78
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519549"
---
# <a name="use-the-azure-portal-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Usare il portale di Azure per assegnare un ruolo Controllo degli accessi in base al ruolo per l'accesso ai dati BLOB e codaUse the Azure portal to assign an RBAC role for access to blob and queue data

Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md). Archiviazione di Azure definisce un set di ruoli RBAC predefiniti che includono set comuni di autorizzazioni usate per accedere ai dati BLOB o di coda.

Quando un ruolo Controllo degli accessi in base al ruolo viene assegnato a un'entità di sicurezza di Azure AD, Azure concede l'accesso a tali risorse per tale entità di sicurezza. È possibile definire l'ambito dell'accesso a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda. Un'entità di sicurezza di Azure AD può essere un utente, un gruppo, un'entità servizio dell'applicazione o [un'identità gestita per](../../active-directory/managed-identities-azure-resources/overview.md)le risorse di Azure.An Azure AD security principal may be a user, a group, an application service principal, or a managed identity for Azure resources.

Questo articolo descrive come usare il portale di Azure per assegnare ruoli RBAC. Il portale di Azure offre un'interfaccia semplice per l'assegnazione dei ruoli RBAC e la gestione dell'accesso alle risorse di archiviazione. È anche possibile assegnare ruoli RBAC per le risorse BLOB e coda usando gli strumenti da riga di comando di Azure o le API di gestione di Archiviazione di Azure.You can also assign RBAC roles for blob and queue resources using Azure command-line tools or the Azure Storage management APIs. Per altre informazioni sui ruoli RBAC per le risorse di archiviazione, vedere Autenticare l'accesso a blob e code di Azure usando Azure Active Directory.For more information about RBAC roles for storage resources, see [Authenticate access to Azure blobs and queues using Azure Active Directory.](storage-auth-aad.md)

## <a name="rbac-roles-for-blobs-and-queues"></a>Ruoli Controllo degli accessi in base al ruolo per BLOB e code

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinare l'ambito delle risorse

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Assegnare ruoli RBAC tramite il portale di AzureAssign RBAC roles using the Azure portal

Dopo aver determinato l'ambito appropriato per un'assegnazione di ruolo, passare a tale risorsa nel portale di Azure.After you have determined the appropriate scope for a role assignment, navigate to that resource in the Azure portal. Visualizzare le impostazioni di controllo di **accesso (IAM)** per la risorsa e seguire queste istruzioni per gestire le assegnazioni di ruolo:

1. Assegnare il ruolo RBAC di Archiviazione di Azure appropriato per concedere l'accesso a un'entità di sicurezza di Azure AD.

1. Assegnare il ruolo [Lettore](../../role-based-access-control/built-in-roles.md#reader) di Azure Resource Manager agli utenti che devono accedere a contenitori o code tramite il portale di Azure usando le credenziali di Azure AD. 

Le sezioni seguenti descrivono ognuno di questi passaggi in modo più dettagliato.

> [!NOTE]
> In quanto proprietario dell'account di archiviazione di Azure, non si ricevono automaticamente le autorizzazioni per l'accesso ai dati. È invece necessario assegnare in modo esplicito a se stessi un ruolo di controllo degli accessi in base al ruolo per Archiviazione di Azure. È possibile assegnare questo ruolo a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda.
>
> Non è possibile assegnare un ruolo con ambito a un contenitore o a una coda se nell'account di archiviazione è abilitato uno spazio dei nomi gerarchico.

### <a name="assign-a-built-in-rbac-role"></a>Assegnare un ruolo RBAC incorporatoAssign a built-in RBAC role

Prima di assegnare un ruolo a un'entità di sicurezza, assicurarsi di considerare l'ambito delle autorizzazioni concesse. Esaminare la sezione [Determinare l'ambito delle risorse](#determine-resource-scope) per decidere l'ambito appropriato.

La procedura descritta qui assegna un ruolo con ambito a livello di singolo contenitore, ma è possibile seguire gli stessi passaggi per assegnare un ruolo con ambito a livello di singola coda:

1. Nel [portale di Azure](https://portal.azure.com)passare all'account di archiviazione e visualizzare **Panoramica** per l'account.
1. In Servizi selezionare **BLOB**.
1. Individuare il contenitore per il quale si vuole assegnare un ruolo e visualizzare le impostazioni del contenitore.
1. Selezionare **Controllo di accesso (IAM)** per visualizzare le impostazioni di controllo di accesso per il contenitore. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo.

    ![Schermata che mostra le impostazioni del controllo di accesso del contenitore](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. Fare clic sul pulsante **Aggiungi un'assegnazione di ruolo** per aggiungere un nuovo ruolo.
1. Nella finestra **Aggiungi assegnazione ruolo** selezionare il ruolo Archiviazione di Azure che si vuole assegnare. Quindi cercare per individuare l'entità di sicurezza a cui si desidera assegnare il ruolo.

    ![Screenshot che mostra come assegnare un ruolo di controllo degli accessi in base al ruolo](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Fare clic su **Salva**. L'identità cui è assegnato il ruolo viene visualizzata nell'elenco in corrispondenza del ruolo. Ad esempio, la figura seguente mostra che l'utente aggiunto ha ora autorizzazioni di lettura per i dati nel contenitore denominato *sample-container*.

    ![Schermata che mostra l'elenco degli utenti assegnati a un ruolo](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

È possibile seguire passaggi simili per assegnare un ruolo con ambito all'account di archiviazione, al gruppo di risorse o alla sottoscrizione.

### <a name="assign-the-reader-role-for-portal-access"></a>Assegnare il ruolo Lettore per l'accesso al portaleAssign the Reader role for portal access

Quando si assegna un ruolo predefinito o personalizzato per Archiviazione di Azure a un'entità di sicurezza, si concedono le autorizzazioni a tale entità di sicurezza per eseguire operazioni sui dati nell'account di archiviazione. I ruoli **Lettore dati** incorporati forniscono autorizzazioni di lettura per i dati in un contenitore o in una coda, mentre i ruoli **Collaboratore dati** incorporati forniscono autorizzazioni di lettura, scrittura ed eliminazione per un contenitore o una coda. L'ambito delle autorizzazioni è la risorsa specificata.  
Ad esempio, se si assegna il ruolo **Storage Blob Data Contributor** all'utente Mary a livello di contenitore denominato **sample-container**, a Mary viene concesso l'accesso in lettura, scrittura ed eliminazione a tutti i BLOB in tale contenitore.

Tuttavia, se Mary vuole visualizzare un BLOB nel portale di Azure, il ruolo **Collaboratore dati BLOB** di archiviazione non fornirà autorizzazioni sufficienti per spostarsi nel portale nel BLOB per visualizzarlo. Sono necessarie autorizzazioni aggiuntive di Azure AD per spostarsi all'interno del portale e visualizzare le altre risorse visibili.

Se gli utenti devono essere in grado di accedere ai BLOB nel portale di Azure, assegnare loro un ruolo RBAC aggiuntivo, il ruolo [Lettore,](../../role-based-access-control/built-in-roles.md#reader) a tali utenti, a livello di account di archiviazione o versione superiore. Il ruolo **Lettore** è un ruolo di Azure Resource Manager che consente agli utenti di visualizzare le risorse dell'account di archiviazione, ma non di modificarle. Non fornisce autorizzazioni di lettura ai dati in Archiviazione di Azure, ma solo alle risorse di gestione degli account.

Seguire questi passaggi per assegnare il ruolo Lettore in modo che un utente possa accedere ai BLOB dal portale di Azure.Follow these steps to assign the **Reader** role so that a user can access blobs from the Azure portal. In questo esempio, l'assegnazione ha come ambito l'account di archiviazione:In this example, the assignment is scoped to the storage account:

1. Nel [portale di Azure](https://portal.azure.com)passare all'account di archiviazione.
1. Selezionare **Controllo di accesso (IAM)** per visualizzare le impostazioni di controllo di accesso per l'account di archiviazione. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo.
1. Nella finestra **Aggiungi assegnazione ruolo** selezionare il ruolo **Lettore.In** the Add role assignment window, select the Reader role. 
1. Nel campo **Assegna accesso a** selezionare **Utente, gruppo o entità servizio di Azure AD**.
1. Cercare l'entità di sicurezza a cui si desidera assegnare il ruolo.
1. Salvare l'assegnazione di ruolo.

L'assegnazione del ruolo Lettore è necessaria solo per gli utenti che devono accedere a BLOB o code tramite il portale di Azure.Assigning the **Reader** role is necessary only for users who need to access blobs or queues using the Azure portal.

> [!IMPORTANT]
> La versione di anteprima di Storage Explorer nel portale di Azure non supporta l'uso delle credenziali di Azure AD per visualizzare e modificare i dati BLOB o coda. Storage Explorer nel portale di Azure usa sempre le chiavi dell'account per accedere ai dati. Per usare Storage Explorer nel portale di Azure, è necessario disporre di un ruolo che includa **Microsoft.Storage/storageAccounts/listkeys/action**.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui ruoli RBAC per le risorse di archiviazione, vedere Autenticare l'accesso a blob e code di Azure usando Azure Active Directory.For more information about RBAC roles for storage resources, see [Authenticate access to Azure blobs and queues using Azure Active Directory.](storage-auth-aad.md) 
- Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Che cos'è il controllo degli accessi in base al ruolo?](../../role-based-access-control/overview.md).
- Per informazioni su come assegnare e gestire le assegnazioni di ruolo di controllo degli accessi in base al ruolo con Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST, vedere gli articoli seguenti:
    - [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Gestire il controllo degli accessi in base al ruolo con l'API REST](../../role-based-access-control/role-assignments-rest.md)
- Per informazioni su come autorizzare l'accesso a contenitori e code all'interno delle applicazioni di archiviazione, vedere [Usare Azure AD con applicazioni di Archiviazione di Azure](storage-auth-aad-app.md).
