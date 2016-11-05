---
title: 'Esercitazione: Integrazione di Azure Active Directory con DocuSign | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e DocuSign.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2016
ms.author: jeedes

---
# Esercitazione: Integrazione di Azure Active Directory con DocuSign
Questa esercitazione descrive l'integrazione di Azure e DocuSign. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant in DocuSign

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. [Abilitazione dell'integrazione dell'applicazione per DocuSign](#enabling-the-application-integration-for-docusign)
2. [Configurazione dell'accesso Single Sign-On](#configuring-single-sign-on)
3. [Configurazione del provisioning degli account](#configuring-account-provisioning)
4. [Assegnazione degli utenti](#assigning-users)
   
    ![Configurazione dell'accesso Single Sign-On][0]

## Abilitazione dell'integrazione dell'applicazione per DocuSign
Questa sezione descrive come abilitare l'integrazione dell'applicazione per DocuSign.

### Per abilitare l'integrazione dell'applicazione per DocuSign, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Configurazione dell'accesso Single Sign-On][1]
2. Nell'elenco Directory selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Configurazione dell'accesso Single Sign-On][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo Come procedere fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Configurazione dell'accesso Single Sign-On][4]
6. Nella casella di ricerca digitare **DocuSign**.
   
    ![Configurazione dell'accesso Single Sign-On][5]
7. Nel riquadro dei risultati selezionare **DocuSign** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Configurazione dell'accesso Single Sign-On][6]

## Configurazione dell'accesso Single Sign-On
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a DocuSign con il proprio account di Azure AD usando la federazione basata sul protocollo SAML.

### Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **DocuSign** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo Configura accesso Single Sign-On.
   
    ![Configurazione dell'accesso Single Sign-On][7]
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a DocuSign** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su Avanti.
   
    ![Configurazione dell'accesso Single Sign-On][8]
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configurazione dell'accesso Single Sign-On][61]
   
    a. Nella casella di testo **URL di accesso** digitare `https://account.docusign.com/*`.
   
    b. Nella casella di testo **Identificatore** digitare `https://account.docusign.com/*`.
   
    c. Fare clic su **Avanti**.

    > [AZURE.TIP] I valori di URL di accesso e Identificatore sono solo segnaposto. Le istruzioni su come recuperare i valori effettivi per l'ambiente locale sono descritte più avanti in questo argomento.


1. Nella pagina **Configura accesso Single Sign-On in DocuSign** fare clic su **Scarica certificato** e quindi salvare il certificato localmente nel computer.
   
    ![Configurazione dell'accesso Single Sign-On][10]
2. In un'altra finestra del Web browser accedere al **portale di amministrazione di DocuSign** come amministratore.
3. Nel menu di navigazione a sinistra fare clic su **Domains** (Domini).
   
    ![Configurazione dell'accesso Single Sign-On][51]
4. Nel riquadro di destra fare clic su Claim Domain (Richiedi dominio).
   
    ![Configurazione dell'accesso Single Sign-On][52]
5. Nella finestra di dialogo **Claim a domain** (Richiedi un dominio) digitare il dominio aziendale nella casella di testo **Domain Name** (Nome di dominio) e quindi fare clic su **Claim** (Richiedi). Verificare il dominio e che lo stato sia attivo.
   
    ![Configurazione dell'accesso Single Sign-On][53]
6. Nel riquadro di spostamento a sinistra fare clic su **Identity Providers** (Provider di identità).
   
    ![Configurazione dell'accesso Single Sign-On][54]
7. Nel riquadro destro fare clic sul pulsante **Add Identity Provider** (Aggiungi provider di identità).
   
   ![Configurazione dell'accesso Single Sign-On][55]
8. Nella pagina **Identity Provider Settings** (Impostazioni provider di identità) eseguire questa procedura:
   
   ![Configurazione dell'accesso Single Sign-On][56]

    a. Nella casella di testo **Name** (Nome) digitare un nome per la configurazione. Non usare spazi.

    b. Nel portale di Azure classico copiare il valore di Issuer URL (URL autorità di certificazione) e quindi incollarlo nella casella di testo **Identity Provider Issuer** (Autorità di certificazione del provider di identità).

    c. Nel portale di Azure classico copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **Identity Provider Login URL** (URL di accesso provider di identità).

    d. Nel portale di Azure classico copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **Identity Provider Logout URL** (URL di disconnessione provider di identità).

    e. Selezionare **Sign AuthN Request** (Firma richiesta di autenticazione).

    f. Per **Send AuthN request by** (Invia richiesta di autenticazione da) selezionare **POST**.

    g. Per **Send AuthN request by** (Invia richiesta di disconnessione da) selezionare **POST**.


1. Nella sezione **Custom Attribute Mapping** (Mapping attributi personalizzati) scegliere il campo da associare all'attestazione di Azure AD. Nell'esempio è stata usata l'attestazione **emailaddress**, associata al valore di **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Questo è il nome di attestazione predefinito di Azure AD per l'attestazione basata su posta elettronica.
   
   > [!NOTE]
   > Usare l'**Identificatore utente** appropriato per associare l'utente da Azure AD al mapping degli utenti di Docusign. Selezionare il campo corretto e immettere il valore appropriato in base alle impostazioni dell'organizzazione.
   > 
   > 
   
   ![Configurazione dell'accesso Single Sign-On][57]
2. Nella sezione **Identity Provider Certificate** (Certificato provider di identità) fare clic su **Add Certificate** (Aggiungi certificato) e quindi caricare il certificato scaricato dal portale di Azure classico.
   
   ![Configurazione dell'accesso Single Sign-On][58]
3. Fare clic su **Save**.
4. Nella sezione **Identity Providers** (Provider di identità) fare clic su **Actions** (Azioni) e quindi su **Endpoints** (Endpoint).
   
   ![Configurazione dell'accesso Single Sign-On][59]
5. Nel portale di Azure classico tornare alla pagina **Configurare le impostazioni dell'app**.
6. Nella sezione **View SAML 2.0 Endpoints** (Visualizza endpoint SAML 2.0) del **portale di amministrazione di DocuSign** seguire questa procedura:
   
   ![Configurazione dell'accesso Single Sign-On][60]
   
   a. Copiare il valore di **Service Provider Issuer URL** (URL autorità di certificazione provider di servizi) e quindi incollarlo nella casella di testo **Identificatore** nel portale di Azure classico.
   
   b. Copiare il valore di **(URL di accesso provider di servizi)** (URL autorità di certificazione provider di servizi) e quindi incollarlo nella casella di testo **URL di accesso** nel portale di Azure classico.
   
   c. Fare clic su **Close** (Chiudi).
7. Nel portale di Azure classico fare clic su **Avanti**.
8. Nel portale di Azure classico selezionare la **conferma della configurazione dell'accesso Single Sign-On** e quindi fare clic su **Avanti**.
   
   ![Applicazioni][14]
9. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
   
   ![Applicazioni][15]

## Configurazione del provisioning degli account
Questa sezione descrive come abilitare il provisioning degli account utente di Active Directory in DocuSign.

### Per configurare il provisioning utente, eseguire la procedura seguente:
1. Nella pagina di integrazione dell'applicazione **DocuSign** del **portale di Azure classico** fare clic su **Configura provisioning account** per aprire la finestra di dialogo Configura provisioning utenti.
   
    ![Configurazione del provisioning degli account][30]
2. Nella pagina **Impostazioni e credenziali amministratore** specificare le credenziali di un account DocuSign con diritti sufficienti per abilitare il provisioning utenti automatico e quindi fare clic su **Avanti**.
   
    ![Configurazione del provisioning degli account][31]
3. Nella finestra di dialogo **Test connessione** fare clic su **Avvia test** e dopo che il test è riuscito fare clic su **Avanti**.
   
    ![Configurazione del provisioning degli account][32]
4. Nella pagina **Conferma** fare clic su **Operazione completata**.
   
    ![Configurazione del provisioning degli account][33]

## Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

### Per assegnare gli utenti a DocuSign, seguire questa procedura:
1. Nel **portale di Azure classico** creare un account di test.
2. Nella pagina di integrazione dell'applicazione **DocuSign** fare clic su **Assegna utenti**.
   
    ![Assegnazione degli utenti][40]
3. Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Assegnazione degli utenti][41]

È ora necessario attendere 10 minuti e verificare che l'account sia stato sincronizzato con DocuSign.

Come primo passaggio di verifica, è possibile controllare lo stato del provisioning facendo clic su Dashboard nella pagina di integrazione dell'applicazione DocuSign del portale di Azure classico.

![Assegnazione degli utenti][42]

Un ciclo di provisioning utenti completato correttamente è indicato da uno stato correlato:

![Assegnazione degli utenti][43]

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso.

Per informazioni dettagliate sul pannello di accesso, vedere Introduzione al Pannello di accesso.

## Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_06.png

[14]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_18.png

[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png

<!---HONumber=AcomDC_0817_2016-->