<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con DocuSign | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e DocuSign."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/26/2016"
	ms.author="jeedes"/>


# Esercitazione: Integrazione di Azure Active Directory con DocuSign

Questa esercitazione descrive l'integrazione di Azure e DocuSign. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

- Sottoscrizione di Azure valida
- Tenant in DocuSign



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


6. Nella casella di ricerca digitare **Docusign**.

	![Configurazione dell'accesso Single Sign-On][5]

7. Nel riquadro dei risultati selezionare **Docusign** e quindi fare clic su **Completa** per aggiungere l'applicazione.

	![Configurazione dell'accesso Single Sign-On][6]




## Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Docusign tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.


### Per configurare l'accesso Single Sign-On, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Docusign** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo Configura accesso Single Sign-On.

	![Configurazione dell'accesso Single Sign-On][7]

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Docusign** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su Avanti.

	![Configurazione dell'accesso Single Sign-On][8]

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

	![Configurazione dell'accesso Single Sign-On][9]

	a. Nella casella di testo **URL di accesso** digitare l'URL del tenant Docusign adottando il modello seguente: `https://<company name>.docusign.net/Member/MemberLogin.aspx?ssoname=<SSO instance name>`

	b. Fare clic su **Avanti**.


    > [AZURE.TIP] Se non si conosce l'URL app del tenant, provare a contattare Docusign tramite SSOSetup@Docusign.com per ottenere l'URL SSO avviato dal provider di servizi per il tenant.
 

4. Nella pagina **Configura accesso Single Sign-On in Docusign** fare clic su **Scarica certificato** e quindi salvare il certificato localmente nel computer.

	![Configurazione dell'accesso Single Sign-On][10]


5. In un'altra finestra del Web browser accedere al sito aziendale di **Docusign** come amministratore.


6. Nel menu in alto espandere il menu dell'utente, fare clic su **Preferences**, quindi nel riquadro di spostamento a sinistra espandere **Account Management** e infine fare clic su **Features**.

	![Configurazione dell'accesso Single Sign-On][11]

7. Fare clic su **SAML Configuration**, quindi fare clic sul collegamento **SAML Configuration**.



8. Nella sezione **SAML 2.0 Configuration** seguire questa procedura:

	![Configurazione dell'accesso Single Sign-On][13]


    a. Nella pagina **Configura accesso Single Sign-On in Docusign** del portale di Azure classico copiare il valore di URL autorità di certificazione** e incollarlo nella casella di testo **Identity Provider Endpoint URL**.

    > [AZURE.IMPORTANT] Se questa opzione di configurazione non è disponibile, contattare l'account manager di Docusign o il team di supporto SSO tramite posta elettronica ([SSOSetup@docusign.com](mailto:SSOSetup@docusign.com)).
 
    b. Per caricare il certificato scaricato, fare clic su **Browse**.


    c. Selezionare **Enable first name, last name and email address**.


    d. Fare clic su **Save**.


9. Nel portale di Azure classico selezionare la **conferma della configurazione dell'accesso Single Sign-On** e quindi fare clic su **Avanti**.

	![Applicazioni][14]

10. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.

	![Applicazioni][15]


 

## Configurazione del provisioning degli account

Questa sezione descrive come abilitare il provisioning degli account utente di Active Directory in DocuSign.

### Per configurare il provisioning utente, eseguire la procedura seguente:

1. Nella pagina di integrazione dell'applicazione **DocuSign** del **portale di Azure classico** fare clic su **Configura provisioning account** per aprire la finestra di dialogo Configura provisioning utenti.

	![Configurazione del provisioning degli account][30]
 

2. Nella pagina **Impostazioni e credenziali amministratore** fornire le credenziali dell'account DocuSign con diritti sufficienti per abilitare il provisioning utenti e quindi fare clic su **Avanti**.

	![Configurazione del provisioning degli account][31]

3. Nella finestra di dialogo **Test connessione** fare clic su **Avvia test** e dopo che il test è riuscito fare clic su **Avanti**.

	![Configurazione del provisioning degli account][32]

3. Nella pagina **Conferma** fare clic su **Operazione completata**.

![Configurazione del provisioning degli account][33]
 

## Assegnazione degli utenti

Per testare la configurazione, è necessario assegnare l'accesso all'applicazione agli utenti di Azure AD a cui si desidera consentire l'uso dell'applicazione.

### Per assegnare gli utenti a Docusign, seguire questa procedura:

1. Nel **portale di Azure classico** creare un account di test.

2. Nella **pagina di integrazione dell'applicazione Docusign** fare clic su **Assegna utenti**.

	![Assegnazione degli utenti][40]
 

3. Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

	![Assegnazione degli utenti][41]


È ora necessario attendere 10 minuti e verificare che l'account sia stato sincronizzato con Docusign.

Come primo passaggio di verifica, è possibile controllare lo stato del provisioning facendo clic su Dashboard nella pagina di integrazione dell'applicazione Docusign del portale di Azure classico.

![Assegnazione degli utenti][42]

Un ciclo di provisioning utenti completato correttamente è indicato da uno stato correlato:

![Assegnazione degli utenti][43]


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

<!---HONumber=AcomDC_0601_2016-->