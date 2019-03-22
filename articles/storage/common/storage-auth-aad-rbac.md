---
title: 'Usare il portale di Azure per gestire i diritti di accesso di Azure AD a contenitori e le code con RBAC (anteprima): archiviazione di Azure'
description: Usare il controllo di accesso basato sui ruoli (RBAC) dal portale di Azure per assegnare l'accesso a contenitori e le code per entità di sicurezza. Archiviazione di Azure supporta i ruoli RBAC predefiniti e personalizzati per l'autenticazione tramite Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: a38cce7d15b4796bb66803d06f3589569985cb37
ms.sourcegitcommit: 5e4ca656baf3c7d370ab3c0fbad0278aa2c9f1e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319500"
---
# <a name="grant-access-to-azure-containers-and-queues-with-rbac-in-the-azure-portal-preview"></a>Concedere l'accesso a contenitori di Azure e code con accessi nel portale di Azure (anteprima)

Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md). Archiviazione di Azure definisce un set di ruoli di controllo degli accessi in base al ruolo predefiniti che includono un set comune di autorizzazioni usate per accedere a contenitori o code. 

Quando viene assegnato un ruolo RBAC a un'entità di sicurezza di Azure AD, Azure concede l'accesso a tali risorse per tale entità di sicurezza. È possibile definire l'ambito dell'accesso a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda. Un'entità di sicurezza di Azure AD può essere un utente, un gruppo, un'entità servizio dell'applicazione, o un [identità per le risorse di Azure gestito](../../active-directory/managed-identities-azure-resources/overview.md).

