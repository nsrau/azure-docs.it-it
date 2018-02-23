---
title: 'Esercitazione: Integrazione di Azure Active Directory con Vodeclic | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Vodeclic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d77a0f53-e3a3-445e-ab3e-119cef6e2e1d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.openlocfilehash: 940c7bb5040fb91a03b01dc43ee07d52e3d4e63b
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="tutorial-azure-active-directory-integration-with-vodeclic"></a>Esercitazione: Integrazione di Azure Active Directory con Vodelic

Questa esercitazione descrive come integrare Vodeclic con Azure Active Directory (Azure AD).

L'integrazione di Vodeclic con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Vodeclic.
- È possibile abilitare gli utenti per l'accesso automatico a Vodeclic (Single Sign-On, SSO) con i propri account Azure AD.
- Gli account possono essere gestiti da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Vodeclic, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Vodeclic abilitata per l'accesso SSO

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, ottenere una [versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Vodeclic dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-vodeclic-from-the-gallery"></a>Aggiunta di Vodeclic dalla raccolta
Per configurare l'integrazione di Vodeclic in Azure AD, è necessario aggiungere Vodeclic dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Vodeclic dalla raccolta, seguire questa procedura:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic sull'icona **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Per aggiungere una nuova applicazione, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Vodeclic**. Selezionare **Vodeclic** nel pannello dei risultati e quindi selezionare il pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Vodeclic nell'elenco risultati](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Vodeclic usando un utente test di nome "Britta Simon".

Per il corretto funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Vodeclic corrispondente a un utente di Azure AD. In altre parole, deve essere stabilito un collegamento tra un utente di Azure AD e l'utente correlato in Vodeclic.

In Vodeclic assegnare al valore **Username** (Nome utente) lo stesso valore del **nome utente** in Azure AD. Questo passaggio stabilisce il collegamento tra i due utenti.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Vodeclic, completare i blocchi predefiniti seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on): per consentire agli utenti di usare questa funzionalità.
2. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. [Creare un utente di test di Vodeclic](#create-a-vodeclic-test-user) per avere una controparte di Britta Simon in Vodeclic collegata alla rappresentazione dell'utente in Azure AD.
4. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. [Testare l'accesso Single Sign-On](#test-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Vodeclic.

**Per configurare l'accesso Single Sign-On di Azure AD con Vodeclic, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Vodeclic** del portale di Azure selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** per **Modalità Single Sign-On** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_samlbase.png)

3. Per configurare l'applicazione in modalità avviata da **IDP**, nella sezione **URL e dominio Vodeclic** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Vodeclic](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url.png)

    a. Nella casella **Identificatore** digitare un URL usando il modello seguente: `https://<companyname>.lms.vodeclic.net/auth/saml`

    b. Nella casella **URL di risposta** digitare un URL nel formato seguente: `https://<companyname>.lms.vodeclic.net/auth/saml/callback`

4. Se si preferisce configurare l'applicazione in modalità iniziata da **SP**, fare clic sulla casella di controllo **Mostra impostazioni URL avanzate** e seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Vodeclic](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url1.png)

    Nella casella **URL di accesso** digitare un URL usando il modello seguente: `https://<companyname>.lms.vodeclic.net/auth/saml`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Vodeclic](mailto:hotline@vodeclic.com).

5. Nella sezione **Certificato di firma SAML** selezionare **XML metadati** e quindi salvare il file di metadati sul computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_certificate.png) 

6. Selezionare **Salva**.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-vodeclic-tutorial/tutorial_general_400.png)
    
7. Per configurare l'accesso Single Sign-On sul lato **Vodeclic**, è necessario inviare il file **XML metadati** scaricato al [team di supporto di Vodeclic](mailto:hotline@vodeclic.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

> [!TIP]
> Un riepilogo di queste istruzioni è disponibile nel [portale di Azure](https://portal.azure.com) durante la configurazione dell'app. Dopo aver aggiunto l'app dalla sezione **Active Directory** > **Applicazioni aziendali** è sufficiente selezionare la scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Per altre informazioni sulla funzione di documentazione incorporata, vedere la [documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente di test in Azure AD, seguire questa procedura:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_01.png)

2. Per visualizzare un elenco di utenti, passare a **Utenti e gruppi**. Selezionare quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** selezionare **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Create**.
 
### <a name="create-a-vodeclic-test-user"></a>Creare un utente test Vodeclic

In questa sezione viene creato un utente chiamato Britta Simon in Vodeclic. Collaborare con il [team di supporto di Vodeclic](mailto:hotline@vodeclic.com) per aggiungere gli utenti alla piattaforma Vodeclic. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

> [!NOTE]
> In base ai requisiti dell'applicazione, potrebbe essere necessario ottenere l'inserimento del computer nell'elenco elementi consentiti. Per raggiungere tale obiettivo, è necessario condividere l'indirizzo IP pubblico con il [team di supporto Vodeclic](mailto:hotline@vodeclic.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Vodeclic.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Vodeclic, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione applicazioni e quindi passare alla visualizzazione directory. Passare quindi ad **Applicazioni aziendali** e selezionare **Tutte le applicazioni**.

    ![Assegnare utenti][201] 

2. Nell'elenco di applicazioni selezionare **Vodeclic**.

    ![Collegamento di Vodeclic nell'elenco delle applicazioni](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**.

6. Nella finestra di dialogo **Utenti e gruppi** fare clic sul pulsante **Seleziona**.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro Vodeclic nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Vodeclic.

Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_203.png

