---
title: 'Esercitazione: Integrazione di Azure Active Directory con Autotask | Microsoft Docs'
description: Informazioni su come usare Autotask con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora!
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: d22f107be306ad47426b89adb99f07f9d1f6617c
ms.openlocfilehash: 10292e19216e01034a597368e0f2570d49e71610
ms.lasthandoff: 12/28/2016


---

# <a name="tutorial-azure-active-directory-integration-with-autotask"></a>Esercitazione: Integrazione di Azure Active Directory con Autotask

Questa esercitazione descrive l'integrazione di Autotask con Azure Active Directory (Azure AD).

L'integrazione di Autotask con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Autotask 
- È possibile abilitare gli utenti per l'accesso automatico a Autotask (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Autotask, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Autotask abilitata per l'accesso Single Sign-On


> [!NOTE] 
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Autotask dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-autotask-from-the-gallery"></a>Aggiunta di Autotask dalla raccolta
Per configurare l'integrazione di Autotask in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Autotask dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
    
    ![Applications][2]

4. Fare clic su **Add** nella parte inferiore della pagina.
    
    ![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella di ricerca digitare **Autotask**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_01.png)

7. Nel riquadro dei risultati selezionare **Autotask** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Selezione dell'app nella raccolta](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Autotask in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Autotask che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Autotask.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Nomeutente** in Autotask. 

Per configurare e testare l'accesso Single Sign-On di Azure AD con Autotask, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Autotask](#creating-an-autotask-workplace-test-user)**: per avere una controparte di Britta Simon in Autotask collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel Portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione Autotask.

**Per configurare l'accesso Single Sign-On di Azure AD con Autotask, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Autotask** del Portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
     
    ![Configura accesso Single Sign-On][6] 

2. Nella pagina **How would you like users to sign on to Autotask (Stabilire come si desidera che gli utenti accedano ad Autotask)** selezionare **Azure AD Single Sign-On (Single Sign-On di Azure AD)** e quindi fare clic su **Avanti**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_03.png)

3. Nella pagina della finestra di dialogo **Configurare le impostazioni dell'app**, se si desidera configurare l'applicazione in **modalità iniziata da IDP**,seguire la procedura seguente e fare clic su **Avanti**:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_04.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<your-subdomain>.awp.autotask.net/singlesignon/saml/metadata`.

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<your-subdomain>.awp.autotask.net/singlesignon/saml/SSO`.

    c. Fare clic su **Avanti**.

4. Se si desidera configurare l'applicazione in **SP initiated mode** (Modalità iniziata dal provider di servizi) nella finestra di dialogo **Configurare le impostazioni dell'app** fare clic su **"Mostra opzioni avanzate (facoltativo)"**, quindi digitare l'**URL di accesso** e fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_05.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<your-subdomain>.awp.autotask.net/loginsso`.

    b. Fare clic su **Avanti**.

    > [!NOTE] 
    > È necessario aggiornare questi valori con l'URL di accesso, l'ID e l'URL di risposta effettivo. Per ottenere questi valori, contattare il [team di supporto di Autotask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm).

4. Nella pagina **Configure single sign-on at Autotask (Configurare l'accesso Single Sign-On in Autotask)** fare clic su **Download metadata (Scarica metadati)** e salvare il file sul computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_06.png)

5. Per ottenere la configurazione dell'accesso Single Sign-On per l'applicazione, contattare il [team di supporto diAutotask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) e fornire i **metadati** scaricati.

6. Nel portale di Azure classico selezionare la conferma della configurazione e quindi fare clic su **Avanti**.
    
    ![Single Sign-On di Microsoft Azure AD][10]

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
    
    ![Single Sign-On di Microsoft Azure AD][11]



### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_09.png)

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_03.png)

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_04.png)

5. Nella pagina **Informazioni sull'utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_05.png)

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura:
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_06.png)

    a. Nella casella di testo **Nome** digitare **Britta**.  

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_07.png)

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_08.png)

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Completa**.   



### <a name="creating-an-autotask-test-user"></a>Creazione di un utente test di Autotask

In questa sezione viene creato un utente di nome Britta Simon in Autotask. Collaborare con il [team di supporto di Autotask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) per aggiungere gli utenti alla piattaforma Autotask.


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

Questa sezione descrive come abilitare Britta Simon per l’utilizzo dell’accesso Single Sign-On di Azure concedendole l’accesso a Autotask.
    
![Assegna utente][200]

**Per assegnare Britta Simon a Autotask, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
    
    ![Assegna utente][201]

2. Nell'elenco delle applicazioni, selezionare **Autotask**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_50.png)

3. Scegliere **Utenti**dal menu in alto.
    
    ![Assegna utente][203]

4. Nell'elenco di utenti selezionare **Britta Simon**.

5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
    
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.
 
Quando si fa clic sul riquadro di Autotask nel riquadro di accesso, si dovrebbe accedere automaticamente all'applicazione Autotask.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_205.png

