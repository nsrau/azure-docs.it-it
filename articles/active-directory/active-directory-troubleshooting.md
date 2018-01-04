---
title: 'Risoluzione dei problemi: Voce "Active Directory" mancante o non disponibile | Microsoft Docs'
description: Operazioni da eseguire quando la voce di menu Active Directory non viene visualizzata nel portale di gestione di Azure.
services: active-directory
documentationcenter: na
author: bryanla
manager: mtillman
editor: 
ms.assetid: 3383020d-6397-43ea-b7aa-c6a9d6a1e3df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: bryanla
ms.openlocfilehash: 27a884b89224ed5765e9fcef134e711e3952ff72
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="troubleshooting-active-directory-item-is-missing-or-not-available"></a>Risoluzione dei problemi: Voce "Active Directory" mancante o non disponibile
Per la maggior parte delle istruzioni relative all'uso delle funzionalità di Azure Active Directory è necessario accedere al portale di gestione di Azure e fare clic su **Active Directory**. Come procedere però se la voce di menu Active Directory non viene visualizzata o è contrassegnata come **Non disponibile**? Questo argomento illustra come risolvere il problema. Descrive inoltre le condizioni per cui la voce di menu **Active Directory** non viene visualizzata o non è disponibile e illustra le procedure da eseguire.

## <a name="active-directory-is-missing"></a>Active Directory non disponibile
In genere la voce **Active Directory** viene visualizzata nel menu di navigazione sinistro. Le istruzioni relative alle procedure di Azure Active Directory presuppongono che questa voce sia disponibile.

![Schermata: Active Directory in Azure](./media/active-directory-troubleshooting/typical-view.png)

La voce Active Directory viene visualizzata nel menu di navigazione sinistro se si verifica una delle condizioni seguenti. In caso contrario, la voce non viene visualizzata.

* L'utente corrente ha effettuato l'accesso con un account Microsoft (in precedenza Windows Live ID).
  
    Oppure
* Il tenant di Azure ha una directory e l'account corrente è di tipo amministratore di directory.
  
    Oppure
* Il tenant di Azure ha almeno uno spazio dei nomi di Azure AD Access Control (ACS). Per altre informazioni, vedere [Spazio dei nomi di Access Control](https://msdn.microsoft.com/library/azure/gg185908.aspx)
  
    Oppure
* Il tenant di Azure ha almeno un provider di Azure Multi-Factor Authentication. Per altre informazioni, vedere [Amministrazione dei provider di Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

Per creare uno spazio dei nomi di Access Control o un provider Multi-Factor Authentication, fare clic su **+Nuovo** > **Servizi app** > **Active Directory**.

Per ottenere diritti amministrativi per una directory, chiedere a un amministratore di assegnare un ruolo di amministratore al proprio account. Per informazioni dettagliate, vedere [Assegnazione dei ruoli di amministratore in Azure AD](active-directory-assign-admin-roles-azure-portal.md).

## <a name="active-directory-is-not-available"></a>Active Directory non è disponibile
Quando si fa clic su **+Nuovo** > **Servizi app**, viene visualizzata la voce **Active Directory**. In particolare, questa voce viene visualizzata quando per l'utente corrente sono disponibili funzionalità di Active Directory, ad esempio Directory, Access Control o provider di Multi-Factor Authentication.

Tuttavia, durante il caricamento della pagina, la voce viene visualizzata in grigio ed è contrassegnata come **Non disponibile**. Questo stato è temporaneo. Dopo alcuni secondi, la voce diventerà disponibile. Se il ritardo è prolungato, spesso il problema si risolve aggiornando la pagina Web.

![Schermata: Active Directory non disponibile](./media/active-directory-troubleshooting/not-available.png)

