---
title: Gestione di certificati di federazione in Azure AD | Documentazione Microsoft
description: Informazioni su come personalizzare la data di scadenza per i certificati di federazione e su come rinnovare i certificati con scadenza imminente.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f516f7f0-b25a-4901-8247-f5964666ce23
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2017
ms.author: jeedes
ms.openlocfilehash: 1283b570200f05003658824760ecbb6722f241d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gestione di certificati per accesso Single Sign-On federato in Azure Active Directory
Questo articolo tratta domande comuni e informazioni relative ai certificati creati da Azure Active Directory (Azure AD) per stabilire l'accesso Single Sign-On federato (SSO) alle applicazioni SaaS. Queste applicazioni possono essere aggiunte dalla raccolta di app di Azure AD o usando il modello di applicazione non inclusa nella raccolta. Configurare l'applicazione utilizzando l'opzione di SSO federato.

Questo articolo è applicabile solo alle app configurate per l'uso dell'accesso SSO di Microsoft Azure AD tramite la federazione SAML, come illustrato nell'esempio seguente:

![Single Sign-On di Microsoft Azure AD](./media/active-directory-sso-certs/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Certificato generato automaticamente per le applicazioni incluse e non incluse nella raccolta
Se si aggiunge una nuova applicazione dalla raccolta e si configura l'accesso basato su SAML, Azure AD genera un certificato dalla validità di tre anni per l'applicazione. È possibile scaricare questo certificato dalla sezione **Certificato di firma SAML**. Per le applicazioni incluse nella raccolta, in questa sezione può apparire un'opzione di download del certificato o dei metadati, in base al requisito dell'applicazione.

![Accesso Single Sign-On di Azure AD](./media/active-directory-sso-certs/saml_certificate_download.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Personalizzare la data di scadenza per il certificato di federazione ed eseguire il rollover di un nuovo certificato
Per impostazione predefinita, i certificati sono impostati in modo da scadere dopo tre anni. È possibile scegliere una data di scadenza diversa per il certificato eseguendo i passaggi descritti di seguito.
Le schermate usano Salesforce per le finalità dell'esempio, ma questi passaggi possono essere applicati a qualsiasi app SaaS federata.

1. Nel [portale di Azure](https://aad.portal.azure.com), fare clic su **applicazione Enterprise** nel riquadro sinistro e quindi fare clic su **Nuova applicazione** nella pagina **Panoramica**:

   ![Aprire la configurazione guidata di SSO](./media/active-directory-sso-certs/enterprise_application_new_application.png)

2. Cercare l'applicazione inclusa nella raccolta e quindi selezionarla per aggiungerla. Se non si riesce a trovare l'applicazione richiesta, aggiungere l'applicazione usando l'opzione **Applicazione non nella raccolta**. Questa funzionalità è disponibile solo nello SKU di Azure AD Premium (P1 e P2).

    ![Accesso Single Sign-On di Azure AD](./media/active-directory-sso-certs/add_gallery_application.png)

3. Fare clic sul collegamento **Single Sign-On** nel riquadro a sinistra e modificare la **modalità di accesso Single Sign-On** in **Accesso basato su SAML**. Questo passaggio genera un certificato valido tre anni per l'applicazione.

4. Per creare un nuovo certificato, fare clic sul collegamento **Crea nuovo certificato** nella sezione **Certificato di firma SAML**.

    ![Genera un nuovo certificato](./media/active-directory-sso-certs/create_new_certficate.png)

5. Il collegamento **Crea un nuovo certificato** apre il controllo del calendario. È possibile impostare data e ora fino a tre anni dalla data corrente. La data e l'ora selezionate e sono la data e l'ora di scadenza del nuovo certificato. Fare clic su **Salva**.

    ![Scaricare e quindi caricare il certificato](./media/active-directory-sso-certs/certifcate_date_selection.PNG)

6. Ora il nuovo certificato è disponibile per il download. Fare clic sul collegamento **Certificato** per scaricarlo. Per il momento il certificato non è attivo. Per eseguire il rollover del certificato, selezionare la casella di **attivazione del nuovo certificato** e fare clic su **Salva**. Da questo momento in poi Azure AD si avvia usando il nuovo certificato per firmare la risposta.

7.  Per informazioni su come caricare il certificato in un'applicazione SaaS specifica, fare clic sul collegamento che consente di **visualizzare l'esercitazione sulla configurazione dell'applicazione**.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Rinnovare un certificato con scadenza imminente
I passaggi per il rinnovo illustrati di seguito non dovrebbero idealmente comportare tempi di inattività significativi per gli utenti. Le schermate di questa sezione includono Salesforce come esempio, ma i passaggi possono essere applicati a qualsiasi app SaaS federata.

1. Nella pagina **Single Sign-On** dell'applicazione **Azure Active Directory** generare il nuovo certificato per l'applicazione. Per creare il certificato, fare clic sul collegamento **Crea nuovo certificato** nella sezione **Certificato di firma SAML**.

    ![Genera un nuovo certificato](./media/active-directory-sso-certs/create_new_certficate.png)

2. Selezionare la data e l'ora di scadenza per il nuovo certificato e fare clic su **Salva**.

3. Scaricare il certificato nella sezione **Certificato di firma SAML**. Caricare il nuovo certificato nella schermata di configurazione dell'accesso Single Sign-On dell'applicazione SaaS. Per informazioni su come eseguire queste operazioni per un'applicazione SaaS specifica, fare clic sul collegamento all'**esercitazione sulla configurazione dell'applicazione**.
   
4. Per attivare il nuovo certificato in Azure AD selezionare l'opzione **Attiva nuovo certificato** e fare clic sul pulsante **Salva** nella parte superiore della pagina. In questo modo viene eseguito il rollover del certificato sul lato Azure AD. Lo stato del certificato passa da **Nuovo** ad **Attivo**. Da questo momento in poi Azure AD si avvia usando il nuovo certificato per firmare la risposta. 
   
    ![Genera un nuovo certificato](./media/active-directory-sso-certs/new_certificate_download.png)

## <a name="related-articles"></a>Articoli correlati
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Risoluzione dei problemi dell'accesso Single Sign-On basato su SAML](active-directory-saml-debugging.md)
