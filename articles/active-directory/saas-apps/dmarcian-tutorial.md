---
title: 'Esercitazione: Integrazione di Azure Active Directory con dmarcian | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: jeedes
ms.openlocfilehash: 0f8878505280371bf6046c1d1f0d7fc1275dd496
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47039888"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>Esercitazione: Integrazione di Azure Active Directory con dmarcian

Questa esercitazione descrive come integrare dmarcian con Azure Active Directory (Azure AD).

L'integrazione di dmarcian con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a dmarcian.
- È possibile abilitare gli utenti per l'accesso automatico a dmarcian (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con dmarcian, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione abilitata per l'accesso Single Sign-On dmarcian

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di dmarcian dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-dmarcian-from-the-gallery"></a>Aggiunta di dmarcian dalla raccolta
Per configurare l'integrazione di dmarcian in Azure AD, è necessario aggiungere dmarcian dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere dmarcian dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **dmarcian**, selezionare **dmarcian** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![dmarcian nell'elenco risultati](./media/dmarcian-tutorial/tutorial_dmarcian_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con dmarcian in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di dmarcian che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in dmarcian.

Per configurare e testare l'accesso Single Sign-On di Azure AD con dmarcian, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di dmarcian](#create-a-dmarcian-test-user)**: per avere una controparte di Britta Simon in dmarcian collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione dmarcian.

**Per configurare l'accesso Single Sign-On di Azure AD con dmarcian, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **dmarcian** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/dmarcian-tutorial/tutorial_dmarcian_samlbase.png)

3. Nella sezione **URL e dominio dmarcian** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di dmarcian](./media/dmarcian-tutorial/tutorial_dmarcian_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il criterio seguente:
    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di dmarcian](./media/dmarcian-tutorial/tutorial_dmarcian_url1.png)

    Nella casella di testo **URL di accesso** digitare un URL usando il criterio seguente:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, È necessario aggiornare tali valori con i valori reali di identificatore, URL di risposta e URL di accesso. La procedura è descritta più avanti nell'esercitazione. 

5. Nella sezione **Certificato di firma SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e incollarlo nel Blocco note.

    ![Collegamento di download del certificato](./media/dmarcian-tutorial/tutorial_dmarcian_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/dmarcian-tutorial/tutorial_general_400.png)
    
7. In un'altra finestra del Web browser accedere a dmarcian come Amministratore della sicurezza.

8. Fare clic su **Profilo** nell'angolo in alto a destra e passare a **Preferenze**.

    ![Preferenze ](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

9. Scorrere verso il basso e fare clic sulla sezione **Single Sign-On**, quindi fare clic su **Configura**.

    ![Single ](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

10. Nella sezione **Single Sign-On SAML** impostare **Stato** su **Abilitato** e seguire questa procedura:

    ![Autenticazione ](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * Nella sezione **Add dmarcian to your Identity Provider** (Aggiungi dmarcian al provider di identità) fare clic su **COPIA** per copiare l'**URL del servizio consumer di asserzione** per l'istanza e incollarlo nella casella di testo **URL di risposta** nella sezione **URL e dominio di dmarcian** nel portale di Azure.

    * Nella sezione **Add dmarcian to your Identity Provider** (Aggiungi dmarcian al provider di identità) fare clic su **COPIA** per copiare l'**ID entità** per l'istanza e incollarlo nella casella di testo **Identificatore** nella sezione **URL e dominio di dmarcian** nel portale di Azure.

    * Nella sezione **Set up Authentication** (Configura autenticazione), nella casella di testo **Metadati provider di identità**, incollare l'**URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    * Nella sezione **Set up Authentication** (Configura autenticazione) incollare l'URL `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` nella casella di testo **Attribute Statements** (Istruzioni attributi).

    * Nella sezione **Set up Login URL** (Configura URL accesso) copiare l'**URL di accesso** per l'istanza e incollarlo nella casella di testo **URL di accesso** nella sezione **URL e dominio di dmarcian** del portale di Azure.

        > [!Note]
        > È possibile modificare l'**URL di accesso** in base all'organizzazione.

    * Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/dmarcian-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/dmarcian-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/dmarcian-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/dmarcian-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-dmarcian-test-user"></a>Creare un utente di test dmarcian

Per consentire agli utenti di Azure AD di accedere a dmarcian, è necessario effettuarne il provisioning in dmarcian. Nel caso di dmarcian il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a dmarcian come amministratore della sicurezza.

2. Fare clic su **Profilo** nell'angolo in alto a destra e passare a **Gestisci utenti**.

    ![Utente ](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. Sul lato destro della sezione **SSO Users** (Utenti SSO) fare clic su **Aggiungi nuovo utente**.

    ![Aggiungi utente ](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Nella finestra popup **Aggiungi nuovo utente** seguire questa procedura:

    ![Nuovo utente ](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. Nella casella di testo **New User Email** (Posta elettronica nuovo utente) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon@contoso.com**.

    b. Se si vogliono assegnare diritti di amministratore all'utente, selezionare **Make User an Admin** (Imposta utente come amministratore).

    c. Fare clic su **Add User**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a dmarcian.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a dmarcian, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **dmarcian**.

    ![Collegamento di dmarcian nell'elenco delle applicazioni](./media/dmarcian-tutorial/tutorial_dmarcian_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro dmarcian nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione dmarcian.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dmarcian-tutorial/tutorial_general_01.png
[2]: ./media/dmarcian-tutorial/tutorial_general_02.png
[3]: ./media/dmarcian-tutorial/tutorial_general_03.png
[4]: ./media/dmarcian-tutorial/tutorial_general_04.png

[100]: ./media/dmarcian-tutorial/tutorial_general_100.png

[200]: ./media/dmarcian-tutorial/tutorial_general_200.png
[201]: ./media/dmarcian-tutorial/tutorial_general_201.png
[202]: ./media/dmarcian-tutorial/tutorial_general_202.png
[203]: ./media/dmarcian-tutorial/tutorial_general_203.png

