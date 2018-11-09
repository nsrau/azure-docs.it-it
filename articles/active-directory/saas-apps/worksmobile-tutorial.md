---
title: 'Esercitazione: Integrazione di Azure Active Directory con LINE WORKS | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e LINE WORKS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 725f32fd-d0ad-49c7-b137-1cc246bf85d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 0437bd46537d8b7024618706aba12660abca5512
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50414693"
---
# <a name="tutorial-azure-active-directory-integration-with-line-works"></a>Esercitazione: Integrazione di Azure Active Directory con LINE WORKS

Questa esercitazione descrive come integrare LINE WORKS con Azure Active Directory (Azure AD).

L'integrazione di LINE WORKS con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a LINE WORKS.
- È possibile abilitare gli utenti per l'accesso automatico a LINE WORKS (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con LINE WORKS, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di LINE WORKS abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di LINE WORKS dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-line-works-from-the-gallery"></a>Aggiunta di LINE WORKS dalla raccolta
Per configurare l'integrazione di LINE WORKS in Azure AD, è necessario aggiungere LINE WORKS dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere LINE WORKS dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![image](./media/worksmobile-tutorial/selectazuread.png)

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![image](./media/worksmobile-tutorial/a_select_app.png)
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![image](./media/worksmobile-tutorial/a_new_app.png)

4. Nella casella di ricerca digitare **LINE WORKS**, selezionare **LINE WORKS** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![image](./media/worksmobile-tutorial/tutorial_lineworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con LINE WORKS con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di LINE WORKS che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in LINE WORKS.

Per configurare e testare l'accesso Single Sign-On di Azure AD con LINE WORKS, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di LINE WORKS](#create-a-line-works-test-user)**: per avere una controparte di Britta Simon in LINE WORKS collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione LINE WORKS.

**Per configurare l'accesso Single Sign-On di Azure AD con LINE WORKS, seguire questa procedura:**

1. Nel [portale di Azure](https://portal.azure.com/), nella pagina di integrazione dell'applicazione **LINE WORKS**, selezionare **Single Sign-On**.

    ![image](./media/worksmobile-tutorial/b1_b2_select_sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![image](./media/worksmobile-tutorial/b1_b2_saml_sso.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![image](./media/worksmobile-tutorial/b1-domains_and_urlsedit.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://auth.worksmobile.com/d/login/<domain>/?userId=<ID@domain>`

    b. Nella casella di testo **Identificatore** digitare un URL: `worksmobile.com`

    ![image](./media/worksmobile-tutorial/tutorial_lineworks_url.png)

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file di **Certificato (base)** e salvarlo nel computer in uso.

    ![image](./media/worksmobile-tutorial/tutorial_lineworks_certficate.png)

6. Nella sezione **Configura LINE WORKS** copiare l'URL appropriato in base alle esigenze.

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

    ![image](./media/worksmobile-tutorial/d1_samlsonfigure.png) 

7. Per configurare l'accesso Single Sign-On sul lato **LINE WORKS**, leggere i [documenti di LINE WORKS relativi a SSO](https://developers.worksmobile.com/jp/document/1001080101) e configurare un'impostazione di LINE WORKS.

> [!NOTE]
> È necessario convertire il file di certificato scaricato con estensione cert in un file con estensione pem.


### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![image](./media/worksmobile-tutorial/d_users_and_groups.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![image](./media/worksmobile-tutorial/d_adduser.png)

3. In Proprietà utente seguire questa procedura.

    ![image](./media/worksmobile-tutorial/d_userproperties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.
 
### <a name="create-a-line-works-test-user"></a>Creare un utente di test di LINE WORKS

In questa sezione viene creato un utente di nome Britta Simon in LINE WORKS. Accedere alla [pagina di amministrazione di LINE WORKS](https://admin.worksmobile.com) e aggiungere gli utenti nella piattaforma LINE WORKS.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a LINE WORKS.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![image](./media/worksmobile-tutorial/d_all_applications.png)

2. Nell'elenco di applicazioni selezionare **LINE WORKS**.

    ![image](./media/worksmobile-tutorial/tutorial_lineworks_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![image](./media/worksmobile-tutorial/d_leftpaneusers.png)

4. Selezionare il pulsante **Aggiungi** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![image](./media/worksmobile-tutorial/d_assign_user.png)

4. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

5. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro LINE WORKS nel riquadro di accesso, si dovrebbe accedere automaticamente all'applicazione LINE WORKS.
Per altre informazioni in proposito, vedere [Introduzione al pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)


