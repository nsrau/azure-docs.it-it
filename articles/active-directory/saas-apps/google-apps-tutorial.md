---
title: 'Esercitazione: Integrazione di Azure Active Directory con G Suite | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2018
ms.author: jeedes
ms.openlocfilehash: 4ed571d34e5df67f556f39b898e7ae5efc06a3e1
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288935"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Esercitazione: Integrazione di Azure Active Directory con G Suite

Questa esercitazione descrive come integrare G Suite con Azure Active Directory (Azure AD).

L'integrazione di G Suite con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a G Suite.
- È possibile abilitare gli utenti per l'accesso automatico a G Suite (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con G Suite, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione abilitata per l'accesso Single Sign-On a G Suite
- Sottoscrizione di Google Apps o sottoscrizione di Google Cloud Platform

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Domande frequenti

1. **D: Questa integrazione supporta l'integrazione SSO di Google Cloud Platform con Azure AD?**

    A: Sì. Google Cloud Platform e Google Apps condividono la stessa piattaforma di autenticazione. Per eseguire l'integrazione di GCP, è quindi necessario configurare l'accesso SSO con Google Apps.

2. **D: I dispositivi Chromebooks e altri dispositivi Chrome sono compatibili con Single Sign-On di Azure AD?**
  
    R: Sì, gli utenti possono accedere ai dispositivi Chromebook con le credenziali di Azure AD. Per informazioni sui motivi per cui agli utenti può essere richiesto di immettere le credenziali due volte, vedere questo [articolo del supporto tecnico di G Suite](https://support.google.com/chrome/a/answer/6060880).

3. **D: Se si abilita il Single Sign-On, gli utenti potranno usare le credenziali di Azure AD per accedere a qualsiasi prodotto di Google, ad esempio Google Classroom, GMail, Google Drive, YouTube e così via?**

    R: Sì, a seconda della [G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) che si sceglie di abilitare o disabilitare per la propria organizzazione.

4. **D: È possibile abilitare Single Sign-On solo per un subset di utenti di G Suite?**

    R: No, l'attivazione di Single Sign-On richiede immediatamente a tutti gli utenti di G Suite di autenticarsi con le proprie credenziali di Azure AD. Poiché G Suite non supporta più provider di identità, il provider di identità per l'ambiente di G Suite può essere AD Azure o Google, ma non entrambi contemporaneamente.

5. **D: Se un utente ha eseguito l'accesso tramite Windows, viene autenticano automaticamente in G Suite senza che venga richiesta una password?**

    R: Sono disponibili due opzioni per l'abilitazione di questo scenario. Nel primo caso gli utenti possono accedere ai dispositivi Windows 10 tramite [Aggiunta ad Azure Active Directory](../device-management-introduction.md). In alternativa, gli utenti possono accedere ai dispositivi Windows appartenenti a un dominio di Active Directory locale abilitato per il Single Sign-On in Azure AD tramite una distribuzione di [Active Directory Federation Services (AD FS)](../hybrid/plan-connect-user-signin.md) . Per entrambe le opzioni è necessario eseguire la procedura descritta nell'esercitazione seguente per abilitare Single Sign-On tra Azure AD e G Suite.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.
Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di G Suite dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-g-suite-from-the-gallery"></a>Aggiunta di G Suite dalla raccolta

Per configurare l'integrazione di G Suite in Azure AD, è necessario aggiungere G Suite dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere G Suite dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![image](./media/google-apps-tutorial/selectazuread.png)

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![image](./media/google-apps-tutorial/a_select_app.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![image](./media/google-apps-tutorial/a_new_app.png)

4. Nella casella di ricerca digitare **G Suite**, selezionare **G Suite** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![image](./media/google-apps-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con G Suite in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di G Suite che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in G Suite.

Per configurare e testare l'accesso Single Sign-On di Azure AD con G Suite, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di G Suite](#create-a-g-suite-test-user)**: per avere una controparte di Britta Simon in G Suite collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione G Suite.

**Per configurare Single Sign-On di Azure AD con G Suite, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **G Suite** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![image](./media/google-apps-tutorial/b1_b2_select_sso.png)

2. Fare clic su **Modifica modalità Single Sign-On** nella parte superiore della schermata per selezionare la modalità **SAML**.

      ![image](./media/google-apps-tutorial/b1_b2_saml_ssso.png)

3. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![image](./media/google-apps-tutorial/b1_b2_saml_sso.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![image](./media/google-apps-tutorial/b1-domains_and_urlsedit.png)

5. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Nella casella di testo **Identificatore** digitare un URL usando il criterio seguente:
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |

    ![image](./media/google-apps-tutorial/b1-domains_and_urls.png)

    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere tali valori, contattare il [team di supporto del client G Suite](https://www.google.com/contact/).

6. L'applicazione G Suite prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](./media/google-apps-tutorial/i3-attribute.png)

7. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    a. Fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](./media/google-apps-tutorial/i2-attribute.png)

    ![image](./media/google-apps-tutorial/i4-attribute.png)

    b. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo.

    c. Fare clic su **Save**.

8. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il certificato appropriato in base ai propri requisiti e salvarlo nel computer in uso.

    ![image](./media/google-apps-tutorial/certificatebase64.png)

9. Nella sezione **Set up G Suite** (Configura G Suite) copiare l'URL appropriato in base alle proprie esigenze.

    La descrizione dell'URL potrebbe essere:

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

    ![image](./media/google-apps-tutorial/d1_saml.png) 

10. Aprire una nuova scheda nel browser e accedere a [Console di amministrazione di G Suite](http://admin.google.com/) usando l'account amministratore.

11. Fare clic su **Security**. Se non viene visualizzato il collegamento, può essere nascosto sotto il menu **More Controls** nella parte inferiore della schermata.

    ![Fare clic su sicurezza.][10]

12. Nella pagina **Security** (Sicurezza) fare clic su **Set up single sign-on (SSO)** (Configurazione Single Sign-On (SSO)).

    ![Fare clic su SSO.][11]

13. Eseguire le seguenti modifiche di configurazione:

    ![Configurare SSL][12]

    a. Selezionare **Setup SSO with third party identity provider** (Configurare l'accesso SSO con un provider di terze parti).

    b. Nel campo **Sign-in page URL** (URL pagina di accesso) di G Suite incollare il valore dell' **URL di accesso** copiato dal portale di Azure.

    c. Nel campo **Sign-out page URL** (URL pagina di disconnessione) di G Suite incollare il valore dell' **URL di disconnessione** copiato dal portale di Azure.

    d. Nel campo **Change password URL** (URL di modifica della password) di G Suite incollare il valore dell' **URL di modifica della password** copiato dal portale di Azure.

    e. In G Suite, per il **certificato di verifica**, caricare il certificato che è stato scaricato dal portale di Azure.

    f. Selezionare **Use a domain specific issuer** (Usa un'autorità di certificazione specifica del dominio).

    g. Fare clic su **Salva modifiche**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![image](./media/google-apps-tutorial/d_users_and_groups.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![image](./media/google-apps-tutorial/d_adduser.png)

3. In Proprietà utente seguire questa procedura.

    ![image](./media/google-apps-tutorial/d_userproperties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.

### <a name="create-a-g-suite-test-user"></a>Creare un utente di test di G Suite

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in G Suite. G Suite supporta il provisioning automatico abilitato per impostazione predefinita. Non è necessaria alcuna azione dell'utente in questa sezione. Se un utente non esiste in G Suite, ne viene creato uno nuovo quando si prova ad accedere a G Suite Software.

> [!NOTE]
> Assicurarsi che l'utente esiste già in G Suite se non è stato attivato il provisioning in Azure AD prima del test dell'accesso Single Sign-on.

> [!NOTE]
> Se è necessario creare un utente manualmente, contattare il [team di supporto di Google](https://www.google.com/contact/).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a G Suite.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![image](./media/google-apps-tutorial/d_all_applications.png)

2. Nell'elenco di applicazioni selezionare **G Suite**.

    ![image](./media/google-apps-tutorial/d_all_proapplications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![image](./media/google-apps-tutorial/d_leftpaneusers.png)

4. Selezionare il pulsante **Aggiungi** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![image](./media/google-apps-tutorial/d_assign_user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro G Suite nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione G Suite.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png