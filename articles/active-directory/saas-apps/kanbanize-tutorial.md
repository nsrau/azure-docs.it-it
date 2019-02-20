---
title: 'Esercitazione: Integrazione di Azure Active Directory con Kanbanize | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Kanbanize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b436d2f6-bfa5-43fd-a8f9-d2144dc25669
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22c136225e5a8526afd482e5ef8400198947422f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199681"
---
# <a name="tutorial-azure-active-directory-integration-with-kanbanize"></a>Esercitazione: Integrazione di Azure Active Directory con Kanbanize

Questa esercitazione descrive come integrare Kanbanize con Azure Active Directory (Azure AD).

L'integrazione di Kanbanize con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Kanbanize.
- È possibile abilitare gli utenti per l'accesso automatico a Kanbanize (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Kanbanize, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione abilitata per l'accesso Single Sign-On Kanbanize

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Kanbanize dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-kanbanize-from-the-gallery"></a>Aggiunta di Kanbanize dalla raccolta
Per configurare l'integrazione di Kanbanize in Azure AD, è necessario aggiungere Kanbanize dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Kanbanize dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Kanbanize**, selezionare **Kanbanize** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Kanbanize nell'elenco risultati](./media/kanbanize-tutorial/tutorial_kanbanize_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Kanbanize usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Kanbanize che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Kanbanize.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Kanbanize, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Kanbanize](#create-a-kanbanize-test-user)**: per avere una controparte di Britta Simon in Kanbanize collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Kanbanize.

**Per configurare Single Sign-On di Azure AD con Kanbanize, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Kanbanize** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/kanbanize-tutorial/tutorial_kanbanize_samlbase.png)

3. Nella sezione **URL e dominio Kanbanize** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<subdomain>.kanbanize.com/`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<subdomain>.kanbanize.com/saml/acs`

    c. Selezionare **Mostra impostazioni URL avanzate**

    d.  Nella casella di testo **Stato dell'inoltro** digitare un URL: `/ctrl_login/saml_login`

    e. Se si vuole configurare l'applicazione in modalità avviata da **SP** nella sezione **URL di accesso** digitare un URL usando il modello seguente: `https://<subdomain>.kanbanize.com`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Kanbanize](mailto:support@ms.kanbanize.com). 

5. L'applicazione Kanbanize prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra un esempio relativo a questa operazione. Il valore predefinito dell'**ID utente** è **user.userprincipalname** ma Kanbanize si aspetta che venga mappato all'indirizzo e-mail dell'utente. A tale scopo, è possibile usare l'attributo **user.mail** dall'elenco oppure usare il valore di attributo appropriato in base alla configurazione dell'organizzazione
    
    ![Configure Single Sign-On](./media/kanbanize-tutorial/tutorial_Kanbanize_attribute.png)

6. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/kanbanize-tutorial/tutorial_kanbanize_certificate.png) 

7. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/kanbanize-tutorial/tutorial_general_400.png)
    
8. Nella sezione **Configurazione di Kanbanize** fare clic su **Configura Kanbanize** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_configure.png)

9. In un'altra finestra del Web browser accedere a Kanbanize come amministratore della sicurezza. 

10. Andare sulla parte superiore destra della pagina e fare clic sul logo delle **impostazioni**.

    ![Impostazioni di Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_set.png)

11. Nella pagina del pannello Administration (Amministrazione) dal lato sinistro del menu fare clic su **Integrations** (Integrazioni) e quindi abilitare **Single Sign-On**. 

    ![Integrazioni di Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_admin.png)

12. Nella sezione delle integrazioni fare clic su **CONFIGURE** (CONFIGURA) per aprire la pagina **Single Sign-On Integration** (Integrazione Single Sign-On).

    ![Configurazione di Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_config.png)

13. Nella pagina **Single Sign-On Integration** (Integrazione Single Sign-On) in **Configurations** (Configurazioni) seguire questa procedura:

    ![Integrazioni di Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_save.png)

    a. Nella casella di testo **Idp Entity Id** (ID entità provider di identità) incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure.

    b. Nella casella di testo **Idp Login Endpoint** (Endpoint di accesso IdP) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

    c. Nella casella di testo **Idp Logout Endpoint** (Endpoint di disconnessione IdP) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

    d. Nella casella di testo **Attribute name for Email** (Nome attributo per la posta elettronica) immettere questo valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. Nella casella di testo **Attribute name for First Name** (Nome attributo per nome) immettere questo valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. Nella casella di testo **Attribute name for Last Name** (Nome attributo per cognome) immettere questo valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` 
    > [!Note]
    > È possibile ottenere questi valori combinando i valori dello spazio dei nomi e del nome dell'attributo corrispondente nella sezione Attributi utente nel portale di Azure.

    g. Nel Blocco note aprire il certificato con codifica Base 64 scaricato dal portale di Azure copiarne il contenuto senza i contrassegni di inizio e fine e quindi incollarlo nella casella  **Idp X.509 Certificate**  (Certificato X.509 IdP).

    h. Selezionare **Enable login with both SSO and Kanbanize** (Abilita l'accesso sia con SSO che con Kanbanize).
    
    i. Fare clic su **Salva impostazioni**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/kanbanize-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/kanbanize-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/kanbanize-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/kanbanize-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-kanbanize-test-user"></a>Creare un utente test di Kanbanize

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Kanbanize. Kanbanize supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a Kanbanize viene creato un nuovo utente, se questo non esiste già.

>[!Note]
>Per creare un utente manualmente, contattare il  [team del supporto clienti di Kanbanize](mailto:support@ms.kanbanize.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Kanbanize.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Kanbanize, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco applicazioni selezionare **Kanbanize**.

    ![Collegamento di Kanbanize nell'elenco delle applicazioni](./media/kanbanize-tutorial/tutorial_kanbanize_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Kanbanize nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Kanbanize.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kanbanize-tutorial/tutorial_general_01.png
[2]: ./media/kanbanize-tutorial/tutorial_general_02.png
[3]: ./media/kanbanize-tutorial/tutorial_general_03.png
[4]: ./media/kanbanize-tutorial/tutorial_general_04.png

[100]: ./media/kanbanize-tutorial/tutorial_general_100.png

[200]: ./media/kanbanize-tutorial/tutorial_general_200.png
[201]: ./media/kanbanize-tutorial/tutorial_general_201.png
[202]: ./media/kanbanize-tutorial/tutorial_general_202.png
[203]: ./media/kanbanize-tutorial/tutorial_general_203.png

