---
title: Usare il portale di Azure per gestire i diritti di accesso Azure AD ai dati BLOB e di Accodamento con RBAC-archiviazione di Azure | Microsoft Docs
description: Usare il controllo degli accessi in base al ruolo (RBAC) dal portale di Azure per assegnare l'accesso a contenitori e code alle entità di sicurezza. Archiviazione di Azure supporta ruoli RBAC predefiniti e personalizzati per l'autenticazione tramite Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 4c558da6b0a9267c03b26ca1b5f57eb5e7444881
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515016"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-in-the-azure-portal"></a>Concedere l'accesso ai dati di Accodamento e BLOB di Azure con RBAC nell'portale di Azure

Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md). Archiviazione di Azure definisce un set di ruoli RBAC predefiniti che comprende i set comuni di autorizzazioni utilizzate per accedere ai dati di BLOB o di Accodamento. 

Quando un ruolo RBAC viene assegnato a un'entità di sicurezza Azure AD, Azure concede l'accesso a tali risorse per l'entità di sicurezza. È possibile definire l'ambito dell'accesso a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda. Un Azure AD entità di sicurezza può essere un utente, un gruppo, un'entità servizio dell'applicazione o un' [identità gestita per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Questo articolo descrive come usare la portale di Azure per assegnare i ruoli RBAC. Il portale di Azure fornisce un'interfaccia semplice per l'assegnazione di ruoli RBAC e la gestione dell'accesso alle risorse di archiviazione. È anche possibile assegnare ruoli RBAC per le risorse BLOB e di Accodamento usando gli strumenti da riga di comando di Azure o le API di gestione di archiviazione di Azure. Per altre informazioni sui ruoli RBAC per le risorse di archiviazione, vedere [autenticare l'accesso a BLOB e code di Azure usando Azure Active Directory](storage-auth-aad.md). 

## <a name="rbac-roles-for-blobs-and-queues"></a>Ruoli Controllo degli accessi in base al ruolo per BLOB e code

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinare l'ambito della risorsa 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Assegnare i ruoli RBAC usando il portale di Azure

Dopo aver determinato l'ambito appropriato per un'assegnazione di ruolo, passare a tale risorsa nel portale di Azure. Visualizzare le impostazioni di **controllo di accesso (IAM)** per la risorsa e seguire queste istruzioni per gestire le assegnazioni di ruolo:

1. Assegnare il ruolo di archiviazione di Azure appropriato per concedere l'accesso a un'entità di sicurezza Azure AD.

