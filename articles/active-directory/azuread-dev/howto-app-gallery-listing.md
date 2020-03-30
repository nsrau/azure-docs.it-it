---
title: Elencare l'app nella raccolta di applicazioni di Azure AD. Documenti Microsoft
description: Informazioni su come inserire un'applicazione che supporta l'accesso Single Sign-On nella raccolta di app di Azure Active Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 12/06/2019
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 969193b2c0843c4eb217d2bdb9f9ad9a40ccf9af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154968"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Questo articolo illustra come elencare un'applicazione nella raccolta di applicazioni di Azure Active Directory (Azure AD), implementare Single Sign-On (SSO) e gestire l'elenco.

## <a name="what-is-the-azure-ad-application-gallery"></a>Definizione della raccolta di applicazioni di Azure AD

- Esperienza Single Sign-On ottimale per i clienti.
- Configurazione minima e semplice dell'applicazione.
- Ricerca rapida per l'individuazione dell'applicazione nella raccolta.
- Uso di questa integrazione per tutti i clienti con account Free, Basic e Premium di Azure AD.
- Esercitazione dettagliata per la configurazione per i clienti reciproci.
- I clienti che utilizzano System for Cross-Domain Identity Management ([SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) possono utilizzare il provisioning per la stessa app.

## <a name="prerequisites"></a>Prerequisiti

- Per le applicazioni federate (Open ID e SAML/WS-Fed), l'applicazione deve supportare il modello software as-a-service (SaaS) per ottenere elencati nella raccolta di app di Azure AD. Le applicazioni della raccolta aziendale devono supportare più configurazioni del cliente e non qualsiasi cliente specifico.
- Per Open ID Connect, l'applicazione deve essere multi-tenanted e il framework di consenso di [Azure AD](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) deve essere implementato correttamente per l'applicazione. L'utente può inviare la richiesta di accesso a un endpoint comune in modo che qualsiasi cliente possa fornire il consenso all'applicazione. È possibile controllare l'accesso utente in base all'ID del tenant e all'UPN dell'utente ricevuti nel token.
- Per SAML 2.0/WS-Fed, l'applicazione deve essere in grado di eseguire l'integrazione SSO SAML/WS-Fed in modalità SP o IDP. Assicurarsi che questa funzionalità funzioni correttamente prima di inviare la richiesta.
- Per L'accesso SSO con password, assicurarsi che l'applicazione supporti l'autenticazione basata su form in modo che sia possibile eseguire l'archiviazione delle password per ottenere l'accesso Single Sign-On in modo che funzioni come previsto.
- È necessario un account permanente per il test con almeno due utenti registrati.

**Come ottenere Azure AD per sviluppatori?**

È possibile ottenere un account di test gratuito con tutte le funzionalità premium di Azure AD - 90 giorni gratis e può essere esteso fino a quando si fa dev lavoro con esso:You can get a free test account with all the premium Azure AD features - 90 days free and can get extended as long as you do dev work with it:https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>Inviare la richiesta nel portale

Dopo aver verificato che l'integrazione dell'applicazione funzioni con Azure AD, inviare la richiesta di accesso nel [portale Rete applicazioni.](https://microsoft.sharepoint.com/teams/apponboarding/Apps) Se si dispone di un account Office 365, usarlo per accedere a questo portale. In caso contrario, utilizzare l'account Microsoft, ad esempio Outlook o Hotmail, per accedere.

Dopo l'accesso, viene visualizzata la pagina seguente, contattare il team di [integrazione SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)di Azure AD. Specificare l'account di posta elettronica che si desidera utilizzare per l'invio della richiesta. Il team di Azure AD aggiungerà l'account nel portale Microsoft Application Network.The Azure AD team will add the account in the Microsoft Application Network portal.

![Messaggio di richiesta di accesso nel portale di SharePoint](./media/howto-app-gallery-listing/errorimage.png)

Dopo aver aggiunto l'account, è possibile accedere al portale Microsoft Application Network.After the account is added, you can sign in to the Microsoft Application Network portal.

Se dopo l'accesso viene visualizzata la pagina seguente, fornire una motivazione aziendale per la necessità dell'accesso nella casella di testo. Selezionare quindi **Richiedi accesso**.

  ![Casella di giustificazione aziendale nel portale di SharePoint](./media/howto-app-gallery-listing/accessrequest.png)

Il team esamina i dettagli e fornisce l'accesso di conseguenza. Dopo l'approvazione della richiesta, è possibile accedere al portale e inviare la richiesta selezionando il riquadro **Invia richiesta (ISV)** nella home page.

![Riquadro Invia richiesta (ISV) nella home page](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>Problemi relativi all'accesso al portale

Se si sta vedendo questo errore durante l'accesso, allora qui sono i dettagli sul problema e questo è come si può risolvere il problema.

* Se l'accesso è stato bloccato come illustrato di seguito:

  ![problemi di risoluzione dell'applicazione nella galleria](./media/howto-app-gallery-listing/blocked.png)

**Cosa sta succedendo:**

L'utente guest è federato a un tenant principale che è anche un Azure AD. L'utente ospite è ad alto rischio. Microsoft non consente agli utenti ad alto rischio di accedere alle proprie risorse. Tutti gli utenti ad alto rischio (dipendenti o ospiti/fornitori) devono correggere/chiudere il rischio per accedere alle risorse Microsoft. Per gli utenti guest, il rischio per l'utente proviene dal tenant principale e il criterio proviene dal tenant delle risorse (Microsoft in questo caso).
 
**Soluzioni sicure:**

* Gli utenti guest registrati a MFA corredono il proprio rischio per gli utenti. Questa operazione può essere eseguita dall'utente guesthttps://aka.ms/sspr) che esegue una modifica o una reimpostazione della password protetta (nel tenant di casa (questo richiede l'autenticazione a più fattori e SSPR nel tenant principale). La modifica o la reimpostazione della password protetta deve essere avviata in Azure AD e non in alternativa.

* Gli utenti guest hanno i loro amministratori correggere il loro rischio. In questo caso, l'amministratore eseguirà una reimpostazione della password (generazione temporanea della password). Questo non ha bisogno di Protezione identità. L'amministratore dell'utente ospite https://aka.ms/RiskyUsers può andare a e fare clic su 'Reimposta password'.

* Gli utenti ospiti hanno i loro amministratori chiudere / ignorare il loro rischio. Anche in questo caso, questo non ha bisogno di Identity Protection. L'amministratore può https://aka.ms/RiskyUsers andare a e fare clic su 'Elimina rischio utente'. Tuttavia, l'amministratore deve fare la due diligence per garantire che si trattava di una valutazione del rischio falso positivo prima di chiudere il rischio dell'utente. In caso contrario, stanno mettendo a rischio le risorse proprie e di Microsoft sopprimendo una valutazione del rischio senza indagini.

> [!NOTE]
> In caso di problemi di accesso, contattare il team di [integrazione SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)di Azure AD.

## <a name="implement-sso-by-using-the-federation-protocol"></a>Implementare SSO utilizzando il protocollo di federazione

Per inserire un'applicazione nella raccolta di app di Azure AD, è innanzitutto necessario implementare uno dei protocolli di federazione seguenti supportati da Azure AD. È inoltre necessario accettare i termini e le condizioni della raccolta di applicazioni di Azure AD. Leggere i termini e le condizioni della raccolta di applicazioni di Azure AD in [questo sito Web](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

- **OpenID Connect:** per integrare l'applicazione con Azure AD usando il protocollo Open ID Connect, seguire le [istruzioni degli sviluppatori.](v1-authentication-scenarios.md)

    ![Elenco di un'applicazione OpenID Connect nella raccoltaListing an OpenID Connect application in the gallery](./media/howto-app-gallery-listing/openid.png)

    * Se si desidera aggiungere l'applicazione all'elenco nella raccolta utilizzando OpenID Connect, selezionare **OpenID Connect & OAuth 2.0** come illustrato.
    * In caso di problemi di accesso, contattare il team di [integrazione SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)di Azure AD.

- **SAML 2.0** o **WS-Fed:** se l'app supporta SAML 2.0, è possibile integrarla direttamente con un tenant di Azure AD seguendo le [istruzioni per aggiungere un'applicazione personalizzata.](../active-directory-saas-custom-apps.md)

  ![Elenco di un'applicazione SAML 2.0 o WS-Fed nella galleria](./media/howto-app-gallery-listing/saml.png)

  * Se si desidera aggiungere l'applicazione all'elenco nella raccolta utilizzando **SAML 2.0** o **WS-Fed,** selezionare **SAML 2.0/WS-Fed** come illustrato.

  * In caso di problemi di accesso, contattare il team di [integrazione SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)di Azure AD.

## <a name="implement-sso-by-using-the-password-sso"></a>Implementare SSO utilizzando la password SSO

Creare un'applicazione Web con una pagina di accesso HTML per configurare l'[accesso Single Sign-On basato su password](../manage-apps/what-is-single-sign-on.md). L'SSO basato su password, definito anche insieme di credenziali delle password, consente di gestire l'accesso degli utenti e le password per le applicazioni Web che non supportano la federazione delle identità. È utile anche per gli scenari in cui più utenti devono condividere un singolo account, ad esempio per gli account dell'app di social media dell'organizzazione.

![Elenco di un'applicazione SSO con password nella raccoltaListing a password SSO application in the gallery](./media/howto-app-gallery-listing/passwordsso.png)

* Se si desidera aggiungere l'applicazione all'elenco nella raccolta utilizzando la password SSO, selezionare **Password SSO** come illustrato.
* In caso di problemi di accesso, contattare il team di [integrazione SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)di Azure AD.

## <a name="request-for-user-provisioning"></a>Richiesta di provisioning degli utenti

Seguire il processo illustrato nell'immagine seguente per richiedere il provisioning degli utenti.

   ![Richiesta di provisioning degli utenti](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>Aggiornare o rimuovere una presentazione esistente

Per aggiornare o rimuovere un'applicazione esistente nella raccolta di app di Azure AD, è innanzitutto necessario inviare la richiesta nel [portale Rete applicazioni](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se si dispone di un account Office 365, usarlo per accedere a questo portale. In caso contrario, utilizzare l'account Microsoft, ad esempio Outlook o Hotmail, per accedere.

- Selezionare l'opzione appropriata come illustrato nell'immagine seguente.

    ![Elenco di un'applicazione SAML nella raccoltaListing a SAML application in the gallery](./media/howto-app-gallery-listing/updateorremove.png)

    * Per aggiornare un'applicazione esistente, selezionare l'opzione appropriata in base alle proprie esigenze.
    * Per rimuovere un'applicazione esistente dalla raccolta di app di Azure AD, selezionare **Rimuovi elenco di applicazioni dalla raccolta**.
    * In caso di problemi di accesso, contattare il team di [integrazione SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)di Azure AD.

## <a name="list-requests-by-customers"></a>Elencare le richieste dei clienti

I clienti possono inviare una richiesta per elencare un'applicazione selezionando **Richieste di app da parte dei clienti** > **Invia nuova richiesta**.

![Mostra il riquadro delle app richieste dal cliente](./media/howto-app-gallery-listing/customer-submit-request.png)

Ecco il flusso di applicazioni richieste dai clienti.

![Mostra il flusso di app richieste dal cliente](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>Tempistica

La tempistica per il processo di inserimento di un'applicazione SAML 2.0 o WS-Fed nella galleria è da 7 a 10 giorni lavorativi.

  ![Sequenza temporale per l'elenco di un'applicazione SAML nella raccolta](./media/howto-app-gallery-listing/timeline.png)

La sequenza temporale per il processo di presentazione di un'applicazione OpenID Connect nella raccolta è da 2 a 5 giorni lavorativi.

  ![Sequenza temporale per l'elenco di un'applicazione OpenID Connect nella raccolta](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Escalation

Per eventuali escalation, inviare posta elettronica al SaaSApplicationIntegrations@service.microsoft.comteam di [integrazione SSO](mailto:SaaSApplicationIntegrations@service.microsoft.com) di Azure AD in e risponderemo il prima possibile.
