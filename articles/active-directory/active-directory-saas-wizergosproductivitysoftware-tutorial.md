---
title: 'Esercitazione: Integrazione di Azure Active Directory con Wizergos Productivity Software | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Wizergos Productivity Software.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: acc04396-13c5-4c24-ab9a-30fbc9234ebd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 73b3bc05aeb337c12acb7e47c0dbebe6d0196530
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-wizergos-productivity-software"></a>Esercitazione: Integrazione di Azure Active Directory con Wizergos Productivity Software
Questa esercitazione descrive l'integrazione di Wizergos Productivity Software con Azure Active Directory (Azure AD).

L'integrazione di Wizergos Productivity Software con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Wizergos Productivity Software.
* È possibile abilitare gli utenti per l'accesso Single Sign-On automatico a Wizergos Productivity Software (SSO) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Wizergos Productivity Software, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Wizergos Productivity Software abilitata per l'accesso SSO

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è quello di testare l'accesso Single Sign-On (SSO) di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Wizergos Productivity Software dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="adding-wizergos-productivity-software-from-the-gallery"></a>Aggiunta di Wizergos Productivity Software dalla raccolta
Per configurare l'integrazione di Wizergos Productivity Software in Azure AD, è necessario aggiungere Wizergos Productivity Software dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Wizergos Productivity Software dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Wizergos Productivity Software**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_01.png)
7. Nel pannello dei risultati selezionare **Wizergos Productivity Software** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Selezione dell'app nella raccolta](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso Single Sign-On (SSO) di Azure AD
Questa sezione descrive come configurare e testare l'accesso SSO di Azure AD con Wizergos Productivity Software con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di Wizergos Productivity Software che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Wizergos Productivity Software.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Wizergos Productivity Software.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Wizergos Productivity Software, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test Wizergos Productivity Software](#creating-a-wizergos-productivity-software-test-user)** : per avere una controparte di Britta Simon in Wizergos Productivity Software collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-sso"></a>Configurazione dell'accesso Single Sign-On (SSO) di Microsoft Azure AD
In questa sezione nel portale classico viene abilitato l'accesso Single Sign-On di Azure AD, che viene configurato nell'applicazione Wizergos Productivity Software.

**Per configurare Single Sign-On di Azure AD con Wizergos Productivity Software, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Wizergos Productivity Software** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Wizergos Productivity Software** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_03.png)
3. Nella pagina **Configurare le impostazioni dell'app** fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_04.png)
4. Nella pagina **Configura accesso Single Sign-On in Wizergos Productivity Software** fare clic su **Scarica certificato** e quindi salvare il file nel computer:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_05.png)
5. In un'altra finestra del browser Web accedere al tenant Wizergos Productivity Software come amministratore.
6. Dal menu hamburger, selezionare **Admin**(Amministratore).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)
7. Nella pagina visualizzata selezionare **AUTHENTICATION** (AUTENTICAZIONE) nel menu a sinistra e fare clic su **Azure AD**.
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)
8. Seguire questa procedura nella sezione **AUTHENTICATION** (AUTENTICAZIONE).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)
  1. Fare clic sull'icona **UPLOAD** (CARICA) per caricare il certificato scaricato da Azure AD. 
  2. Nella casella di testo **Issuer URL** (URL autorità di certificazione) inserire il valore di **URL autorità di certificazione** dalla configurazione guidata dell'applicazione di Azure AD.
  3. Nella casella di testo **Single Sign-On URL** (URL Single Sign-On) inserire il valore di **URL servizio Single Sign-On** dalla configurazione guidata dell'applicazione di Azure AD.
  4. Nella casella di testo **Single Sign-Out URL** (URL Single Sign-Out) inserire il valore di **URL servizio Single Sign-Out** dalla configurazione guidata dell'applicazione di Azure AD.
  5. Fare clic sul pulsante **Salva** .
9. Nel portale di Azure classico selezionare la conferma della configurazione e fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
10. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
    
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_09.png)
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_03.png)
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_04.png)
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_05.png) 
  1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
  2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
  3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_06.png)
  1. Nella casella di testo **Nome** digitare **Britta**.  
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_07.png)
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_08.png)
  1. Prendere nota del valore visualizzato in **Nuova password**.
  2. Fare clic su **Complete**.   

### <a name="create-a-wizergos-productivity-software-test-user"></a>Creare un utente di test Wizergos Productivity Software
In questa sezione viene creato un utente chiamato Britta Simon in Wizergos Productivity Software. Per aggiungere utenti alla piattaforma di Wizergos Productivity Software, collaborare con il team di Wizergos Productivity Software tramite l'indirizzo [support@wizergos.com](emailTo:support@wizergos.com) .

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso SSO di Azure concedendole l'accesso a Wizergos Productivity Software.

  ![Assegna utente][200]

**Per assegnare Britta Simon a Wizergos Productivity Software, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione Directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201]
2. Nell'elenco di applicazioni selezionare **Wizergos Productivity Software**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_50.png)
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Wizergos Productivity Software nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Wizergos Productivity Software.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_205.png

