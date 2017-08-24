---
title: 'Esercitazione: Eseguire l''integrazione di Azure Active Directory con vxMaintain | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e vxMaintain.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 841a1066-593c-4603-9abe-f48496d73d10
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: ad87534af448356b8cc80d8ddd278bfb8a9165e7
ms.contentlocale: it-it
ms.lasthandoff: 08/10/2017

---
# <a name="tutorial-integrate-azure-active-directory-with-vxmaintain"></a>Esercitazione: Eseguire l'integrazione di Azure Active Directory con vxMaintain

Questa esercitazione descrive come integrare vxMaintain con Azure Active Directory (Azure AD).

Questa integrazione offre diversi vantaggi importanti. È possibile:

- È possibile controllare in Azure AD chi può accedere a vxMaintain.
- È possibile abilitare gli utenti per l'accesso automatico a vxMaintain (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con vxMaintain, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di vxMaintain abilitata per l'accesso SSO

> [!NOTE]
> Per testare i passaggi di questa esercitazione non è consigliabile usare un ambiente di produzione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede le due procedure di base seguenti:

* Aggiunta di vxMaintain dalla raccolta
* Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-vxmaintain-from-the-gallery"></a>Aggiungere vxMaintain dalla raccolta
Per configurare l'integrazione di vxMaintain in Azure AD, è necessario aggiungere vxMaintain dalla raccolta all'elenco di app SaaS gestite.

Per aggiungere vxMaintain dalla raccolta, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Riquadro "Applicazioni aziendali"][2]
    
3. Per aggiungere un'applicazione, fare clic su **Nuova applicazione** nella finestra di dialogo **Tutte le applicazioni**.

    ![Pulsante "Nuova applicazione"][3]

4. Nella casella di ricerca digitare **vxMaintain**.

    ![Elenco a discesa "Modalità Single Sign-On"](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_search.png)

5. Nell'elenco risultati, selezionare **vxMaintain** e quindi fare clic su **Aggiungi**.

    ![Collegamento di vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso SSO di Azure AD con vxMaintain usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di vxMaintain corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in vxMaintain.

Per stabilire la relazione di collegamento, in vxMaintain assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso SSO di Azure AD con vxMaintain, è necessario completare le procedure di base seguenti.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

In questa sezione è possibile abilitare l'accesso SSO di Azure AD nel portale di Azure e configurare l'accesso SSO nell'applicazione vxMaintain seguendo questa procedura:

1. Nella pagina di integrazione dell'applicazione **vxMaintain** del portale di Azure fare clic su **Single Sign-On**.

    ![Comando "Single Sign-On"][4]

2. Nell'elenco a discesa **Modalità Single Sign-On** selezionare **Accesso basato su SAML** per abilitare l'accesso SSO.
 
    ![Comando "Accesso basato su SAML"](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_samlbase.png)

3. In **URL e dominio vxMaintain** seguire questa procedura:

    ![Sezione URL e dominio vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_url.png)

    a. Nella casella **Identificatore** digitare un URL con la sintassi seguente: `https://<company name>.verisae.com`

    b. Nella casella **URL di risposta** digitare un URL con la sintassi seguente: `https://<company name>.verisae.com/DataNett/action/ssoConsume/mobile?_log=true`

    > [!NOTE] 
    > I valori precedenti non sono valori reali. Aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto di vxMaintain](http://www.verisae.com/contact-us).
 
4. In **Certificato di firma SAML** selezionare **XML metadati** e quindi salvare il file di metadati nel computer.

    ![Sezione "Certificato di firma SAML"](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_certificate.png) 

5. Selezionare **Salva**.

    ![Pulsante Salva](./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_400.png)

6. Per configurare l'accesso SSO di **vxMaintain**, inviare il file **XML metadati** scaricato al [team di supporto di vxMaintain](http://www.verisae.com/contact-us).

> [!TIP]
> Durante la configurazione dell'app, nel [portale di Azure](https://portal.azure.com) è disponibile un riepilogo delle istruzioni precedenti. Dopo aver aggiunto l'app dalla sezione **Active Directory** > **Applicazioni aziendali**, selezionare la scheda **Single Sign-On** e accedere alla documentazione incorporata dalla sezione **Configurazione**. 
>
>Per altre informazioni sulla funzionalità di documentazione incorporata vedere [Gestione dell'accesso Single Sign-On per le app aziendali](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
In questa sezione si crea un utente di test Britta Simon nel portale di Azure seguendo questa procedura:

![Utente di test di Azure AD][100]

1. Nel **portale di Azure** fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante "Azure Active Directory"](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_01.png) 

2. Per visualizzare un elenco di utenti, passare a **Utenti e gruppi** > **Tutti gli utenti**.
    
    ![Collegamento "Tutti gli utenti"](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_02.png)  
    Verrà visualizzata la finestra di dialogo **Tutti gli utenti**. 

3. Per aprire la finestra di dialogo **Utente**, fare clic su **Aggiungi**.
 
    ![Pulsante Aggiungi](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_03.png) 

4. Nella finestra di dialogo **Utente** seguire questa procedura:
 
    ![Finestra di dialogo Utente](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_04.png) 

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo e-mail dell'utente di test Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore generato nella casella **Password**.

    d. Selezionare **Crea**.
 
### <a name="create-a-vxmaintain-test-user"></a>Creare un utente di test di vxMaintain

In questa sezione viene creato un utente di test di nome Britta Simon in vxMaintain. Collaborare con il [team di supporto di vxMaintain](http://www.verisae.com/contact-us) per aggiungere gli utenti alla piattaforma vxMaintain. Prima di usare l'accesso SSO, creare e attivare gli utenti.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a vxMaintain. A tale scopo, seguire questa procedura:

![Utente di test nell'elenco Nome visualizzato][200] 

1. Nella visualizzazione **Applicazioni** del portale di Azure passare a visualizzazione **Directory** > **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Collegamento "Tutte le applicazioni"][201] 

2. Nell'elenco **Applicazioni** selezionare **vxMaintain**.

    ![Collegamento di vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_app.png) 

3. Nel riquadro sinistro fare clic su **Utenti e gruppi**.

    ![Collegamento "Utenti e gruppi"][202] 

4. Fare clic su **Aggiungi** e quindi nel riquadro **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Collegamento "Utenti e gruppi"][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti** e quindi fare clic su **Seleziona**.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.
    
### <a name="test-your-azure-ad-single-sign-on"></a>Testare l'accesso Single Sign-On di Azure AD

In questa sezione viene testata la configurazione dell'accesso SSO di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro **vxMaintain** nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione vxMaintain.

Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="next-steps"></a>Passaggi successivi

* [Elenco di esercitazioni sull'integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_203.png


