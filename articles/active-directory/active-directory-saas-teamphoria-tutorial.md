---
title: 'Esercitazione: Integrazione di Azure Active Directory con Teamphoria| Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Teamphoria.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 2a35efb04d7fe22abc6894c149caf090666ce016
ms.lasthandoff: 04/12/2017


---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Esercitazione: Integrazione di Azure Active Directory con Teamphoria

Questa esercitazione illustra come integrare Teamphoria con Azure Active Directory (Azure AD).

L'integrazione di Teamphoria con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Teamphoria
- È possibile abilitare gli utenti per l'accesso automatico a Teamphoria (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with Teamphoria, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Teamphoria.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Teamphoria, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Teamphoria abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Teamphoria dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-teamphoria-from-the-gallery"></a>Aggiungere Teamphoria dalla raccolta
Per configurare l'integrazione di Teamphoria in Azure AD, è necessario aggiungere Teamphoria dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Teamphoria dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Digitare **Teamphoria** nella casella di ricerca.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_search.png)

5. Nel pannello dei risultati selezionare **Teamphoria** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Teamphoria usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Teamphoria che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Teamphoria.

La relazione di collegamento viene stabilita assegnando il valore del **nome utente** di Azure AD come valore di **Username** (Nome utente) in Teamphoria.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Teamphoria, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Teamphoria](#creating-a-teamphoria-test-user)** : per avere una controparte di Britta Simon in Teamphoria collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Teamphoria.

**Per configurare l'accesso Single Sign-On di Azure AD con Teamphoria, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Teamphoria** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

3. Nella sezione **URL e dominio Teamphoria** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<sub-domain>.teamphoria.com/login`    

    > [!NOTE] 
    > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'URL di accesso effettivo. Contattare il [team di supporto clienti di Teamphoria](https://www.teamphoria.com/) per richiedere l'URL di accesso. 

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Teamphoria** fare clic su **Configura Teamphoria** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_configure.png) 

7. Per configurare l'accesso Single Sign-On sul lato **Teamphoria**, accedere all'applicazione Teamphoria come amministratore.

8. Passare all'opzione **ADMIN SETTINGS** (Impostazioni di amministrazione) nella barra degli strumenti di sinistra e scegliere **SINGLE SIGN-ON** sotto la scheda di configurazione per aprire la finestra di configurazione SSO.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/admin_sso_configure.png)

9. Fare clic sull'opzione **ADD NEW IDENTITY PROVIDER** (Aggiungi nuovo provider di identità) nell'angolo superiore destro per aprire il modulo e aggiungere le impostazioni per l'accesso Single Sign-On.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/add_new_identity_provider.png)

10. Immettere i dettagli nei campi come descritto di seguito.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **DISPLAY NAME** (Nome visualizzato): immettere il nome del plug-in visualizzato nella pagina di amministrazione.

    b. **BUTTON NAME** (Nome pulsante): il nome della scheda che verrà visualizzata nella pagina di accesso per l'accesso Single Sign-On.

    c. **CERTIFICATE** (Certificato): aprire il certificato scaricato in precedenza dal portale di Azure nel blocco note, copiare il contenuto dello stesso e incollarlo qui nella casella.

    d. **ENTRY POINT** (Punto di ingresso): incollare l'**URL del servizio Single Sign-On SAML** copiato in precedenza dal portale di Azure.

    e. Posizionare il selettore dell'opzione su **ON** e fare clic su **SAVE** (Salva).    

<!--### Next steps

To ensure users can sign-in to Teamphoria after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Teamphoria prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Teamphoria in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Teamphoria users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-teamphoria-test-user"></a>Creare un utente test di Teamphoria

Per consentire agli utenti di Azure AD di accedere a Teamphoria, è necessario eseguirne il provisioning in Teamphoria. Nel caso di Teamphoria, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Teamphoria come amministratore.

2. Fare clic sulle impostazioni **ADMIN** nella barra degli strumenti di sinistra e scegliere **USERS** (Utenti) nella scheda **MANAGE** (Gestisci) per aprire la pagina di amministrazione per gli utenti.

    ![Aggiungere un dipendente](./media/active-directory-saas-teamphoria-tutorial/admin_manage_users.png)

3. Fare clic sull'opzione **MANUAL INVITE** (Invito manuale).

    ![Invita persone](./media/active-directory-saas-teamphoria-tutorial/admin_manage_add_users.png)    

4. In questa pagina effettuare l'operazione seguente. 
    
    ![Invita persone](./media/active-directory-saas-teamphoria-tutorial/manual_user_invite.png)    

    a. Nella casella di testo **EMAIL ADDRESS** (Indirizzo email) immettere l'**indirizzo email** di Britta Simon.

    b. Nella casella di testo **FIRST NAME** (Nome) digitare **Britta**.

    c. Nella casella di testo **LAST NAME** (Cognome) digitare **Simon**.

    d. Fare clic su **INVITE 1 USER** (Invita 1 utente). L'utente deve accettare l'invito per essere creato nel sistema.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione viene concesso a Britta Simon l'accesso a Teamphoria per consentirle di usare l'accesso Single Sign-On di Azure.

![Assegna utente][200] 

**Per assegnare Britta Simon a Teamphoria, seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione con le applicazioni e quindi passare alla visualizzazione con le directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Teamphoria**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_203.png


