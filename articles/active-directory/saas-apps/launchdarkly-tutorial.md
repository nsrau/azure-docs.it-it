---
title: 'Esercitazione: Integrazione di Azure Active Directory con LaunchDarkly | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e LaunchDarkly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0e9cb37e-16bf-493b-bfc8-8d9840545a1e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 54bf459f6acd7649f3ad1a546bef1d0429393161
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420760"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Esercitazione: Integrazione di Azure Active Directory con LaunchDarkly

Questa esercitazione descrive come integrare LaunchDarkly con Azure Active Directory (Azure AD).

L'integrazione di LaunchDarkly con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a LaunchDarkly.
- È possibile abilitare gli utenti per l'accesso automatico a LaunchDarkly (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con LaunchDarkly, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione di LaunchDarkly abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di LaunchDarkly dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-launchdarkly-from-the-gallery"></a>Aggiunta di LaunchDarkly dalla raccolta
Per configurare l'integrazione di LaunchDarkly in Azure AD, è necessario aggiungere LaunchDarkly dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere LaunchDarkly dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **LaunchDarkly**, selezionare **LaunchDarkly** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![LaunchDarkly nell'elenco risultati](./media/launchdarkly-tutorial/tutorial_launchdarkly_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con LaunchDarkly in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di LaunchDarkly che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in LaunchDarkly.

Per configurare e testare l'accesso Single Sign-On di Azure AD con LaunchDarkly, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test di LaunchDarkly](#create-a-launchdarkly-test-user)**: per avere una controparte di Britta Simon in LaunchDarkly collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione LaunchDarkly.

**Per configurare Single Sign-On di Azure AD con LaunchDarkly, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **LaunchDarkly** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Single Sign-On](./media/launchdarkly-tutorial/tutorial_launchdarkly_samlbase.png)

1. Nella sezione **URL e dominio LaunchDarkly** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di LaunchDarkly](./media/launchdarkly-tutorial/tutorial_launchdarkly_url.png)

    a. Nella casella di testo **Identificatore (ID entità)** digitare l'URL: `app.launchdarkly.com`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`
    
    > [!NOTE]
    > Il valore di URL di risposta non è reale. È necessario aggiornarlo con l'URL di risposta reale, descritto più avanti nell'esercitazione.

1. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di LaunchDarkly](./media/launchdarkly-tutorial/tutorial_launchdarkly_url1.png)

    Nella casella di testo **URL accesso** digitare l'URL: `https://app.launchdarkly.com`

1. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/launchdarkly-tutorial/tutorial_launchdarkly_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/launchdarkly-tutorial/tutorial_general_400.png)
    
1. Nella sezione **Configurazione di LaunchDarkly** fare clic su **Configura LaunchDarkly** per aprire la finestra **Configura accesso**. Copiare **URL servizio Single Sign-On SAML** dalla **sezione di riferimento rapido**.

    ![Configurazione di LaunchDarkly](./media/launchdarkly-tutorial/tutorial_launchdarkly_configure.png)

1. In un'altra finestra del Web browser accedere al sito aziendale di LaunchDarkly come amministratore.

1. Selezionare **Account Settings** (Impostazioni account) nel pannello di spostamento a sinistra.

    ![Configurazione di LaunchDarkly](./media/launchdarkly-tutorial/configure1.png)

1. Fare clic sulla scheda **Security** (Sicurezza).

    ![Configurazione di LaunchDarkly](./media/launchdarkly-tutorial/configure2.png)

1. Fare clic su **ENABLE SSO** (ABILITA SSO) e quindi su **EDIT SAML CONFIGURATION** (MODIFICA CONFIGURAZIONE SAML).

    ![Configurazione di LaunchDarkly](./media/launchdarkly-tutorial/configure3.png)

1. Nella sezione **Edit your SAML configuration** (Modifica la configurazione SAML) seguire questa procedura:

    ![Configurazione di LaunchDarkly](./media/launchdarkly-tutorial/configure4.png)

    a. Copiare l'**URL del servizio consumer di SAML** dell'istanza in uso e incollarlo nella casella di testo URL di risposta della sezione **URL e dominio LaunchDarkly** del portale di Azure.

    b. Nella casella di testo **Sign-on URL** (URL di accesso) incollare il valore di **URL servizio Single Sign-On** copiato dal portale di Azure.

    c. Aprire il certificato scaricato dal portale di Azure nel Blocco note, copiare il contenuto e quindi incollarlo nella casella **X.509 certificate** (Certificato X.509) oppure caricare direttamente il certificato facendo clic sul collegamento **upload one** (caricarne uno).

    d. Fare clic su **Save**

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/launchdarkly-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/launchdarkly-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/launchdarkly-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/launchdarkly-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="create-a-launchdarkly-test-user"></a>Creare un utente di test di LaunchDarkly

Questa sezione descrive come creare un utente chiamato Britta Simon in LaunchDarkly. LaunchDarkly supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già, un nuovo utente viene creato durante un tentativo di accesso a LaunchDarkly.
>[!Note]
>Per creare un utente manualmente, contattare il [team del supporto clienti di LaunchDarkly](mailto:support@launchdarkly.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a LaunchDarkly.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a LaunchDarkly, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco di applicazioni selezionare **LaunchDarkly**.

    ![Collegamento di LaunchDarkly nell'elenco delle applicazioni](./media/launchdarkly-tutorial/tutorial_launchdarkly_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro LaunchDarkly nel pannello di accesso, si accederà automaticamente all'applicazione LaunchDarkly.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/launchdarkly-tutorial/tutorial_general_01.png
[2]: ./media/launchdarkly-tutorial/tutorial_general_02.png
[3]: ./media/launchdarkly-tutorial/tutorial_general_03.png
[4]: ./media/launchdarkly-tutorial/tutorial_general_04.png

[100]: ./media/launchdarkly-tutorial/tutorial_general_100.png

[200]: ./media/launchdarkly-tutorial/tutorial_general_200.png
[201]: ./media/launchdarkly-tutorial/tutorial_general_201.png
[202]: ./media/launchdarkly-tutorial/tutorial_general_202.png
[203]: ./media/launchdarkly-tutorial/tutorial_general_203.png

