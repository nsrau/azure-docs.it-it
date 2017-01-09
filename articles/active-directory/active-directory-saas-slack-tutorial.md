---
title: 'Esercitazione: Integrazione di Azure Active Directory con Slack | Microsoft Docs'
description: Informazioni su come usare Slack con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c7a0b761-75b7-4e6b-9980-71d645643a78
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4d5144c6a690c2338dec45b27dcb308328b6fecb
ms.openlocfilehash: 37440a8ba397c4dc227a448dfa574cebd14be49c


---

# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Esercitazione: Integrazione di Azure Active Directory con Slack

Questa esercitazione descrive l'integrazione di Slack con Azure Active Directory (Azure AD).

L'integrazione di Slack con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Slack
- È possibile abilitare gli utenti per l'accesso automatico a Slack (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Slack, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Slack abilitata per l'accesso Single Sign-On


> [!NOTE]
>  Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Slack dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-slack-from-the-gallery"></a>Aggiunta di Slack dalla raccolta
Per configurare l'integrazione di Slack in Azure AD è necessario aggiungere Slack dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Slack dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
    
    ![Applications][2]

4. Fare clic su **Add** nella parte inferiore della pagina.
    
    ![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella di ricerca digitare **Slack**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

7. Nel riquadro dei risultati selezionare **Slack**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Selezione dell'app nella raccolta](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Slack in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Slack che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Slack.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **nome utente** in Slack.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Slack, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Slack](#creating-a-slack-test-user)**: per avere una controparte di Britta Simon in Slack collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione Slack.

L'applicazione Slack prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla scheda degli**attributi**dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione. 

![Configura accesso Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png)

**Per configurare l'accesso Single Sign-On di Azure AD con Slack, seguire questa procedura:**

1. Nel menu visualizzato nella parte superiore della pagina di integrazione dell'applicazione **Slack** del portale di Azure classico fare clic su **Attributi**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

2. Nella finestra di dialogo **Attributi token SAML** seguire questa procedura per ciascuna riga della tabella:

    | Nome attributo | Valore attributo |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    a. Fare clic su **aggiungi attributo utente** per aprire la finestra di dialogo **Aggiungi attributo utente**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)
    
    b. Nella casella di testo **Nome attributo** , digitare il nome dell'attributo indicato per quella riga.
    
    c. Nell'elenco **Valore attributo** digitare il valore dell'attributo indicato per quella riga.
    
    d. Fare clic su **Completa**

3. Nel menu in alto fare clic su **Avvio rapido**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png) 

4. Nella pagina **How would you like users to sign on to Slack** (Stabilire come si desidera che gli utenti accedano a Slack) selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)

5. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura e fare clic su **Avanti**:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<company name>.slack.com`.

    b. Fare clic su **Avanti**.

    > [!NOTE] 
    > È necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere tale valore, contattare il team di supporto di Slack.

6. Nella pagina **Configure single sign-on at Slack** (Configura accesso Single Sign-On in Slack) fare clic su **Scarica certificato** e quindi salvare il file nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png)

7.  In un'altra finestra del browser Web accedere al sito aziendale di Slack come amministratore.

8.  Accedere a **Microsoft Azure AD**, quindi andare su **Impostazioni team**.

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

9.  Nella sezione **Impostazioni team** fare clic sulla scheda **Autenticazione**, quindi fare clic su **Cambia impostazioni**.

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

10. Nella finestra di dialogo **Impostazioni di autenticazione SAML** , eseguire la procedura seguente:

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a.  Nella casella di testo **SAML 2.0 Endpoint (HTTP)** inserire il valore di **URL SSO SAML** dalla configurazione guidata dell'applicazione di Azure AD.

    b.  Nella casella di testo **Identity Provider Issuer** (Autorità di certificazione del provider di identità) inserire il valore di **URL autorità di certificazione** dalla configurazione guidata dell'applicazione di Azure AD.

    c.  Aprire il certificato scaricato nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato pubblico**.

    d.  Deselezionare **Consenti agli utenti di modificare l'indirizzo di posta elettronica**.

    e.  Selezionare **Consenti agli utenti di scegliere il proprio nome utente**.

    f.  In **Authentication for your team must be used by** (L'autenticazione per il team deve essere usata da) selezionare **It’s optional** (Facoltativo).

    g.  Fare clic su **Salva configurazione**.

11. Nel portale di Azure classico selezionare la conferma della configurazione e quindi fare clic su **Avanti**.
    
    ![Single Sign-On di Microsoft Azure AD][10]

12. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
    
    ![Single Sign-On di Microsoft Azure AD][11]



### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_09.png)

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png)

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png)

5. Nella pagina **Informazioni sull'utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_05.png)

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura:
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_06.png)

    a. Nella casella di testo **Nome** digitare **Britta**.  

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_07.png)

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_08.png)

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Completa**.   



### <a name="creating-a-slack-test-user"></a>Creazione di un utente test di Slack

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Slack. Slack supporta il provisioning JIT, abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a Slack verrà creato un nuovo utente, se non esiste ancora.

> [!NOTE] 
> Per creare un utente manualmente, è necessario contattare il team di supporto di Slack.


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

L'obiettivo di questa sezione consiste nell'abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure con la concessione dell'accesso a Slack.
    
![Assegna utente][200]

**Per assegnare Britta Simon a Slack, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
    
    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **Slack**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png)

3. Scegliere **Utenti**dal menu in alto.
    
    ![Assegna utente][203]

4. Nell'elenco di utenti selezionare **Britta Simon**.

5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
    
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.
 
Quando si fa clic sul riquadro Slack nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Slack.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-slack-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-slack-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-slack-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-slack-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-slack-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO4-->


