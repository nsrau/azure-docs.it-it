---
title: Visualizzazione di app con il tenant di Azure Active Directory per la gestione delle identità
description: Informazioni su come visualizzare tutte le applicazioni usando il tenant di Azure Active Directory per la gestione delle identità.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 5229b123986149903d562bc0b3d6902c0136f647
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90707884"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>Visualizzazione di app con il tenant di Azure AD per la gestione delle identità
La [serie di guide introduttive sulla gestione delle applicazioni](view-applications-portal.md) offre le nozioni di base. Si apprenderà come visualizzare tutte le app usando il tenant di Azure AD per la gestione delle identità. Questo articolo illustra in maniera approfondita i tipi di app disponibili.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Perché viene visualizzata un'applicazione specifica nell'elenco di tutte le applicazioni?
Quando viene applicato un filtro a **tutte le applicazioni**, l' **elenco** **tutte le applicazioni** Mostra ogni oggetto entità servizio nel tenant. Gli oggetti entità servizio possono essere visualizzati in questo elenco in diversi modi:
- Quando si aggiunge un'applicazione dalla raccolta di applicazioni, incluse:
   - **Applicazioni Azure ad-Enterprise** : app aggiunte al tenant usando l'opzione **applicazioni aziendali** nel portale di Azure ad. In genere le app sono integrate con lo standard SAML.
   - **Azure ad-registrazioni app** : app aggiunte al tenant usando l'opzione **registrazioni app** nel portale di Azure ad. In genere app personalizzate sviluppate usando gli standard Open ID Connect e OAuth.
   - **Applicazioni del proxy di applicazione**: un'applicazione in esecuzione nell'ambiente locale per cui si vuole offrire l'accesso Single Sign-On sicuro all'esterno.
- Quando si effettua l'iscrizione o si accede a un'applicazione di terze parti integrata con Azure Active Directory. ad esempio [Smartsheet](https://app.smartsheet.com/b/home) o [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).
- App Microsoft, ad esempio Microsoft 365.
- Quando si aggiunge una nuova registrazione di applicazione creando un'applicazione personalizzata con il [Registro applicazioni](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration).
- Quando si aggiunge una nuova registrazione di applicazione creando un'applicazione personalizzata con il [portale di registrazione delle applicazioni V2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration).
- Quando si aggiunge un'applicazione, si sta sviluppando usando i [metodi di autenticazione ASP.NET](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) di Visual Studio o [servizi connessi](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/)
- Quando si crea un oggetto entità servizio usando il [modulo Azure AD PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).
- Quando un amministratore [consente a un'applicazione](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) di usare i dati nel tenant.
- Quando un [utente consente a un'applicazione](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) di usare i dati nel tenant.
- Quando si abilitano determinati servizi che archiviano dati nel tenant, Un esempio è la reimpostazione della password, che è modellata come un'entità servizio per archiviare in modo sicuro i criteri di reimpostazione password.

Per altre informazioni su come e perché, le app vengono aggiunte alla directory, vedere [come vengono aggiunte le applicazioni ai Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="next-steps"></a>Passaggi successivi
[Gestione di applicazioni con Azure Active Directory](what-is-application-management.md)
