---
title: 'Esercitazione: Integrazione di Azure Active Directory con Secret Server (On-Premises)| Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Secret Server (On-Premises).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: c3a8f4643175e1da4dd278af238a436a34151616
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-secret-server-on-premises"></a>Esercitazione: Integrazione di Azure Active Directory con Secret Server (On-Premises)

Questa esercitazione descrive come integrare Secret Server (On-Premises) con Azure Active Directory (Azure AD).

L'integrazione di Secret Server (On-Premises) con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Secret Server (On-Premises).
- È possibile abilitare gli utenti per l'accesso automatico a Secret Server (On-Premises) (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Secret Server (On-Premises), sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Secret Server (On-Premises) abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Secret Server (On-Premises) dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Aggiunta di Secret Server (On-Premises) dalla raccolta
Per configurare l'integrazione di Secret Server (On-Premises) in Azure AD, è necessario aggiungere Secret Server (On-Premises) dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Secret Server (On-Premises) dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Secret Server (On-Premises)**, selezionare **Secret Server (On-Premises)** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Secret Server (On-Premises) nell'elenco risultati](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Secret Server (On-Premises) usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Secret Server (On-Premises) corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Secret Server (On-Premises).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Secret Server (On-Premises), è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Secret Server (On-Premises)](#create-a-secret-server-on-premises-test-user)**: per avere una controparte di Britta Simon in Secret Server (On-Premises) collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Secret Server (On-Premises).

**Per configurare l'accesso Single Sign-On di Azure AD con Secret Server (On-Premises), seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Secret Server (On-Premises)** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_samlbase.png)

3. Nella sezione **URL e dominio Secret Server (On-Premises)** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Secret Server (On-Premises)](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_url.png)

    a. Nella casella di testo **Identificatore** immettere il valore scelto dall'utente come esempio: `https://secretserveronpremises.azure`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx `

    > [!NOTE]
    > L'ID entità illustrato in precedenza è solo un esempio ed è possibile scegliere qualsiasi valore univoco che identifica l'istanza di Secret Server in Azure AD. È necessario inviare tale ID entità al [team di supporto clienti di Secret Server (On-Premises)](https://thycotic.force.com/support/s/) affinché il team possa eseguire la configurazione. Per altri dettagli, leggere [questo articolo](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Secret Server (On-Premises)](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<SecretServerURL>/login.aspx`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto di Secret Server (On-Premises)](https://thycotic.force.com/support/s/).

5. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_certificate.png)

6. Selezionare **Mostra impostazioni avanzate per la firma di certificati** e quindi selezionare **Opzione di firma** per **Firma asserzione e risposta SAML**.

    ![Opzioni di firma](./media/active-directory-saas-secretserver-on-premises-tutorial/signing.png)

7. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_400.png)
    
8. Nella sezione **Configurazione di Secret Server (On-Premises)** fare clic su **Configura Secret Server (On-Premises)** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di Secret Server (On-Premises)](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_configure.png)

9. Per configurare l'accesso Single Sign-On in **Secret Server (On-Premises)**, è necessario inviare il **certificato (Base64) scaricato, l'URL di disconnessione, l'URL del servizio Single Sign-On SAML** e l'**ID entità SAML** al [team di supporto di Secret Server (On-Premises)](https://thycotic.force.com/support/s/). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-secretserver-on-premises-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-secretserver-on-premises-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-secretserver-on-premises-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-secretserver-on-premises-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-secret-server-on-premises-test-user"></a>Creare un utente di test di Secret Server (On-Premises)

In questa sezione viene creato un utente di nome Britta Simon in Secret Server (On-Premises). Collaborare con il [team di supporto di Secret Server (On-Premises)](https://thycotic.force.com/support/s/) per aggiungere gli utenti nella piattaforma di Secret Server (On-Premises). Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Secret Server (On-Premises).

![Assegnare il ruolo utente][200]

**Per assegnare Britta Simon a Secret Server (On-Premises), seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **Secret Server (On-Premises)**.

    ![Collegamento di Secret Server (On-Premises) nell'elenco delle applicazioni](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Secret Server (On-Premises) nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Secret Server (On-Premises).
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_203.png

