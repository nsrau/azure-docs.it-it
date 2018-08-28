---
title: 'Esercitazione: Integrazione di Azure Active Directory con Comm100 Live Chat | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Comm100 Live Chat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2018
ms.author: jeedes
ms.openlocfilehash: b85162c8392e8ecdb08a3ed04ff5b9de835808a1
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "42146411"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>Esercitazione: Integrazione di Azure Active Directory con Comm100 Live Chat

Questa esercitazione descrive come integrare Comm100 Live Chat con Azure Active Directory (Azure AD).

L'integrazione di Comm100 Live Chat con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Comm100 Live Chat.
- È possibile abilitare gli utenti per l'accesso automatico a Comm100 Live Chat (Single Sign-On) con gli account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Comm100 Live Chat, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Comm100 Live Chat abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Comm100 Live Chat dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Aggiunta di Comm100 Live Chat dalla raccolta
Per configurare l'integrazione di Comm100 Live Chat in Azure AD, è necessario aggiungere Comm100 Live Chat dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Comm100 Live Chat dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Comm100 Live Chat**, selezionare **Comm100 Live Chat** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Comm100 Live Chat nell'elenco dei risultati](./media/comm100livechat-tutorial/tutorial_comm100livechat_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Comm100 Live Chat in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Comm100 Live Chat che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Comm100 Live Chat.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Comm100 Live Chat, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Comm100 Live Chat](#create-a-comm100-live-chat-test-user)**: per avere una controparte di Britta Simon in Comm100 Live Chat collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Comm100 Live Chat.

**Per configurare Single Sign-On di Azure AD con Comm100 Live Chat, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Comm100 Live Chat** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/comm100livechat-tutorial/tutorial_comm100livechat_samlbase.png)

3. Nella sezione **URL e dominio Comm100 Live Chat** seguire questa procedura:

    ![Informazioni su URL e dominio per Single Sign-On di Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`
    
    > [!NOTE] 
    > Poiché il valore dell'URL di accesso non è reale, È necessario aggiornarlo con l'URL di accesso effettivo, descritto più avanti nell'esercitazione.

4. L'applicazione Comm100 Live Chat prevede che le asserzioni SAML contengano attributi specifici. Configurare gli attributi seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.

    ![Configure Single Sign-On](./media/comm100livechat-tutorial/tutorial_attribute_03.png)
    
5. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura:
    
    |  Nome attributo  | Valore attributo |
    | --------------- | -------------------- |    
    |   email    | user.mail |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/comm100livechat-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/comm100livechat-tutorial/tutorial_attribute_05.png)
    
    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.
    
    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Lasciare vuota la casella **Spazio dei nomi**.
    
    e. Fare clic su **OK**.

6. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/comm100livechat-tutorial/tutorial_comm100livechat_certificate.png) 

7. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/comm100livechat-tutorial/tutorial_general_400.png)

8. Nella sezione **Configurazione di Comm100 Live Chat** fare clic su **Configura Comm100 Live Chat** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML e l'URL di disconnessione** dalla sezione **Riferimento rapido**.

    ![Configurazione di Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_configure.png)

9. In un'altra finestra del Web browser accedere a Comm100 Live Chat come amministratore della sicurezza.

10. Fare clic su **Account personale** in alto a destra.

    ![Account personale in Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

11. A sinistra nel menu fare clic su **Security** (Sicurezza) e quindi su **Agent Single Sign-On** (Agente Single Sign-On).

    ![Sicurezza in Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

12. Nella pagina **Agent Single Sign-On** (Agente Single Sign-On) seguire questa procedura:

    ![Sicurezza in Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

    a. Copiare il primo collegamento evidenziato e incollarlo nella casella di testo **URL accesso** nella sezione **URL e dominio Comm100 Live Chat** del portale di Azure.

    b. Nella casella di testo **SAML SSO URL** (URL SSO SAML) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

    c. Nella casella di testo **URL disconnessione remota** incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

    d. Fare clic su **Choose a File** (Scegli un file) per caricare il certificato codificato in base 64 scaricato dal portale di Azure in **Certificate** (Certificato).

    e. Fare clic su **Save Changes** (Salva modifiche).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente di test in Azure AD, seguire questa procedura:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/comm100livechat-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/comm100livechat-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/comm100livechat-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/comm100livechat-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-comm100-live-chat-test-user"></a>Creare un utente di test di Comm100 Live Chat

Per consentire agli utenti di Azure AD di accedere a Comm100 Live Chat, è necessario effettuarne il provisioning in Comm100 Live Chat. In Comm100 Live Chat, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a Comm100 Live Chat come amministratore della sicurezza.

2. Fare clic su **Account personale** in alto a destra.

    ![Account personale in Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. A sinistra nel menu fare clic su **Agents** (Agenti) e quindi su **New Agent** (Nuovo agente).

    ![Agente in Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Nella pagina **New Agent** (Nuovo agente) seguire questa procedura:

    ![Nuovo agente in Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. Nella casella di testo **Email** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **Brittasimon@contoso.com**.

    b. Nella casella di testo **First Name** (Nome) immettere il nome dell'utente, ad esempio **Britta**.

    c. Nella casella di testo **Last name** (Cognome) immettere il cognome dell'utente, ad esempio **Simon**.

    d. Nella casella di testo **Display Name** (Nome visualizzato) digitare il nome visualizzato dell'utente, ad esempio **Britta Simon**.

    e. Nella casella di testo **Password** digitare la password.

    f. Fare clic su **Save** (Salva).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Comm100 Live Chat.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Comm100 Live Chat, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Comm100 Live Chat**.

    ![Collegamento di Comm100 Live Chat nell'elenco delle applicazioni](./media/comm100livechat-tutorial/tutorial_comm100livechat_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Comm100 Live Chat nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Comm100 Live Chat.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/comm100livechat-tutorial/tutorial_general_01.png
[2]: ./media/comm100livechat-tutorial/tutorial_general_02.png
[3]: ./media/comm100livechat-tutorial/tutorial_general_03.png
[4]: ./media/comm100livechat-tutorial/tutorial_general_04.png

[100]: ./media/comm100livechat-tutorial/tutorial_general_100.png

[200]: ./media/comm100livechat-tutorial/tutorial_general_200.png
[201]: ./media/comm100livechat-tutorial/tutorial_general_201.png
[202]: ./media/comm100livechat-tutorial/tutorial_general_202.png
[203]: ./media/comm100livechat-tutorial/tutorial_general_203.png

