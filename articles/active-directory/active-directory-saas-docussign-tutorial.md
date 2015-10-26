<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con DocuSign | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e DocuSign."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/06/2015"
	ms.author="markvi"/>


# Esercitazione: Integrazione di Azure Active Directory con DocuSign

Questa esercitazione descrive l'integrazione di Azure e DocuSign. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

- Sottoscrizione di Azure valida
- Tenant in DocuSign



Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. [Abilitazione dell'integrazione dell'applicazione per DocuSign](#enabling-the-application-integration-for-docusign) 


2. [Configurazione dell'accesso Single Sign-On](#configuring-single-sign-on)


3. [Configurazione del provisioning degli account](#configuring-account-provisioning)


4. [Assegnazione degli utenti](#assigning-users)




<br><br>![Configurazione dell'accesso Single Sign-On][0]<br>


 

## Abilitazione dell'integrazione dell'applicazione per DocuSign

Questa sezione descrive come abilitare l'integrazione dell'applicazione per DocuSign.

### Per abilitare l'integrazione dell'applicazione per DocuSign, seguire questa procedura:

1. Nel portale di gestione di Azure fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br>![Configurazione dell'accesso Single Sign-On][1]<br>

2. Nell'elenco Directory selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto. <br><br>![Configurazione dell'accesso Single Sign-On][2]<br>

4. Fare clic su **Aggiungi** nella parte inferiore della pagina. <br><br>![Applicazioni][3]<br>

5. Nella finestra di dialogo Come procedere fare clic su **Aggiungi un'applicazione dalla raccolta**. <br><br>![Configurazione dell'accesso Single Sign-On][4]<br>


6. Nella casella di ricerca digitare **Docusign**. <br><br>![Configurazione dell'accesso Single Sign-On][5]<br>

7. Nel riquadro dei risultati selezionare **Docusign**, quindi fare clic su **Completa** per aggiungere l'applicazione. <br><br>![Configurazione dell'accesso Single Sign-On][6]<br>




## Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Docusign tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.


### Per configurare l'accesso Single Sign-On, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Docusign** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo Configura accesso Single Sign-On. <br><br>![Configurazione dell'accesso Single Sign-On][7]<br>

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Docusign** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su Avanti. <br><br>![Configurazione dell'accesso Single Sign-On][8]<br>

3. Nella pagina **Configura URL app**, nella casella di testo **URL di accesso a Docusign** digitare l'URL del tenant Docusign, quindi fare clic su **Avanti**. Lo schema dell'URL è il seguente: *https://<yourcompanyname>.docusign.net/Member/MemberLogin.aspx?ssoname=<yourSSOInstanceName>* <br><br>![Configurazione dell'accesso Single Sign-On][9]<br>


    > [AZURE.TIP]Se non si conosce l'URL app del tenant, provare a contattare Docusign tramite SSOSetup@Docusign.com per ottenere l'URL SSO avviato dal provider di servizi per il tenant.
 

4. Nella pagina **Configura accesso Single Sign-On in Docusign** fare clic su **Download certificato** e quindi salvare il certificato localmente nel computer. <br><br>![Configurazione dell'accesso Single Sign-On][10]<br>


5. In un'altra finestra del Web browser accedere al sito aziendale di **Docusign** come amministratore.


6. Nel menu in alto espandere il menu dell'utente, fare clic su **Preferences**, quindi nel riquadro di spostamento a sinistra espandere **Account Management** e infine fare clic su **Features**. <br><br>![Configurazione dell'accesso Single Sign-On][11]<br>

7. Fare clic su **SAML Configuration**, quindi fare clic sul collegamento **SAML Configuration**.



8. Nella sezione **SAML 2.0 Configuration** seguire questa procedura: <br><br>![Configurazione dell'accesso Single Sign-On][13]<br>


    a. Nella finestra di dialogo **Configura accesso Single Sign-On in Docusign** del portale di Azure, copiare il valore di URL autorità di certificazione** e incollarlo nella casella di testo **Identity Provider Endpoint URL**.

    > [AZURE.IMPORTANT]Se questa opzione di configurazione non è disponibile, contattare l'account manager di Docusign o il team di supporto SSO tramite posta elettronica ([SSOSetup@docusign.com](mailto:SSOSetup@docusign.com)).
 
    b. Per caricare il certificato scaricato, fare clic su **Browse**.


    c. Selezionare **Enable first name, last name and email address**.


    d. Fare clic su **Save**.


9. Nel portale di Azure AD selezionare la **conferma della configurazione dell'accesso Single Sign-On** e quindi fare clic su **Avanti**. <br><br>![Applicazioni][14]<br>

10. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**. <br><br>![Applicazioni][15]<br>


 

## Configurazione del provisioning degli account

Questa sezione descrive come abilitare il provisioning degli account utente di Active Directory in DocuSign.

### Per configurare il provisioning utente, eseguire la procedura seguente:

1. Nella pagina di integrazione dell'applicazione **DocuSign** del **portale di gestione di Azure** fare clic su **Configura provisioning utenti** per aprire la finestra di dialogo Configura provisioning utenti. <br><br>![Configurazione del provisioning degli account][30]<br>
 

2. Nella pagina **Impostazioni e credenziali amministratore** fornire le credenziali dell'account DocuSign con diritti sufficienti per abilitare il provisioning utenti e quindi fare clic su **Avanti**. <br><br>![Configurazione del provisioning degli account][31]<br>

3. Nella finestra di dialogo **Test connessione** fare clic su **Avvia test** e dopo che il test è riuscito, fare clic su **vanti**. <br><br>![Configurazione del provisioning degli account][32]<br>

3. Nella pagina **Conferma** fare clic su **Operazione completata**.

<br><br>![Configurazione del provisioning degli account][33]<br>
 

## Assegnazione degli utenti

Per testare la configurazione, è necessario assegnare l'accesso all'applicazione agli utenti di Azure AD a cui si desidera consentire l'uso dell'applicazione.

### Per assegnare gli utenti a Docusign, seguire questa procedura:

1. Nel **portale di Azure AD** creare un account di test.

2. Nella pagina di integrazione dell'applicazione **Docusign** fare clic su **Assegna utenti**. <br><br>![Assegnazione degli utenti][40]<br>
 

3. Selezionare l'utente test, fare clic su **Assegna**, quindi fare clic su **Sì** per confermare l'assegnazione.

<br><br>![Assegnazione degli utenti][41]<br>


È ora necessario attendere 10 minuti e verificare che l'account sia stato sincronizzato con Docusign.

Come primo passaggio di verifica, è possibile controllare lo stato del provisioning selezionando il Dashboard nella pagina di integrazione dell'applicazione Docusign del portale di gestione di Azure. <br><br>![Assegnazione degli utenti][42]<br>

Un ciclo di provisioning utenti completato correttamente è indicato da uno stato correlato: <br><br>![Assegnazione degli utenti][43]<br>


Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso.

Per informazioni dettagliate sul pannello di accesso, vedere Introduzione al Pannello di accesso.





## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_06.png
[11]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_07.png

[13]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_09.png
[14]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_18.png

<!---HONumber=Oct15_HO3-->