---
title: Informazioni sul modo in cui gli utenti vengono assegnati alle app in Azure Active Directory
description: Informazioni sul modo in cui gli utenti vengono assegnati a un'app che usa Azure Active Directory per la gestione delle identità.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 411e9a6e059075dfc2928ed09c0c604d600fd9be
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604156"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Informazioni sul modo in cui gli utenti vengono assegnati alle app in Azure Active Directory
Questo articolo consente di comprendere come vengono assegnati gli utenti a un'applicazione nel tenant.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Come vengono assegnati gli utenti a un'applicazione in Azure AD?
Un utente deve prima essere assegnato a un'applicazione per potervi accedere. L'assegnazione può essere eseguita da un amministratore, da un delegato aziendale o, talvolta, dall'utente stesso. Di seguito vengono descritti i modi in cui gli utenti possono essere assegnati alle applicazioni:

*  Un amministratore [assegna un utente](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) direttamente all'applicazione
*  Un amministratore [assegna un gruppo](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal), del quale l'utente è membro, all'applicazione, inclusi:
    * Un gruppo che è stato sincronizzato da locale
    * Un gruppo di sicurezza statico creato nel cloud
    * Un [gruppo di sicurezza dinamico](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) creato nel cloud
    * Un gruppo Microsoft 365 creato nel cloud
    * Il gruppo [Tutti gli utenti](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)
*  Un amministratore abilita [l'accesso alle applicazioni self-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) per consentire a un utente di aggiungere un'applicazione usando la funzionalità app [personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Aggiungi app** **senza approvazione aziendale**
*  Un amministratore abilita [l'accesso alle applicazioni self-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) per consentire a un utente di aggiungere un'applicazione usando la funzionalità app [personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Aggiungi app** , ma solo **con l'approvazione precedente da un set selezionato di responsabili approvazione aziendali**
*  Un amministratore abilita la [Gestione gruppi self-service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) per consentire a un utente di partecipare a un gruppo assegnato a un'applicazione **senza l'approvazione aziendale**
*  Un amministratore abilita la [Gestione gruppi self-service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) per consentire a un utente di partecipare a un gruppo assegnato a un'applicazione, ma solo **con l'approvazione previa di una serie di responsabili approvazione aziendali selezionati**
*  Un amministratore assegna una licenza direttamente a un utente per un'applicazione di prima entità, ad esempio [Microsoft 365](https://products.office.com/)
*  Un amministratore assegna una licenza a un gruppo di cui l'utente è membro in un'applicazione di prima entità, ad esempio [Microsoft 365](https://products.office.com/)
*  Un [amministratore dà il consenso a un'applicazione](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) per essere usata da tutti gli utenti e quindi un utente effettua l'accesso all'applicazione
* Un utente [dà il consenso a un'applicazione](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) da solo effettuando l'accesso all'applicazione

## <a name="next-steps"></a>Passaggi successivi
* [Serie di guide di avvio rapido sulla gestione delle applicazioni](view-applications-portal.md)
* [Cos'è la gestione delle applicazioni?](what-is-application-management.md)
* [Che cos'è l'accesso Single Sign-On?](what-is-single-sign-on.md)