Questo articolo descrive come usare il portale di Azure per assegnare i ruoli RBAC. Il portale di Azure fornisce una semplice interfaccia per l'assegnazione dei ruoli RBAC e la gestione dell'accesso alle risorse di archiviazione. È anche possibile assegnare i ruoli RBAC per le risorse blob e coda usando gli strumenti da riga di comando di Azure o le API di gestione di archiviazione di Azure. Per altre informazioni sui ruoli RBAC per le risorse di archiviazione, vedere [autentica l'accesso ad Azure di BLOB e code usando Azure Active Directory (anteprima)](storage-auth-aad.md). 

## <a name="rbac-roles-for-blobs-and-queues"></a>Ruoli Controllo degli accessi in base al ruolo per BLOB e code

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinare l'ambito di risorse 

Prima di assegnare un ruolo RBAC a un'entità di sicurezza, determinare l'ambito di accesso che deve avere l'entità di sicurezza. Le procedure consigliate indicano che è sempre consigliabile concedere solo l'ambito più ristretto possibile.

Nell'elenco seguente vengono descritti i livelli in corrispondenza del quale è possibile definire l'ambito di accesso alle risorse blob e di Accodamento di Azure, inizia con l'ambito più ristretto:

- **Un singolo contenitore.** In questo ambito, un'entità di sicurezza ha accesso a tutti i BLOB nel contenitore, nonché le proprietà del contenitore e i metadati.
- **Una singola coda.** In questo ambito, un'entità di sicurezza può accedere ai messaggi nella coda, nonché le proprietà della coda e i metadati.
- **L'account di archiviazione.** In questo ambito, un'entità di sicurezza ha accesso a tutti i contenitori e i BLOB o per tutte le code e i relativi messaggi.
- **Il gruppo di risorse.** In questo ambito, un'entità di sicurezza ha accesso a tutti i contenitori o le code in tutti gli account di archiviazione nel gruppo di risorse.
- **La sottoscrizione.** In questo ambito, un'entità di sicurezza ha accesso a tutti i contenitori o le code in tutti gli account di archiviazione in tutti i gruppi di risorse nella sottoscrizione.

Dopo aver definito l'ambito desiderato per un'assegnazione di ruolo, passare alla risorsa appropriata nel portale di Azure. Visualizzare il **controllo di accesso (IAM)** impostazioni per la risorsa e seguire le istruzioni nelle sezioni successive per gestire le assegnazioni di ruolo.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Assegnare ruoli RBAC tramite il portale di Azure

Concessione dell'accesso alle risorse blob e coda con le credenziali di Azure AD nel portale di Azure prevede i passaggi seguenti: 

1. Assegnare il ruolo RBAC di archiviazione di Azure appropriato per concedere l'accesso a contenitori o le code. Per l'accesso in lettura, assegnare il **lettore di dati Blob (anteprima)** oppure **coda lettore di dati (anteprima)** ruolo. Per la lettura, scrittura ed eliminazione accesso, assegnare il **collaboratore ai dati Blob (anteprima)** oppure **collaboratore ai dati della coda (anteprima)** ruolo. È anche possibile assegnare un ruolo personalizzato.

1. Azure Resource Manager assegnare [lettore](../../role-based-access-control/built-in-roles.md#reader) ruolo agli utenti che devono accedere a contenitori o le code usando il portale di Azure usando le proprie credenziali di Azure AD. 

Le sezioni seguenti descrivono ognuno di questi passaggi in modo più dettagliato.

### <a name="assign-a-built-in-rbac-role"></a>Assegnare un ruolo RBAC predefinito

Prima di assegnare un ruolo a un'entità di sicurezza, assicurarsi di prendere in considerazione l'ambito delle autorizzazioni che si stanno concedendo. Rivedere le [determinano l'ambito di risorse](#determine-resource-scope) sezione per decidere l'ambito appropriato.

La procedura descritta qui assegna un ruolo con ambito a livello di singolo contenitore, ma è possibile seguire gli stessi passaggi per assegnare un ruolo con ambito a livello di singola coda: 

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione e visualizzare la sezione **Panoramica** per l'account.
1. In Servizi selezionare **BLOB**. 
1. Individuare il contenitore per il quale si vuole assegnare un ruolo e visualizzare le impostazioni del contenitore. 
1. Selezionare **Controllo di accesso (IAM)** per visualizzare le impostazioni di controllo di accesso per il contenitore. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo.

    ![Screenshot che mostra le impostazioni di controllo di accesso contenitore](media/storage-auth-aad-rbac/portal-access-control-container.png)

1. Fare clic sul pulsante **Aggiungi un'assegnazione di ruolo** per aggiungere un nuovo ruolo.
1. Nel **aggiungere un'assegnazione di ruolo** finestra, selezionare il ruolo di archiviazione di Azure che si desidera assegnare. Quindi eseguire una ricerca per individuare l'entità di sicurezza a cui si desidera assegnare il ruolo.

    ![Screenshot che mostra come assegnare un ruolo di controllo degli accessi in base al ruolo](media/storage-auth-aad-rbac/add-rbac-role.png)

1. Fare clic su **Save**. L'identità cui è assegnato il ruolo viene visualizzata nell'elenco in corrispondenza del ruolo. Ad esempio, la figura seguente mostra che l'utente aggiunto ha ora autorizzazioni di lettura per i dati nel contenitore denominato *sample-container*.

    ![Screenshot che illustra l'elenco di utenti assegnati a un ruolo](media/storage-auth-aad-rbac/container-scoped-role.png)

È possibile seguire la procedura per assegnare un ruolo con ambito per l'account di archiviazione, gruppo di risorse o sottoscrizione.

> [!NOTE]
> In quanto proprietario dell'account di archiviazione di Azure, non si ricevono automaticamente le autorizzazioni per l'accesso ai dati. È invece necessario assegnare in modo esplicito a se stessi un ruolo di controllo degli accessi in base al ruolo per Archiviazione di Azure. È possibile assegnare questo ruolo a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda.
> 
> È possibile assegnare un ruolo con ambito limitato a un contenitore o coda se l'account di archiviazione ha uno spazio dei nomi gerarchico abilitata.

### <a name="assign-the-reader-role-for-portal-access"></a>Assegnare il ruolo lettore per l'accesso al portale

Quando si assegna un ruolo predefinito o personalizzato per l'archiviazione di Azure a un'entità di sicurezza, si siano concedendo le autorizzazioni per tale entità di sicurezza per eseguire operazioni sui dati nell'account di archiviazione. L'elemento predefinito **lettore di dati** ruoli forniscono le autorizzazioni di lettura per i dati in un contenitore o coda, mentre incorporato nelle **collaboratore Data** ruoli forniscono leggere, scrivere ed eliminare le autorizzazioni per un contenitore o coda. Le autorizzazioni sono limitate alla risorsa specificata.  

Ad esempio, se si assegna il **collaboratore ai dati di archiviazione Blob (anteprima)** ruolo all'utente Mary a livello di un contenitore denominato **esempio-container**, quindi Mary è concesso di lettura, scrittura ed eliminare l'accesso a tutti i BLOB nel contenitore.

Tuttavia, se Mary desideri visualizzare un blob nel portale di Azure, il **collaboratore ai dati di archiviazione Blob (anteprima)** ruolo di per sé non fornisce le autorizzazioni sufficienti per passare tramite il portale per il blob per visualizzarlo. Aggiuntive sono necessarie autorizzazioni di Azure AD per passare tramite il portale e visualizzare le altre risorse che non sono visibili.

Se gli utenti devono essere in grado di accedere ai BLOB nel portale di Azure e quindi assegnare loro un ulteriore ruolo RBAC, le [lettore](../../role-based-access-control/built-in-roles.md#reader) ruolo, agli utenti, a livello di account di archiviazione o versione successiva. Il **lettore** ruolo è un ruolo di Azure Resource Manager che consente agli utenti di visualizzare risorse dell'account di archiviazione, ma non modificarli. Non fornisce le autorizzazioni di lettura ai dati in archiviazione di Azure, ma solo alle risorse di gestione di account.

Seguire questi passaggi per assegnare il **lettore** ruolo in modo che un utente può accedere ai BLOB dal portale di Azure. In questo esempio, l'ambito dell'assegnazione corrisponde all'account di archiviazione:

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.
1. Selezionare **controllo di accesso (IAM)** per visualizzare le impostazioni di controllo di accesso dell'account di archiviazione. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo.
1. Nel **aggiungere un'assegnazione di ruolo** finestra, seleziona la **lettore** ruolo. 
1. Dal **assegna accesso a** elenco a discesa, selezionare **utente, gruppo o entità servizio Azure AD**.
1. Eseguire una ricerca per individuare l'entità di sicurezza a cui si desidera assegnare il ruolo.
1. Salvare l'assegnazione di ruolo.

> [!NOTE]
> Assegnazione del ruolo di lettore è necessaria solo per gli utenti che devono accedere ai BLOB o code usando il portale di Azure. 

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Che cos'è il controllo degli accessi in base al ruolo?](../../role-based-access-control/overview.md).
- Per informazioni su come assegnare e gestire le assegnazioni di ruolo di controllo degli accessi in base al ruolo con Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST, vedere gli articoli seguenti:
    - [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Gestire il controllo degli accessi in base al ruolo con l'API REST](../../role-based-access-control/role-assignments-rest.md)
- Per informazioni su come autorizzare l'accesso a contenitori e code all'interno delle applicazioni di archiviazione, vedere [Usare Azure AD con applicazioni di Archiviazione di Azure](storage-auth-aad-app.md).
- Per altre informazioni sull'integrazione di Azure AD per contenitori e code di Azure, vedere il post di blog del team di Archiviazione di Azure sull'[annuncio della versione di anteprima di Autenticazione di Azure AD per Archiviazione di Azure](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
