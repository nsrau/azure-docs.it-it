---
title: Visualizzare i membri di un ruolo di amministratore e di autorizzazioni ruoli in Azure Active Directory | Microsoft Docs
description: È ora possibile visualizzare e gestire i membri di un ruolo di amministratore di Azure AD nel portale. Per gli utenti che gestiscono spesso le assegnazioni di ruolo.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/10/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 5a42f48e85eea95211b36e0c08dcb0edb4928a20
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38489923"
---
# <a name="view-members-and-descriptions-of-administrator-roles-in-azure-active-directory"></a>Visualizzare i membri e le descrizioni dei ruoli di amministratore in Azure Active Directory

È ora possibile visualizzare e gestire tutti i membri dei ruoli di amministratore nel portale di Azure Active Directory. Se si gestiscono frequentemente le assegnazioni di ruolo, questa esperienza risulterà particolarmente interessante. E in caso di dubbi sull'effettiva utilità dei ruoli, è possibile visualizzare un elenco dettagliato delle autorizzazioni per ognuno dei ruoli di amministratore di Azure AD.

È facile vedere anche le proprie autorizzazioni. Fare clic su **Ruolo dell'utente** per accedere rapidamente alla propria pagina utente e visualizzare un elenco di tutti i ruoli assegnati attivi. Fare clic sui puntini di sospensione a destra di ogni riga per aprire la descrizione dettagliata del ruolo.

![elenco dei ruoli nel portale di Azure AD](./media/directory-manage-roles-portal/role-list.png)

Selezionare l'intera riga per visualizzare l'elenco dei membri assegnati. È possibile selezionare la **gestione nel servizio PIM** per funzionalità di gestione aggiuntive. Gli amministratori dei ruoli con privilegi possono trasformare le assegnazioni "permanenti" (sempre attive nel ruolo) in "idonee" (nel ruolo solo con privilegi elevati). In assenza del servizio PIM è comunque possibile selezionare **Gestisci in PIM** per registrarsi per una versione di prova gratuita. Privileged Identity Management richiede un [piano di licenza di Azure AD Premium P2](../privileged-identity-management/subscription-requirements.md).

![elenco dei membri di un ruolo di amministratore](./media/directory-manage-roles-portal/member-list.png)

Se l'utente è un amministratore globale o un amministratore di ruoli con privilegi, può aggiungere o rimuovere facilmente i membri, filtrare l'elenco o selezionare un membro per passare alla pagina dell'utente e visualizzare i ruoli assegnati attivi. 

## <a name="detailed-role-permissions-in-the-portal"></a>Autorizzazioni ruoli in dettaglio nel portale

Quando si visualizzano i membri di un ruolo, selezionare **Descrizione** per visualizzare l'elenco completo delle autorizzazioni concesse in base all'assegnazione del ruolo. La pagina include collegamenti alla documentazione pertinente, che contiene indicazioni utili per la gestione dei ruoli della directory.

![elenco di autorizzazioni per un ruolo di amministratore](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Passaggi successivi

* Condividere le informazioni nel [forum di Azure AD sui ruoli amministrativi](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Per altre informazioni sui ruoli e l'assegnazione del ruolo di amministratore, vedere l'articolo sull'[assegnazione dei ruoli di amministratore](directory-assign-admin-roles.md).
* Per le autorizzazioni utente predefinite, vedere un [confronto tra le autorizzazioni predefinite per gli utenti guest e quelle per gli utenti membro](../fundamentals/users-default-permissions.md).
