---
title: 'Esercitazione: Integrazione di Azure Active Directory con Lesson.ly | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Lesson.ly.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 8c9dc6e6-5d85-4553-8a35-c7137064b928
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a14be44b66c5e61350785ddfaf5b96125a196103
ms.openlocfilehash: 242cc7d58acb4af66d7ea5da4502db4ce2b414c6
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-lessonly"></a>Esercitazione: Integrazione di Azure Active Directory con Lesson.ly
Questa esercitazione descrive l'integrazione di Lesson.ly con Azure Active Directory (Azure AD).

L'integrazione di Lesson.ly con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Lesson.ly
* È possibile abilitare gli utenti per l'accesso automatico a Lesson.ly (Single Sign-On) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Lesson.ly, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Lesson.ly abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Lesson.ly dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-lessonly-from-the-gallery"></a>Aggiunta di Lesson.ly dalla raccolta
Per configurare l'integrazione di Lesson.ly in Azure AD, è necessario aggiungere Lesson.ly dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Lesson.ly dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Lesson.ly**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_01.png)
7. Nel riquadro dei risultati selezionare **Lesson.ly** e quindi fare clic su **Completa** per aggiungere l'applicazione.

![Creazione di un utente test di Azure AD](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Lesson.ly con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Lesson.ly che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Lesson.ly.

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** (Nome utente) in Lesson.ly.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Lesson.ly, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test Lesson.ly](#creating-a-Lessonly-test-user)** : per avere una controparte di Britta Simon in Lesson.ly collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Lesson.ly tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

L'applicazione Lesson.ly prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli **attributi del token SAML**.
La schermata seguente illustra un esempio relativo a questa operazione.

![Configura accesso Single Sign-On](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_06.png) 

**Per configurare l'accesso Single Sign-On di Azure AD con Lesson.ly, seguire questa procedura:**

1. Nel menu visualizzato nella parte superiore della pagina di integrazione dell'applicazione Lesson.ly del portale di Azure classico fare clic su **Attributi** per aprire la finestra di dialogo **Attributi token SAML**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_07.png) 
2. Per aggiungere i mapping di attributi obbligatori, eseguire la procedura seguente:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_08.png) 
   
    a. Per ogni riga di dati nella tabella precedente, fare clic su **aggiungi attributo utente**.
   
    b. Nella casella di testo **Nome attributo** , digitare il nome dell'attributo indicato per quella riga.
   
    c. Nell’elenco **Valore attributo** selezionare il valore dell'attributo indicato per quella riga.
   
    d. Fare clic su **Completa**
3. Fare clic su **Applica modifiche**.
4. Nel browser fare clic su **Indietro** per aprire nuovamente la finestra di dialogo Avvio rapido.
5. Fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
6. Nella pagina **Stabilire come si desidera che gli utenti accedano a Lesson.ly** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_03.png) 
7. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_04.png) 

    a. Nella casella di testo URL di accesso digitare l'URL usato dagli utenti per accedere all'applicazione Lessonly adottando il modello seguente: **“https://nomesocietà.lesson.ly/signin”**. Quando si fa riferimento a un nome generico, **nomesocietà** deve essere sostituito da un nome effettivo.


1. Nella pagina **Configura accesso Single Sign-On in Lesson.ly** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_05.png) 
   
    a. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.
   
    b. Fare clic su **Avanti**.
2. Per ottenere la configurazione dell'accesso SSO per l'applicazione, contattare il team di supporto di Lesson.ly all'indirizzo dev@lessonly.com. Allegare il file del certificato scaricato al messaggio di posta elettronica e condividere gli URL dei metadati (ID entità, URL di accesso SSO e URL di disconnessione SSO) con il team Lesson.ly perché possa configurare SSO.
3. Nel portale di Azure classico, selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
   
   ![Single Sign-On di Microsoft Azure AD][10]
4. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
   ![Single Sign-On di Microsoft Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_05.png) 
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_06.png) 
   
    a. Nella casella di testo **Nome** digitare **Britta**.  
   
    b. Nella casella di testo **Cognome** digitare **Simon**.
   
    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
    d. Nell'elenco **Ruolo** selezionare **Utente**.
   
    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_08.png) 
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.   

### <a name="creating-a-lessonly-test-user"></a>Creazione di un utente test Lesson.ly
Questa sezione descrive come creare un utente chiamato Britta Simon in Lesson.ly. Lesson.ly supporta il provisioning just-in-time, che è abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a Lesson.ly verrà creato un nuovo utente, se questo non esiste già. [Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on).

> [!NOTE]
> Per creare un utente manualmente, è necessario contattare il team di supporto di Lesson.ly.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Lesson.ly.

![Assegna utente][200] 

**Per assegnare Britta Simon a Lesson.ly, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco di applicazioni selezionare **Lesson.ly**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Lesson.ly nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Lesson.ly.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_205.png

