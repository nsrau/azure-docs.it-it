---
title: 'Esercitazione: Integrazione di Azure Active Directory con OfficeSpace Software | Microsoft Docs'
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
ms.date: 03/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: fb61a2d622b0d829a134b6ce5dfef6e9fb44fa1e
ms.lasthandoff: 03/22/2017


---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Esercitazione: Integrazione di Azure Active Directory con OfficeSpace Software

Questa esercitazione descrive come integrare OfficeSpace Software con Azure Active Directory (Azure AD).

L'integrazione di OfficeSpace Software con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a OfficeSpace Software
- È possibile abilitare gli utenti per l'accesso automatico a OfficeSpace Software (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con OfficeSpace Software, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di OfficeSpace Software abilitata per l'accesso Single Sign-On


> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di OfficeSpace Software dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-officespace-software-from-the-gallery"></a>Aggiunta di OfficeSpace Software dalla raccolta
Per configurare l'integrazione di OfficeSpace Software in Azure AD, è necessario aggiungere OfficeSpace Software dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere OfficeSpace Software dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **OfficeSpace Software**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_001.png)

5. Nel pannello dei risultati selezionare **OfficeSpace Software** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

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

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione OfficeSpace Software.

**Per configurare l'accesso Single Sign-On di Azure AD con OfficeSpace Software, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **OfficeSpace Software** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_01.png)

3. Nella sezione **URL e dominio OfficeSpace Software** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_02.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. Nella casella di testo **Identificatore** digitare il valore adottando il modello seguente: `<company name>.officespacesoftware.com`

    > [!NOTE] 
    > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto di OfficeSpace Software](mailto:support@officespacesoftware.com). 

4. L'applicazione OfficeSpace Software si aspetta che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_03.png)

5. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** selezionare **user.mail** come **Identificatore utente**. Per ogni riga illustrata nella tabella seguente, seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | --- | --- |    
    | email | user.mail |
    | name | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_04.png)

    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_05.png)
    
    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.
    
    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **Ok**

6. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_08.png) 

7. Fare clic su **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_general_400.png)

8. Nella sezione **Configurazione di OfficeSpace Software** fare clic su **Configura OfficeSpace Software** per aprire la finestra **Configura accesso**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_09.png) 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_10.png)

9. In un'altra finestra del browser Web accedere al tenant di OfficeSpace Software come amministratore.

10. Passare a **Impostazioni** e fare clic su **Connettori**.

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_002.png)

11. Fare clic su **Autenticazione SAML**.

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_003.png)

12. Nella sezione **SAML Authentication** seguire questa procedura:

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_004.png)

    a. Nella casella di testo **Logout provider URL** (URL provider di disconnessione) inserire il valore di **Sign-Out URL** (URL di disconnessione) dalla finestra di configurazione dell'applicazione di Azure AD.

    b. Nella casella di testo **Client IDP target url** (URL destinazione IDP client) inserire il valore di **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) dalla finestra di configurazione dell'applicazione di Azure AD.

    c. Copiare il valore di **Identificazione personale** dal certificato scaricato e incollarlo nella casella di testo **Client idp cert fingerprint** (Impronta digitale del certificato IDP client). 

    d. Fare clic su **Salva impostazioni**.

    > [!NOTE]
    > Per informazioni dettagliate, vedere [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI) 
  

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**. 



### <a name="creating-an-officespace-software-test-user"></a>Creazione di un utente test di OfficeSpace Software

Questa sezione descrive come creare un utente di nome Britta Simon in OfficeSpace Software. OfficeSpace Software supporta il provisioning just-in-time, che è abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a OfficeSpace Software verrà creato un nuovo utente, se non esiste già.

> [!NOTE]
> Per creare un utente manualmente, è necessario contattare il [team di supporto di OfficeSpace Software](mailto:support@officespacesoftware.com).


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure, concedendole l'accesso a OfficeSpace Software.

![Assegna utente][200] 

**Per assegnare Britta Simon a OfficeSpace Software, seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione applicazioni, passare alla visualizzazione directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **OfficeSpace Software**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_50.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    


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

[100]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_203.png