1. Assegnare il ruolo di [lettore](../../role-based-access-control/built-in-roles.md#reader) Azure Resource Manager agli utenti che devono accedere a contenitori o code tramite il portale di Azure usando le credenziali Azure ad. 

Le sezioni seguenti descrivono ognuno di questi passaggi in modo più dettagliato.

> [!NOTE]
> In quanto proprietario dell'account di archiviazione di Azure, non si ricevono automaticamente le autorizzazioni per l'accesso ai dati. È invece necessario assegnare in modo esplicito a se stessi un ruolo di controllo degli accessi in base al ruolo per Archiviazione di Azure. È possibile assegnare questo ruolo a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda.
> 
> Non è possibile assegnare un ruolo con ambito a un contenitore o a una coda se per l'account di archiviazione è abilitato uno spazio dei nomi gerarchico.

### <a name="assign-a-built-in-rbac-role"></a>Assegnare un ruolo predefinito RBAC

Prima di assegnare un ruolo a un'entità di sicurezza, assicurarsi di prendere in considerazione l'ambito delle autorizzazioni da concedere. Esaminare la sezione [determinare l'ambito della risorsa](#determine-resource-scope) per stabilire l'ambito appropriato.

La procedura descritta qui assegna un ruolo con ambito a livello di singolo contenitore, ma è possibile seguire gli stessi passaggi per assegnare un ruolo con ambito a livello di singola coda: 

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione e visualizzare la sezione **Panoramica** per l'account.
1. In Servizi selezionare **BLOB**. 
1. Individuare il contenitore per il quale si vuole assegnare un ruolo e visualizzare le impostazioni del contenitore. 
1. Selezionare **Controllo di accesso (IAM)** per visualizzare le impostazioni di controllo di accesso per il contenitore. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo.

    ![Screenshot che mostra le impostazioni di controllo di accesso del contenitore](media/storage-auth-aad-rbac-portal/portal-access-control-container.png)

1. Fare clic sul pulsante **Aggiungi un'assegnazione di ruolo** per aggiungere un nuovo ruolo.
1. Nella finestra **Aggiungi assegnazione ruolo** selezionare il ruolo di archiviazione di Azure che si vuole assegnare. Quindi cercare per individuare l'entità di sicurezza a cui si vuole assegnare il ruolo.

    ![Screenshot che mostra come assegnare un ruolo di controllo degli accessi in base al ruolo](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Fare clic su **Save**. L'identità cui è assegnato il ruolo viene visualizzata nell'elenco in corrispondenza del ruolo. Ad esempio, la figura seguente mostra che l'utente aggiunto ha ora autorizzazioni di lettura per i dati nel contenitore denominato *sample-container*.

    ![Screenshot che mostra l'elenco di utenti assegnati a un ruolo](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

È possibile seguire una procedura simile per assegnare un ruolo con ambito per l'account di archiviazione, il gruppo di risorse o la sottoscrizione.

### <a name="assign-the-reader-role-for-portal-access"></a>Assegnare il ruolo lettore per l'accesso al portale

Quando si assegna un ruolo predefinito o personalizzato per archiviazione di Azure a un'entità di sicurezza, si concede a tale entità di sicurezza le autorizzazioni per l'esecuzione di operazioni sui dati nell'account di archiviazione. I ruoli predefiniti di lettura **dati** forniscono le autorizzazioni di lettura per i dati in un contenitore o in una coda, mentre i ruoli di **collaboratore dati** incorporati forniscono le autorizzazioni di lettura, scrittura ed eliminazione a un contenitore o a una coda. L'ambito delle autorizzazioni è la risorsa specificata.  

Ad esempio, se si assegna il ruolo di **collaboratore dati BLOB di archiviazione** all'utente Mary al livello di un contenitore denominato **Sample-Container**, a Mary viene concesso l'accesso in lettura, scrittura ed eliminazione a tutti i BLOB presenti nel contenitore.

Tuttavia, se Mary vuole visualizzare un BLOB nella portale di Azure, il ruolo di **collaboratore dati BLOB di archiviazione** da solo non fornirà autorizzazioni sufficienti per spostarsi tra il portale e il BLOB per poterlo visualizzare. Sono necessarie altre autorizzazioni di Azure AD per spostarsi nel portale e visualizzare le altre risorse visibili in tale posizione.

Se gli utenti devono essere in grado di accedere ai BLOB nel portale di Azure, assegnare loro un ruolo RBAC aggiuntivo, il ruolo [lettore](../../role-based-access-control/built-in-roles.md#reader) , a tali utenti, a livello di account di archiviazione o superiore. Il ruolo **Reader** è un ruolo Azure Resource Manager che consente agli utenti di visualizzare le risorse dell'account di archiviazione, ma non di modificarle. Non fornisce le autorizzazioni di lettura per i dati in archiviazione di Azure, ma solo per le risorse di gestione degli account.

Attenersi alla procedura seguente per assegnare il ruolo **Reader** in modo che un utente possa accedere ai blob dal portale di Azure. In questo esempio, l'ambito dell'assegnazione è l'account di archiviazione:

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.
1. Selezionare **controllo di accesso (IAM)** per visualizzare le impostazioni di controllo di accesso per l'account di archiviazione. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo.
1. Nella finestra **Aggiungi assegnazione ruolo** selezionare il ruolo **lettore** . 
1. Dal campo **assegna accesso a** selezionare **Azure ad utente, gruppo o entità servizio**.
1. Eseguire una ricerca per individuare l'entità di sicurezza a cui si desidera assegnare il ruolo.
1. Salvare l'assegnazione di ruolo.

> [!NOTE]
> L'assegnazione del ruolo Reader è necessaria solo per gli utenti che devono accedere a BLOB o code usando il portale di Azure. 

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui ruoli RBAC per le risorse di archiviazione, vedere [autenticare l'accesso a BLOB e code di Azure usando Azure Active Directory](storage-auth-aad.md). 
- Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Che cos'è il controllo degli accessi in base al ruolo?](../../role-based-access-control/overview.md).
- Per informazioni su come assegnare e gestire le assegnazioni di ruolo di controllo degli accessi in base al ruolo con Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST, vedere gli articoli seguenti:
    - [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Gestire il controllo degli accessi in base al ruolo con l'API REST](../../role-based-access-control/role-assignments-rest.md)
- Per informazioni su come autorizzare l'accesso a contenitori e code all'interno delle applicazioni di archiviazione, vedere [Usare Azure AD con applicazioni di Archiviazione di Azure](storage-auth-aad-app.md).
