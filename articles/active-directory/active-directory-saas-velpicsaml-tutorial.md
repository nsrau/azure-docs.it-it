---
title: 'Esercitazione: Integrazione di Azure Active Directory con Velpic SAML| Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 5325f3cca00167e6b7b687509ce43435447ad2f4
ms.lasthandoff: 04/07/2017


---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Esercitazione: Integrazione di Azure Active Directory con Velpic SAML

Questa esercitazione spiega come integrare Velpic SAML con Azure Active Directory (Azure AD).

L'integrazione di Velpic SAML con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Velpic SAML
- È possibile abilitare gli utenti per l'accesso automatico a Velpic SAML (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Velpic SAML, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Velpic SAML abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Velpic SAML dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-velpic-saml-from-the-gallery"></a>Aggiungere Velpic SAML dalla raccolta
Per configurare l'integrazione di Velpic SAML in Azure AD, è necessario aggiungere Velpic SAML dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Velpic SAML dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Velpic SAML**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_search.png)

5. Nel pannello dei risultati selezionare **Velpic SAML** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Velpic SAML in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Velpic SAML che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Velpic SAML.

La relazione di collegamento viene stabilita assegnando il valore del **nome utente** di Azure AD come valore di **Username** (Nome utente) in Velpic SAML.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Velpic SAML, è necessario completare i seguenti blocchi predefiniti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Velpic SAML](#creating-a-velpic-saml-test-user)** : per avere una controparte di Britta Simon in Velpic SAML collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Velpic SAML.

**Per configurare Single Sign-On di Azure AD con Velpic SAML, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Velpic SAML** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_samlbase.png)

3. Immettere i dettagli nella sezione **URL e dominio Velpic SAML**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_url.png)

    a. Nella casella di testo **URL di accesso** digitare il valore come: `https://<sub-domain>.velpicsaml.net`

    b. Nella casella di testo **Identificatore** incollare il valore dell'**URL di Single Sign-On** `https://auth.velpic.com/saml/v2/<entity-id>/login`
    
    > [!NOTE]
    > Si noti che l'URL di Single Sign-On verrà indicato dal team di Velpic SAML e il valore dell'identificatore sarà disponibile quando si configura il plug-in SSO sul lato Velpic SAML. È necessario copiare tale valore dalla pagina dell'applicazione Velpic SAML e incollarlo qui.

4. Nella sezione **Certificato di firma SAML** fare clic su **XML metadati** e quindi salvare il file XML nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_400.png)

6. Nella sezione Configurazione Velpic SAML scegliere Configura Velpic SAML per aprire la finestra di dialogo Configura accesso. Copiare l'ID di entità SAML dalla sezione Riferimento rapido.

7. In un'altra finestra del Web browser accedere al sito aziendale di Velpic SAML come amministratore.

8. Fare clic sulla scheda **Manage** (Gestisci), passare alla sezione **Integration** (Integrazione) e qui scegliere **Plugins** per creare un nuovo plug-in per l'accesso.

    ![Plug-in](./media/active-directory-saas-velpicsaml-tutorial/velpic_1.png)

9. Fare clic sul pulsante **Add plugin** (Aggiungi plug-in).
    
    ![Plug-in](./media/active-directory-saas-velpicsaml-tutorial/velpic_2.png)

10. Fare clic sul riquadro **SAML** nella pagina Add plugin (Aggiungi plug-in).
    
    ![Plug-in](./media/active-directory-saas-velpicsaml-tutorial/velpic_3.png)

11. Immettere il nome del nuovo plug-in SAML e scegliere il pulsante **Add** (Aggiungi).

    ![Plug-in](./media/active-directory-saas-velpicsaml-tutorial/velpic_4.png)

12. Immettere i dettagli seguenti:

    ![Plug-in](./media/active-directory-saas-velpicsaml-tutorial/velpic_5.png)

    a. Nella casella di testo **Name** (Nome) digitare il nome del plug-in SAML.

    b. Nella casella di testo **Issuer URL** (URL dell'autorità di certificazione) incollare l'**ID di entità SAML** copiato in precedenza dalla finestra **Configura accesso** del portale di Azure.

    c. Nel riquadro della **configurazione dei metadati del provider** caricare il file XML dei metadati scaricato dal portale di Azure.

    d. È anche possibile scegliere di abilitare SAML al momento del provisioning selezionando la casella di controllo **Auto create new users** (Crea automaticamente nuovi utenti). Se un utente non esiste in Velpic e questo flag non è abilitato, l'accesso da Azure avrà esito negativo. Se il flag è abilitato, verrà automaticamente eseguito il provisioning dell'utente in Velpic al momento dell'accesso. 

    e. Copiare l'**URL di Single Sign-On** dalla casella di testo e incollarlo nel portale di Azure.
    
    f. Fare clic su **Save**.

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-velpic-saml-test-user"></a>Creare un utente test di Velpic SAML

Questo passaggio in genere non è necessario poiché l'applicazione supporta il provisioning degli utenti just-in-time. Se il provisioning automatico degli utenti non è abilitato, la creazione manuale dell'utente può essere eseguita come descritto di seguito.

Accedere al sito aziendale di Velpic SAML come amministratore e seguire questa procedura:
    
1. Fare clic sulla scheda Manage (Gestisci) e passare alla sezione Users (Utenti), quindi fare clic sul pulsante New (Nuovo) per aggiungere gli utenti.

    ![Aggiungi utente](./media/active-directory-saas-velpicsaml-tutorial/velpic_7.png)

2. Nella finestra di dialogo **Create New User** (Crea nuovo utente) effettuare le operazioni seguenti.

    ![user](./media/active-directory-saas-velpicsaml-tutorial/velpic_8.png)
    
    a. Digitare il nome di Britta Simon nella casella di testo **First Name** (Nome).

    b. Digitare il cognome di Britta Simon nella casella di testo **Last Name** (Cognome).

    c. Digitare il nome utente di Britta Simon nella casella di testo **User Name** (Nome utente).

    d. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica dell'account di Britta Simon.

    e. Le informazioni rimanenti sono facoltative, se necessario si possono compilare.
    
    f. Fare clic su **SAVE**.    

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione si concede a Britta Simon l'accesso a Velpic SAML per consentirle di usare l'accesso Single Sign-On di Azure.

![Assegna utente][200] 

**Per assegnare Britta Simon a Velpic SAML, seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione con le applicazioni e quindi passare alla visualizzazione con le directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Velpic SAML**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

1. Quando si fa clic sul riquadro Velpic SAML nel Pannello di accesso, viene visualizzata la pagina di accesso dell'applicazione Velpic SAML, in cui dovrebbe apparire il pulsante **Log In With Azure AD** (Accedi con Azure AD).

    ![Plug-in](./media/active-directory-saas-velpicsaml-tutorial/velpic_6.png)

2. Fare clic sul pulsante **Log In With Azure AD** (Accedi con Azure AD) per accedere a Velpic usando il proprio account Azure AD.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_203.png


