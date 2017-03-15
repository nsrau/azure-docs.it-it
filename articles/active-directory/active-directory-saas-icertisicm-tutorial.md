---
title: 'Esercitazione: Integrazione di Azure Active Directory con Icertis Contract Management Platform | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Icertis Contract Management Platform.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 6627e6dd-f559-4cd4-a509-f6d9a4961b49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 51c6f95585e2a3c00bfdfa660941ef010b20fe7d
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-icertis-contract-management-platform"></a>Esercitazione: Integrazione di Azure Active Directory con Icertis Contract Management Platform
L'obiettivo di questa esercitazione è di descrivere l'integrazione di Icertis Contract Management Platform con Azure Active Directory (Azure AD).

L'integrazione di Icertis Contract Management Platform con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi ha accesso a Icertis Contract Management Platform
* È possibile abilitare gli utenti per l'accesso Single Sign-On automatico a Icertis Contract Management Platform (SSO) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Icertis Contract Management Platform, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Una sottoscrizione a Icertis Contract Management Platform abilitata per l'accesso SSO

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è quello di testare l'accesso Single Sign-On (SSO) di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Icertis Contract Management Platform dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="add-icertis-contract-management-platform-from-the-gallery"></a>Aggiungere Icertis Contract Management Platform dalla raccolta
Per configurare l'integrazione di Icertis Contract Management Platform in Azure AD, è necessario aggiungere Icertis Contract Management Platform dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Icertis Contract Management Platform dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca, digitare **Icertis Contract Management Platform**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_01.png)
7. Nel riquadro dei risultati selezionare **Icertis Contract Management Platform**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Selezione dell'app nella raccolta](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso Single Sign-On (SSO) di Azure AD
Questa sezione descrive come configurare e testare l'accesso SSO di Azure AD con Icertis Contract Management Platform in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di Icertis Contract Management Platform che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Icertis Contract Management Platform.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Icertis Contract Management Platform.

Per configurare e testare l'accesso SSO di Azure AD con Icertis Contract Management Platform, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Icertis Contract Management Platform](#creating-a-icertis-contract-management-platform-test-user)** : per avere una controparte di Britta Simon in Icertis Contract Management Platform collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD
In questa sezione viene abilitato l'accesso SSO di Azure AD nel portale di Azure classico e viene configurato l'accesso SSO nell'applicazione Icertis Contract Management Platform.

**Per configurare l'accesso Single Sign-On di Azure AD con Icertis Contract Management Platform, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Icertis Contract Management Platform** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Icertis Contract Management Platform** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura e fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_04.png)
  1. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente:   `https://<company name>.icertis.com`
  2. Fare clic su **Avanti**.

   >[!NOTE]
   >Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'URL di accesso effettivo. Per ottenere tali valori, contattare Icertis Contract Management Platform.
   >

1. Nella pagina **Configura accesso Single Sign-On in Icertis Contract Management Platform** seguire questa procedura e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_05.png)
  1. Fare clic su **Scarica metadati**e quindi salvare il file nel computer.
  2. Fare clic su **Avanti**.
2. Per ottenere SSO configurato per l'applicazione, contattare il team di supporto di Icertis Contract Management Platform e fornire gli elementi seguenti: 
   *  Il file dei **metadati scaricato** 
   *  L' **ID entità** 
   *   **URL SSO SAML** 
   *   **URL servizio Single Sign-Out**
3. Nel portale di Azure classico selezionare la conferma della configurazione e fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
4. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_09.png)
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_03.png)
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_04.png)
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_05.png)
  1. In Tipo di utente selezionare Nuovo utente nell'organizzazione. 
  2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
  3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_06.png)
  1. Nella casella di testo **Nome** digitare **Britta**.  
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_07.png)
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_08.png)
  1. Prendere nota del valore visualizzato in **Nuova password**. 
  2. Fare clic su **Completa**.   

### <a name="create-a-icertis-contract-management-platform-test-user"></a>Creare un utente test di Icertis Contract Management Platform
In questa sezione viene creato un utente chiamato Britta Simon in Icertis Contract Management Platform. Collaborare con il team di supporto di Icertis Contract Management Platform per aggiungere gli utenti a Icertis Contract Management Platform.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso SSO di Azure concedendole l'accesso a Icertis Contract Management Platform.

![Assegna utente][200]

**Per assegnare Britta Simon a Icertis Contract Management Platform, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201]
2. Nell'elenco delle applicazioni selezionare **Icertis Contract Management Platform**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_50.png)
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Icertis Contract Management Platform nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Icertis Contract Management Platform.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_205.png

