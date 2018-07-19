---
title: 'Esercitazione: Integrazione di Azure Active Directory con Teamphoria| Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Teamphoria.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: jeedes
ms.openlocfilehash: 794945caeea113dc6f1cc2ab5e11a76c3e88c83e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041909"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Esercitazione: Integrazione di Azure Active Directory con Teamphoria

Questa esercitazione illustra come integrare Teamphoria con Azure Active Directory (Azure AD).

L'integrazione di Teamphoria con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Teamphoria
- È possibile abilitare gli utenti per l'accesso automatico a Teamphoria (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Teamphoria, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Teamphoria abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.
Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Teamphoria dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-teamphoria-from-the-gallery"></a>Aggiungere Teamphoria dalla raccolta
Per configurare l'integrazione di Teamphoria in Azure AD, è necessario aggiungere Teamphoria dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Teamphoria dalla raccolta, seguire questa procedura:**

1. Nel **[Portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![APPLICAZIONI][3]

4. Digitare **Teamphoria** nella casella di ricerca.

    ![Creazione di un utente test di Azure AD](./media/teamphoria-tutorial/tutorial_teamphoria_search.png)

5. Nel pannello dei risultati selezionare **Teamphoria** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Teamphoria usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Teamphoria che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Teamphoria.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Teamphoria, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Teamphoria](#creating-a-teamphoria-test-user)** : per avere una controparte di Britta Simon in Teamphoria collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Teamphoria.

**Per configurare l'accesso Single Sign-On di Azure AD con Teamphoria, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Teamphoria** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

3. Nella sezione **URL e dominio Teamphoria** seguire questa procedura:

    ![Configure Single Sign-On](./media/teamphoria-tutorial/tutorial_teamphoria_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<sub-domain>.teamphoria.com/login`   

    > [!NOTE] 
    > Poiché il valore dell'URL di accesso non è reale, È necessario aggiornare questo valore con l'URL di accesso effettivo. Contattare il [team di supporto clienti di Teamphoria](https://www.teamphoria.com/) per richiedere l'URL di accesso.

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il certificato nel computer.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/tutorial_teamphoria_certificate.png)

5. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/teamphoria-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Teamphoria** fare clic su **Configura Teamphoria** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configure Single Sign-On](./media/teamphoria-tutorial/tutorial_teamphoria_configure.png)

7. Per configurare l'accesso Single Sign-On sul lato **Teamphoria**, accedere all'applicazione Teamphoria come amministratore.

8. Passare all'opzione **ADMIN SETTINGS** (Impostazioni di amministrazione) nella barra degli strumenti di sinistra e scegliere **SINGLE SIGN-ON** nella scheda di configurazione per aprire la finestra di configurazione SSO.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/admin_sso_configure.png)

9. Fare clic sull'opzione **ADD NEW IDENTITY PROVIDER** (Aggiungi nuovo provider di identità) nell'angolo superiore destro per aprire il modulo e aggiungere le impostazioni per l'accesso Single Sign-On.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/add_new_identity_provider.png)

10. Immettere i dettagli nei campi come descritto di seguito.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **NOME VISUALIZZATO**: immettere il nome del plug-in visualizzato nella pagina di amministrazione.

    b. **NOME PULSANTE**: il nome della scheda che verrà visualizzata nella pagina di accesso per l'accesso Single Sign-On.

    c. **CERTIFICATO**: aprire il certificato scaricato in precedenza dal portale di Azure nel Blocco note, copiare il contenuto dello stesso e incollarlo qui nella casella.

    d. **PUNTO DI INGRESSO**: incollare l'**URL del servizio Single Sign-On SAML** copiato in precedenza dal portale di Azure.

    e. Posizionare il selettore dell'opzione su **ON** e fare clic su **SAVE** (Salva).

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/teamphoria-tutorial/create_aaduser_01.png) 

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Creazione di un utente test di Azure AD](./media/teamphoria-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/teamphoria-tutorial/create_aaduser_03.png)

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/teamphoria-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.

### <a name="creating-a-teamphoria-test-user"></a>Creare un utente test di Teamphoria

Per consentire agli utenti di Azure AD di accedere a Teamphoria, è necessario eseguirne il provisioning in Teamphoria. Nel caso di Teamphoria, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Teamphoria come amministratore.

2. Fare clic sulle impostazioni **ADMIN** nella barra degli strumenti di sinistra e scegliere **USERS** (Utenti) nella scheda **MANAGE** (Gestisci) per aprire la pagina di amministrazione per gli utenti.

    ![Aggiungere un dipendente](./media/teamphoria-tutorial/admin_manage_users.png)

3. Fare clic sull'opzione **MANUAL INVITE** (Invito manuale).

    ![Invita persone](./media/teamphoria-tutorial/admin_manage_add_users.png)

4. In questa pagina effettuare l'operazione seguente.
    
    ![Invita persone](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Nella casella di testo **EMAIL ADDRESS** (Indirizzo email) immettere l'**indirizzo email** di Britta Simon.

    b. Nella casella di testo **FIRST NAME** (Nome) digitare **Britta**.

    c. Nella casella di testo **LAST NAME** (Cognome) digitare **Simon**.

    d. Fare clic su **INVITE 1 USER** (Invita 1 utente). L'utente deve accettare l'invito per essere creato nel sistema.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione viene concesso a Britta Simon l'accesso a Teamphoria per consentirle di usare l'accesso Single Sign-On di Azure.

![Assegna utente][200]

**Per assegnare Britta Simon a Teamphoria, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **Teamphoria**.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/tutorial_teamphoria_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Se si desidera testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/teamphoria-tutorial/tutorial_general_203.png
