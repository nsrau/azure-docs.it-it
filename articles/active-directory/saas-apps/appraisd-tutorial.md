---
title: 'Esercitazione: Integrazione di Azure Active Directory con Appraisd | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.openlocfilehash: dae9a59b89f03a50b1adaed55cd8f97c06906526
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49118197"
---
# <a name="tutorial-azure-active-directory-integration-with-appraisd"></a>Esercitazione: Integrazione di Azure Active Directory con Appraisd

Questa esercitazione descrive come integrare Appraisd con Azure Active Directory (Azure AD).

L'integrazione di Appraisd con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Appraisd.
- È possibile abilitare gli utenti per l'accesso automatico ad Appraisd (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Appraisd, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Appraisd abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Appraisd dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-appraisd-from-the-gallery"></a>Aggiunta di Appraisd dalla raccolta
Per configurare l'integrazione di Appraisd in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Appraisd dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![image](./media/appraisd-tutorial/selectazuread.png)

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![image](./media/appraisd-tutorial/a_select_app.png)
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![image](./media/appraisd-tutorial/a_new_app.png)

4. Nella casella di ricerca digitare **Appraisd**, selezionare **Appraisd** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![image](./media/appraisd-tutorial/tutorial_appraisd_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Appraisd usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Appraisd che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Appraisd.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Appraisd, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Appraisd](#create-an-appraisd-test-user)**: per avere una controparte di Britta Simon in Appraisd collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Appraisd.

**Per configurare Single Sign-On di Azure AD con Appraisd, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Appraisd** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![image](./media/appraisd-tutorial/B1_B2_Select_SSO.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML** per abilitare il Single Sign-On.

    ![image](./media/appraisd-tutorial/b1_b2_saml_sso.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![image](./media/appraisd-tutorial/b1-domains_and_urlsedit.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_url.png)

    a. Fare clic su **Impostare URL aggiuntivi**. 

    b. Nella casella di testo **Stato dell'inoltro** digitare un URL: `<TENANTCODE>`

    c. Se si vuole configurare l'applicazione in modalità avviata da **SP** nella sezione **URL di accesso** digitare un URL usando il modello seguente: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > L'URL di accesso effettivo e il valore dello stato dell'inoltro vengono visualizzati nella pagina di configurazione SSO di Appraisd, descritta più avanti nell'esercitazione.
    
5. L'applicazione Appraisd prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Fare clic su **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](./media/appraisd-tutorial/i3-attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:
    
    a. Fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](./media/appraisd-tutorial/i2-attribute.png)

    ![image](./media/appraisd-tutorial/i4-attribute.png)

    b. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo.

    c. Fare clic su **Save**. 

7. Nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare il **Certificato (Base64)** e salvarlo nel computer.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_certficate.png) 

8. Nella sezione **Configura Appraisd** copiare l'URL appropriato in base alle proprie esigenze.

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

    ![image](./media/appraisd-tutorial/d1_samlsonfigure.png)

9. In un'altra finestra del Web browser accedere ad Appraisd come amministratore della sicurezza.

10. Nell'angolo in alto a destra della pagina fare clic sull'icona **Settings** (Impostazioni), quindi passare a **Configuration** (Configurazione).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

11. Nella parte sinistra del menu fare clic su **SAML single sign-on** (Single Sign-On SAML).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

12. Nella pagina **SAML 2.0 Single Sign-On configuration** (Configurazione Single Sign-On SAML 2.0) seguire questa procedura:
    
    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Copiare il valore di **Default Relay State** (Stato di inoltro predefinito) e incollarlo nella casella di testo **Stato dell'inoltro** in **Configurazione SAML di base** nel portale di Azure.

    b. Copiare il valore di **Service-initiated login URL** (URL di accesso avviato dal servizio) e incollarlo nella casella di testo **URL di accesso** in **Configurazione SAML di base** nel portale di Azure.

13. Scorrere la stessa pagina verso il basso sotto **Identifying users** (Identificazione utenti) e seguire questa procedura:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Nella casella di testo **Identity Provider Single Sign-On URL** (URL di accesso Single Sign-On del provider di identità) incollare il valore di **URL di accesso** copiato dal portale di Azure e fare clic su **Salva**.

    b. Nella casella di testo **Identity Provider Issuer URL** (URL autorità di certificazione provider di identità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure e fare clic su **Salva**.

    c. Nel Blocco note aprire il certificato con codifica Base 64 scaricato dal portale di Azure, copiarne il contenuto e quindi incollarlo nella casella **X.509 Certificate** (Certificato X.509) e fare clic su **Save** (Salva).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel portale di Azure, nel riquadro sinistro, selezionare **Azure Active Directory**, selezionare **Utenti** e quindi selezionare **Tutti gli utenti**.

    ![image](./media/appraisd-tutorial/d_users_and_groups.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![image](./media/appraisd-tutorial/d_adduser.png)

3. In Proprietà utente seguire questa procedura.

    ![image](./media/appraisd-tutorial/d_userproperties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.
 
### <a name="create-an-appraisd-test-user"></a>Creare un utente di test di Appraisd

Per consentire agli utenti di Azure AD di accedere ad Appraisd, è necessario effettuarne il provisioning in Appraisd. Nel caso di Appraisd il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere ad Appraisd come amministratore della sicurezza.

2. Nell'angolo in alto a destra della pagina fare clic sull'icona **Settings** (Impostazioni), quindi passare ad **Administration centre** (Centro di amministrazione).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Nella barra degli strumenti nella parte superiore della pagina fare clic su **People** (Persone), quindi passare ad **Add a new user** (Aggiungi un nuovo utente).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Nella pagina **Add a New User** (Aggiungi un nuovo utente) seguire questa procedura:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)
    
    a. Nella casella di testo **First name** (Nome) immettere il nome dell'utente, ad esempio **Britta**.

    b. Nella casella di testo **Last name** (Cognome) immettere il cognome dell'utente, ad esempio **Simon**.

    c. Nella casella di testo **Email** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **Brittasimon@contoso.com**.

    d. Fare clic su **Add User**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Appraisd.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![image](./media/appraisd-tutorial/d_all_applications.png)

2. Nell'elenco delle applicazioni selezionare **Appraisd**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![image](./media/appraisd-tutorial/d_leftpaneusers.png)

4. Selezionare il pulsante **Aggiungi** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![image](./media/appraisd-tutorial/d_assign_user.png)

4. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

5. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Appraisd nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Appraisd.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
