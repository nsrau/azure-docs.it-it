---
title: 'Esercitazione: Integrazione di Azure Active Directory con Boomi | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: 8e05afa9-2eda-4975-a0cc-6d408065860f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6a60db808388bf1fdf9441518920f2eb4e5fcc4d
ms.openlocfilehash: bafbfb710d8cdb370d21d9299c447a0dfed3c468


---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Esercitazione: Integrazione di Azure Active Directory con Boomi

Questa esercitazione descrive come integrare Boomi con Azure Active Directory (Azure AD).

L'integrazione di Boomi con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Boomi
- È possibile abilitare gli utenti per l'accesso automatico a Boomi (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Boomi, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Boomi abilitata per l'accesso Single Sign-On


> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Boomi dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-boomi-from-the-gallery"></a>Aggiunta di Boomi dalla raccolta
Per configurare l'integrazione di Boomi in Azure AD, è necessario aggiungere Boomi dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Boomi dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applications][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella di ricerca digitare **Boomi**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_01.png)

7. Nel riquadro dei risultati selezionare **Boomi** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Boomi in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Boomi che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Boomi.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** in Boomi.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Boomi, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Boomi](#creating-a-boomi-test-user)**: per avere una controparte di Britta Simon in Boomi collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione Boomi.

L'applicazione Boomi si aspetta un formato specifico per l'asserzione SAML ed è richiesta l'impostazione del valore dell'attributo NameIdentifier con l'ID federazione dell'utente. Per impostazione predefinita, Azure AD usa UserPrincipalName per l'attributo NameIdentifier. Per la corretta integrazione è però necessario modificare questo valore in modo che corrisponda all'ID federazione dell'utente in Boomi. È possibile modificarlo dalla scheda **Attributo** come illustrato nella schermata seguente. L'integrazione funzionerà solo dopo aver completato correttamente il mapping.

![Configura accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_51.png)

**Per configurare l'accesso Single Sign-On di Azure AD con Boomi, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Boomi** del portale classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On][6] 

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Boomi** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_03.png)

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_04.png)

    a. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://platform.boomi.com/sso/<account name>/saml`

    b. Fare clic su **Avanti**.

    > [!NOTE] 
    > Si noti che questo non è il valore reale. È necessario aggiornare questo valore con l'URL di risposta effettivo. Per ottenere tale valore, contattare il team di supporto di Boomi.

4. Nella pagina **Configura accesso Single Sign-On in Boomi** fare clic su **Download certificato** e quindi salvare il file nel computer:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_05.png) 

    > [!NOTE]
    > Si noti che il valore attestazione NameID nella risposta deve corrispondere all'ID federazione configurato nel sistema Boomi. È quindi necessario collaborare con il team di supporto di Boomi per mappare l'identificatore utente appropriato all'interno dell'organizzazione come ID federazione. Per impostazione predefinita Azure AD verranno imposterà NameIdentifier come valore UPN. È possibile modificarlo dalla scheda Attributo come illustrato nella schermata seguente. L'integrazione funzionerà solo dopo aver completato correttamente il mapping. 
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_51.png)

5. In un'altra finestra del Web browser accedere al sito aziendale di Boomi come amministratore. 

6. Passare a **Nome società** e scegliere **Configurazione**.

7. Fare clic sulla scheda **SSO options** e seguire questa procedura.

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_06.png)

    a. Selezionare la casella di controllo **Abilita Single Sign-On SAML**.

    b. Fare clic su **Importa** per caricare il certificato scaricato da Azure AD in **Identity Provider Certificate** (Certificato del Provider di identità).
    
    c. Nella casella di testo **URL di accesso provider di identità** inserire il valore di **URL accesso remoto** dalla configurazione guidata dell'applicazione di Azure AD.

    d. Come **Federation Id Location** selezionare il pulsante di opzione **Federation Id is in NameID element of the Subject**. 

    e. Fare clic sul pulsante **Salva** .

8. Nel portale di Azure classico selezionare la conferma della configurazione e fare clic su **Avanti**.

    ![Single Sign-On di Microsoft Azure AD][10]

9. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
  
    ![Single Sign-On di Microsoft Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_09.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_04.png) 

5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_05.png) 

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_06.png) 

    a. Nella casella di testo **Nome** digitare **Britta**.  

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_07.png) 

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_08.png) 

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Completa**.   



### <a name="creating-a-boomi-test-user"></a>Creazione di un utente test di Boomi

Per consentire agli utenti di Azure AD di accedere a Boomi, è necessario eseguirne il provisioning in Boomi. Nel caso di Boomi, il provisioning è un'attività manuale.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, seguire questa procedura:

1. Accedere al sito aziendale di Boomi come amministratore.

2. Dopo aver effettuato l'accesso, passare a **Gestione utenti** e scegliere **Utenti**.

    ![Utenti](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_001.png "Users")

3. Fare clic sull'icona **+**. Si aprirà la finestra di dialogo **Add/Maintain User Roles** (Aggiungi/Gestisci ruoli utente).

    ![Utenti](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_002.png "Users")

    ![Utenti](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_003.png "Users")

4. Immettere **Indirizzo posta elettronica utente** dell'utente.

5. Immettere **Nome** e **Cognome** dell'utente.

6. Immettere l'**ID federazione** dell'utente. Ogni utente deve avere un ID federazione che lo identifichi in modo univoco all'interno dell'account. 

7. Assegnare il ruolo **Utente standard** all'utente. Non assegnare il ruolo Amministratore perché altrimenti l'utente avrebbe l'accesso normale ad AtomSphere, nonché l'accesso Single Sign-on.

8. Fare clic su **OK**.

    > [!NOTE]
    > L'utente non riceverà un messaggio di notifica di benvenuto contenente una password che può essere utilizzata per accedere all'account di AtomSphere perché la password verrà gestita tramite il provider di identità. È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Boomi per eseguire il provisioning degli account utente di AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Boomi.

![Assegna utente][200] 

**Per assegnare Britta Simon a Boomi, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Boomi**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_50.png) 

3. Scegliere **Utenti**dal menu in alto.

    ![Assegna utente][203] 

4. Nell'elenco di utenti selezionare **Britta Simon**.

5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
    
    ![Assegna utente][205]



### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Boomi nel pannello di accesso, si accederà automaticamente all'applicazione Boomi.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


