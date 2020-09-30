---
title: Visualizzare e assegnare autorizzazioni per i ruoli di amministratore-Azure AD | Microsoft Docs
description: È ora possibile visualizzare e gestire i membri di un ruolo di amministratore di Azure AD nel portale. Per gli utenti che gestiscono spesso le assegnazioni di ruolo.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 06/15/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e548e99cc60d67b477fd087b993764bf7f223592
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541187"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Visualizzare e assegnare i ruoli di amministratore in Azure Active Directory

È ora possibile visualizzare e gestire tutti i membri dei ruoli di amministratore nel portale di Azure Active Directory. Se si gestiscono frequentemente le assegnazioni di ruolo, questa esperienza risulterà particolarmente interessante. E in caso di dubbi sull'effettiva utilità dei ruoli, è possibile visualizzare un elenco dettagliato delle autorizzazioni per ognuno dei ruoli di amministratore di Azure AD.

## <a name="view-all-roles"></a>Visualizzare tutti i ruoli

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Azure Active Directory**.

1. Selezionare **ruoli e amministratori** per visualizzare l'elenco di tutti i ruoli disponibili.

1. Selezionare i puntini di sospensione a destra di ogni riga per visualizzare le autorizzazioni per il ruolo. Selezionare un ruolo per visualizzare gli utenti assegnati al ruolo. Se viene visualizzato un valore diverso dall'immagine seguente, leggere la nota in [visualizzare le assegnazioni per i ruoli con privilegi](#view-assignments-for-privileged-roles) per verificare se si è in Privileged Identity Management (PIM).

    ![elenco dei ruoli nel portale di Azure AD](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Visualizzare i ruoli personali

È facile visualizzare anche le proprie autorizzazioni. Selezionare **Your Role** (Ruolo dell'utente) sulla pagina **Roles and administrators** (Ruoli e amministratori) per visualizzare i ruoli attualmente assegnati all'utente.

## <a name="view-assignments-for-privileged-roles"></a>Visualizzare le assegnazioni per i ruoli con privilegi

È possibile selezionare la **gestione nel servizio PIM** per funzionalità di gestione aggiuntive. Gli amministratori dei ruoli con privilegi possono trasformare le assegnazioni "permanenti" (sempre attive nel ruolo) in "idonee" (nel ruolo solo con privilegi elevati). Se non si dispone di Privileged Identity Management, è comunque possibile selezionare **Gestisci in PIM** per iscriversi a una versione di valutazione. Privileged Identity Management richiede un [piano di licenza di Azure AD Premium P2](../privileged-identity-management/subscription-requirements.md).

![elenco dei membri di un ruolo di amministratore](./media/directory-manage-roles-portal/member-list.png)

Se l'utente è un amministratore globale o un amministratore di ruoli con privilegi, può aggiungere o rimuovere facilmente i membri, filtrare l'elenco o selezionare un membro per visualizzare i ruoli assegnati attivi.

> [!Note]
> Se si dispone di una licenza Azure AD Premium P2 e si usano già Privileged Identity Management, tutte le attività di gestione dei ruoli vengono eseguite in Privileged Identity Management e non in Azure AD.
>
> ![Azure AD ruoli gestiti in PIM per gli utenti che usano già PIM e hanno una licenza Premium P2](./media/directory-manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="view-a-users-role-permissions"></a>Visualizzare le autorizzazioni del ruolo di un utente

Quando si visualizzano i membri di un ruolo, selezionare **Descrizione** per visualizzare l'elenco completo delle autorizzazioni concesse in base all'assegnazione del ruolo. La pagina include collegamenti alla documentazione pertinente, che contiene indicazioni utili per la gestione dei ruoli della directory.

![Screenshot che mostra la pagina "Global Administrator-Description".](./media/directory-manage-roles-portal/role-description.png)

## <a name="download-role-assignments"></a>Scaricare le assegnazioni di ruolo

Per scaricare tutte le assegnazioni per un ruolo specifico, nella pagina **ruoli e amministratori** selezionare un ruolo e quindi selezionare **Scarica assegnazioni di ruolo**. Viene scaricato un file CSV in cui sono elencate le assegnazioni in tutti gli ambiti per tale ruolo.

![Scarica tutte le assegnazioni per un ruolo](./media/directory-manage-roles-portal/download-role-assignments.png)

## <a name="assign-a-role"></a>Assegnare un ruolo

1. Accedere al [portale di Azure](https://portal.azure.com) con autorizzazioni di amministratore globale o ruolo con privilegi e selezionare **Azure Active Directory**.

1. Selezionare **ruoli e amministratori** per visualizzare l'elenco di tutti i ruoli disponibili.

1. Selezionare un ruolo per visualizzare le relative assegnazioni.

    ![Screenshot che mostra la pagina "amministratori utente-assegnazioni" con l'azione "Gestisci in PIM" selezionata.](./media/directory-manage-roles-portal/member-list.png)

1. Selezionare **Aggiungi assegnazioni** e selezionare i ruoli che si desidera assegnare. È possibile selezionare la **gestione nel servizio PIM** per funzionalità di gestione aggiuntive. Se viene visualizzato un valore diverso dall'immagine seguente, leggere la nota in [visualizzare le assegnazioni per i ruoli con privilegi](#view-assignments-for-privileged-roles) per verificare se si è in PIM.

    ![elenco di autorizzazioni per un ruolo di amministratore](./media/directory-manage-roles-portal/directory-role-select-role.png)

## <a name="next-steps"></a>Passaggi successivi

* Condividere le informazioni nel [forum di Azure AD sui ruoli amministrativi](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Per altre informazioni sui ruoli e l'assegnazione del ruolo di amministratore, vedere l'articolo sull'[assegnazione dei ruoli di amministratore](directory-assign-admin-roles.md).
* Per le autorizzazioni utente predefinite, vedere un [confronto tra le autorizzazioni predefinite per gli utenti guest e quelle per gli utenti membro](../fundamentals/users-default-permissions.md).
