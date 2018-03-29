---
title: Nascondere un'applicazione dall'esperienza utente in Azure Active Directory | Microsoft Docs
description: Nascondere un'applicazione dall'esperienza utente nei pannelli di accesso di Azure Active Directory o nelle schermate di avvio di Office 365.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2018
ms.author: markvi
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 7da8c60feed7cbb630e5b48653c657cc8fed9e99
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="hide-an-application-from-users-experience-in-azure-active-directory"></a>Nascondere un'applicazione dall'esperienza utente in Azure Active Directory

Se non si vuole visualizzare un'applicazione nei pannelli di accesso o nelle schermate di avvio di Office 365 degli utenti, sono disponibili opzioni per nascondere il riquadro dell'app.  Sono disponibili le due opzioni seguenti per nascondere le applicazioni dalle schermate di avvio degli utenti.

- Nascondere un'applicazione di terze parti dai pannelli di accesso o dalle schermate di avvio delle app di Office 365
- Nascondere tutte le applicazioni di Office 365 dai pannelli di accesso degli utenti

Nascondendo l'app, gli utenti mantengono le autorizzazioni per l'app ma non la potranno vedere nelle icone di avvio delle app. La gestione dell'app aziendale richiede autorizzazioni appropriate ed è necessario essere amministratore globale della directory.


## <a name="hiding-an-application-from-users-end-user-experiences"></a>Nascondere un'applicazione dall'esperienza degli utenti finali
Per nascondere le applicazioni dal pannello di accesso è possibile seguire questa procedura, a seconda delle situazioni specifiche.

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>Qual è la procedura per nascondere un'app di terze parti dal pannello di accesso o dalle icone di avvio delle app di Office 365 di un utente?
Seguire questa procedura per nascondere un'applicazione dal pannello di accesso e dalle schermate di avvio delle app di Office 365 di un utente.

1.  Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2.  Selezionare **Tutti i servizi**, immettere **Azure Active Directory** nella casella di testo e quindi premere **INVIO**.
3.  Nella schermata **Azure Active Directory - *nomedirectory*** , vale a dire la schermata di Azure AD per la directory che si sta gestendo, selezionare **Applicazioni aziendali**.
![App aziendali](media/active-directory-coreapps-hide-third-party-app/app1.png)
4.  Nella schermata **Applicazioni aziendali** selezionare **Tutte le applicazioni**. Viene visualizzato un elenco di app che è possibile gestire.
5.  Nella schermata **Applicazioni aziendali - Tutte le applicazioni** selezionare un'app.</br>
![App aziendali](media/active-directory-coreapps-hide-third-party-app/app2.png)
6.  Nella schermata ***nomeapp***, vale a dire la schermata con il nome dell'app selezionata nel titolo, selezionare Proprietà.
7.  Nella schermata ***nomeapp* - Proprietà** selezionare **Sì** per **Visibile agli utenti?**.
![App aziendali](media/active-directory-coreapps-hide-third-party-app/app3.png)
8.  Selezionare il comando **Salva** .

### <a name="how-do-i-hide-office-365-applications-from-users-access-panel"></a>Come nascondere le applicazioni di Office 365 dal pannello di accesso dell'utente

Seguire questa procedura per nascondere tutte le applicazioni di Office 365 dal pannello di accesso. Queste app saranno ancora visibili nel portale di Office 365.

1.  Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2.  Selezionare **Tutti i servizi**, immettere **Azure Active Directory** nella casella di testo e quindi premere **INVIO**.
3.  Nella schermata **Azure Active Directory - *nomedirectory*** , vale a dire la schermata di Azure AD per la directory che si sta gestendo, selezionare **Impostazioni utente**.
4.  Nell'area **Applicazioni aziendali** della schermata **Impostazioni utente** selezionare **Sì** per **Gli utenti possono visualizzare solo le app di Office 365 nel portale di Office 365**.

![App aziendali](media/active-directory-coreapps-hide-third-party-app/apps4.png)

## <a name="next-steps"></a>Passaggi successivi
* [Visualizzare tutti i gruppi personali](active-directory-groups-view-azure-portal.md)
* [Assegnare un utente o gruppo a un'app aziendale](active-directory-coreapps-assign-user-azure-portal.md)
* [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Modificare il nome o il logo di un'app aziendale](active-directory-coreapps-change-app-logo-user-azure-portal.md)

