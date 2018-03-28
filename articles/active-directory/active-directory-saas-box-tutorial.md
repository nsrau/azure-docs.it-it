---
title: 'Esercitazione: Integrare Azure Active Directory con Box | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Box.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2017
ms.author: jeedes
ms.openlocfilehash: 638ae63057df00375b05a58e3ceab510e2a608de
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="integrate-azure-active-directory-with-box"></a>Integrare Azure Active Directory con Box

Questa esercitazione descrive come integrare Azure Active Directory (Azure AD) con Box.

L'integrazione di Azure AD con Box offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Box.
- È possibile abilitare l'accesso automatico degli utenti a Box (Single Sign-On) con i loro account Azure AD.
- È possibile gestire gli account da una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Box, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Box abilitata per l'accesso Single Sign-On

> [!NOTE]
> Per testare i passaggi di questa esercitazione, è consigliabile *non* usare un ambiente di produzione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Box dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-box-from-the-gallery"></a>Aggiunta di Box dalla raccolta
Per configurare l'integrazione di Azure AD con Box, aggiungere Box dalla raccolta al proprio elenco di app SaaS gestite eseguendo queste operazioni:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Finestra "Applicazioni aziendali"][2]
    
3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore della finestra.

    ![Pulsante "Nuova applicazione"][3]

4. Nella casella di ricerca digitare **Box**, selezionare **Box** nell'elenco dei risultati e quindi selezionare **Aggiungi**.

    ![Box nell'elenco risultati](./media/active-directory-saas-box-tutorial/tutorial_box_search.png)
### <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Box con un utente di test di nome "Britta Simon".

Per il corretto funzionamento dell'accesso Single Sign-On, Azure AD deve poter identificare quale sia la controparte di un utente di Azure AD in Box. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e lo stesso utente in Box.

Per stabilire la relazione di collegamento, assegnare a *Username* (Nome utente) in Box il valore del *nome utente* in Azure AD.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Box, completare i blocchi predefiniti nelle cinque sezioni seguenti.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

Abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure e configurarlo nell'applicazione Box eseguendo queste operazioni:

1. Nella pagina di integrazione dell'applicazione **Box** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento "Single Sign-On"][4]

2. Nella finestra **Single Sign-On** selezionare **Accesso basato su SAML** nella casella **Modalità Single Sign-On**.
 
    ![Finestra "Single Sign-On"](./media/active-directory-saas-box-tutorial/tutorial_box_samlbase.png)

3. In **URL e dominio Box** eseguire queste operazioni:

    ![Informazioni su URL e dominio di Box per l'accesso Single Sign-On](./media/active-directory-saas-box-tutorial/url3.png)

    a. Nella casella **URL accesso** digitare un URL in questo formato: *https://\<sottodominio>.box.com*.

    b. Nella casella di testo **Identificatore** digitare **box.net**.
     
    > [!NOTE] 
    > I valori precedenti non sono valori reali. Aggiornarli con l'URL e l'identificatore di accesso effettivi. Per ottenere i valori, contattare il [team del supporto clienti di Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). 

4. In **Certificato di firma SAML** selezionare **XML metadati** e quindi salvare il file di metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-box-tutorial/tutorial_box_certificate.png) 

5. Selezionare **Salva**.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-box-tutorial/tutorial_general_400.png)
    
6. Per configurare l'accesso Single Sign-On per l'applicazione, seguire la procedura descritta in [Set up SSO on your own](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown) (Configurare Single Sign-On manualmente).

> [!NOTE] 
> Se non si è in grado di abilitare le impostazioni dell'accesso Single Sign-On per l'account Box, potrebbe essere necessario contattare il [team del supporto clienti di Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) e fornire il file XML scaricato.

> [!TIP]
> Durante la configurazione dell'app, nel [portale di Azure](https://portal.azure.com) è disponibile un riepilogo delle istruzioni precedenti. Dopo aver aggiunto l'app nella sezione **Active Directory** > **Applicazioni aziendali**, selezionare la scheda **Single Sign-On** e quindi accedere alla documentazione integrata nella sezione **Configurazione** nella parte inferiore. Per altre informazioni sulla funzionalità di documentazione integrata vedere [Documentazione integrata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
>

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione si crea un utente di test Britta Simon nel portale di Azure seguendo questa procedura:

![Creare un utente test di Azure AD][100]

1. Nel portale di Azure fare clic su **Azure Active Directory** nel riquadro sinistro.

    ![Collegamento Azure Active Directory](./media/active-directory-saas-box-tutorial/create_aaduser_01.png)

2. Per visualizzare un elenco degli utenti correnti, selezionare **Utenti e gruppi** > **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-box-tutorial/create_aaduser_02.png)

3. Nella parte superiore della finestra **Tutti gli utenti** selezionare **Aggiungi**.

    ![Pulsante Aggiungi](./media/active-directory-saas-box-tutorial/create_aaduser_03.png)

    Viene visualizzata la finestra **Utente**.

4. Nella finestra **Utente** seguire questa procedura:

    ![Finestra Utente](./media/active-directory-saas-box-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Create**.
 
### <a name="create-a-box-test-user"></a>Creare un utente di test di Box

In questa sezione viene creato un utente di test di nome Britta Simon in Box. Box supporta il provisioning JIT, abilitato per impostazione predefinita. Se non esiste già, un nuovo utente viene automaticamente creato quando si tenta di accedere a Box. Non è necessario creare manualmente l'utente.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Box. A tale scopo, seguire questa procedura:

![Assegnare il ruolo utente][200]

1. Nel portale di Azure aprire la visualizzazione **Applicazioni**, passare alla visualizzazione **Directory** e quindi selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Collegamenti "Applicazioni aziendali" e "Tutte le applicazioni"][201] 

2. Nell'elenco **Applicazioni** selezionare **Box**.

    ![Collegamento Box](./media/active-directory-saas-box-tutorial/tutorial_box_app.png)  

3. Nel riquadro sinistro fare clic su **Utenti e gruppi**.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic su **Aggiungi** e quindi nel riquadro **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**.

6. Fare clic sul pulsante **Seleziona**.

7. Nella finestra **Aggiungi assegnazione** selezionare **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro **Box** nel pannello di accesso, viene visualizzata la pagina di accesso per accedere all'applicazione Box.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sull'integrazione di app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configurare il provisioning utenti](active-directory-saas-box-userprovisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-box-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-box-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-box-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-box-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-box-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-box-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-box-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-box-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-box-tutorial/tutorial_general_203.png

