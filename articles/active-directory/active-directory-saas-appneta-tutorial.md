---
title: 'Esercitazione: Integrazione di Azure Active Directory con AppNeta Performance Monitor | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e AppNeta Performance Monitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 643a45fb-d6fc-4b32-b721-68899f8c7d44
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: jeedes
ms.openlocfilehash: 6c06b2819516664a367ca31925366eabd05e688e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-azure-active-directory-integration-with-appneta-performance-monitor"></a>Esercitazione: Integrazione di Azure Active Directory con AppNeta Performance Monitor

Questa esercitazione descrive come integrare AppNeta Performance Monitor con Azure Active Directory (Azure AD).

L'integrazione di AppNeta Performance Monitor con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad AppNeta Performance Monitor.
- È possibile abilitare gli utenti per l'accesso automatico a AppNeta Performance Monitor (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con AppNeta Performance Monitor, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di AppNeta Performance Monitor abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di AppNeta Performance Monitor dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>Aggiunta di AppNeta Performance Monitor dalla raccolta
Per configurare l'integrazione di AppNeta Performance Monitor in Azure AD, è necessario aggiungere AppNeta Performance Monitor dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere AppNeta Performance Monitor dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **AppNeta Performance Monitor**, selezionare **AppNeta Performance Monitor** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![AppNeta Performance Monitor nell'elenco risultati](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con AppNeta Performance Monitor con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di AppNeta Performance Monitor che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in AppNeta Performance Monitor.

Per configurare e testare l'accesso Single Sign-On di Azure AD con AppNeta Performance Monitor, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di AppNeta Performance Monitor](#create-an-appneta-performance-monitor-test-user)**: per avere una controparte di Britta Simon in AppNeta Performance Monitor collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione AppNeta Performance Monitor.

**Per configurare l'accesso Single Sign-On di Azure AD con AppNeta Performance Monitor, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **AppNeta Performance Monitor** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_samlbase.png)

3. Nella sezione **URL e dominio AppNeta Performance Monitor** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di AppNeta Performance Monitor](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.pm.appneta.com`

    b. Nella casella di testo **Identificatore** digitare il valore: `PingConnect`

    > [!NOTE] 
    > Poiché il valore dell'URL di accesso non è reale, è necessario aggiornare questo valore con l'URL di accesso Sign-On effettivo. Contattare il [team di supporto del client AppNeta Performance Monitor](mailto:support@appneta.com) per ottenere questo valore. 

5. L'applicazione AppNeta Performance Monitor prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione di Attributi token SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione.

    ![Configure Single Sign-On](./media/active-directory-saas-appneta-tutorial/attribute.png)

6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente ed eseguire i passaggi descritti di seguito:
           
    | Nome attributo | Valore attributo |
    | ---------------| ----------------|
    | firstName| user.givenname|
    | lastName| user.surname|
    | email| user.userprincipalname|
    | name| user.userprincipalname|
    | groups   | user.assignedroles |
    | phone| user.telephonenumber |
    | title| user. jobtitle|

    > [!NOTE]
    > "groups" fa riferimento al gruppo di sicurezza in AppNeta di cui viene eseguito il mapping a un "ruolo" in Azure AD. Vedere [questo](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) documento che illustra come creare ruoli personalizzati in Azure AD.
        
    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/active-directory-saas-appneta-tutorial/tutorial_attribute_04.png)
    
    ![Configure Single Sign-On](./media/active-directory-saas-appneta-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Lasciare vuota la casella relativa allo spazio dei nomi.
    
    e. Fare clic su **OK**.  

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-appneta-tutorial/tutorial_general_400.png)

6. Per configurare l'accesso Single Sign-On sul lato **AppNeta Performance Monitor**, è necessario inviare il file **XML metadati** scaricato al [team di supporto di AppNeta Performance Monitor](mailto:support@appneta.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-appneta-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-appneta-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-appneta-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-appneta-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-an-appneta-performance-monitor-test-user"></a>Creare un utente di test di AppNeta Performance Monitor

Questa sezione descrive come creare un utente chiamato Britta Simon in AppNeta Performance Monitor. AppNeta Performance Monitor supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso ad AppNeta Performance Monitor viene creato un nuovo utente, se questo non esiste già.
>[!Note]
>Se è necessario creare un utente manualmente, contattare il [team di supporto di AppNeta Performance Monitor](mailto:support@appneta.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso ad AppNeta Performance Monitor.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon ad AppNeta Performance Monitor, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **AppNeta Performance Monitor**.

    ![Collegamento AppNeta Performance Monitor nell'elenco delle applicazioni](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro AppNeta Performance Monitor nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione AppNeta Performance Monitor.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_203.png

