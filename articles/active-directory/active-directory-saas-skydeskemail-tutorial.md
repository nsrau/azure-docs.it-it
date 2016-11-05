---
title: 'Esercitazione: Integrazione di Azure Active Directory con Skydesk Email | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Skydesk Email.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2016
ms.author: jeedes

---
# Esercitazione: Integrazione di Azure Active Directory con Skydesk Email
Questa esercitazione descrive l'integrazione di Skydesk Email con Azure Active Directory (Azure AD).

L'integrazione di Skydesk Email con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Skydesk Email
* È possibile abilitare gli utenti perché possano accedere automaticamente, ossia tramite accesso Single Sign-On, a Skydesk Email con i propri account Azure AD
* È possibile gestire gli account in una posizione centrale, il portale di Azure Active Directory classico.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti
Per configurare l'integrazione di Azure AD con Skydesk Email, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Skydesk Email abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Skydesk Email dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## Aggiunta di Skydesk Email dalla raccolta
Per configurare l'integrazione di Skydesk Email in Azure AD, è necessario aggiungere Skydesk Email dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Skydesk Email dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Skydesk Email**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_01.png)
7. Nel riquadro dei risultati selezionare **Skydesk Email** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_02.png)

## Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Skydesk Email usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Skydesk Email che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Skydesk Email.

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **nome utente** in Skydesk Email.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Skydesk Email, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Skydesk Email](#creating-a-Skydesk-Email-test-user)**: per avere una controparte di Britta Simon in Skydesk Email che sia collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione Skydesk Email.

**Per configurare l'accesso Single Sign-On di Azure AD con Skydesk Email, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Skydesk Email** nel portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6]
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Skydesk Email** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_03.png)
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_04.png)

    a. Nella casella di testo URL di accesso digitare l'URL usato dagli utenti per accedere all'applicazione Skydesk Email usando il modello seguente: **"https://mail.skydesk.jp/portal/<nome società>"**.

    b. Fare clic su **Avanti**.


1. Nella pagina **Configura accesso Single Sign-On in Skydesk Email** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_05.png)
   
    a. Fare clic su **Scarica certificato** e quindi salvare il file nel computer.
   
    b. Fare clic su **Avanti**.
2. Per abilitare l'accesso Single Sign-On in **Skydesk Email**, seguire questa procedura:
   
    a. Accedere al proprio account Skydesk Email come amministratore.
   
    b. Nel menu in alto fare clic su Setup e quindi fare clic selezionare Org.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_51.png)
   
    c. Nel pannello sinistro selezionare Domains.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_53.png)
   
    d. Fare clic su Add Domain.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_54.png)
   
    e. Immettere il nome di dominio e quindi verificarlo.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_55.png)
   
    f. Nel pannello sinistro fare clic su **SAML Authentication**
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_52.png)
3. Nella pagina della finestra di dialogo **SAML Authentication** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_56.png)
   
   > [!NOTE]
   > Per usare l'autenticazione basata su SAML, è necessario che il **dominio sia verificato** e l'**URL del portale** sia impostato. È possibile impostare l'URL del portale usando un nome univoco.
   > 
   > 
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. Nel portale di Azure AD classico copiare il valore di **URL SSO SAML** e quindi incollarlo nella casella di testo **URL di accesso**.

    b. Nel portale di Azure AD classico copiare il valore di **URL servizio Single Sign-On** e quindi incollarlo nella casella di testo **URL disconnessione**.

    c. **Modifica URL password** è facoltativo e può essere lasciato vuoto.

    d. Fare clic su **Get Key From File** per selezionare il certificato Skydesk Email scaricato e quindi fare clic su **Open** per caricare il certificato.

    e. In **Algorithm** selezionare **RSA**.

    f. Fare clic su **Ok** per salvare le modifiche.


1. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
   
    ![Accesso Single Sign-On di Azure AD][10]
2. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
   
    ![Accesso Single Sign-On di Azure AD][11]

### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_09.png)
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_03.png)
4. Per aprire la finestra di dialogo **Aggiungi utente**, nella barra degli strumenti in basso fare clic su **Aggiungi utente**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_04.png)
5. Nella pagina della finestra di dialogo **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_05.png)
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_06.png)
   
   a. Nella casella di testo **Nome** digitare **Britta**.
   
   b. Nella casella di testo **Cognome** digitare **Simon**.
   
   c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   d. Nell'elenco **Ruolo**, selezionare **Utente**.
   
   e. Fare clic su **Avanti**.
7. Nella pagina della finestra di dialogo **Ottieni password temporanea**, fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_07.png)
8. Nella pagina della finestra di dialogo **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_08.png)
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.

### Creazione di un utente test di Skydesk Email
In questa sezione viene creato un utente di nome Britta Simon in Skydesk Email.

a. Nel pannello sinistro di Skydesk Email selezionare **User Access** e quindi immettere il nome utente.

![Configura accesso Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_58.png)

[AZURE.NOTE] Per creare utenti in blocco, è necessario contattare il team di supporto tecnico di Skydesk Email.

### Assegnazione dell'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure, concedendole così l'accesso a Skydesk Email.

![Assegna utente][200]

**Per assegnare Britta Simon a Skydesk Email, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201]
2. Nell'elenco delle applicazioni selezionare **Skydesk Email**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_50.png)
3. Scegliere **Utenti** dal menu in alto.
   
    ![Assegna utente][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna** nella barra degli strumenti in basso.
   
    ![Assegna utente][205]

### Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Skydesk Email nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Skydesk Email.

## Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->