---
title: 'Esercitazione: integrazione di Azure Active Directory con Sauce Labs - Mobile and Web Testing | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Sauce Labs - Mobile and Web Testing.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3142d947-70e5-4345-8a30-b92d8715fac9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c4e3d074ad241ec6add6fe43b77413ce283e389
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207008"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Esercitazione: integrazione di Azure Active Directory con Sauce Labs - Mobile and Web Testing

Questa esercitazione descrive come integrare Sauce Labs - Mobile and Web Testing con Azure Active Directory (Azure AD).

L'integrazione di Sauce Labs - Mobile and Web Testing con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Sauce Labs - Mobile and Web Testing.
- È possibile abilitare gli utenti per l'accesso automatico a Sauce Labs - Mobile and Web Testing (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Sauce Labs - Mobile and Web Testing, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione abilitata per accesso Single Sign-On per Sauce Labs - Mobile and Web Testing

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Sauce Labs - Mobile and Web Testing dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Aggiunta di Sauce Labs - Mobile and Web Testing dalla raccolta
Per configurare l'integrazione di Sauce Labs - Mobile and Web Testing in Azure AD, è necessario aggiungere Sauce Labs - Mobile and Web Testing dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Sauce Labs - Mobile and Web Testing dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca, digitare **Sauce Labs - Mobile and Web Testing**, selezionare **Sauce Labs - Mobile and Web Testing** nel pannello dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Sauce Labs - Mobile and Web Testing nell'elenco risultati](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Sauce Labs - Mobile and Web Testing usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Sauce Labs - Mobile and Web Testing che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Sauce Labs - Mobile and Web Testing.

Per configurare e testare l'accesso Single Sign-On di Azure AD conSauce Labs - Mobile and Web Testing, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test di Sauce Labs - Mobile and Web Testing](#create-a-sauce-labs---mobile-and-web-testing-test-user)**: per avere una controparte di Britta Simon in Sauce Labs - Mobile and Web Testing collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Sauce Labs - Mobile and Web Testing.

**Per configurare l'accesso Single Sign-On di Azure AD conSauce Labs - Mobile and Web Testing, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Sauce Labs - Mobile and Web Testing** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Single Sign-On](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_samlbase.png)

3. Nella sezione **Sauce Labs - Mobile and Web Testing**, l'utente non deve eseguire alcuna operazione perché l'applicazione è già preintegrata in Azure.

    ![Informazioni sull'accesso Single Sign-On agli URL e al dominio di Sauce Labs - Mobile and Web Testing](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_url.png)

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_certificate.png)

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_400.png)

6. In un'altra finestra del Web browser, accedere al sito aziendale di Sauce Labs - Mobile and Web Testing come amministratore.

7. Fare clic su **Icona utente** e selezionare la scheda **Gestione team**.

    ![Configure Single Sign-On](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

8. Immettere il **Nome di dominio** nella casella di testo.

    ![Configure Single Sign-On](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

9. Fare clic sulla scheda **Configura**.

    ![Configure Single Sign-On](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

10. Nella sezione **Single Sign-On**, eseguire la procedura seguente.

    ![Configure Single Sign-On](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Fare clic su **Sfoglia** e caricare il file di metadati scaricato da Azure AD.

    b. Selezionare la casella di controllo **CONSENTI IL PROVISIONING JIT**.

    c. Fare clic su **Salva**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
  
### <a name="create-a-sauce-labs---mobile-and-web-testing-test-user"></a>Creare un utente test di Sauce Labs - Mobile and Web Testing

Questa sezione descrive come creare un utente chiamato Britta Simon in Sauce Labs - Mobile and Web Testing. Sauce Labs - Mobile and Web Testing supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a Sauce Labs - Mobile and Web Testing viene creato un nuovo utente, se questo non esiste già.
>[!Note]
>Se è necessario creare un utente manualmente, contattare il  [team di supporto di Sauce Labs - Mobile and Web Testing](mailto:support@saucelabs.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Sauce Labs - Mobile and Web Testing.

![Assegnare il ruolo utente][200]

**Per aggiungere Sauce Labs - Mobile and Web Testing, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni, selezionare **Sauce Labs - Mobile and Web Testing**.

    ![Collegamento di Sauce Labs - Mobile and Web Testing nell'elenco delle applicazioni](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Sauce Labs - Mobile and Web Testing nel Pannello di accesso, si dovrebbe ottenere automaticamente l'accesso all'applicazione Sauce Labs - Mobile and Web Testing.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_01.png
[2]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_02.png
[3]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_03.png
[4]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_04.png

[100]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_100.png

[200]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_200.png
[201]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_201.png
[202]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_202.png
[203]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_203.png
