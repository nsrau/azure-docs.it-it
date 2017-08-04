---
title: Come gestire i certificati di federazione in Azure AD | Documentazione Microsoft
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
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 4b859d631617fc3d375711876cf23e201662e43a
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="managing-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gestione di certificati per Single Sign-On federato in Azure Active Directory
Questo articolo tratta domande comuni e informazioni relative ai certificati creati da Azure Active Directory per stabilire l'accesso Single Sign-On federato (SSO) alle applicazioni SaaS. Queste applicazioni possono essere aggiunte dalla raccolta di app di Azure AD o usando il modello di applicazione non inclusa nella raccolta. L'applicazione deve essere configurata usando l'opzione Single Sign-On federato.

Questo articolo è applicabile solo alle app configurate per l'uso dell'accesso **Single Sign-On di Microsoft Azure AD** con la **federazione SAML**, come illustrato nell'esempio seguente:

![Single Sign-On di Microsoft Azure AD](./media/active-directory-sso-certs/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Certificato generato automaticamente per le applicazioni incluse e non incluse nella raccolta
Se si aggiunge una nuova applicazione dalla raccolta e si configura l'accesso basato su SAML, Azure AD genera un certificato valido 3 anni per l'applicazione. È possibile scaricare questo certificato dalla sezione **Certificato di firma SAML**. Per le applicazioni incluse nella raccolta, in questa sezione può apparire l'opzione di download del **certificato** o dei **metadati della federazione**, in base al requisito dell'applicazione.

![Single Sign-On di Microsoft Azure AD](./media/active-directory-sso-certs/saml_certificate_download.png)

## <a name="how-to-customize-the-expiration-date-for-your-federation-certificate-and-roll-over-the-new-certificate"></a>Come personalizzare la data di scadenza per il certificato di federazione ed eseguire il rollover del nuovo certificato
Per impostazione predefinita, i certificati sono impostati in modo da scadere dopo tre anni. È possibile scegliere una data di scadenza diversa per il certificato seguendo questa procedura: nelle schermate incluse viene usato Salesforce a titolo di esempio, ma i passaggi sono validi per qualsiasi app SaaS federata.

1. Nel portale di gestione di [Microsoft Azure Active Directory](https://aad.portal.azure.com) fare clic su **Applicazione aziendale** e quindi su **Nuova applicazione** dalla pagina **Panoramica** come illustrato di seguito:

   ![Aprire la configurazione guidata di SSO.](./media/active-directory-sso-certs/enterprise_application_new_application.png)

2. Cercare l'applicazione inclusa nella raccolta e selezionarla per aggiungerla. Se non si riesce a trovare l'applicazione richiesta, aggiungere l'applicazione usando l'opzione **Applicazione non nella raccolta**. Questa funzionalità è disponibile solo nello SKU di **Azure AD Premium (P1 e P2)**.

    ![Single Sign-On di Microsoft Azure AD](./media/active-directory-sso-certs/add_gallery_application.png)

3. Fare clic sul collegamento **Single Sign-On** nel riquadro di spostamento a sinistra e modificare la **modalità di accesso Single Sign-on** in **Accesso basato su SAML**. In questo modo viene generato un certificato valido tre anni per l'applicazione.

4. Per creare un nuovo certificato, fare clic sul collegamento **Crea nuovo certificato** nella sezione **Certificato di firma SAML**.

    ![Genera un nuovo certificato](./media/active-directory-sso-certs/create_new_certficate.png)

5. Il collegamento Crea nuovo certificato apre il controllo calendario e consente di impostare qualsiasi data e ora entro tre anni. La data e l'ora selezionate e l'ora sono la data e l'ora di scadenza del nuovo certificato. Fare clic sul pulsante **Salva** per salvare il certificato.

    ![Scaricare e quindi caricare il certificato](./media/active-directory-sso-certs/certifcate_date_selection.PNG)

6. Ora il nuovo certificato è disponibile per il download. Fare clic sul collegamento **Certificato** per scaricarlo. Per il momento il certificato non è attivo. Per eseguire il rollover del certificato, selezionare la casella di **attivazione del nuovo certificato** e fare clic su Salva. Da questo momento in poi Azure AD si avvia usando il nuovo certificato per firmare la risposta.

7.  Per informazioni su come caricare il certificato in un'applicazione SaaS specifica, fare clic sul collegamento che consente di visualizzare nella pagina l'esercitazione sulla configurazione dell'applicazione.

## <a name="how-to-renew-a-certificate-that-will-soon-expire"></a>Come rinnovare un certificato con scadenza imminente
I passaggi per il rinnovo illustrati di seguito non dovrebbero idealmente comportare tempi di inattività significativi per gli utenti. Le schermate usate in questa sezione includono Salesforce come esempio, ma i passaggi possono essere applicati a qualsiasi app SaaS federata.

1. Nella pagina **Single Sign-On** dell'applicazione **Azure Active Directory** generare il nuovo certificato per l'applicazione. Per creare il certificato, fare clic sul collegamento **Crea nuovo certificato** nella sezione **Certificato di firma SAML**.

    ![Genera un nuovo certificato](./media/active-directory-sso-certs/create_new_certficate.png)

2. Selezionare la data e l'ora di scadenza per il nuovo certificato e fare clic sul pulsante **Salva**.

3. Scaricare il certificato nella sezione Certificato di firma SAML. Caricare il nuovo certificato nella schermata di configurazione dell'accesso Single Sign-On dell'app SaaS. Per informazioni su come eseguire queste operazioni per un'applicazione SaaS specifica, fare clic sul collegamento all'**esercitazione sulla configurazione dell'applicazione**.
   
4. Per attivare il nuovo certificato in Azure AD selezionare l'opzione che consente di attivare il nuovo certificato e fare clic sul pulsante **Salva** nella parte superiore della pagina. In questo modo viene eseguito il rollover del certificato sul lato Azure AD. Lo stato del certificato passa da **Nuovo** ad **Attivo**. Da questo momento in poi Azure AD si avvia usando il nuovo certificato per firmare la risposta. 
   
    ![Genera un nuovo certificato](./media/active-directory-sso-certs/new_certificate_download.png)

## <a name="related-articles"></a>Articoli correlati
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Risoluzione dei problemi dell'accesso Single Sign-On basato su SAML](active-directory-saml-debugging.md)
