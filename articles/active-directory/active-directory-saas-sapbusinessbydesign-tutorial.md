---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP Business ByDesign | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP Business ByDesign.
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
ms.date: 09/09/2016
ms.author: jeedes

---
# Esercitazione: Integrazione di Azure Active Directory con SAP Business ByDesign
Questa esercitazione descrive come integrare SAP Business ByDesign con Azure Active Directory (Azure AD).

L'integrazione di SAP Business ByDesign con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a SAP Business ByDesign
* È possibile abilitare gli utenti per l'accesso automatico a SAP Business ByDesign (Single Sign-On) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti
Per configurare l'integrazione di Azure AD con SAP Business ByDesign, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di SAP Business ByDesign abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di SAP Business ByDesign dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## Aggiunta di SAP Business ByDesign dalla raccolta
Per configurare l'integrazione di SAP Business ByDesign in Azure AD, è necessario aggiungere SAP Business ByDesign dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SAP Business ByDesign dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **SAP Business ByDesign**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_01.png)
7. Nel riquadro dei risultati selezionare **SAP Business ByDesign** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_02.png)

## Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SAP Business ByDesign con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di SAP Business ByDesign che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP Business ByDesign.

La relazione di collegamento viene stabilita assegnando al valore di **Nome utente** in Azure AD lo stesso valore di **Username** in SAP Business ByDesign.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SAP Business ByDesign, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di SAP Business ByDesign](#creating-an-sap-business-bydesign-test-user)**: per avere una controparte di Britta Simon in SAP Business ByDesign collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD
In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione SAP Business ByDesign.

L'applicazione SAP Business ByDesign prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla scheda **Attribute (Attributo)** dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.

**Per configurare l'accesso Single Sign-On di Azure AD con SAP Business ByDesign, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SAP Business ByDesign** del portale di Azure classico fare clic su **Attributi** nel menu in alto.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_80.png)
2. Nell'elenco degli attributi del token SAML selezionare l'attributo nameidentifier e quindi fare clic su **Modifica**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_84.png)
3. Nella finestra di dialogo Modifica attributo utente seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_85.png)
   
    a. Dall'elenco Valori attributo selezionare la funzione **ExtractMailPrefix()**.
   
    b. Nell'elenco Posta selezionare l'attributo utente da usare per l'implementazione. Ad esempio, se si vuole usare il valore EmployeeID come identificatore utente univoco e il valore dell'attributo è stato archiviato in ExtensionAttribute2, selezionare **user.extensionattribute2**.
   
    c. Fare clic su **Complete**.
4. Nella pagina di integrazione dell'applicazione **SAP Business ByDesign** nel portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6]
5. Nella pagina **Stabilire come si desidera che gli utenti accedano a SAP Business ByDesign**, selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_03.png)
6. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_04.png)
   
    a. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione SAP Business ByDesign usando il modello seguente: `https://<servername>.sapbydesign.com`
   
    b. Fare clic su **Avanti**
7. Nella pagina **Configura accesso Single Sign-On in SAP Business ByDesign** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_05.png)
   
    a. Fare clic su **Scarica metadati** e quindi salvare il file nel computer.
   
    b. Fare clic su **Avanti**.
8. Per ottenere l'accesso SSO configurato per l'applicazione, seguire questa procedura:
   
    a. Accedere al portale di SAP Business ByDesign con diritti di amministratore.
   
    b. Passare a **Management Common Task** (Attività comuni di gestione utenti e applicazioni) e fare clic sulla scheda **Identity Provider** (Provider di identità).
   
    c. Fare clic su **New Identity Provider** (Nuovo provider di identità) e selezionare il file XML dei metadati scaricato dal portale di Azure classico. Quando si importano i metadati, il sistema carica automaticamente il certificato di firma e il certificato di crittografia necessari.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)
   
    d. Per includere l'**URL del servizio consumer di asserzione** nella richiesta SAML, selezionare **Include Assertion Consumer Service URL** (Includi URL del servizio Consumer di asserzione).
   
    e. Fare clic su **Activate Single Sign-On** (Attiva Single Sign-On).
   
    f. Salvare le modifiche.
   
    g. Fare clic sulla scheda **My System** (Sistema locale).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)
   
    h. Copiare il valore di **SSO URL** (URL SSO) e incollarlo nella casella di testo **Azure AD Sign On URL** (URL di accesso Azure AD).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)
   
    i. Specificare se il dipendente può scegliere manualmente tra l'accesso con ID utente e password o SSO selezionando **Manual Identity Provider Selection** (Selezione manuale provider di identità).
   
    j. Nella sezione**SSO URL** (URL SSO), specificare l'URL che deve essere usato dai dipendenti per l'accesso al sistema. Nell'elenco a discesa URL Sent to Employee (URL inviato al dipendente) è possibile scegliere tra le opzioni seguenti:
   
    **Non-SSO URL** (URL non SSO)
   
    Il sistema invia al dipendente solo il normale URL di sistema. Il dipendente non può accedere con SSO e deve usare invece la password o il certificato.
   
    **SSO URL** (URL SSO)
   
    Il sistema invia al dipendente solo l'URL SSO. Il dipendente può accedere con SSO. La richiesta di autenticazione viene reindirizzata tramite IdP.
   
    **Automatic Selection** (Selezione automatica)
   
    Se SSO non è attivo, il sistema invia al dipendente il normale URL di sistema. Se SSO è attivo, il sistema verifica se il dipendente ha una password. Se è disponibile una password, vengono inviati al dipendente sia l'URL SSO che l'URL non SSO. Tuttavia, se il dipendente non ha una password, riceverà solo l'URL SSO.
   
    k. Salvare le modifiche.
9. Nel portale classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
   
    ![Accesso Single Sign-On di Azure AD][10]
10. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
    
    ![Accesso Single Sign-On di Azure AD][11]

### Creazione di un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_09.png)
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_03.png)
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_04.png)
5. Nella pagina della finestra di dialogo **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_05.png)
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Next**.
6. Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_06.png)
   
   a. Nella casella di testo **Nome** digitare **Britta**.
   
   b. Nella casella di testo **Cognome** digitare **Simon**.
   
   c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   d. Nell'elenco **Ruolo**, selezionare **Utente**.
   
   e. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_07.png)
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_08.png)
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Completa**.

### Creazione di un utente test SAP Business ByDesign
In questa sezione viene creato un utente di nome Britta Simon in SAP Business ByDesign. Per aggiungere gli utenti nella piattaforma SAP Business ByDesign, contattare il team di supporto di SAP Business ByDesign.

> [!NOTE]
> Assicurarsi che il valore di NameID corrisponda al campo username nella piattaforma SAP Business ByDesign.
> 
> 

### Assegnazione dell'utente test di Azure AD
In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SAP Business ByDesign.

![Assegna utente][200]

**Per assegnare Britta Simon a SAP Business ByDesign, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione Directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201]
2. Nell'elenco delle applicazioni, selezionare **SAP Business ByDesign**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_50.png)
3. Scegliere **Utenti** dal menu in alto.
   
    ![Assegna utente][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna** sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### Test dell'accesso Single Sign-On
In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro SAP Business ByDesign nel pannello di accesso, si accederà automaticamente all'applicazione SAP Business ByDesign.

## Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0914_2016-->