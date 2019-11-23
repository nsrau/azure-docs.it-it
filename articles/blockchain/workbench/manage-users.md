---
title: Manage users in Azure Blockchain Workbench
description: Come gestire gli utenti in Azure Blockchain Workbench.
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 573ec477a3e75beb91f90da0545fb7d4c0f9bf39
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324702"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Gestire gli utenti in Azure Blockchain Workbench

Azure Blockchain Workbench include la gestione degli utenti per persone e organizzazioni che fanno parte del consorzio.

## <a name="prerequisites"></a>Prerequisiti

È necessaria una distribuzione di Blockchain Workbench. Per informazioni dettagliate sulla distribuzione, vedere [Distribuzione di Azure Blockchain Workbench](deploy.md).

## <a name="add-azure-ad-users"></a>Aggiungere utenti di Azure AD

Azure Blockchain Workbench usa Azure Active Directory (Azure AD) per autenticazione, controllo di accesso e ruoli. Gli utenti nel tenant di Azure AD per Blockchain Workbench possono autenticarsi e usare Blockchain Workbench. Aggiungere utenti al ruolo applicazione Amministratore per permettere loro di interagire ed eseguire azioni.

Gli utenti di Blockchain Workbench devono essere inclusi nel tenant di Azure AD prima di poterli assegnare ad applicazioni e ruoli. Per aggiungere utenti ad Azure AD, completare i passaggi seguenti:

1.  Accedere al [portale di Azure](https://portal.azure.com).
2.  Selezionare l'account nell'angolo superiore destro e passare al tenant di Azure AD associato a Blockchain Workbench.
3.  Selezionare **Azure Active Directory > Utenti**. Verrà visualizzato un elenco di utenti nella directory.
4.  Per aggiungere utenti alla directory, selezionare **Nuovo utente**. Per gli utenti esterni, selezionare **Nuovo utente guest**.

    ![Nuovo utente](./media/manage-users/add-ad-user.png)

5.  Completare i campi obbligatori per il nuovo utente. Selezionare **Create** (Crea).

Per altre informazioni su come gestire gli utenti in Azure AD, visitare il sito della documentazione di [Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="manage-blockchain-workbench-administrators"></a>Gestire gli amministratori di Blockchain Workbench

Dopo aver aggiunto gli utenti alla directory, il passaggio successivo consiste nello scegliere gli utenti da definire come amministratori di Blockchain Workbench. Gli utenti inclusi nel gruppo **Administrator** sono associati al **ruolo applicazione Amministratore** in Blockchain Workbench. Gli amministratori possono aggiungere o rimuovere utenti, assegnare utenti a scenari specifici e creare nuove applicazioni.

Per aggiungere utenti al gruppo **Administrator** nella directory di Azure AD:

1.  Accedere al [portale di Azure](https://portal.azure.com).
2.  Verificare di trovarsi all'interno del tenant di Azure AD associato a Blockchain Workbench selezionando il proprio account nell'angolo superiore destro.
3.  Selezionare **Azure Active Directory > Applicazioni aziendali**.
4.  Selezionare l'applicazione client Azure AD per Blockchain Workbench
    
    ![Registrazioni di tutte le applicazioni aziendali](./media/manage-users/select-blockchain-client-app.png)

5.  Selezionare **Utenti e gruppi > Aggiungi utente**.
6.  In **Aggiungi assegnazione** selezionare **Utenti**. Scegliere o cercare l'utente che si vuole aggiungere come amministratore. Dopo aver scelto l'utente, fare clic su **Seleziona**.

    ![Aggiungere un'assegnazione](./media/manage-users/add-user-assignment.png)

9.  Verificare che l'opzione **Ruolo** sia impostata su **Amministratore**
10. Selezionare **Assegna**. Gli utenti aggiunti vengono visualizzati nell'elenco con il ruolo di amministratore assegnato.

    ![Utenti di app client Blockchain](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Gestione dei membri di Blockchain Workbench

Usare l'applicazione Blockchain Workbench per gestire utenti e organizzazioni che fanno parte del consorzio. È possibile aggiungere o rimuovere utenti in ruoli e applicazioni.

1. [Aprire Blockchain Workbench](deploy.md#blockchain-workbench-web-url) nel browser e accedere come amministratore.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    I membri vengono aggiunti a ogni applicazione. I membri possono avere uno o più ruoli applicazione per avviare contratti o eseguire azioni.

2. Per gestire i membri di un'applicazione, selezionare il riquadro di un'applicazione nel riquadro **Applicazioni**.

    Il numero di membri associati all'applicazione selezionata verrà visualizzato nel riquadro dei membri.

    ![Selezionare l'applicazione](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Aggiungere un membro all'applicazione

1. Selezionare il riquadro dei membri per visualizzare un elenco dei membri correnti.
2. Selezionare **Aggiungi membri**.

    ![Aggiungere membri](./media/manage-users/application-add-members.png)

3. Cercare il nome dell'utente.  Sono elencati solo gli utenti di Azure AD che esistono nel tenant di Blockchain Workbench. Se non è possibile trovare l'utente, è necessario [aggiungere utenti di Azure AD](#add-azure-ad-users).

    ![Aggiungere membri](./media/manage-users/find-user.png)

4. Nell'elenco a discesa **Ruolo** selezionare un ruolo.

    ![Selezionare i membri di un ruolo](./media/manage-users/application-select-role.png)

5. Selezionare **Aggiungi** per aggiungere il membro con il ruolo associato all'applicazione.

#### <a name="remove-member-from-application"></a>Rimuovere un membro dall'applicazione

1. Selezionare il riquadro dei membri per visualizzare un elenco dei membri correnti.
2. Per l'utente che si vuole rimuovere, scegliere **Rimuovi** nell'elenco a discesa del ruolo.

    ![Rimuovere un membro](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Modificare o aggiungere un ruolo

1. Selezionare il riquadro dei membri per visualizzare un elenco dei membri correnti.
2. Per l'utente che si vuole modificare, fare clic sull'elenco a discesa e selezionare il nuovo ruolo.

    ![Modificare il ruolo](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Passaggi successivi

In questa procedura dettagliata si è appreso come gestire gli utenti di Azure Blockchain Workbench. Per informazioni su come creare un'applicazione blockchain, proseguire con la procedura dettagliata successiva.

> [!div class="nextstepaction"]
> [Creare un'applicazione blockchain in Azure Blockchain Workbench](create-app.md)
