---
title: 'Esercitazione: Integrazione di Azure Active Directory con LinkedIn Learning| Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e LinkedIn Learning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: ffa689e9556e57560138d9629c616bd3a284f9b6
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36222308"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>Esercitazione: Integrazione di Azure Active Directory con LinkedIn Learning

Questa esercitazione descrive come integrare LinkedIn Learning con Azure Active Directory (Azure AD).

L'integrazione di LinkedIn Learning con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a LinkedIn Learning
- È possibile abilitare gli utenti per l'accesso automatico a LinkedIn Learning (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con LinkedIn Learning sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di LinkedIn Learning abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di LinkedIn Learning dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-linkedin-learning-from-the-gallery"></a>Aggiungere LinkedIn Learning dalla raccolta
Per configurare l'integrazione di LinkedIn Learning in Azure AD è necessario aggiungere LinkedIn Learning dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere LinkedIn Learning dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca online digitare **LinkedIn Learning**. Nel riquadro dei risultati fare clic su **LinkedIn Learning** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con LinkedIn Learning in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di LinkedIn Learning che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in LinkedIn Learning.

La relazione di collegamento viene stabilita assegnando il valore del **nome utente** di Azure AD come valore di **Username** (nome utente) in LinkedIn Learning.

Per configurare e testare l'accesso Single Sign-On di Azure AD con LinkedIn Learning è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di LinkedIn Learning](#creating-a-linkedin-learning-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione LinkedIn Learning.

**Per configurare l'accesso Single Sign-On di Azure AD con LinkedIn Learning seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **LinkedIn Learning** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/linkedinlearning-tutorial/tutorial-linkedin_01.png)

3. In un'altra finestra del Web browser accedere al tenant LinkedIn Learning come amministratore.

