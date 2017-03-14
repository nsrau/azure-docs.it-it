---
title: 'Esercitazione: Integrazione di Azure Active Directory con Samanage | Documentazione Microsoft'
description: Informazioni su come usare Samanage con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eacd69207e21a5a7c3ef71902bc6c20c37f2003c
ms.openlocfilehash: 0936f7e0b5e1967b629b31cac3c817d04a75287f
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Esercitazione: Integrazione di Azure Active Directory con Samanage
Questa esercitazione descrive l'integrazione di Samanage con Azure Active Directory (Azure AD).

L'integrazione di Samanage con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Samanage
* È possibile abilitare gli utenti per l'accesso Single Sign-On automatico a Samanage (SSO) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Samanage, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure valida
* Un tenant di Samanage

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è quello di testare l'accesso Single Sign-On (SSO) di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Samanage dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="adding-samanage-from-the-gallery"></a>Aggiunta di Samanage dalla raccolta
Per configurare l'integrazione di Samanage in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Samanage dalla raccolta, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-samanage-tutorial/tutorial_general_01.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-samanage-tutorial/tutorial_general_04.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Samanage**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_01.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Samanage**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Samanage](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_02.png "Samanage")

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso Single Sign-On (SSO) di Azure AD
Questa sezione descrive come configurare e testare l'accesso SSO di Azure AD con Samanage con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di Samanage che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Samanage.

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in Samanage.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Samanage, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test per Samanage](#creating-a-Samanage-test-user)** : per avere una controparte di Britta Simon in Samanage collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-sso"></a>Configurazione dell'accesso Single Sign-On (SSO) di Microsoft Azure AD
In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale classico e viene configurato l'accesso SSO nell'applicazione Samanage.

**Per configurare l'accesso SSO di Azure AD con Samanage, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Samanage** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-samanage-tutorial/tutorial_general_05.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Samanage** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Accesso Single Sign-On di Microsoft Azure AD](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_03.png "Accesso Single Sign-On di Microsoft Azure AD")
3. Nella pagina Configurare le impostazioni dell'app seguire questa procedura:
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_04.png "Configurare l'URL dell'app") 
 1. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<Company Name>.samanage.com/saml_login/<Company Name>`. 
 2. Fare clic su **Avanti**.
 
   >[!NOTE]
   >Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'URL di accesso effettivo. Per ottenere questi valori, vedere il passaggio 8.c per altri dettagli o contattare Samanage.
   > 
 
4. Nella pagina **Configura accesso Single Sign-On in Samanage** fare clic su **Scarica certificato**, quindi salvare il file di certificato nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_05.png "Configurare l'accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Samanage come amministratore.
6. Fare clic su **Dashboard** e selezionare **Setup** (Configurazione) nel riquadro di spostamento a sinistra.
   
   ![Dashboard](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Dashboard")
7. Fare clic su **Single Sign-On**.
   
   ![Single Sign-On](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_002.png "Single Sign-On")
8. Nella sezione **Login using SAML** (Accesso tramite SAML) seguire questa procedura:
   
   ![Login using SAML](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_003.png "Login using SAML")
 1. Fare clic su **Enable Single Sign-On with SAML**(Abilita Single Sign-On con SAML).  
 2. Nella casella di testo **Identity Provider URL** (URL del provider di identità) inserire il valore di **ID provider di identità** dalla configurazione guidata dell'applicazione Azure AD.    
 3. Verificare che **Login URL** (URL di accesso) corrisponda all'**URL di accesso** indicato nel passaggio 3.
 4. Nella casella di testo **Logout URL** (URL di disconnessione) inserire il valore di **URL disconnessione remota** dalla configurazione guidata dell'applicazione Azure AD.
 5. Nella casella di testo **SAML Issuer** (Autorità di certificazione SAML) digitare l'URI ID app impostato nel provider di identità.
 6. Aprire il certificato con codifica Base&64; nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **Paste your Identity Provider x.509 Certificate below** (Incollare il certificato X.509 del provider di identità di seguito).
 7. Fare clic su **Create users if they do not exist in Samanage**(Crea utenti se non presenti in Samanage).
 8. Fare clic su **Update**.
9. Nel portale di Azure classico selezionare la conferma della configurazione e fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_06.png "Configurare l'accesso Single Sign-On")
10. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_07.png "Configurare l'accesso Single Sign-On")

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creazione di un utente test di Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_00.png)

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_01.png)
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_02.png)
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_03.png)
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_04.png)
 1. In Tipo di utente selezionare Nuovo utente nell'organizzazione. 
 2. Nella casella di testo **Nome utente** digitare **BrittaSimon**. 
 3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_05.png) 
 1. Nella casella di testo **Nome** digitare **Britta**.   
 2. Nella casella di testo **Cognome** digitare **Simon**. 
 3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**. 
 4. Nell'elenco **Ruolo** selezionare **Utente**. 
 5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_06.png)
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_07.png)  
 1. Prendere nota del valore visualizzato in **Nuova password**. 
 2. Fare clic su **Completa**.   

### <a name="create-a-samanage-test-user"></a>Creare un utente test per Samanage
Per consentire agli utenti di Azure AD di accedere a Samanage, è necessario eseguirne il provisioning in Samanage. Nel caso di Samanage, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Samanage come amministratore.
2. Fare clic su **Dashboard** e selezionare **Setup** (Configurazione) nel riquadro di spostamento a sinistra.
   
   ![Installazione](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Installazione")
3. Fare clic sulla scheda **Users** .
   
   ![Utenti](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_006.png "Utenti")
4. Fare clic su **Nuovo utente**.
   
   ![Nuovo utente](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_007.png "Nuovo utente")
5. In **Name** (Nome) e **Email Address** (Indirizzo di posta elettronica) digitare nome e indirizzo di posta elettronica di un account Azure AD di cui si vuole effettuare il provisioning e fare clic su **Create user** (Crea utente).
   
   ![Creat User](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_008.png "Creat User")
   
   >[!NOTE]
   >Il titolare dell'account Azure AD riceverà un messaggio di posta elettronica contenente un collegamento da selezionare per confermare e attivare l'account. È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da Samanage per eseguire il provisioning degli account utente di Azure AD.
   >  

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon a usare l'accesso Single Sign-On di Azure concedendole l'accesso a Samanage.

![Assegnare utenti](./media/active-directory-saas-samanage-tutorial/assign_aaduser_00.png "Assegnare utenti")

**Per assegnare Britta Simon a Samanage, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegnare utenti](./media/active-directory-saas-samanage-tutorial/assign_aaduser_01.png "Assegnare utenti")
2. Nell'elenco di applicazioni selezionare **Samanage**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_08.png)
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegnare utenti](./media/active-directory-saas-samanage-tutorial/assign_aaduser_02.png "Assegnare utenti")
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegnare utenti](./media/active-directory-saas-samanage-tutorial/assign_aaduser_03.png "Assegnare utenti")

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Samanage nel pannello di accesso, verrà eseguito automaticamente l'accesso all'applicazione Samanage.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


