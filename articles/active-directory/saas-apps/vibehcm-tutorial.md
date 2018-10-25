---
title: 'Esercitazione: Integrazione di Azure Active Directory con Vibe HCM | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Vibe HCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4379bef7-adc9-4b6d-9384-c46d9a914bfe
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: jeedes
ms.openlocfilehash: 395f99c986e0a51e8dd2c440d258724e862f275c
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2018
ms.locfileid: "48020704"
---
# <a name="tutorial-azure-active-directory-integration-with-vibe-hcm"></a>Esercitazione: Integrazione di Azure Active Directory con Vibe HCM

Questa esercitazione descrive come integrare Vibe HCM con Azure Active Directory (Azure AD).

L'integrazione di Vibe HCM con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Vibe HCM.
- È possibile abilitare gli utenti per l'accesso automatico a Vibe HCM (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Vibe HCM sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione abilitata per l'accesso Single Sign-On di Vibe HCM

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Vibe HCM dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-vibe-hcm-from-the-gallery"></a>Aggiunta di Vibe HCM dalla raccolta
Per configurare l'integrazione di Vibe HCM in Azure AD, è necessario aggiungere Vibe HCM dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Vibe HCM dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Vibe HCM**, selezionare **Vibe HCM** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Vibe HCM nell'elenco risultati](./media/vibehcm-tutorial/tutorial_vibehcm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Vibe HCM usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Vibe HCM che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Vibe HCM.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Vibe HCM, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Vibe HCM](#create-a-vibe-hcm-test-user)**: per avere una controparte di Britta Simon in Vibe HCM collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Vibe HCM.

**Per configurare Single Sign-On di Azure AD con Vibe HCM, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Vibe HCM** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/vibehcm-tutorial/tutorial_vibehcm_samlbase.png)

3. Nella sezione **URL e dominio Vibe HCM** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Vibe HCM](./media/vibehcm-tutorial/tutorial_vibehcm_url.png)

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Vibe HCM](./media/vibehcm-tutorial/tutorial_vibehcm_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<companyName>.vibehcm.com/portal.jsp`
     
    > [!NOTE] 
    > Il valore dell'URL di accesso non è reale. è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto di Vibe HCM](mailto:support@vibehcm.com).
 
4. Nella sezione **Certificato di firma SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e incollarlo nel Blocco note.

    ![Collegamento di download del certificato](./media/vibehcm-tutorial/tutorial_vibehcm_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/vibehcm-tutorial/tutorial_general_400.png)

6. Per configurare l'accesso Single Sign-On sul lato **Vibe HCM**, è necessario inviare l'**URL dei metadati di federazione dell'app** copiato al [team di supporto di Vibe HCM](mailto:support@vibehcm.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/vibehcm-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/vibehcm-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/vibehcm-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/vibehcm-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-vibe-hcm-test-user"></a>Creare un utente test di Vibe HCM

In questa sezione viene creato un utente chiamato Britta Simon in Vibe HCM. Collaborare con il [team di supporto di Vibe HCM](mailto:support@vibehcm.com) per aggiungere gli utenti alla piattaforma Vibe HCM. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Vibe HCM.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Vibe HCM seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Vibe HCM**.

    ![Collegamento di Vibe HCM nell'elenco delle applicazioni](./media/vibehcm-tutorial/tutorial_vibehcm_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Vibe HCM nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Vibe HCM.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/vibehcm-tutorial/tutorial_general_01.png
[2]: ./media/vibehcm-tutorial/tutorial_general_02.png
[3]: ./media/vibehcm-tutorial/tutorial_general_03.png
[4]: ./media/vibehcm-tutorial/tutorial_general_04.png

[100]: ./media/vibehcm-tutorial/tutorial_general_100.png

[200]: ./media/vibehcm-tutorial/tutorial_general_200.png
[201]: ./media/vibehcm-tutorial/tutorial_general_201.png
[202]: ./media/vibehcm-tutorial/tutorial_general_202.png
[203]: ./media/vibehcm-tutorial/tutorial_general_203.png

