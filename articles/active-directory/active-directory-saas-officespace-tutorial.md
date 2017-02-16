---
title: 'Esercitazione: Integrazione di Azure Active Directory con OfficeSpace Software | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e OfficeSpace Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 01e2bcf3fa32546a7952ddc919f99b46a74755a2
ms.openlocfilehash: e0933b1a7e19fc70f9c5e81225b296412837385e


---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Esercitazione: Integrazione di Azure Active Directory con OfficeSpace Software

Questa esercitazione descrive come integrare OfficeSpace Software con Azure Active Directory (Azure AD).

L'integrazione di OfficeSpace Software con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a OfficeSpace Software
- È possibile abilitare gli utenti per l'accesso automatico a OfficeSpace Software (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con OfficeSpace Software, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di OfficeSpace Software abilitata per l'accesso Single Sign-On


> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di OfficeSpace Software dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-officespace-software-from-the-gallery"></a>Aggiunta di OfficeSpace Software dalla raccolta
Per configurare l'integrazione di OfficeSpace Software in Azure AD, è necessario aggiungere OfficeSpace Software dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere OfficeSpace Software dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applications][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella di ricerca digitare **OfficeSpace Software**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_001.png)

7. Nel riquadro dei risultati selezionare **OfficeSpace Software**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con OfficeSpace Software usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di OfficeSpace Software che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in OfficeSpace Software.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in OfficeSpace Software.

Per configurare e testare l'accesso Single Sign-On di Azure AD con OfficeSpace Software, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di OfficeSpace Software ](#creating-an-officespace-software-test-user)**: per avere una controparte di Britta Simon in OfficeSpace Software collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e come configurare l'accesso Single Sign-On nell'applicazione OfficeSpace Software.

L'applicazione OfficeSpace Software si aspetta che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla scheda degli**attributi**dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione. 

![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_01.png)

**Per configurare l'accesso Single Sign-On di Azure AD con OfficeSpace Software, seguire questa procedura:**

1. Nel menu visualizzato nella parte superiore della pagina di integrazione dell'applicazione **OfficeSpace Software** del portale di Azure classico fare clic su **Attributi**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_02.png)

2. Nella finestra di dialogo **Attributi token SAML** seguire questa procedura per ciascuna riga della tabella:
    
    | Nome attributo | Valore attributo |
    | --- | --- |    
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier | user.mail |
    | email | user.mail |
    | name | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

    a. Fare clic su **aggiungi attributo utente** per aprire la finestra di dialogo **Aggiungi attributo utente**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_03.png)
    
    b. Nella casella di testo **Nome attributo** , digitare il nome dell'attributo indicato per quella riga.
    
    c. Nell'elenco **Valore attributo** digitare il valore dell'attributo indicato per quella riga.
    
    d. Fare clic su **Completa**

3. Nel menu in alto fare clic su **Avvio rapido**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_04.png) 

4. Nella pagina di integrazione dell'applicazione **OfficeSpace Software** del portale classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_05.png)

5. Nella pagina **Stabilire come si desidera che gli utenti accedano a OfficeSpace Software** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_06.png)

6. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_07.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<company name>.officespace.com/users/sign_in/saml`

    b. Fare clic su **Avanti**.

    > [!NOTE] 
    > Si noti che questo non è il valore reale. È necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere questo valore, contattare il [team di supporto di OfficeSpace Software](emaiLto:support@officespacesoftware.com).

7. Nella pagina **Configura accesso Single Sign-On in OfficeSpace Software** fare clic su **Scarica certificato** e salvare il file nel computer:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_08.png) 

8. In un'altra finestra del browser Web accedere al tenant di OfficeSpace Software come amministratore.

9. Passare a **ADMIN** e fare clic su **Connettori**.

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_002.png)

10. Fare clic su **Autorizzazione SAML**.

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_003.png)

11. Nella sezione **Autorizzazione SAML** eseguire la procedura seguente:

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_004.png)

    a. Nella casella di testo **Logout provider url** (URL provider disconnessione) inserire il valore di **URL accesso remoto** dalla configurazione guidata dell'applicazione Azure AD.

    b. Nella casella di testo **Client idp target url** (URL destinazione IDP client) inserire il valore di **URL accesso remoto** dalla configurazione guidata dell'applicazione Azure AD.

    c. Copiare il valore di **Identificazione personale** dal certificato scaricato e incollarlo nella casella di testo **Client idp cert fingerprint** (Impronta digitale del certificato IDP client). 

    d. Fare clic su **Salva impostazioni**.

    > [!NOTE]
    > Per informazioni dettagliate, vedere [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

12. Nel portale classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.

    ![Single Sign-On di Microsoft Azure AD][10]

13. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
  
    ![Single Sign-On di Microsoft Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_09.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_04.png) 

5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_05.png) 

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_06.png) 

    a. Nella casella di testo **Nome** digitare **Britta**.  

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_07.png) 

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_08.png) 

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Completa**.   



### <a name="creating-an-officespace-software-test-user"></a>Creazione di un utente test di OfficeSpace Software

Questa sezione descrive come creare un utente di nome Britta Simon in OfficeSpace Software. OfficeSpace Software supporta il provisioning just-in-time, che è abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a OfficeSpace Software verrà creato un nuovo utente, se non esiste già.

> [!NOTE]
> Per creare un utente manualmente, è necessario contattare il [team di supporto di OfficeSpace Software](emaiLto:support@officespacesoftware.com).


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure, concedendole l'accesso a OfficeSpace Software.

![Assegna utente][200] 

**Per assegnare Britta Simon a OfficeSpace Software, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **OfficeSpace Software**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_50.png) 

3. Scegliere **Utenti**dal menu in alto.

    ![Assegna utente][203] 

4. Nell'elenco di utenti selezionare **Britta Simon**.

5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
    
    ![Assegna utente][205]



### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro OfficeSpace Software nel pannello di accesso, si accederà automaticamente all'applicazione OfficeSpace Software.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


