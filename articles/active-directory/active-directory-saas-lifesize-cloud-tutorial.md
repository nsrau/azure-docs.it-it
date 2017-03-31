---
title: 'Esercitazione: Integrazione di Azure Active Directory con Lifesize Cloud | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Lifesize Cloud.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: 8edbee8e554a7818b97669a4bb64b31ada67b1a5
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Esercitazione: Integrazione di Azure Active Directory con Lifesize Cloud
Questa esercitazione descrive come integrare Lifesize Cloud con Azure Active Directory (Azure AD).

L'integrazione di Lifesize Cloud con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Lifesize Cloud
* È possibile abilitare gli utenti per l'accesso Single Sign-On (SSO) automatico a Lifesize Cloud con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Lifesize Cloud, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Lifesize Cloud abilitata per l'accesso Single Sign-On (SSO)

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

* Aggiunta di Lifesize Cloud dalla raccolta
* Configurazione e test dell'accesso Single Sign-On (SSO) di Azure AD

## <a name="add-lifesize-cloud-from-the-gallery"></a>Aggiungere Lifesize Cloud dalla raccolta
Per configurare l'integrazione di Lifesize Cloud in Azure AD, è necessario aggiungere Lifesize Cloud dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Lifesize Cloud dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Lifesize Cloud**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_01.png)
7. Nel riquadro dei risultati selezionare **Lifesize Cloud** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Lifesize Cloud con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Lifesize Cloud che corrisponde a un utente di Azure AD. In altre parole deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Lifesize Cloud.

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in Lifesize Cloud.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Lifesize Cloud, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Lifesize Cloud](#creating-a-lifesize-cloud-test-user)**: per avere una controparte di Britta Simon in Lifesize Cloud collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD
In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione Lifesize Cloud.

**Per configurare l'accesso Single Sign-On di Azure AD con Lifesize Cloud, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Lifesize Cloud** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Lifesize Cloud** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_04.png)   
  1. Nella casella di testo **URL di accesso** digitare l'URL utilizzato dagli utenti per accedere all'applicazione Lifesize Cloud adottando il modello seguente: **https://login.lifesizecloud.com/ls/?acs**.
  2. Fare clic su **Avanti**.
4. Nella pagina **Configura accesso Single Sign-On in Lifesize Cloud** seguire questa procedura:
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_05.png)
   1. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.
   2. Fare clic su **Avanti**.
5. Al fine di configurare l'accesso Single Sign-On per l'applicazione, effettuare l'accesso all'applicazione Lifesize Cloud con privilegi di amministratore.
6. Nell'angolo superiore destro fare clic sul proprio nome e quindi su **Impostazioni avanzate**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)
7. Nelle impostazioni avanzate fare clic sul collegamento **SSO Configuration** (Configurazione SSO). Verrà aperta la pagina SSO Configuration (Configurazione SSO) per l'istanza.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)
8. Ora configurare i valori seguenti nell'interfaccia utente di configurazione SSO.    
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
  1. Copiare il valore dell'URL dell'autorità di certificazione da Azure AD e incollarlo nella casella di testo **Identity Provider Issuer** (Autorità di certificazione del provider di identità). 
  2. Copiare il valore dell'URL di accesso remoto da Azure AD e incollarlo nella casella di testo **URL di accesso**.   
  3. Aprire il certificato scaricato nel Blocco note, copiare il contenuto del certificato, escluse le righe di inizio e fine certificato, e incollarlo nella casella di testo **Certificato X.509**.
  4. Nel mapping dell'attributo SAML per la casella di testo **Nome** inserire il valore **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
  5. Nel mapping dell'attributo SAML per la casella di testo **Cognome** inserire il valore **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
  6. Nel mapping dell'attributo SAML per la casella di testo **E-mail** inserire il valore **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**
9. Per verificare la configurazione è possibile fare clic sul pulsante **Prova**.
   
   >[!NOTE]
   >Perché la prova abbia esito positivo, è necessario completare la configurazione guidata di Azure AD e anche fornire l'accesso a utenti o gruppi che possano eseguire il test.
   >  
10. Abilitare l'accesso SSO con il pulsante **Enable SSO** (Abilita SSO).
11. Fare clic sul pulsante **Aggiorna** in modo che tutte le impostazioni vengano salvate. Verrà generato il valore RelayState. Copiare il valore RelayState generato nella casella di testo. Questo valore servirà nei passaggi successivi.
12. Nel portale di Azure classico selezionare la conferma della configurazione e quindi fare clic su **Avanti**.
    
   ![Single Sign-On di Microsoft Azure AD][10]
13. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
    
    ![Single Sign-On di Microsoft Azure AD][11]
    
**Per accedere al portale di gestione di Azure:**

1. Eseguire l'accesso ad **https://portal.azure.com** usando le credenziali di amministratore.
2. Nel riquadro di spostamento a sinistra fare clic sul collegamento **Altri servizi**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_09.png)
3. Cercare Azure Active Directory e fare clic sul collegamento **Azure Active Directory**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_10.png)
4. Tutte le proprie applicazioni SaaS saranno disponibili sotto il pulsante **Applicazioni aziendali**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_11.png)
5. Fare clic sul collegamento **Tutte le applicazioni** nel pannello successivo.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_12.png)
6. Cercare l'applicazione Lifesize per cui si desidera configurare RelayState. 
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_13.png)
7. Fare clic sul collegamento **Single sign-on** nel pannello.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_14.png)
8. Verrà visualizzata la casella di controllo **Show advanced URL settings** (Mostra impostazioni URL avanzate). Fare clic sulla casella di controllo.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_15.png)
9. Ora configurare il RelayState per l'applicazione, visibile nella pagina di configurazione SSO dell'applicazione Lifesize. 
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_16.png)
10. Salvare le impostazioni.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_05.png) 
   1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:

   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_06.png) 
   1. Nella casella di testo **Nome** digitare **Britta**.   
   2. Nella casella di testo **Cognome** digitare **Simon**.
   3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   4. Nell'elenco **Ruolo** selezionare **Utente**.
   5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_08.png) 
    1. Prendere nota del valore visualizzato in **Nuova password**.
    2. Fare clic su **Complete**.   

### <a name="create-an-lifesize-cloud-test-user"></a>Creare un utente di test di Lifesize Cloud
In questa sezione viene creato un utente chiamato Britta Simon in Lifesize Cloud. Lifesize Cloud supporta il provisioning utenti automatico. Dopo l'autenticazione in Azure AD sarà eseguito il provisioning automatico dell'utente nell'applicazione. 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Lifesize Cloud.

![Assegna utente][200] 

**Per assegnare Britta Simon a Lifesize Cloud, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco di applicazioni selezionare **Lifesize Cloud**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Lifesize Cloud nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Lifesize Cloud.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_205.png

