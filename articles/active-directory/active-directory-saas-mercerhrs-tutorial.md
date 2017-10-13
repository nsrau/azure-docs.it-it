---
title: 'Esercitazione: integrazione di Azure Active Directory con Mercer BenefitsCentral | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Mercer BenefitsCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3788b28c-49aa-4208-9acd-630362008e89
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: jeedes
ms.openlocfilehash: c577a0163af04bec5737a215e788c3fb92c653c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mercer-benefitscentral-mbc"></a>Esercitazione: integrazione di Azure Active Directory con Mercer BenefitsCentral

Questa esercitazione descrive come integrare Mercer BenefitsCentral con Azure Active Directory.

L'integrazione di Mercer BenefitsCentral con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Mercer BenefitsCentral.
- È possibile abilitare gli utenti per l'accesso automatico a Mercer BenefitsCentral, ovvero al Single Sign-On, con i propri account di Azure AD.
- È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Mercer BenefitsCentral, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Una sottoscrizione abilitata al Single Sign-On di Mercer BenefitsCentral

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Mercer BenefitsCentral dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-mercer-benefitscentral-mbc-from-the-gallery"></a>Aggiunta di Mercer BenefitsCentral dalla raccolta
Per configurare l'integrazione di Mercer BenefitsCentral in Azure AD, è necessario aggiungere Mercer BenefitsCentral dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Mercer BenefitsCentral dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Mercer BenefitsCentral**, selezionare **Mercer BenefitsCentral** dal pannello dei risultati quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Mercer BenefitsCentral nell'elenco dei risultati](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Mercer BenefitsCentral con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Mercer BenefitsCentral che corrisponde all'utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Mercer BenefitsCentral.

Per stabilire la relazione di collegamento, in Mercer BenefitsCentral assegnare il valore di **nome utente** di Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Mercer BenefitsCentral, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Mercer BenefitsCentral](#create-a-mercer-benefitscentral-mbc-test-user)**: per avere una controparte di Britta Simon in Mercer BenefitsCentral collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Mercer BenefitsCentral.

**Per configurare l'accesso Single Sign-On di Azure AD con Mercer BenefitsCentral, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Mercer BenefitsCentral** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento per la configurazione dell'accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_samlbase.png)

3. Nella sezione **Mercer BenefitsCentral (MBC) Domain and URLs** (URL e dominio di Mercer BenefitsCentral) seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Mercer BenefitsCentral](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `stg.mercerhrs.com/saml2.0`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://ssous-stg.mercerhrs.com/SP2/Saml2AssertionConsumer.aspx`

    > [!NOTE] 
    > Il valore dell'URL di risposta non è reale. è necessario aggiornare questo valore con l'URL di risposta effettivo. Per ottenere questo valore contattare il [team di supporto di Mercer BenefitsCentral](https://www.mercer.com/contact-us.html).

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_400.png)

6. Nella sezione **Mercer BenefitsCentral (MBC) Configuration** (Configurazione di Mercer BenefitsCentral) fare clic su **Configure Mercer BenefitsCentral (MBC)** (Configura Mercer BenefitsCentral) per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configurazione di Mercer BenefitsCentral](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_configure.png) 

7. Per configurare l'accesso Single Sign-On sul lato **Mercer BenefitsCentral**, è necessario inviare il file **XML metadati** scaricato e il valore dell'**URL del servizio Single Sign-On SAML** al [team di supporto di Mercer BenefitsCentral](https://www.mercer.com/contact-us.html). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
  
### <a name="create-a-mercer-benefitscentral-mbc-test-user"></a>Creare un utente test di Mercer BenefitsCentral

In questa sezione viene creato un utente chiamato Britta Simon in Mercer HRS. Contattare il [team di supporto di Mercer BenefitsCentral](https://www.mercer.com/contact-us.html) per aggiungere gli utenti nella piattaforma Mercer HRS. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Mercer BenefitsCentral.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Mercer BenefitsCentral, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Mercer BenefitsCentral**.

    ![Collegamento di Mercer BenefitsCentral nell'elenco delle applicazioni](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Mercer BenefitsCentral del Pannello di accesso, l'utente effettua automaticamente l'accesso all'applicazione Mercer BenefitsCentral.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_203.png

