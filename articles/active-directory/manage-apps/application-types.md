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
ms.openlocfilehash: d83b69ea7c2a4459d30c4ceb90f62ab6a1a10cc2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648175"
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
- Quando si aggiunge una nuova registrazione di applicazione creando un'applicazione personalizzata con il [Registro applicazioni](../develop/quickstart-register-app.md).
- Quando si aggiunge una nuova registrazione di applicazione creando un'applicazione personalizzata con il [portale di registrazione delle applicazioni V2.0](../develop/quickstart-register-app.md).
- Quando si aggiunge un'applicazione, si sta sviluppando usando i [metodi di autenticazione ASP.NET](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) di Visual Studio o [servizi connessi](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/)
- Quando si crea un oggetto entità servizio usando il [modulo Azure AD PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).
- Quando un amministratore [consente a un'applicazione](../develop/howto-convert-app-to-be-multi-tenant.md) di usare i dati nel tenant.
- Quando un [utente consente a un'applicazione](../develop/howto-convert-app-to-be-multi-tenant.md) di usare i dati nel tenant.
- Quando si abilitano determinati servizi che archiviano dati nel tenant, Un esempio è la reimpostazione della password, che è modellata come un'entità servizio per archiviare in modo sicuro i criteri di reimpostazione password.

Per altre informazioni su come e perché, le app vengono aggiunte alla directory, vedere [come vengono aggiunte le applicazioni ai Azure ad](../develop/active-directory-how-applications-are-added.md).

## <a name="next-steps"></a>Passaggi successivi
[Gestione di applicazioni con Azure Active Directory](what-is-application-management.md)