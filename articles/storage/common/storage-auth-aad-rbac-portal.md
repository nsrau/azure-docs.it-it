---
title: Usare il portale di Azure per gestire i diritti di accesso di Azure AD per i dati blob e coda con RBAC - archiviazione di Azure | Microsoft Docs
description: Usare il controllo di accesso basato sui ruoli (RBAC) dal portale di Azure per assegnare l'accesso a contenitori e le code per entità di sicurezza. Archiviazione di Azure supporta i ruoli RBAC predefiniti e personalizzati per l'autenticazione tramite Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 8214ff821bad8a46eb710c8b9506d337715db103
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58450042"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-in-the-azure-portal"></a>Concedere l'accesso a Azure blob e Accodamento dei dati con accessi nel portale di Azure

Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md). Archiviazione di Azure definisce un set di ruoli RBAC predefiniti che includono set di autorizzazioni utilizzate per accedere ai dati blob o di Accodamento comuni. 

Quando viene assegnato un ruolo RBAC a un'entità di sicurezza di Azure AD, Azure concede l'accesso a tali risorse per tale entità di sicurezza. È possibile definire l'ambito dell'accesso a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda. Un'entità di sicurezza di Azure AD può essere un utente, un gruppo, un'entità servizio dell'applicazione, o un [identità per le risorse di Azure gestito](../../active-directory/managed-identities-azure-resources/overview.md).

