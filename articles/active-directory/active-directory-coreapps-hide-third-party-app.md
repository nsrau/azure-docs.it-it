---
title: Nascondere un'applicazione di terze parti dall'esperienza utente in Azure Active Directory | Microsoft Docs
description: Come nascondere un'applicazione di terze parti dall'esperienza utente in Azure Active Directory
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: billmath
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 976cbb1341493186b9996d250ebca8f2f3688fdf
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="hide-a-third-party-application-from-users-experience-in-azure-active-directory"></a>Nascondere un'applicazione di terze parti dall'esperienza utente in Azure Active Directory

Se non si vuole visualizzare un'applicazione di terze parti (un'app pubblicata da editori diversi da Microsoft) nei pannelli di accesso o nelle icone di avvio di Office 365 degli utenti, è disponibile un'opzione per nascondere il riquadro dell'app. Nascondendo l'app, gli utenti mantengono le autorizzazioni per l'app ma non la potranno vedere nelle icone di avvio delle app. È necessario disporre delle autorizzazioni appropriate per gestire l'app aziendale ed essere amministratore globale della directory.

## <a name="hiding-a-third-party-app-from-a-users-experience"></a>Nascondere un'app di terze parti da un'esperienza utente
Seguire questa procedura per nascondere un'app di terze parti dal pannello di accesso e dalle icone di avvio delle app di Office 365 di un utente

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>Qual è la procedura per nascondere un'app di terze parti dal pannello di accesso o dalle icone di avvio delle app di Office 365 di un utente?

1.  Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2.  Selezionare **Altri servizi**, immettere **Azure Active Directory** nella casella di testo e quindi selezionare **Invio**.
3.  Nella schermata **Azure Active Directory - *nomedirectory***, vale a dire la schermata di Azure AD per la directory che si sta gestendo, selezionare **Applicazioni aziendali**.
![App aziendali](media/active-directory-coreapps-hide-third-party-app/app1.png)
4.  Nella schermata **Applicazioni aziendali** selezionare **Tutte le applicazioni**. Viene visualizzato un elenco di app che è possibile gestire.
5.  Nella schermata **Applicazioni aziendali - Tutte le applicazioni** selezionare un'app.</br>
![App aziendali](media/active-directory-coreapps-hide-third-party-app/app2.png)
6.  Nella schermata ***nomeapp***, vale a dire la schermata con il nome dell'app selezionata nel titolo, selezionare Proprietà.
7.  Nella schermata ***nomeapp* - Proprietà** selezionare **Sì** per **Visibile agli utenti?**.
![App aziendali](media/active-directory-coreapps-hide-third-party-app/app3.png)
8.  Selezionare il comando **Salva** .

## <a name="next-steps"></a>Passaggi successivi
* [Visualizzare tutti i gruppi personali](active-directory-groups-view-azure-portal.md)
* [Assegnare un utente o gruppo a un'app aziendale](active-directory-coreapps-assign-user-azure-portal.md)
* [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Modificare il nome o il logo di un'app aziendale](active-directory-coreapps-change-app-logo-user-azure-portal.md)
