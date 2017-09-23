---
title: 'Esercitazione: Integrazione di Azure Active Directory con Splunk Enterprise e Splunk Cloud | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Splunk Enterprise e Splunk Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b3e2b4a9-749c-4895-813d-db46f8dfdbf8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/09/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: b78e9b7161207a74880e912241d5e965b353d1c5
ms.contentlocale: it-it
ms.lasthandoff: 03/28/2017

---

# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Esercitazione: Integrazione di Azure Active Directory con Splunk Enterprise e Splunk Cloud

Questa esercitazione descrive come integrare Splunk Enterprise e Splunk Cloud con Azure Active Directory (Azure AD).

L'integrazione di Splunk Enterprise e Splunk Cloud con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Splunk Enterprise e Splunk Cloud
- È possibile abilitare gli utenti per l'accesso automatico a Splunk Enterprise e Splunk Cloud Single Sign-On (SSO) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Splunk Enterprise e Splunk Cloud, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Splunk Enterprise o Splunk Cloud abilitata per l'accesso SSO


>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
>

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Splunk Enterprise e Splunk Cloud dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD


## <a name="add-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Aggiungere Splunk Enterprise e Splunk Cloud dalla raccolta
Per configurare l'integrazione di Splunk Enterprise e Splunk Cloud in Azure AD, è necessario aggiungere Splunk Enterprise e Splunk Cloud dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Splunk Enterprise e Splunk Cloud, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro.

    ![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applications][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella di ricerca digitare **Splunk Enterprise o Splunk Cloud**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_01.png)

7. Nel pannello dei risultati selezionare **Splunk Enterprise e Splunk Cloud** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_02.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Splunk Enterprise e Splunk Cloud con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Splunk Enterprise e Splunk Cloud che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Splunk Enterprise e Splunk Cloud.

La relazione di collegamento viene stabilita assegnando il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente) in Splunk Enterprise e Splunk Cloud.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Splunk Enterprise e Splunk Cloud, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Splunk Enterprise e Splunk Cloud](#creating-a-splunk-enterprise-and-splunk-cloud-test-user)**: per avere una controparte di Britta Simon in Splunk Enterprise e Splunk Cloud collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso SSO di Azure AD nel portale di Azure classico e viene configurato l'accesso SSO nell'applicazione Splunk Enterprise e Splunk Cloud.


**Per configurare Single Sign-On di Azure AD con Splunk Enterprise e Splunk Cloud, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Splunk Enterprise e Splunk Cloud** del portale classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
     
    ![Configura accesso Single Sign-On][6] 

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Splunk Enterprise e Splunk Cloud** selezionare **Accesso Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_03.png) 

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_04.png) 
  1. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione Splunk Enterprise e Splunk Cloud adottando il modello seguente: `https://<splunkserverUrl>/en-US/app/launcher/home`
  2. Nella casella di testo **Identificatore** digitare l'URL del server Splunk.
  3. Nella casella di testo **URL di risposta** digitare l'URL nel formato seguente: `https://<splunkserver>/saml/acs`
  4. Fare clic su **Avanti**.
 
4. Nella pagina **Configura accesso Single Sign-On in Splunk Enterprise e Splunk Cloud** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_05.png)
  1. Fare clic su **Scarica metadati**e quindi salvare il file nel computer.
  2. Fare clic su **Avanti**.

5. Per ottenere la configurazione dell'accesso Single Sign-On per l'applicazione, contattare il team di supporto di Splunk Enterprise e Splunk Cloud e fornire gli elementi seguenti:

    * I **metadati della federazione** scaricati.
6. Nel portale classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
    
    ![Single Sign-On di Microsoft Azure AD][10]

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
 
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_09.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_04.png) 

5. Nella pagina **Informazioni sull'utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_05.png) 
  1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
  2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
  3. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura:
  
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_06.png) 
  1. Nella casella di testo **Nome** digitare **Britta**.  
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_07.png) 

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_08.png) 
  1. Prendere nota del valore visualizzato in **Nuova password**.
  2. Fare clic su **Completa**.   

### <a name="create-a-splunk-enterprise-and-splunk-cloud-test-user"></a>Creare un utente test di Splunk Enterprise e Splunk Cloud

In questa sezione viene creato un utente chiamato Britta Simon in Splunk Enterprise e Splunk Cloud. Rivolgersi al team di supporto di Splunk Enterprise e Splunk Cloud per aggiungere gli utenti nella piattaforma Splunk Enterprise e Splunk Cloud.


### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso SSO di Azure concedendole l'accesso a Splunk Enterprise e Splunk Cloud.

![Assegna utente][200] 

**Per assegnare Britta Simon a Splunk Enterprise e Splunk Cloud, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Splunk Enterprise e Splunk Cloud**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_50.png) 

3. Scegliere **Utenti**dal menu in alto.

    ![Assegna utente][203]

4. Nell'elenco di utenti selezionare **Britta Simon**.

5. Fare clic su **Assegna**sulla barra degli strumenti in basso.

    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso SSO di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Splunk Enterprise e Splunk Cloud nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Splunk Enterprise e Splunk Cloud.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_205.png