Questo articolo descrive come usare il portale di Azure per assegnare i ruoli RBAC. Il portale di Azure fornisce una semplice interfaccia per l'assegnazione dei ruoli RBAC e la gestione dell'accesso alle risorse di archiviazione. È anche possibile assegnare i ruoli RBAC per le risorse blob e coda usando gli strumenti da riga di comando di Azure o le API di gestione di archiviazione di Azure. Per altre informazioni sui ruoli RBAC per le risorse di archiviazione, vedere [autentica l'accesso ad Azure di BLOB e code usando Azure Active Directory](storage-auth-aad.md). 

## <a name="rbac-roles-for-blobs-and-queues"></a>Ruoli Controllo degli accessi in base al ruolo per BLOB e code

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinare l'ambito di risorse 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Assegnare ruoli RBAC tramite il portale di Azure

Dopo aver determinato l'ambito appropriato per un'assegnazione di ruolo, passare a tale risorsa nel portale di Azure. Visualizzare il **controllo di accesso (IAM)** impostazioni per la risorsa e seguire queste istruzioni per gestire le assegnazioni di ruolo:

1. Assegnare il ruolo RBAC di archiviazione di Azure appropriato per concedere l'accesso a un'entità di sicurezza di Azure AD.

1. Azure Resource Manager assegnare [lettore](../../role-based-access-control/built-in-roles.md#reader) ruolo agli utenti che devono accedere a contenitori o le code usando il portale di Azure usando le proprie credenziali di Azure AD. 

Le sezioni seguenti descrivono ognuno di questi passaggi in modo più dettagliato.

### <a name="assign-a-built-in-rbac-role"></a>Assegnare un ruolo RBAC predefinito

Prima di assegnare un ruolo a un'entità di sicurezza, assicurarsi di prendere in considerazione l'ambito delle autorizzazioni che si stanno concedendo. Rivedere le [determinano l'ambito di risorse](#determine-resource-scope) sezione per decidere l'ambito appropriato.

La procedura descritta qui assegna un ruolo con ambito a livello di singolo contenitore, ma è possibile seguire gli stessi passaggi per assegnare un ruolo con ambito a livello di singola coda: 

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione e visualizzare la sezione **Panoramica** per l'account.
1. In Servizi selezionare **BLOB**. 
1. Individuare il contenitore per il quale si vuole assegnare un ruolo e visualizzare le impostazioni del contenitore. 
1. Selezionare **Controllo di accesso (IAM)** per visualizzare le impostazioni di controllo di accesso per il contenitore. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo.

    ![Screenshot che mostra le impostazioni di controllo di accesso contenitore](media/storage-auth-aad-rbac-portal/portal-access-control-container.png)

1. Fare clic sul pulsante **Aggiungi un'assegnazione di ruolo** per aggiungere un nuovo ruolo.
1. Nel **aggiungere un'assegnazione di ruolo** finestra, selezionare il ruolo di archiviazione di Azure che si desidera assegnare. Quindi eseguire una ricerca per individuare l'entità di sicurezza a cui si desidera assegnare il ruolo.

    ![Screenshot che mostra come assegnare un ruolo di controllo degli accessi in base al ruolo](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Fare clic su **Save**. L'identità cui è assegnato il ruolo viene visualizzata nell'elenco in corrispondenza del ruolo. Ad esempio, la figura seguente mostra che l'utente aggiunto ha ora autorizzazioni di lettura per i dati nel contenitore denominato *sample-container*.

    ![Screenshot che illustra l'elenco di utenti assegnati a un ruolo](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

È possibile seguire la procedura per assegnare un ruolo con ambito per l'account di archiviazione, gruppo di risorse o sottoscrizione.

> [!NOTE]
> In quanto proprietario dell'account di archiviazione di Azure, non si ricevono automaticamente le autorizzazioni per l'accesso ai dati. È invece necessario assegnare in modo esplicito a se stessi un ruolo di controllo degli accessi in base al ruolo per Archiviazione di Azure. È possibile assegnare questo ruolo a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda.
> 
> È possibile assegnare un ruolo con ambito limitato a un contenitore o coda se l'account di archiviazione ha uno spazio dei nomi gerarchico abilitata.

### <a name="assign-the-reader-role-for-portal-access"></a>Assegnare il ruolo lettore per l'accesso al portale

Quando si assegna un ruolo predefinito o personalizzato per l'archiviazione di Azure a un'entità di sicurezza, si siano concedendo le autorizzazioni per tale entità di sicurezza per eseguire operazioni sui dati nell'account di archiviazione. L'elemento predefinito **lettore di dati** ruoli forniscono le autorizzazioni di lettura per i dati in un contenitore o coda, mentre l'oggetto incorporato **collaboratore Data** ruoli forniscono leggere, scrivere ed eliminare le autorizzazioni per un contenitore o coda. Le autorizzazioni sono limitate alla risorsa specificata.  

Ad esempio, se si assegna il **collaboratore ai dati di archiviazione Blob** ruolo all'utente Mary a livello di un contenitore denominato **esempio-container**, quindi Mary è concesso di lettura, scrittura ed eliminare l'accesso a tutti i BLOB presenti in tale contenitore.

Tuttavia, se Mary desideri visualizzare un blob nel portale di Azure, il **collaboratore ai dati Blob di archiviazione** ruolo di per sé non fornisce le autorizzazioni sufficienti per passare tramite il portale per il blob per visualizzarlo. Aggiuntive sono necessarie autorizzazioni di Azure AD per passare tramite il portale e visualizzare le altre risorse che non sono visibili.

Se gli utenti devono essere in grado di accedere ai BLOB nel portale di Azure e quindi assegnare loro un ulteriore ruolo RBAC, le [lettore](../../role-based-access-control/built-in-roles.md#reader) ruolo, agli utenti, a livello di account di archiviazione o versione successiva. Il **lettore** ruolo è un ruolo di Azure Resource Manager che consente agli utenti di visualizzare risorse dell'account di archiviazione, ma non modificarli. Non fornisce le autorizzazioni di lettura ai dati in archiviazione di Azure, ma solo alle risorse di gestione di account.

Seguire questi passaggi per assegnare il **lettore** ruolo in modo che un utente può accedere ai BLOB dal portale di Azure. In questo esempio, l'ambito dell'assegnazione corrisponde all'account di archiviazione:

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.
1. Selezionare **controllo di accesso (IAM)** per visualizzare le impostazioni di controllo di accesso dell'account di archiviazione. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo.
1. Nel **aggiungere un'assegnazione di ruolo** finestra, seleziona la **lettore** ruolo. 
1. Dal **assegna accesso a** campi, selezionare **utente, gruppo o entità servizio Azure AD**.
1. Eseguire una ricerca per individuare l'entità di sicurezza a cui si desidera assegnare il ruolo.
1. Salvare l'assegnazione di ruolo.

> [!NOTE]
> Assegnazione del ruolo di lettore è necessaria solo per gli utenti che devono accedere ai BLOB o code usando il portale di Azure. 

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui ruoli RBAC per le risorse di archiviazione, vedere [autentica l'accesso ad Azure di BLOB e code usando Azure Active Directory](storage-auth-aad.md). 
- Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Che cos'è il controllo degli accessi in base al ruolo?](../../role-based-access-control/overview.md).
- Per informazioni su come assegnare e gestire le assegnazioni di ruolo di controllo degli accessi in base al ruolo con Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST, vedere gli articoli seguenti:
    - [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Gestire il controllo degli accessi in base al ruolo con l'API REST](../../role-based-access-control/role-assignments-rest.md)
- Per informazioni su come autorizzare l'accesso a contenitori e code all'interno delle applicazioni di archiviazione, vedere [Usare Azure AD con applicazioni di Archiviazione di Azure](storage-auth-aad-app.md).