4. In **Account Center** (Centro account) fare clic su **Global Settings** (Impostazioni globali) in **Settings** (Impostazioni). Selezionare anche **Learning - Default** (Learning - Predefinito) nell'elenco a discesa.

    ![Configure Single Sign-On](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

5. Fare clic su **OR Click Here to load and copy individual fields from the form** (O fare clic qui per caricare e copiare i singoli file dal modulo) e copiare l'**ID entità** e l'**URL ACS (Assertion Consumer Access)**

    ![Configure Single Sign-On](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

6. Nella sezione **URL e dominio LinkedIn Learning** del portale di Azure seguire questa procedura se si desidera configurare SSO in modalità **avviata da IdP**

    ![Configure Single Sign-On](./media/linkedinlearning-tutorial/tutorial_linkedin_signon_01.png)

    a. Nella casella di testo **Identificatore** immettere l'**ID entità** copiato dal portale di LinkedIn 

    b. Nella casella di testo **URL di risposta** immettere l'**URL ACS (Assertion Consumer Access)** copiato dal portale di LinkedIn

7. Se si vuole configurare SSO in modalità **avviata da SP**, scegliere l'opzione Mostra impostazioni URL avanzate nella sezione di configurazione e configurare l'URL di accesso nel modo seguente:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Configure Single Sign-On](./media/linkedinlearning-tutorial/tutorial_linkedin_signon_02.png)   
    
8. L'applicazione LinkedIn Learning richiede le asserzioni SAML in un formato specifico. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra un esempio relativo a questa operazione. Il valore predefinito dell'**ID utente** è **user.userprincipalname** ma LinkedIn Learning prevede che venga mappato all'indirizzo di posta elettronica dell'utente. A tale scopo è possibile usare l'attributo **user.mail** dall'elenco oppure usare il valore di attributo appropriato in base alla configurazione dell'organizzazione. 

    ![Configure Single Sign-On](./media/linkedinlearning-tutorial/updateusermail.png)
    
9. Nella sezione **Attributi utente** fare clic su **Visualizza e modifica tutti gli altri attributi utente** e impostare gli attributi. L'utente deve aggiungere quattro attestazioni denominate **email**, **department**, **firstname** e **lastname** e il valore deve essere mappato rispettivamente a **user.mail**, **user.department**, **user.givenname** e **user.surname**

    | Nome attributo | Valore attributo |
    | --- | --- |
    | email| user.mail |    
    | department| user.department |
    | firstname| user.givenname |
    | lastname| user.surname |
    
    ![Creazione di un utente test di Azure AD](./media/linkedinlearning-tutorial/userattribute.png)
    
    a. Fare clic su **Aggiungi attributo** per aprire la relativa finestra di dialogo.

    ![Creazione di un utente test di Azure AD](./media/linkedinlearning-tutorial/tutorial_attribute_04.png)

    ![Creazione di un utente test di Azure AD](./media/linkedinlearning-tutorial/tutorial_attribute_05.png)
    
    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.
    
    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **Ok**

10. Seguire questa procedura sull'attributo **name**.

    a. Fare clic sull'attributo per aprire la finestra **Modifica attributo**.

    ![Configure Single Sign-On](./media/linkedinlearning-tutorial/url_update.png)

    b. Eliminare il valore dell'URL dallo **spazio dei nomi**.
    
    c. Fare clic su **Ok** per salvare l'impostazione.

11. Nella sezione **Certificato di firma SAML** fare clic su **XML metadati** e quindi salvare il file XML nel computer.

    ![Configure Single Sign-On](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_certificate.png)

12. Fare clic su **Save**.

    ![Configure Single Sign-On](./media/linkedinlearning-tutorial/tutorial_general_400.png)

13. Accedere alla sezione **LinkedIn Admin Settings** (Impostazioni di amministrazione LinkedIn). Caricare il file XML scaricato dal portale di Azure facendo clic sull'opzione Upload XML file (Carica file XML).

    ![Configure Single Sign-On](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

14. Fare clic su **On** per abilitare SSO. Lo stato SSO passa da **Not Connected** (Non connesso) a **Connected** (Connesso)

    ![Configure Single Sign-On](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/linkedinlearning-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/linkedinlearning-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/linkedinlearning-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/linkedinlearning-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.

### <a name="creating-a-linkedin-learning-test-user"></a>Creare un utente test di LinkedIn Learning

L'applicazione LinkedIn Learning supporta il provisioning dell'utente just-in-time e dopo l'autenticazione gli utenti sono automaticamente creati nell'applicazione. Nella pagina delle impostazioni di amministrazione del portale di LinkedIn Learning spostare il selettore dell'opzione **Automatically Assign licenses** (Assegna automaticamente le licenze) per attivareil provisioning just-in-time, assegnando una licenza all'utente. LinkedIn Learning supporta anche il provisioning utenti automatico; [qui](linkedinlearning-provisioning-tutorial.md) è possibile trovare altre informazioni su come configurare il provisioning utenti automatico.

   ![Creazione di un utente test di Azure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a LinkedIn Learning.

![Assegna utente][200] 

**Per assegnare Britta Simon a LinkedIn Learning seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **LinkedIn Learning**.

    ![Configure Single Sign-On](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_0001.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro LinkedIn Learning nel Pannello di accesso, viene visualizzata la pagina di accesso di Azure e, se l'accesso ha esito positivo, si accede all'applicazione LinkedIn Learning.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Configura provisioning utenti](linkedinlearning-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/linkedinlearning-tutorial/tutorial_general_01.png
[2]: ./media/linkedinlearning-tutorial/tutorial_general_02.png
[3]: ./media/linkedinlearning-tutorial/tutorial_general_03.png
[4]: ./media/linkedinlearning-tutorial/tutorial_general_04.png

[100]: ./media/linkedinlearning-tutorial/tutorial_general_100.png

[200]: ./media/linkedinlearning-tutorial/tutorial_general_200.png
[201]: ./media/linkedinlearning-tutorial/tutorial_general_201.png
[202]: ./media/linkedinlearning-tutorial/tutorial_general_202.png
[203]: ./media/linkedinlearning-tutorial/tutorial_general_203.png