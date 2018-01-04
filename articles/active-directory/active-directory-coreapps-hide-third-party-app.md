---
title: Nascondere un'applicazione mediante l'esperienza dell'utente in Azure Active Directory | Documenti Microsoft
description: Come nascondere un'applicazione mediante l'esperienza dell'utente in Azure Active Directory
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
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
ms.openlocfilehash: 667fdd45bc9eb1f01ce3883006bb29274478cb83
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="hide-an-application-from-users-experience-in-azure-active-directory"></a>Nascondere un'applicazione mediante l'esperienza dell'utente in Azure Active Directory

Se si dispone di un'applicazione che non si desidera visualizzare su pannelli di accesso degli utenti o avvio di Office 365, è disponibile un'opzione per nascondere il riquadro dell'app. Questa opzione è disponibile solo per applicazioni di terze parti (app non pubblicate da Microsoft). Nascondendo l'app, gli utenti mantengono le autorizzazioni per l'app ma non la potranno vedere nelle icone di avvio delle app. È necessario disporre delle autorizzazioni appropriate per gestire l'app aziendale ed essere amministratore globale della directory. 

## <a name="hiding-an-application-from-users-end-user-experiences"></a>Nascondere un'applicazione da un'esperienza utente finale dell'utente
Attenersi alla seguente procedura per nascondere un'applicazione dal Pannello di accesso dell'utente e di avvio di app di Office 365

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
