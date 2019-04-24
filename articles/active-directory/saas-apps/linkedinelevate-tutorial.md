---
title: 'Esercitazione: Integrazione di Azure Active Directory con LinkedIn Elevate | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e LinkedIn Elevate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ca8e537f261b59fb4e069d47d24e21abbdeca46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60259612"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Esercitazione: Integrazione di Azure Active Directory con LinkedIn Elevate

Questa esercitazione descrive come integrare LinkedIn Elevate con Azure Active Directory (Azure AD).

L'integrazione di LinkedIn Elevate con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a LinkedIn Elevate
- È possibile abilitare gli utenti per l'accesso automatico a LinkedIn Elevate (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con LinkedIn Elevate sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di LinkedIn Elevate abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.
Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di LinkedIn Elevate dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Aggiungere LinkedIn Elevate dalla raccolta
Per configurare l'integrazione di LinkedIn Elevate in Azure AD è necessario aggiungere LinkedIn Elevate dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere LinkedIn Elevate dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]

1. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca online digitare **LinkedIn Elevate**. Nel riquadro dei risultati fare clic su **LinkedIn Elevate** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con LinkedIn Elevate in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di LinkedIn Elevate che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in LinkedIn Elevate.

La relazione di collegamento viene stabilita assegnando il valore del **nome utente** di Azure AD come valore di **Username** (nome utente) in LinkedIn Elevate.

Per configurare e testare l'accesso Single Sign-On di Azure AD con LinkedIn Elevate è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente test di LinkedIn Elevate](#creating-a-linkedin-elevate-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione LinkedIn Elevate.

**Per configurare l'accesso Single Sign-On di Azure AD con LinkedIn Elevate seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **LinkedIn Elevate** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](./media/linkedinelevate-tutorial/tutorial-linkedin_01.png)

1. In un'altra finestra del Web browser accedere al tenant LinkedIn Elevate come amministratore.

