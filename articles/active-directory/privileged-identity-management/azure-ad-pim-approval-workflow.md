---
title: Approvare o rifiutare le richieste per i ruoli della directory di Azure AD in PIM | Microsoft Docs
description: Informazioni su come approvare o rifiutare le richieste per i ruoli della directory di Azure in Azure AD Privileged Identity Management (PIM).
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
ms.component: pim
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7bf1e437e97fdb4d929af23bd7b2a9abb49268df
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189159"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>Approvare o rifiutare le richieste per i ruoli della directory di Azure AD in PIM

Con Privileged Identity Management è possibile configurare ruoli per richiedere l’approvazione per l’attivazione e scegliere uno o più utenti o gruppi come responsabili di approvazione con delega.

## <a name="view-pending-approvals-requests"></a>Visualizzare le approvazioni (richieste) in sospeso

In qualità di responsabile approvazione con delega riceverà notifiche e-mail quando una richiesta è in attesa di approvazione. Per visualizzare queste richieste nel portale PIM, dal dashboard (nella nuova navigazione) selezionare la scheda “Richieste di approvazione in sospeso” nella barra di navigazione sinistra.

![](media/azure-ad-pim-approval-workflow/image023.png)

Da qui, è possibile visualizzare un elenco delle richieste in attesa di approvazione:

![](media/azure-ad-pim-approval-workflow/image024.png)

## <a name="approve-or-deny-requests-for-role-elevation-single-andor-bulk"></a>Approvare o rifiutare le richieste di elevazione del ruolo (singolarmente e/o in blocco)

Selezionare le richieste che si desidera approvare o rifiutare, quindi fare clic sul pulsante della barra delle azioni che corrisponde alla decisione:

![](media/azure-ad-pim-approval-workflow/image025.png)

## <a name="provide-justification-for-my-approvaldenial"></a>Indicare una giustificazione per l’approvazione/il rifiuto

Viene aperto un nuovo pannello in cui è possibile approvare o rifiutare più richieste con una sola operazione. Immettere una giustificazione per la decisione e fare su Approva (o Rifiuta) in fondo al pannello:

![](media/azure-ad-pim-approval-workflow/image029.png)

Al termine del processo di richiesta, il simbolo dello stato riflette la decisione presa (in questo esempio, la decisione è un’approvazione):

![](media/azure-ad-pim-approval-workflow/image031.png)

## <a name="next-steps"></a>Passaggi successivi

- [Approvare o rifiutare le richieste per i ruoli delle risorse di Azure in PIM](pim-resource-roles-approval-workflow.md)
- [Notifiche tramite posta elettronica in PIM](pim-email-notifications.md)
