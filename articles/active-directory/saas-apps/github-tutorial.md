---
title: 'Esercitazione: Integrazione di Azure Active Directory con GitHub | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeedes
ms.openlocfilehash: dbd4634c575fd4f1886d3e7714ef9ddabbde0f8a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341158"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Esercitazione: Integrazione di Azure Active Directory con GitHub

Questa esercitazione descrive come integrare GitHub con Azure Active Directory (Azure AD).

L'integrazione di GitHub con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a GitHub.
- È possibile abilitare gli utenti per l'accesso automatico a GitHub (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con GitHub, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione abilitata GitHub per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di GitHub dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-github-from-the-gallery"></a>Aggiunta di GitHub dalla raccolta
Per configurare l'integrazione di GitHub in Azure AD, è necessario aggiungere GitHub dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere GitHub dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![image](./media/github-tutorial/selectazuread.png)

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![image](./media/github-tutorial/a_select_app.png)
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![image](./media/github-tutorial/a_new_app.png)

4. Nella casella di ricerca digitare **GitHub**, selezionare **GitHub** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![image](./media/github-tutorial/tutorial_github_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con GitHub in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di GitHub che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in GitHub.

Per configurare e testare l'accesso Single Sign-On di Azure AD con GitHub, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test di GitHub](#create-a-github-test-user)**: per avere una controparte di Britta Simon in GitHub collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione GitHub.

**Per configurare Single Sign-On di Azure AD con GitHub, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione [GitHub](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![image](./media/github-tutorial/b1_b2_select_sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare il Single Sign-On.

    ![image](./media/github-tutorial/b1_b2_saml_sso.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![image](./media/github-tutorial/b1-domains_and_urlsedit.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![image](./media/github-tutorial/tutorial_github_url.png) 

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://github.com/orgs/<entity-id>/sso`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il criterio seguente: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. In questo caso, è consigliabile di usare il valore univoco della stringa nell'identificatore. Passare alla sezione Admin di GitHub per recuperare questi valori.

5. L'applicazione GitHub prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Fare clic su **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](./media/github-tutorial/i3-attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura:
    
    a. Fare clic su **Modifica** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](./media/github-tutorial/i2-attribute.png)

    ![image](./media/github-tutorial/i4-attribute.png)

    b. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo.

    c. Fare clic su **Save**.
 
7. Nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare il **Certificato (Base64)** e salvarlo nel computer.

    ![image](./media/github-tutorial/tutorial_github_certficate.png)

8. Nella sezione **Configura GitHub** copiare l'URL appropriato in base alle proprie esigenze.

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

    ![image](./media/github-tutorial/d1_samlsonfigure.png) 

9. In un'altra finestra del Web browser accedere al sito aziendale di GitHub come amministratore.

10. Passare a **Settings** (Impostazioni) e fare clic su **Security** (Sicurezza).

    ![Impostazioni](./media/github-tutorial/tutorial_github_config_github_03.png)

11. Selezionare la casella **Enable SAML authentication** (Abilita autenticazione SAML) mostrando i campi di configurazione dell'accesso Single Sign-On. Usare quindi il valore URL di Single Sign-On per aggiornare l'URL di Single Sign-On nella configurazione di Azure AD.

    ![Impostazioni](./media/github-tutorial/tutorial_github_config_github_13.png)

12. Configurare i campi seguenti:

    ![Impostazioni](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. Nella casella di testo **URL di accesso** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella di testo **Autorità di certificazione** incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Aprire il certificato scaricato nel Blocco note dal portale di Azure e incollarne il contenuto nella casella di testo **Certificato pubblico**.

    d. Fare clic sull'icona **Modifica** icona per modificare **Metodo di firma** e **Metodo digest** da **RSA-SHA1** e **SHA1**alla **RSA-SHA256** e **SHA256** come illustrato di seguito.

    ![image](./media/github-tutorial/tutorial_github_sha.png) 
    
13. Fare clic su **Test SAML configuration** (Test configurazione SAML) per confermare l'assenza di errori di convalida o errori durante l'accesso SSO.

    ![Impostazioni](./media/github-tutorial/tutorial_github_config_github_06.png)

14. Fare clic su **Save**

> [!NOTE]
> L'accesso Single Sign-On in GitHub esegue l'autenticazione in un'organizzazione specifica in GitHub e non sostituisce l'autenticazione di GitHub. Pertanto, se la sessione GitHub.com dell'utente è scaduta, potrebbe venire richiesto di eseguire l'autenticazione con l'ID/la password GitHub durante il processo di accesso Single Sign-On.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel portale di Azure, nel riquadro sinistro, selezionare **Azure Active Directory**, selezionare **Utenti** e quindi selezionare **Tutti gli utenti**.

    ![image](./media/github-tutorial/d_users_and_groups.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![image](./media/github-tutorial/d_adduser.png)

3. In Proprietà utente seguire questa procedura.

    ![image](./media/github-tutorial/d_userproperties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.
 
### <a name="create-a-github-test-user"></a>Creare un utente test GitHub

Questa sezione descrive come creare un utente chiamato Britta Simon in GitHub. GitHub supporta il provisioning utenti automatico, che è abilitato per impostazione predefinita. È possibile scoprire più dettagli [qui](github-provisioning-tutorial.md) su come configurare il provisioning utenti automatico.

**Per creare un utente manualmente, seguire questa procedura:**

1. Accedere al sito aziendale di GitHub come amministratore.

2. Fare clic su **Persone**.

    ![Persone](./media/github-tutorial/tutorial_github_config_github_08.png "Persone")

3. Fare clic su **Invite member** (Invita membro).

    ![Invitare utenti](./media/github-tutorial/tutorial_github_config_github_09.png "Invitare utenti")

4. Nella finestra di dialogo **Invite member** (Invita membro) seguire questa procedura:

    a. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica dell'account di Britta Simon.

    ![Invitare persone](./media/github-tutorial/tutorial_github_config_github_10.png "Invitare persone")

    b. Fare clic su **Send Invitation** (Invia invito).

    ![Invitare persone](./media/github-tutorial/tutorial_github_config_github_11.png "Invitare persone")

    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata all'uso di Single Sign-On di Azure con accesso a GitHub.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![image](./media/github-tutorial/d_all_applications.png)

2. Nell'elenco di applicazioni selezionare **GitHub**.

    ![image](./media/github-tutorial/tutorial_github_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![image](./media/github-tutorial/d_leftpaneusers.png)

4. Selezionare il pulsante **Aggiungi** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![image](./media/github-tutorial/d_assign_user.png)

4. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

5. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro GitHub nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione GitHub.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)


