---
title: 'Esercitazione: Integrazione di Azure Active Directory con RightScale | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e RightScale.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 3a8d376d-95fb-4dd7-832a-4fdd4dd7c87c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: adcc4e35f8febe1d0dc1bc093954dee56bf34652
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Esercitazione: Integrazione di Azure Active Directory con RightScale
Questa esercitazione descrive l'integrazione di RightScale con Azure Active Directory (Azure AD).

L'integrazione di RightScale con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a RightScale
* È possibile abilitare gli utenti per l'accesso automatico a RightScale Single Sign-On (SSO) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con RightScale, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di RightScale abilitata per l'accesso SSO

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
>  

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è quello di testare l'accesso Single Sign-On (SSO) di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di RightScale dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD (SSO)

## <a name="add-rightscale-from-the-gallery"></a>Aggiungere RightScale dalla raccolta
Per configurare l'integrazione di RightScale in Azure AD, è necessario aggiungere RightScale dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere RightScale dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]

6. Nella casella di ricerca digitare **RightScale**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_01.png)

7. Nel riquadro dei risultati selezionare **RightScale** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso SSO di Azure AD con ADP RightScale in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di RightScale che corrisponde a un utente di Azure AD. In altre parole deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in RightScale.  

Per configurare e testare l'accesso Single Sign-On di Azure AD con RightScale, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di RightScale](#creating-a-rightscale-test-user)** : per avere una controparte di Britta Simon in RightScale collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come abilitare l'accesso SSO di Azure AD nel portale classico e come configurare l'accesso SSO nell'applicazione RightScale.

**Per configurare l'accesso Single Sign-On di Azure AD con RightScale, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **RightScale** del portale classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a RightScale** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_03.png) 

3. Nella pagina della finestra di dialogo **Configurare le impostazioni dell'app**, se si desidera configurare l'applicazione in **modalità iniziata da IDP**,seguire la procedura seguente e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_04.png) 
  1. Nella casella di testo URL di risposta digitare l'URL nel formato seguente: `https://login.rightscale.com/login/saml2/consume`
  2. Fare clic su **Avanti**.

1. Se si desidera configurare l'applicazione in **SP initiated mode** (Modalità iniziata dal provider di servizi) nella finestra di dialogo **Configurare le impostazioni dell'app** fare clic su **"Mostra opzioni avanzate (facoltativo)"**, quindi digitare l'**URL di accesso** e fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_06.png) 
  1. Nella casella di testo URL di accesso digitare l'URL usato dagli utenti per accedere all'applicazione RightScale nel formato seguente: `https://login.rightscale.com/`
  2. Fare clic su **Avanti**.

2. Nella pagina **Configura accesso Single Sign-On in RightScale** seguire questa procedura e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_05.png) 
  1. Fare clic su **Scarica certificato**e quindi salvare il file del certificato con codifica Base 64 nel computer.
  2. Fare clic su **Avanti**.

3. Per configurare l'accesso SSO per l'applicazione, è necessario accedere al tenant di RightScale come amministratore.
  1. Nel menu in alto fare clic sulla scheda **Settings** (Impostazioni) e selezionare **Single Sign-On**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_001.png) 
  2. Fare clic sul pulsante "**new**" (nuovo) per aggiungere i **provider di identità SAML**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_002.png)  
  3. Nella casella **Nome visualizzato**digitare il nome dell'azienda.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_003.png) 
  4. Selezionare **Allow RightScale-initiated SSO using a discovery hint** (Consenti accesso SSO avviato da RightScale tramite un hint di individuazione) e digitare il **nome di dominio** nella casella di testo sottostante.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_004.png)
  5. Copiare l'URL SSO SAML da Azure AD in **SAML SSO Endpoint** (Endpoint SSO SAML) in RightScale.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_005.png)
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_006.png)
  6. Copiare l'ID entità da Azure AD a **SAML EntityID** (ID entità SAML) in RightScale.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_007.png)
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_008.png)
  7. Fare clic sul pulsante **Sfoglia** per caricare il certificato scaricato al passaggio 4.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_009.png)
  8. Fare clic su **Salva**.

4. Nel portale classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]

5. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_09.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_04.png) 

5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_05.png) 
  1. In **Tipo di utente** selezionare **Nuovo utente nell'organizzazione**.
  2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
  3. Fare clic su **Avanti**.

6. Nella pagina **Profilo utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_06.png) 
  1. Nella casella di testo **Nome** digitare **Britta**.  
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_07.png) 

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_08.png)  
  1. Prendere nota del valore visualizzato in **Nuova password**.
  2. Fare clic su **Completa**.   

### <a name="create-a-rightscale-test-user"></a>Creare un utente test di RightScale
In questa sezione viene creato un utente chiamato Britta Simon in RightScale. Rivolgersi al team di supporto di RightScale mediante support@rightscale.com per aggiungere gli utenti nella piattaforma RightScale.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon all'uso dell'accesso SSO di Azure concedendole l'accesso a RightScale.

![Assegna utente][200] 

**Per assegnare Britta Simon a RightScale, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione Directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **RightScale**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_50.png) 

3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 

4. Nell'elenco di utenti selezionare **Britta Simon**.

5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  

Quando si fa clic sul riquadro RightScale nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione RightScale.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_205.png

