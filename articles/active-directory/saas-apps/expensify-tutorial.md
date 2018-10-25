---
title: 'Esercitazione: Integrazione di Azure Active Directory con Expensify | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory ed Expensify.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: jeedes
ms.openlocfilehash: c9ee0af3cbf2c1aa7b24d2f4cf5fba9d664bc087
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248041"
---
# <a name="tutorial-azure-active-directory-integration-with-expensify"></a>Esercitazione: Integrazione di Azure Active Directory con Expensify

Questa esercitazione descrive come integrare Expensify con Azure Active Directory (Azure AD).

L'integrazione di Expensify con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Expensify.
- È possibile abilitare gli utenti per l'accesso automatico a Expensify (Single Sign-On) con l'account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Expensify, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Expensify abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Expensify dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-expensify-from-the-gallery"></a>Aggiunta di Expensify dalla raccolta

Per configurare l'integrazione di Expensify in Azure AD, è necessario aggiungere Expensify dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Expensify dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![image](./media/expensify-tutorial/selectazuread.png)

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![image](./media/expensify-tutorial/a_select_app.png)
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![image](./media/expensify-tutorial/a_new_app.png)

4. Nella casella di ricerca digitare **Expensify**, selezionare **Expensify** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![image](./media/expensify-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Expensify con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Expensify che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Expensify.

Per stabilire la relazione di collegamento, in Expensify assegnare il valore di **nome utente** di Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Expensify, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente test di Expensify](#create-an-expensify-test-user)**: per avere una controparte di Britta Simon in Expensify collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Expensify.

**Per configurare l'accesso Single Sign-On di Azure AD con Expensify, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Expensify** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![image](./media/expensify-tutorial/b1_b2_select_sso.png)

2. Fare clic su **Modifica modalità Single Sign-On** nella parte superiore della schermata per selezionare la modalità **SAML**.

      ![image](./media/expensify-tutorial/b1_b2_saml_ssso.png)

3. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare il Single Sign-On.

    ![image](./media/expensify-tutorial/b1_b2_saml_sso.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![image](./media/expensify-tutorial/b1-domains_and_urlsedit.png)

5. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare l'URL come: `https://www.expensify.com/authentication/saml/login`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://www.<companyname>.expensify.com`

    ![image](./media/expensify-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Sostituire la sezione <companyname> dell'identificatore URL con il dominio della società. Vedi l'esempio di `https://contoso.expensify.com` precedente. In Expensify, è il nome del dominio come indicato in **Settings > Domain Control** (Impostazioni > Controllo di dominio).

    ![Informazioni sul dominio Expensify](./media/expensify-tutorial/tutorial_expensify_domain.png)

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il certificato appropriato in base ai propri requisiti e salvarlo nel computer in uso.

    ![image](./media/expensify-tutorial/certificatebase64.png)

7. Per abilitare SSO in Expensify è necessario avere abilitato **Domain Control** (Controllo di dominio) nell'applicazione. Per abilitare il controllo di dominio nell'applicazione seguire [questa procedura](http://help.expensify.com/domain-control). Per ulteriore supporto, contattare il [team di supporto clienti di Expensify](mailto:help@expensify.com). Dopo aver abilitato il controllo di dominio seguire questa procedura:
   
    ![Configure Single Sign-On](./media/expensify-tutorial/tutorial_expensify_51.png)
    
    a. Accedere all'applicazione Expensify.
    
    b. Nel pannello sinistro fare clic su **Settings** e passare a **SAML**.
    
    c. Impostare l'opzione **SAML Login** (Accesso SAML) su **Enabled** (Abilitato).
    
    d. Aprire i metadati della federazione scaricati da Azure AD nel Blocco note, copiare il contenuto e incollarlo nella casella di testo **Identity Provider Metadata** (Metadati del provider di identità).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel portale di Azure, nel riquadro sinistro, selezionare **Azure Active Directory**, selezionare **Utenti** e quindi selezionare **Tutti gli utenti**.

    ![image](./media/expensify-tutorial/d_users_and_groups.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![image](./media/expensify-tutorial/d_adduser.png)

3. In Proprietà utente seguire questa procedura.

    ![image](./media/expensify-tutorial/d_userproperties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.
 
### <a name="create-an-expensify-test-user"></a>Creare un utente test di Expensify

In questa sezione viene creato un utente chiamato Britta Simon in Expensify. Collaborare con il [team di supporto clienti di Expensify](mailto:help@expensify.com) per aggiungere gli utenti nella piattaforma Expensify.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Expensify.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![image](./media/expensify-tutorial/d_all_applications.png)

2. Nell'elenco di applicazioni selezionare **Expensify**.

    ![image](./media/expensify-tutorial/d_all_proapplications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![image](./media/expensify-tutorial/d_leftpaneusers.png)

4. Fare clic sul pulsante **Aggiungi** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![image](./media/expensify-tutorial/d_assign_user.png)

4. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

5. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Expensify nel pannello di accesso, verrà eseguito automaticamente l'accesso all'applicazione Expensify.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)




