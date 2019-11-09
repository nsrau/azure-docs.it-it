---
title: Approva o rifiuta le richieste per i ruoli di Azure AD in PIM-Azure AD | Microsoft Docs
description: Informazioni su come approvare o negare le richieste per i ruoli di Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f663f7daad19e77dcc1cc95a6a324e881d92b28
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847137"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Approva o rifiuta le richieste per i ruoli di Azure AD in Privileged Identity Management

Con Azure Active Directory (Azure AD) Privileged Identity Management (PIM), è possibile configurare i ruoli in modo da richiedere l'approvazione per l'attivazione e scegliere uno o più utenti o gruppi come responsabili approvazione delegati. I responsabili approvazione delegati hanno 24 ore di tempo per approvare le richieste. Se una richiesta non viene approvata entro 24 ore, l'utente idoneo deve inviare una nuova richiesta. L'intervallo di tempo di 24 ore per l'approvazione non è configurabile.

Attenersi alla procedura descritta in questo articolo per approvare o negare le richieste di Azure AD ruoli.

## <a name="view-pending-requests"></a>Visualizzazione delle richieste in sospeso

In qualità di responsabile approvazione delegata, si riceverà una notifica di posta elettronica quando una richiesta di ruolo Azure AD è in attesa dell'approvazione. È possibile visualizzare le richieste in sospeso in Privileged Identity Management.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Ruoli di Azure AD**.

1. Fare clic su **Approva richieste**.

    ![Ruoli di Azure AD-approva le richieste](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    È possibile visualizzare un elenco delle richieste in attesa di approvazione.

## <a name="approve-requests"></a>Approvare le richieste

1. Selezionare le richieste da approvare e quindi fare clic su **Approva** per aprire il riquadro Approva le richieste selezionate.

    ![Approva l'elenco di richieste con l'opzione approva evidenziata](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Digitare un motivo nella casella **Motivo dell'approvazione**.

    ![Approva il riquadro delle richieste selezionate con un motivo dell'approvazione](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Fare clic su **Approve** (Approva).

    L'icona di stato verrà aggiornata per indicare l'approvazione.

    ![Approva il riquadro delle richieste selezionate dopo aver fatto clic sul pulsante approva](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Negare le richieste

1. Selezionare le richieste da rifiutare e quindi fare clic su **Nega** per aprire il riquadro Rifiuta le richieste selezionate.

    ![Approva elenco richieste con l'opzione Nega evidenziata](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Digitare un motivo nella casella **Motivo del rifiuto**.

    ![Nega il riquadro delle richieste selezionate con un motivo di negazione](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Fare clic su **Nega**.

    L'icona di stato verrà aggiornata per indicare il rifiuto.

## <a name="next-steps"></a>Passaggi successivi

- [Notifiche tramite posta elettronica in Privileged Identity Management](pim-email-notifications.md)
- [Approva o rifiuta le richieste per i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-approval-workflow.md)
