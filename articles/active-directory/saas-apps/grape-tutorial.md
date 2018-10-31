---
title: 'Esercitazione: Integrazione di Azure Active Directory con Gra-Pe | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Gra-Pe.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 073f8641-b64d-4754-b1a6-2b91c865b13d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeedes
ms.openlocfilehash: 5e642cba3354f4f8589e44d975c0cecf5692c5b5
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49432922"
---
# <a name="tutorial-azure-active-directory-integration-with-gra-pe"></a>Esercitazione: Integrazione di Azure Active Directory con Gra-Pe

Questa esercitazione descrive come integrare Gra-Pe con Azure Active Directory (Azure AD).

L'integrazione di Gra-Pe con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Gra-Pe.
- È possibile abilitare gli utenti per l'accesso automatico a Gra-Pe (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Gra-Pe, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione Gra-Pe abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Gra-Pe dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-gra-pe-from-the-gallery"></a>Aggiunta di Gra-Pe dalla raccolta
Per configurare l'integrazione di Gra-Pe in Azure AD è necessario aggiungere Gra-Pe dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Gra-Pe dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![image](./media/grape-tutorial/selectazuread.png)

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![image](./media/grape-tutorial/a_select_app.png)
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![image](./media/grape-tutorial/a_new_app.png)

4. Nella casella di ricerca digitare **Gra-Pe**, selezionare **Gra-Pe** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![image](./media/grape-tutorial/tutorial_grape_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Gra-Pe in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Gra-Pe che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Gra-Pe.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Gra-Pe, è necessario completare le procedure seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Gra-Pe](#create-a-gra-pe-test-user)**: per avere una controparte di Britta Simon in Gra-Pe collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Gra-Pe.

**Per configurare l'accesso Single Sign-On di Azure AD con Gra-Pe, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Gra-Pe** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![image](./media/grape-tutorial/b1_b2_select_sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![image](./media/grape-tutorial/b1_b2_saml_sso.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![image](./media/grape-tutorial/b1-domains_and_urlsedit.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    Nella casella di testo **URL di accesso** digitare un URL, ad esempio: `https://btm.tts.co.jp/portal/apl/SSOLogin.aspx`

    ![image](./media/grape-tutorial/tutorial_grape_url.png)

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare il file **Certificato (Base64)** e salvarlo nel computer in uso.

    ![image](./media/grape-tutorial/tutorial_grape_certficate.png)

6. Nella sezione **Set up Gra-Pe** (Configura Gra-Pe) copiare l'URL appropriato in base alle proprie esigenze.

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

    ![image](./media/grape-tutorial/d1_samlsonfigure.png) 

7. Per configurare l'accesso Single Sign-On in **Gra-Pe** è necessario inviare il file **Certificato (Base64)** scaricato nonché **l'URL di accesso, l'identificatore di Azure AD e l'URL di disconnessione** copiati al [team di supporto di Gra-Pe](https://www.toppantravel.com/inquiry/). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![image](./media/grape-tutorial/d_users_and_groups.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![image](./media/grape-tutorial/d_adduser.png)

3. In Proprietà utente seguire questa procedura.

    ![image](./media/grape-tutorial/d_userproperties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.
 
### <a name="create-a-gra-pe-test-user"></a>Creare un utente di test di Gra-Pe

In questa sezione viene creato un utente di nome Britta Simon in Gra-Pe. Insieme al [team di supporto di Gra-Pe](https://www.toppantravel.com/inquiry/) aggiungere gli utenti alla piattaforma Gra-Pe. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Gra-Pe.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![image](./media/grape-tutorial/d_all_applications.png)

2. Nell'elenco delle applicazioni, selezionare **Gra-Pe**.

    ![image](./media/grape-tutorial/tutorial_grape_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![image](./media/grape-tutorial/d_leftpaneusers.png)

4. Selezionare il pulsante **Aggiungi** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![image](./media/grape-tutorial/d_assign_user.png)

4. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

5. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Gra-Pe nel pannello di accesso si accederà automaticamente all'applicazione Gra-Pe.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)