1. In **Account Center** (Centro account) fare clic su **Global Settings** (Impostazioni globali) in **Settings** (Impostazioni). Selezionare anche **Elevate - Elevate AAD Test** (Elevate - Test AAD Elevate) dall'elenco a discesa.

    ![Configure Single Sign-On](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Fare clic su **OR Click Here to load and copy individual fields from the form** (O fare clic qui per caricare e copiare i singoli file dal modulo) e copiare l'**ID entità** e l'**URL ACS (Assertion Consumer Access)**

    ![Configure Single Sign-On](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

1. Nel portale di Azure, in **URL e dominio LinkedIn Elevate**, attenersi alla procedura seguente per configurare SSO in modalità **avviata da IdP**

    ![Configure Single Sign-On](./media/linkedinelevate-tutorial/tutorial_linkedin_signon_01.png)

    a. Nella casella di testo **Identificatore** immettere l'**ID entità** copiato dal portale di LinkedIn 

    b. Nella casella di testo **URL di risposta** immettere l'**URL ACS (Assertion Consumer Access)** copiato dal portale di LinkedIn

1. Se si vuole configurare SSO in modalità **avviata da SP**, scegliere l'opzione Mostra impostazioni URL avanzate nella sezione di configurazione e configurare l'URL di accesso con il modello seguente:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>` 

    ![Configure Single Sign-On](./media/linkedinelevate-tutorial/tutorial_linkedin_signon_02.png) 

1. L'applicazione LinkedIn Elevate richiede le asserzioni SAML in un formato specifico. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra un esempio relativo a questa operazione. Il valore predefinito dell'**ID utente** è **user.userprincipalname** ma LinkedIn Elevate prevede che venga mappato all'indirizzo di posta elettronica dell'utente. A tale scopo è possibile usare l'attributo **user.mail** dall'elenco oppure usare il valore di attributo appropriato in base alla configurazione dell'organizzazione.

    ![Configure Single Sign-On](./media/linkedinelevate-tutorial/updateusermail.png)

1. Nella sezione **Attributi utente** fare clic su **Visualizza e modifica tutti gli altri attributi utente** e impostare gli attributi. È necessario aggiungere un'altra attestazione denominata **department** e il valore deve essere mappato a **user.department**.

    | Nome attributo | Valore attributo |
    | --- | --- |
    | department| user.department |

      ![Creazione di un utente test di Azure AD](./media/linkedinelevate-tutorial/userattribute.png)

      a. Fare clic su Aggiungi attributo per aprire la pagina dei dettagli dell'attributo e aggiungere l'attributo department come mostrato di seguito.

      ![Creazione di un utente test di Azure AD](./media/linkedinelevate-tutorial/adduserattribute.png)

      b. Fare clic su **Ok** per salvare l'attributo.

      c. Modificare il nome dell'attributo **emailaddress** in **email**.

1. Nella sezione **Certificato di firma SAML** fare clic su **XML metadati** e quindi salvare il file XML nel computer.

    ![Configure Single Sign-On](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_certificate.png) 

1. Fare clic su **Save**.

    ![Configure Single Sign-On](./media/linkedinelevate-tutorial/tutorial_general_400.png)

1. Accedere alla sezione **LinkedIn Admin Settings** (Impostazioni di amministrazione LinkedIn). Caricare il file XML appena scaricato dal portale di Azure facendo clic sull'opzione Upload XML file (Carica file XML).

    ![Configure Single Sign-On](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Fare clic su **On** per abilitare SSO. Lo stato SSO passa da **Not Connected** (Non connesso) a **Connected** (Connesso)

    ![Configure Single Sign-On](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/linkedinelevate-tutorial/create_aaduser_01.png) 

1. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.

    ![Creazione di un utente test di Azure AD](./media/linkedinelevate-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.

    ![Creazione di un utente test di Azure AD](./media/linkedinelevate-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/linkedinelevate-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="creating-a-linkedin-elevate-test-user"></a>Creare un utente test di LinkedIn Elevate

L'applicazione LinkedIn Elevate supporta il provisioning dell'utente just-in-time e dopo l'autenticazione gli utenti verranno automaticamente creati nell'applicazione. Nella pagina delle impostazioni di amministrazione del portale di LinkedIn Elevate spostare il selettore dell'opzione **Automatically Assign licenses** (Assegna automaticamente le licenze) per attivare il provisioning just-in-time, assegnando una licenza all'utente. LinkedIn Elevate supporta anche il provisioning utenti automatico; [qui](linkedinelevate-provisioning-tutorial.md) è possibile trovare altre informazioni su come configurare il provisioning utenti automatico.

   ![Creazione di un utente test di Azure AD](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione viene concesso a Britta Simon l'accesso a LinkedIn Elevate per consentirle di usare l'accesso Single Sign-On di Azure.

![Assegna utente][200] 

**Per assegnare Britta Simon a LinkedIn Elevate seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione applicazioni, passare alla visualizzazione directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201]

1. Nell'elenco delle applicazioni selezionare **LinkedIn Elevate**.

    ![Configure Single Sign-On](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_0001.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro LinkedIn Elevate nel Pannello di accesso, viene visualizzata la pagina di accesso di Azure e, se l'accesso ha esito positivo, si accede all'applicazione LinkedIn Elevate.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Esercitazione: Configurazione di LinkedIn Elevate per il provisioning utenti automatico con Azure Active Directory](linkedinelevate-provisioning-tutorial.md)
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Configura provisioning utenti](linkedinelevate-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/linkedinElevate-tutorial/tutorial_general_01.png
[2]: ./media/linkedinElevate-tutorial/tutorial_general_02.png
[3]: ./media/linkedinElevate-tutorial/tutorial_general_03.png
[4]: ./media/linkedinElevate-tutorial/tutorial_general_04.png

[100]: ./media/linkedinElevate-tutorial/tutorial_general_100.png

[200]: ./media/linkedinElevate-tutorial/tutorial_general_200.png
[201]: ./media/linkedinElevate-tutorial/tutorial_general_201.png
[202]: ./media/linkedinElevate-tutorial/tutorial_general_202.png
[203]: ./media/linkedinElevate-tutorial/tutorial_general_203.png
