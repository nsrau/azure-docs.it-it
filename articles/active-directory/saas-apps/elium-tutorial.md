---
title: 'Esercitazione: Integrazione di Azure Active Directory con Elium | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory ed Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: jeedes
ms.openlocfilehash: dfa90474632b2cf18055e0ba95994f120cb293ef
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447785"
---
# <a name="tutorial-azure-active-directory-integration-with-elium"></a>Esercitazione: Integrazione di Azure Active Directory con Elium

Questa esercitazione descrive come integrare Elium con Azure Active Directory (Azure AD).

L'integrazione di Elium con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Elium.
- È possibile abilitare gli utenti per l'accesso automatico a Elium (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Elium, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Elium abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Elium dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-elium-from-the-gallery"></a>Aggiunta di Elium dalla raccolta
Per configurare l'integrazione di Elium in Azure AD, è necessario aggiungere Elium dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Elium dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **Elium**, selezionare **Elium** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Elium nell'elenco dei risultati](./media/elium-tutorial/tutorial_elium_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Elium usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Elium corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Elium.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Elium, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test di Elium](#create-an-elium-test-user)**: per avere una controparte di Britta Simon in Elium collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Elium.

**Per configurare l'accesso Single Sign-On di Azure AD con Elium, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Elium** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/elium-tutorial/tutorial_elium_samlbase.png)

1. Nella sezione **URL e dominio Elium** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Elium](./media/elium-tutorial/tutorial_elium_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<platform-domain>.elium.com/login/saml2/acs`

1. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Elium](./media/elium-tutorial/tutorial_elium_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: ` https://<platform-domain>.elium.com/login/saml2/login`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, È possibile ottenere questi valori dal **file di metadati SP** che è possibile scaricare all'indirizzo `https://<platform-domain>.elium.com/login/saml2/metadata` e che viene illustrato più avanti in questa esercitazione.

1. L'applicazione Elium prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione.

    ![Configure Single Sign-On](./media/elium-tutorial/tutorial_attribute.png)

1. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente ed eseguire i passaggi descritti di seguito:
           
    | Nome attributo | Valore attributo |   
    | ---------------| ----------------|
    | email   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user. jobtitle|
    | company| user.companyname|
    
    > [!NOTE]
    > Queste sono le attestazioni predefinite. **Solo l'attestazione email è obbligatoria**. Anche per il provisioning JIT è obbligatoria solo l'attestazione email. Altre attestazioni personalizzate possono variare in base alla piattaforma del cliente.

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/elium-tutorial/tutorial_attribute_04.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    ![Configure Single Sign-On](./media/elium-tutorial/tutorial_attribute_05.png)

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Lasciare vuota la casella relativa allo spazio dei nomi.
    
    e. Fare clic su **OK**. 

1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/elium-tutorial/tutorial_elium_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/elium-tutorial/tutorial_general_400.png)
    
1. In un'altra finestra del Web browser accedere al sito aziendale di Elium come amministratore.

1. Fare clic **sul profilo**utente nell'angolo in alto a destra e quindi selezionare **Administration** (Amministrazione).

    ![Configure Single Sign-On](./media/elium-tutorial/user1.png)

1. Selezionare la scheda **Security** (Sicurezza).

    ![Configure Single Sign-On](./media/elium-tutorial/user2.png)

1. Scorrere fino alla sezione **Single sign-on (SSO)** e seguire questa procedura:

    ![Configure Single Sign-On](./media/elium-tutorial/user3.png)

    a. Copiare il valore di **Verify that SAML2 authentication works for your account** (Verificare il funzionamento dell'autenticazione SAML2 per l'account) e incollarlo nella casella di testo **URL di accesso** nella sezione **URL e dominio Elium** del portale di Azure.

    > [!NOTE]
    > Dopo avere configurato SSO, è sempre possibile accedere alla pagina di accesso remoto all'URL seguente: `https://<platform_domain>/login/regular/login` 

    b. Selezionare la casella di controllo **Enable SAML2 federation** (Abilita federazione SAML2).

    c. Selezionare la casella di controllo **JIT Provisioning** (Provisioning JIT).

    d. Aprire **SP Metadata** (Metadati SP) facendo clic su **Download**.

    e. Cercare **entityID** nel file **SP Metadata** (Metadati SP), copiare il valore di **entityID** e incollarlo nella casella di testo **Identificatore** nella sezione **URL e dominio Elium** nel portale di Azure. 

    ![Configure Single Sign-On](./media/elium-tutorial/user4.png)

    f. Cercare **AssertionConsumerService** nel file **SP Metadata** (Metadati SP), copiare il valore di **Location** (Posizione) e incollarlo nella casella di testo **URL di risposta** nella sezione **URL e dominio Elium** nel portale di Azure.

    ![Configure Single Sign-On](./media/elium-tutorial/user5.png)

    g. Copiare il contenuto del file dei metadati scaricato dal portale di Azure nel Blocco note e incollarlo nella casella di testo **IdP Metadata** (Metadati IdP).

    h. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/elium-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/elium-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/elium-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/elium-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-an-elium-test-user"></a>Creare un utente di test di Elium

L'obiettivo di questa sezione consiste nel creare un utente di test di nome Britta Simon in Elium. Elium supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante il tentativo di accesso a Elium viene creato un nuovo utente, se questo non esiste già.
>[!Note]
>Se è necessario creare un utente manualmente, contattare il [team di supporto di Elium](mailto:support@elium.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Elium.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Elium, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **Elium**.

    ![Collegamento di Elium nell'elenco delle applicazioni](./media/elium-tutorial/tutorial_elium_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Elium nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Elium.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/elium-tutorial/tutorial_general_01.png
[2]: ./media/elium-tutorial/tutorial_general_02.png
[3]: ./media/elium-tutorial/tutorial_general_03.png
[4]: ./media/elium-tutorial/tutorial_general_04.png

[100]: ./media/elium-tutorial/tutorial_general_100.png

[200]: ./media/elium-tutorial/tutorial_general_200.png
[201]: ./media/elium-tutorial/tutorial_general_201.png
[202]: ./media/elium-tutorial/tutorial_general_202.png
[203]: ./media/elium-tutorial/tutorial_general_203.png

