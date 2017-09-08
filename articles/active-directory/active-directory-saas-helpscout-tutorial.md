---
title: 'Esercitazione: Integrazione di Azure Active Directory con Help Scout | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Help Scout.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 84cee39c28a0f7e6b9878441e504131795673020
ms.contentlocale: it-it
ms.lasthandoff: 08/28/2017

---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Esercitazione: Integrazione di Azure Active Directory con Help Scout

Questa esercitazione descrive come integrare Help Scout con Azure Active Directory (Azure AD).

L'integrazione di Help Scout con Azure AD offre i vantaggi seguenti:

- In Azure AD è possibile controllare chi può accedere a Help Scout.
- È possibile connettere automaticamente gli utenti a Help Scout usando Single Sign-On e l'account Azure AD di un utente.
- È possibile gestire gli account da una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Help Scout, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Help Scout, con Single Sign-On attivato 

> [!NOTE]
> Se si testano i passaggi di questa esercitazione, è consigliabile non testarli in un ambiente di produzione.

Raccomandazioni per il test dei passaggi di questa esercitazione:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiungere Help Scout dalla raccolta.
2. Configurare e testare l'accesso Single Sign-On di Azure AD.

## <a name="add-help-scout-from-the-gallery"></a>Aggiungere Help Scout dalla raccolta
Per configurare l'integrazione di Help Scout con Azure AD, nella raccolta aggiungere Help Scout al proprio elenco delle app SaaS gestite.

Per aggiungere Help Scout dalla raccolta:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel menu sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Selezionare **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pagina Applicazioni aziendali][2]
    
3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca immettere **Help Scout**. Nei risultati della ricerca selezionare **Help Scout** e quindi selezionare **Aggiungi**.

    ![Help Scout nell'elenco risultati](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Help Scout in base a un utente di test di nome *Britta Simon*.

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente di Azure AD in Help Scout. Deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Help Scout.

Per stabilire la relazione di collegamento, in Help Scout per **Username** (Nome utente) assegnare il valore del **nome utente** in Azure AD.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Help Scout, completare le attività seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#set-up-azure-ad-single-sign-on). Configura un utente per l'uso di questa funzionalità.
2. [Creare un utente test di Azure AD](#create-an-azure-ad-test-user). Testa l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. [Creare un utente di test di Help Scout](#create-a-help-scout-test-user). Crea una controparte di Britta Simon in Help Scout collegata alla rappresentazione dell'utente in Azure AD.
4. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user). Configura Britta Simon per usare l'accesso Single Sign-On di Azure AD.
5. [Testare l'accesso Single Sign-On](#test-single-sign-on). Verifica se la configurazione funziona.

### <a name="set-up-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione si configura l'accesso Single Sign-On di Azure AD nel portale di Azure, quindi si configura Single Sign-On nell'applicazione Help Scout.

Per configurare l'accesso Single Sign-On di Azure AD con Help Scout:

1. Nella pagina di integrazione dell'applicazione **Help Scout** del portale di Azure selezionare **Single Sign-On**.
 
    ![Collegamento di configurazione di Single Sign-On][4]

2. Nella pagina **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML**.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. In **URL e dominio Help Scout**, per configurare l'applicazione in modalità avviata da IDP, seguire questa procedura:

    1. Nella casella **Identificatore** immettere un URL con il modello seguente: `urn:auth0:helpscout:<instancename>`

    2. Nella casella **URL di risposta** immettere un URL con il modello seguente: `https://helpscout.auth0.com/login/callback?connection=<instancename>`

    ![Informazioni su URL e dominio per Single Sign-On di Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url.png)

4. Se si preferisce configurare l'applicazione in modalità avviata da SP, selezionare la casella di controllo **Mostra impostazioni URL avanzate** e quindi seguire questa procedura:

    * Nella casella **URL di accesso** immettere un URL con il formato seguente: `https://secure.helpscout.net/members/login/`

    ![Informazioni su URL e dominio per Single Sign-On di Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url1.png)
 
    > [!NOTE] 
    > I valori in questi URL sono forniti solo a scopo dimostrativo. È necessario aggiornarli con l'URL dell'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto di Help Scout](mailto:help@helpscout.com). 

5. In **Certificato di firma SAML** selezionare **XML metadati** e quindi salvare il file di metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. Selezionare **Salva**.

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-helpscout-tutorial/tutorial_general_400.png)
    
7. Per configurare l'accesso Single Sign-On sul lato Help Scout, inviare il file XML di metadati scaricato al [team di supporto di Help Scout](mailto:help@helpscout.com). Il team di supporto di Help Scout applica questa impostazione in modo che la connessione Single Sign-On SAML sia impostata correttamente su entrambi i lati.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile nel [portale di Azure](https://portal.azure.com) durante la configurazione dell'app. Dopo avere aggiunto l'app selezionando **Active Directory** > **Applicazioni aziendali**, selezionare la scheda **Single Sign-On**. È possibile accedere alla documentazione incorporata nella sezione **Configurazione** nella parte inferiore della pagina. Per altre informazioni, vedere la [documentazione incorporata su Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione viene creato un utente di test chiamato Britta Simon nel portale di Azure.

![Creare un utente test di Azure AD][100]

Per creare un utente di test in Azure AD:

1. Nel portale di Azure fare clic su **Azure Active Directory** nel menu sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-helpscout-tutorial/create_aaduser_01.png)

2. Selezionare **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.

    ![Selezionare Utenti e gruppi e quindi selezionare Tutti gli utenti.](./media/active-directory-saas-helpscout-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente**, nella parte superiore della pagina **Tutti gli utenti** selezionare **Aggiungi**.

    ![Pulsante Aggiungi](./media/active-directory-saas-helpscout-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** completare questa procedura:

    1. Nella casella **Nome** immettere **BrittaSimon**.

    2. Nella casella **Nome utente** immettere l'indirizzo di posta elettronica dell'utente Britta Simon.

    3. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    4. Selezionare **Crea**.

        ![Finestra di dialogo Utente](./media/active-directory-saas-helpscout-tutorial/create_aaduser_04.png)

 
### <a name="create-a-help-scout-test-user"></a>Creare un utente test di Help Scout

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Help Scout. Help Scout supporta il provisioning JIT, che è attivato per impostazione predefinita.

In questa sezione non è necessario completare azioni o attività. Se un utente non esiste in Help Scout, ne viene creato uno nuovo quando si prova ad accedere a Help Scout.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si consente all'utente Britta Simon di usare l'accesso Single Sign-On di Azure AD concedendo all'account utente l'accesso a Help Scout.

![Assegnare il ruolo utente][200] 

Per assegnare Britta Simon a Help Scout:

1. Nel portale di Azure aprire la visualizzazione applicazioni e quindi passare alla visualizzazione directory. Selezionare **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Help Scout**.

    ![Collegamento di Help Scout nell'elenco delle applicazioni](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento Utenti e gruppi][202]

4. Selezionare **Aggiungi**. Quindi nella pagina **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella pagina **Utenti e gruppi** selezionare **Britta Simon** nell'elenco di utenti.

6. Nella pagina **Utenti e gruppi** selezionare **Seleziona**.

7. Nella pagina **Aggiungi assegnazione** selezionare **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro Help Scout nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Help Scout.

Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_203.png


