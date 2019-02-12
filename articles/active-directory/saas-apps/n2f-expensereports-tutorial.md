---
title: 'Esercitazione: Integrazione di Azure Active Directory con N2F - Expense reports | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e N2F - Expense reports.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.openlocfilehash: d8d3d9c14ee1eaa5e5d6dc3245c0d4071306f12a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811631"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Esercitazione: Integrazione di Azure Active Directory con N2F - Expense reports

Questa esercitazione descrive come integrare N2F - Expense reports con Azure Active Directory (Azure AD).

L'integrazione di N2F - Expense reports con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a N2F - Expense reports.
- È possibile abilitare gli utenti per l'accesso automatico a N2F - Expense reports (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con N2F - Expense reports, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di N2F - Expense reports abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di N2F - Expense reports dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Aggiunta di N2F - Expense reports dalla raccolta

Per configurare l'integrazione di N2F - Expense reports in Azure AD, è necessario aggiungere N2F - Expense reports dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere N2F - Expense reports dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **N2F - Expense reports**, selezionare **N2F - Expense reports** dal pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![N2F - Expense reports nell'elenco dei risultati](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con N2F - Expense reports usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di N2F - Expense reports corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in N2F - Expense reports.

Per configurare e testare l'accesso Single Sign-On di Azure AD con N2F - Expense reports, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **Creare un utente di test di N2F - Expense reports**: per avere una controparte di Britta Simon in N2F - Expense reports collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione N2F - Expense reports.

**Per configurare l'accesso Single Sign-On di Azure AD con N2F - Expense reports, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **N2F - Expense reports** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Single Sign-On](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_samlbase.png)

3. Nella sezione **URL e dominio N2F - Expense reports**, se si vuole configurare l'applicazione in modalità avviata da **IDP**, non è necessario eseguire alcun passaggio, perché l'app è già preintegrata in Azure.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di N2F - Expense reports](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url1.png)

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di N2F - Expense reports](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url2.png)

    Nella casella di testo **URL accesso** digitare l'URL: `https://www.n2f.com/app/`

5. Nella sezione **Certificato di firma SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e incollarlo nel Blocco note.

    ![Collegamento di download del certificato](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_certificate.png)

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/n2f-expensereports-tutorial/tutorial_general_400.png)

7. Nella sezione **Configurazione di N2F - Expense reports** fare clic su **Configura N2F - Expense reports** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML** dalla **sezione di riferimento rapido.**

    ![Configurazione di N2F - Expense reports](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_configure.png)

8. In un'altra finestra del Web browser accedere al sito aziendale di N2F - Expense reports come amministratore.

9. Fare clic su **Settings** (Impostazioni) e quindi selezionare **Advance Settings** (Impostazioni avanzate) dall'elenco a discesa.

    ![Configurazione di N2F - Expense reports](./media/n2f-expensereports-tutorial/configure1.png)

10. Selezionare la scheda **Account settings** (Impostazioni account).

    ![Configurazione di N2F - Expense reports](./media/n2f-expensereports-tutorial/configure2.png)

11. Selezionare **Authentication** (Autenticazione) e quindi la scheda **Add an authentication method** (Aggiungi un metodo di autenticazione).

    ![Configurazione di N2F - Expense reports](./media/n2f-expensereports-tutorial/configure3.png)

12. Selezionare **SAML Microsoft Office 365** come metodo di autenticazione.

    ![Configurazione di N2F - Expense reports](./media/n2f-expensereports-tutorial/configure4.png)

13. Nella sezione **Authentication method** (Metodo di autenticazione) seguire questa procedura:

    ![Configurazione di N2F - Expense reports](./media/n2f-expensereports-tutorial/configure5.png)

    a. Nella casella di testo **Entity ID** (ID entità) incollare il valore di **ID entità SAML** copiato dal portale di Azure.

    b. Nella casella di testo **Metadata URL** (URL dei metadati) incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    c. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/n2f-expensereports-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/n2f-expensereports-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/n2f-expensereports-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/n2f-expensereports-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="create-a-n2f---expense-reports-test-user"></a>Creare un utente di test di N2F - Expense reports

Per consentire agli utenti di Azure AD di accedere a N2F - Expense reports, è necessario effettuarne il provisioning in N2F - Expense reports. Nel caso di N2F - Expense reports, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di N2F - Expense reports come amministratore.

2. Fare clic su **Settings** (Impostazioni) e quindi selezionare **Advance Settings** (Impostazioni avanzate) dall'elenco a discesa.

   ![Aggiungere un utente in N2F - Expense reports](./media/n2f-expensereports-tutorial/configure1.png)

3. Selezionare la scheda **Users** (Utenti) dal riquadro di spostamento a sinistra.

    ![Configurazione di N2F - Expense reports](./media/n2f-expensereports-tutorial/user1.png)

4. Selezionare la scheda **New user** (Nuovo utente).

   ![Configurazione di N2F - Expense reports](./media/n2f-expensereports-tutorial/user2.png)

5. Nella sezione **User** (Utente) seguire questa procedura:

    ![Configurazione di N2F - Expense reports](./media/n2f-expensereports-tutorial/user3.png)

    a. Nella casella di testo **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon@contoso.com**.

    b. Nella casella di testo **First name** (Nome) immettere il nome dell'utente, ad esempio **Britta**.

    c. Nella casella di testo **Name** (Nome) immettere il nome dell'utente, ad esempio **BrittaSimon**.

    d. Scegliere **Role, Direct manager (N+1)** (Ruolo, Responsabile diretto N+1) e **Division** (Reparto) in base alle esigenze della propria organizzazione.

    e. Fare clic su **Validate and send invitation** (Convalida e invia invito).

    > [!NOTE]
    > Se si riscontrano problemi durante l'aggiunta dell'utente, contattare il [team di supporto di N2F - Expense reports](mailto:support@n2f.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a N2F - Expense reports.

![Assegnare il ruolo utente][200]

**Per assegnare Britta Simon a N2F - Expense reports, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **N2F - Expense reports**.

    ![Collegamento di N2F - Expense reports nell'elenco delle applicazioni](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro N2F - Expense reports nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione N2F - Expense reports.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/n2f-expensereports-tutorial/tutorial_general_01.png
[2]: ./media/n2f-expensereports-tutorial/tutorial_general_02.png
[3]: ./media/n2f-expensereports-tutorial/tutorial_general_03.png
[4]: ./media/n2f-expensereports-tutorial/tutorial_general_04.png

[100]: ./media/n2f-expensereports-tutorial/tutorial_general_100.png

[200]: ./media/n2f-expensereports-tutorial/tutorial_general_200.png
[201]: ./media/n2f-expensereports-tutorial/tutorial_general_201.png
[202]: ./media/n2f-expensereports-tutorial/tutorial_general_202.png
[203]: ./media/n2f-expensereports-tutorial/tutorial_general_203.png

