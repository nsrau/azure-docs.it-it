---
title: Applicazione non prevista nell'elenco delle applicazioni | Microsoft Docs
description: Come visualizzare tutte le applicazioni nel tenant e capire come le applicazioni vengono visualizzate nell'elenco Tutte le applicazioni in Applicazioni aziendali
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 4765b71714e88ee91cb9938ad4bb34033cf12422
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="unexpected-application-in-my-applications-list"></a>Applicazione non prevista nell'elenco delle applicazioni

Questo articolo illustra come vengono visualizzate le applicazioni nell'elenco **Tutte le applicazioni** in **Applicazioni aziendali**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Come visualizzare tutte le applicazioni nel tenant

Per visualizzare tutte le applicazioni nel tenant, è necessario usare il controllo **Filtro** per visualizzare **Tutte le applicazioni** nell'elenco **Tutte le applicazioni**. A questo scopo, eseguire la procedura seguente.

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **coamministratore.**

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

6.  Fare clic sul controllo **Filtro** all'inizio dell'**elenco Tutte le applicazioni**.

7.  Nel pannello del **filtro** impostare l'opzione **Mostra** su **Tutte le applicazioni.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Perché viene visualizzata un'applicazione specifica nell'elenco di tutte le applicazioni?

Quando si imposta il filtro su **Tutte le applicazioni**, l'**elenco** di **tutte le applicazioni** visualizza ogni oggetto entità servizio nel tenant. Gli oggetti entità servizio possono essere visualizzati in questo elenco in diversi modi:

1.  Quando si aggiunge un'applicazione dalla raccolta di applicazioni, incluse:

   1. **Applicazioni della raccolta di Azure AD**: un'applicazione che è già stata integrata per un accesso Single Sign-On con Azure AD.

   2. **Applicazioni del proxy applicazioni**: un'applicazione in esecuzione nell'ambiente locale per cui si vuole specificare un punto di accesso singolo esterno.

   3. **Applicazioni personalizzate**: un'applicazione che l'organizzazione vuole sviluppare nella piattaforma di sviluppo delle applicazioni di Azure AD, ma che non è ancora disponibile.

   4. **Applicazioni non incluse nella raccolta**: tutte le altre applicazioni. Qualsiasi collegamento Web o qualsiasi applicazione che usa un campo di nome utente e password supporta i protocolli SAML o OpenID Connect o supporta SCIM che può essere integrato per un accesso Single Sign-On con Azure AD.

2.  Quando si effettua l'iscrizione o si esegue l'accesso a un'applicazione di terze parti integrata con Azure Active Directory<sup></sup>, ad esempio [Smartsheet](https://app.smartsheet.com/b/home) o [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3.  Quando si esegue l'accesso o si aggiunge una licenza a un utente o gruppo per un'applicazione proprietaria, ad esempio [Microsoft Office 365](http://products.office.com/).

4.  Quando si aggiunge una nuova registrazione di applicazione tramite la creazione di un'applicazione personalizzata usando [Registro applicazioni](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration).

5.  Quando si aggiunge una nuova registrazione di applicazione tramite la creazione di un'applicazione personalizzata usando il [portale di registrazione delle applicazioni V2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration#visit-the-microsoft-app-registration-portal).

6.  Quando si aggiunge un'applicazione sviluppata con i [metodi di autenticazione ASP.net](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) o con i [Servizi connessi](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx) di Visual Studio .

7.  Quando si crea un oggetto entità servizio usando il [modulo Azure AD PowerShell](/powershell/azure/install-adv2?view=azureadps-2.0).

8.  Quando un amministratore [concede l'autorizzazione a un'applicazione](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) di usare i dati nel tenant.

9.  Quando un [utente consente a un'applicazione](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) di usare i dati nel tenant.

10. Quando si abilitano determinati servizi che archiviano dati nel tenant, ad esempio la reimpostazione della password, basata su un modello di un'entità servizio per archiviare in modo sicuro i criteri di reimpostazione delle password.

Per ottenere altre informazioni dettagliate sull'aggiunta di app alla directory, vedere [Come vengono aggiunte le applicazioni in Azure AD e perché](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Si vuole rimuovere un'assegnazione specifica di un utente o gruppo a un'applicazione

Per rimuovere un'assegnazione di un utente o di un gruppo da un'applicazione, seguire i passaggi elencati nell'articolo [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale in anteprima di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal).

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Si vuole disabilitare tutti gli accessi a un'applicazione per tutti gli utenti

Per disabilitare tutti gli accessi utente a un'applicazione, seguire i passaggi elencati nell'articolo [Disabilitare gli accessi utente per un'app aziendale in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal).

## <a name="i-want-to-delete-an-application-entirely"></a>Si vuole eliminare completamente un'applicazione

Per **eliminare un'applicazione**, seguire queste istruzioni:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **coamministratore.**

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'**elenco di tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione che si desidera eliminare.

7.  Dopo il caricamento dell'applicazione, fare clic sull'icona **Elimina** che si trova nella parte superiore del pannello **Panoramica** dell'applicazione.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Si vuole disabilitare tutte le operazioni future di consenso da parte dell'utente a tutte le applicazioni

La disabilitazione di consenso da parte dell'utente per l'intera directory impedisce agli utenti finali di consentire l'accesso a qualsiasi applicazione. Gli amministratori possono sempre dare consenso per conto degli utenti. Per informazioni sul consenso alle applicazioni e sui motivi per cui si desideri dare o meno consenso, leggere [Informazioni sul consenso dell'utente e dell'amministratore](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).

Per **disabilitare tutte le operazioni future di consenso degli utenti nella directory intera**, seguire queste istruzioni:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare **"Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Impostazioni utente**.

6.  Disabilitare tutte le future operazioni di consenso degli utenti impostando l'opzione **Gli utenti possono consentire alle app di accedere ai propri dati** su **No** e facendo clic sul pulsante **Salva**.

## <a name="next-steps"></a>Passaggi successivi
[Gestione di applicazioni con Azure Active Directory](active-directory-enable-sso-scenario.md)
