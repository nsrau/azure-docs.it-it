---
title: 'Esercitazione: Integrazione di Azure Active Directory con Procore SSO | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Procore SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 042a41eaa6bb21670b4c12068f1b017222f64b99
ms.lasthandoff: 04/12/2017


---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Esercitazione: Integrazione di Azure Active Directory con Procore SSO

Questa esercitazione descrive come integrare Procore SSO con Azure Active Directory (Azure AD).

L'integrazione di Procore SSO con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Procore SSO
- È possibile abilitare gli utenti per l'accesso Single Sign-On automatico a Procore SSO con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with Procore SSO, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Procore SSO.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Procore SSO, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Procore SSO abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Procore SSO dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-procore-sso-from-the-gallery"></a>Aggiungere Procore SSO dalla raccolta
Per configurare l'integrazione di Procore SSO in Azure AD, è necessario aggiungere Procore SSO dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Procore SSO dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Procore SSO**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_search.png)

5. Nel pannello dei risultati selezionare **Procore SSO** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Procore SSO con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Procore SSO che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Procore SSO.

La relazione di collegamento viene stabilita assegnando il valore del **nome utente** di Azure AD come valore di **Username** (Nome utente) in Procore SSO.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Procore SSO, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Procore SSO](#creating-a-procore-sso-test-user)** : per avere una controparte di Britta Simon in Procore SSO collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Procore SSO.

**Per configurare Single Sign-On di Azure AD con Procore SSO, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Procore SSO** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_samlbase.png)

3. Nella sezione **URL e dominio Procore SSO** l'utente deve eseguire alcuna operazione perché l'applicazione è già preintegrata in Azure.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_url.png)

4. Nella sezione **Certificato di firma SAML** fare clic su **XML metadati** e quindi salvare il file XML nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-procoresso-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Procore SSO** fare clic su **Configura Procore SSO** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_configure.png) 

7. Per configurare l'accesso Single Sign-On sul lato **Procore SSO**, accedere al sito aziendale di Procore come amministratore.

8. Nell'elenco della casella degli strumenti scegliere **Admin** per aprire la pagina delle impostazioni SSO.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-procoresso-tutorial/procore_tool_admin.png)

9. Incollare i valori nelle caselle come descritto di seguito.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-procoresso-tutorial/procore_setting_admin.png)    

    a. Incollare l'ID entità SAML copiato dal portale di Azure nella casella **Single Sign On Issuer URL** (URL autorità di certificazione Single Sign-On).

    b. Incollare l'URL del servizio Single Sign-On SAML copiato dal portale di Azure nella casella **SAML Sign On Target URL** (URL di destinazione accesso SAML).

    c. Aprire il file **XML dei metadati** scaricato in precedenza dal portale di Azure e copiare il certificato nel tag denominato **X509Certificate**. Incollare il valore copiato nel **Single Sign On x509 Certificate** (Certificato x509 Single Sign-On).

10. Fare clic su **Save Changes** (Salva modifiche).

11. Dopo queste impostazioni, è necessario inviare il **nome di dominio**, ad esempio **contoso.com**, con cui si effettua l'accesso a Procore al [team di supporto Procore](https://support.procore.com/), che attiverà l'accesso Single Sign-On federato per quel dominio.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-procore-sso-test-user"></a>Creare un utente test di Procore SSO

Attenersi alla procedura seguente per creare un utente test di Procore sul relativo lato.

1. Accedere al sito aziendale di Procore come amministratore.    

2. Nell'elenco a discesa della casella degli strumenti fare clic su **Directory** per aprire la pagina della directory aziendale.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-procoresso-tutorial/Procore_sso_directory.png)

3. Fare clic su **Add a Person** (Aggiungi una persona) per aprire il modulo e immettere le opzioni seguenti.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-procoresso-tutorial/Procore_user_add.png)

    a. Digitare il nome di Britta Simon nella casella di testo **First Name** (Nome), ad esempio **Britta**.

    b. Digitare il cognome di Britta Simon nella casella di testo **Last Name** (Cognome), ad esempio **Simon**.

    c. Nella casella di testo **Email Address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'utente, ad esempio **BrittaSimon@contoso.com**.

    d. Selezionare **Permission Template** (Modello di autorizzazione) per **Apply Permission Template Later** (Applica modello di autorizzazione più tardi).

    e. Fare clic su **Crea**.

4. Verificare e aggiornare i dettagli del contatto appena aggiunto.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-procoresso-tutorial/Procore_user_check.png)

5. Se è richiesto un invito via posta elettronica, fare clic su **Save and Send Invitation** (Salva e invia invito) oppure scegliere **Save** (Salva) per salvare direttamente e completare la registrazione dell'utente.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-procoresso-tutorial/Procore_user_save.png)    

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione viene concesso a Britta Simon l'accesso a Procore SSO per consentirle di usare l'accesso Single Sign-On di Azure.

![Assegna utente][200] 

**Per assegnare Britta Simon a Procore SSO, seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione con le applicazioni e quindi passare alla visualizzazione con le directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Procore SSO**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586). Quando si fa clic sul riquadro Procore SSO nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Procore SSO.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_203.png


