---
title: 'Esercitazione: Integrazione di Azure Active Directory con Tangoe Command Premium Mobile | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Tangoe Command Premium Mobile.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 2b0b544c-9c2c-49cd-862b-ec2ee9330126
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: bb140097831453d46f6bfef1c9fbe569eefb3020
ms.lasthandoff: 02/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-tangoe-command-premium-mobile"></a>Esercitazione: Integrazione di Azure Active Directory con Tangoe Command Premium Mobile
Questa esercitazione descrive come integrare Tangoe Command Premium Mobile con Azure Active Directory (Azure AD).

L'integrazione di Tangoe Command Premium Mobile con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Tangoe Command Premium Mobile.
* È possibile abilitare gli utenti per l'accesso automatico a Tangoe Command Premium Mobile (Single Sign-On) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale con il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Tangoe Command Premium Mobile, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure.
* Sottoscrizione di Tangoe Command Premium Mobile abilitata per l'accesso Single Sign-On (SSO)

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Tangoe Command Premium Mobile dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-tangoe-command-premium-mobile-from-the-gallery"></a>Aggiunta di Tangoe Command Premium Mobile dalla raccolta
Per configurare l'integrazione di Tangoe Command Premium Mobile in Azure AD, è necessario aggiungere Tangoe Command Premium Mobile dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Tangoe Command Premium Mobile dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Tangoe Command Premium Mobile**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_01.png)
7. Nel riquadro dei risultati selezionare **Tangoe Command Premium Mobile** e quindi fare clic su **Completa** per aggiungere l'applicazione.

![Creazione di un utente test di Azure AD](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Tangoe Command Premium Mobile con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Tangoe Command Premium Mobile che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Tangoe Command Premium Mobile.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** in Tangoe Command Premium Mobile.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Tangoe Command Premium Mobile, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Tangoe Command Premium Mobile](#creating-an-tangoe-test-user)** : per avere una controparte di Britta Simon in Tangoe Command Premium Mobile collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione Tangoe Command Premium Mobile.

**Per configurare Single Sign-On di Azure AD con Tangoe Command Premium Mobile, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Tangoe Command Premium Mobile** del portale classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6]
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Tangoe Command Premium Mobile** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_04.png) 

   1. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione Tangoe Command Premium Mobile adottando il modello seguente: **"https://sso.tangoe.com/sp/startSSO.ping?PartnerIdpId=\<autortà di certificazione tenant\>&Target=\<URL pagina di destinazione\>"**.

   2. Nella casella di testo **URL di risposta** digitare l'URL adottando il modello seguente: **"https://sso.tangoe.com/sp/ACS.saml2"**

    > [!NOTE]  
    > Se non si conoscono i valori corretti per gli URL, è possibile usare i valori riportati in alto come segnaposto e richiedere i valori corretti al supporto tecnico di Tangoe.
    >

4. Nella pagina **Configura accesso Single Sign-On in Tangoe Command Premium Mobile** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_05.png) 
   
   1. Fare clic su **Scarica metadati**e quindi salvare il file nel computer.
   2. Fare clic su **Avanti**.

5. Per ottenere l'accesso Single Sign-On configurato per l'applicazione, contattare il supporto tecnico di Tangoe e fornire i seguenti elementi:

   - Il file dei metadati scaricato
   - L' **URL autorità di certificazione**
   - **URL SSO SAML**
   - L' **URL servizio Single Sign-Out**

6. Nel portale di Azure classico, selezionare la conferma della configurazione e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.

* Nell'elenco di utenti selezionare **Britta Simon**.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_04.png)
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_05.png) 
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_06.png) 
   
  1. Nella casella di testo **Nome** digitare **Britta**.  
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_08.png) 
   
  1. Prendere nota del valore visualizzato in **Nuova password**.
  2. Fare clic su **Completa**.   

### <a name="creating-an-tangoe-command-premium-mobile-test-user"></a>Creazione di un utente test di Tangoe Command Premium Mobile
In questa sezione viene creato un utente chiamato Britta Simon in Tangoe Command Premium Mobile. L'applicazione Tangoe Command Premium Mobile necessita che venga eseguito il provisioning di tutti gli utenti all'interno dell'applicazione prima di eseguire l'accesso Single Sign-On. Contattare il supporto tecnico Tangoe per eseguire il provisioning di tutti gli utenti nell'applicazione. 

> [!NOTE]
> Se è necessario creare manualmente un utente o un batch di utenti, è necessario contattare il team di supporto Tangoe Command Premium Mobile.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Tangoe Command Premium Mobile.

![Assegna utente][200] 

**Per assegnare Britta Simon a Tangoe Command Premium Mobile, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

 ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Tangoe Command Premium Mobile**.

 ![Configura accesso Single Sign-On](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_50.png) 
 
3. Scegliere **Utenti**dal menu in alto.

 ![Assegna utente][203] 

4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.

![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Tangoe Command Premium Mobile nel pannello di accesso, viene effettuato automaticamente l'accesso all'applicazione Tangoe Command Premium Mobile.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_205.png

