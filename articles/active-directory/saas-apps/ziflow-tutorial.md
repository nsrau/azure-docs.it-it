---
title: 'Esercitazione: Integrazione di Azure Active Directory con Ziflow | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.openlocfilehash: ca687f15187d4dd485d2c59e3c46fa3158802ff4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180003"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Esercitazione: Integrazione di Azure Active Directory con Ziflow

Questa esercitazione descrive come integrare Ziflow con Azure Active Directory (Azure AD).

L'integrazione di Ziflow con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Ziflow.
- È possibile abilitare gli utenti per l'accesso automatico a Ziflow (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Ziflow, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Ziflow abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Ziflow dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-ziflow-from-the-gallery"></a>Aggiunta di Ziflow dalla raccolta
Per configurare l'integrazione di Ziflow in Azure AD, è necessario aggiungere Ziflow dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Ziflow dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Ziflow**, selezionare **Ziflow** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Ziflow nell'elenco risultati](./media/ziflow-tutorial/tutorial_ziflow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Ziflow usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Ziflow che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Ziflow.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Ziflow, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Ziflow](#create-a-ziflow-test-user)**: per avere una controparte di Britta Simon in Ziflow collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Ziflow.

**Per configurare l'accesso Single Sign-On di Azure AD con Ziflow, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Ziflow** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Single Sign-On](./media/ziflow-tutorial/tutorial_ziflow_samlbase.png)

3. Nella sezione **URL e dominio Ziflow** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Ziflow](./media/ziflow-tutorial/tutorial_ziflow_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > I valori precedenti non sono valori reali. È necessario aggiornare il valore dell'ID univoco nell'identificatore e nell'URL di accesso con il valore effettivo, come descritto più avanti nell'esercitazione.

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/ziflow-tutorial/tutorial_ziflow_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/ziflow-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Ziflow** fare clic su **Configura Ziflow** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML e l'URL di disconnessione** dalla sezione **Riferimento rapido**.

    ![Configurazione di Ziflow](./media/ziflow-tutorial/tutorial_ziflow_configure.png) 

7. In un'altra finestra del Web browser accedere a Ziflow come amministratore della sicurezza.

8. Fare clic su Avatar nell'angolo in alto a destra e quindi fare clic su **Manage account** (Gestisci account).

    ![Gestione della configurazione di Ziflow](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

9. In alto a sinistra fare clic su **Single Sign-On**.

    ![Single Sign-On nella configurazione di Ziflow](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

10. Nella pagina **Single Sign-On** seguire questa procedura:

    ![Single Sign-On nella configurazione di Ziflow](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. In **Type** (Tipo) selezionare **SAML2.0**.

    b. Nella casella di testo **Sign-in URL** (URL di accesso) incollare il valore di **URL servizio Single Sign-On SAML** copiato dal portale di Azure.

    c. Caricare nel **certificato di firma X509** il certificato codificato in base 64 scaricato dal portale di Azure.

    d. Nella casella di testo **Sign Out URL** (URL di disconnessione) incollare il valore di **URL di disconnessione** copiato dal portale di Azure.

    e. Dalla sezione **Configuration Settings for your Identifier Provider** (Impostazioni di configurazione per il provider di identità) copiare il valore dell'ID univoco evidenziato e aggiungerlo con l'identificatore e l'URL di accesso nella sezione **URL e dominio Ziflow** del portale di Azure.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/ziflow-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/ziflow-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/ziflow-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/ziflow-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
  
### <a name="create-a-ziflow-test-user"></a>Creare un utente di test di Ziflow

Per consentire agli utenti di Azure AD di accedere a Ziflow, è necessario effettuarne il provisioning in Ziflow. Nel caso di Ziflow il provisioning è un'attività manuale.

Per eseguire il provisioning di un account utente, seguire questa procedura:

1. Accedere a Ziflow come amministratore della sicurezza.

2. Passare a **People** (Persone) nella parte superiore.

    ![People (Persone) nella configurazione di Ziflow](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Fare clic su **Add** (Aggiungi) e quindi su **Add user** (Aggiungi utente).

    ![Aggiunta di un utente nella configurazione di Ziflow](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. Nella sezione **Add a user** (Aggiungi un utente) seguire questa procedura:

    ![Aggiunta di un utente nella configurazione di Ziflow](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. Nella casella di testo **E-mail** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio brittasimon@contoso.com.

    b. Nella casella di testo **First name** (Nome) immettere il nome dell'utente, ad esempio Britta.

    c. Nella casella di testo **Last name** (Nome) immettere il cognome dell'utente, ad esempio Simon.

    d. Selezionare il ruolo di Ziflow.

    e. Fare clic su **Add 1 user** (Aggiungi 1 utente).

    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Ziflow.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Ziflow, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Ziflow**.

    ![Collegamento di Ziflow nell'elenco delle applicazioni](./media/ziflow-tutorial/tutorial_ziflow_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Ziflow nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Ziflow.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ziflow-tutorial/tutorial_general_01.png
[2]: ./media/ziflow-tutorial/tutorial_general_02.png
[3]: ./media/ziflow-tutorial/tutorial_general_03.png
[4]: ./media/ziflow-tutorial/tutorial_general_04.png

[100]: ./media/ziflow-tutorial/tutorial_general_100.png

[200]: ./media/ziflow-tutorial/tutorial_general_200.png
[201]: ./media/ziflow-tutorial/tutorial_general_201.png
[202]: ./media/ziflow-tutorial/tutorial_general_202.png
[203]: ./media/ziflow-tutorial/tutorial_general_203.png

