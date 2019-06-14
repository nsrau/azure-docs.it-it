---
title: Approvare o rifiutare le richieste per i ruoli di Azure AD in PIM - Azure Active Directory | Microsoft Docs
description: Informazioni su come approvare o rifiutare le richieste per i ruoli di Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47e9033b33c70a72a1685696f75a442e88eed033
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60289725"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-pim"></a>Approvare o rifiutare le richieste per i ruoli di Azure AD in PIM

Con Azure Active Directory (Azure AD) Privileged Identity Management (PIM), è possibile configurare i ruoli per richiedere l'approvazione per l'attivazione e scegliere uno o più utenti o gruppi come responsabili approvazione con delega. I responsabili approvazione delegati hanno 24 ore di tempo per approvare le richieste. Se una richiesta non viene approvata entro 24 ore, l'utente idoneo deve inviare una nuova richiesta. L'intervallo di tempo di 24 ore per l'approvazione non è configurabile.

Seguire i passaggi descritti in questo articolo per approvare o rifiutare le richieste per ruoli di Azure AD.

## <a name="view-pending-requests"></a>Visualizzare le richieste in sospeso

Come responsabile approvazione con delega, si riceverà una notifica e-mail quando una richiesta di ruolo di Azure AD è in attesa di approvazione. È possibile visualizzare queste richieste in sospeso in PIM.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Ruoli di Azure AD**.

1. Fare clic su **Approva richieste**.

    ![Ruoli AD Azure PIM - ruoli](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    È possibile visualizzare un elenco delle richieste in attesa di approvazione.

## <a name="approve-requests"></a>Approvare le richieste

1. Selezionare le richieste da approvare e quindi fare clic su **Approva** per aprire il riquadro Approva le richieste selezionate.

    ![Elenco delle richieste in attesa di approvazione in PIM](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Digitare un motivo nella casella **Motivo dell'approvazione**.

    ![Approvazione delle richieste selezionate in PIM](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Fare clic su **Approve** (Approva).

    L'icona di stato verrà aggiornata per indicare l'approvazione.

    ![Approvazione delle richieste selezionate in PIM](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Rifiutare le richieste

1. Selezionare le richieste da rifiutare e quindi fare clic su **Nega** per aprire il riquadro Rifiuta le richieste selezionate.

    ![Elenco delle richieste in attesa di approvazione in PIM](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Digitare un motivo nella casella **Motivo del rifiuto**.

    ![Rifiutare le richieste selezionate in PIM](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Fare clic su **Nega**.

    L'icona di stato verrà aggiornata per indicare il rifiuto.

## <a name="next-steps"></a>Passaggi successivi

- [Notifiche tramite posta elettronica in PIM](pim-email-notifications.md)
- [Approvare o rifiutare le richieste per i ruoli delle risorse di Azure in PIM](pim-resource-roles-approval-workflow.md)
