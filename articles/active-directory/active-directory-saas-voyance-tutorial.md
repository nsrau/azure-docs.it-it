---
title: 'Esercitazione: Integrazione di Azure Active Directory con Voyance | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Voyance.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 539dc1f9-64c9-4dce-b259-2b0b49dcf857
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/09/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 0746f010e8d001a66f8de0a32ea0147774da657b
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-voyance"></a>Esercitazione: integrazione di Azure Active Directory con Voyance

Questa esercitazione descrive come integrare Voyance con Azure Active Directory (Azure AD).

L'integrazione di Voyance con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Voyance
- È possibile abilitare gli utenti per l'accesso automatico a Voyance Single Sign-On (SSO) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Voyance, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Voyance abilitata per l'accesso SSO

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
>

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Voyance dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="add-voyance-from-the-gallery"></a>Aggiungere Voyance dalla raccolta
Per configurare l'integrazione di Voyance in Azure AD, è necessario aggiungere Voyance dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Voyance dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applications][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella di ricerca digitare **Voyance**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_01.png)

7. Nel riquadro dei risultati selezionare **Voyance** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_0001.png)


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso SSO di Azure AD con Voyance con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di Voyance che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Voyance.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Voyance.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Voyance, è necessario completare le operazioni fondamentali seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Voyance](#creating-a-voyance-test-user)**: per avere una controparte di Britta Simon in Voyance collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale classico e viene configurato l'accesso Single Sign-On nell'applicazione Voyance.


**Per configurare l'accesso Single Sign-On di Azure AD con Voyance, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Voyance** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On][6]

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Voyance** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_02.png)

3. Nella pagina della finestra di dialogo **Configurare le impostazioni dell'app**, se si desidera configurare l'applicazione in **modalità iniziata da IDP**,seguire la procedura seguente e fare clic su **Avanti**:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_03.png)
  1. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<company name>.nyansa.com`.
  2. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<company name>.nyansa.com/saml/create/`.
  3. Fare clic su **Avanti**.

4. Se si desidera configurare l'applicazione in **SP initiated mode** (Modalità iniziata dal provider di servizi) nella finestra di dialogo **Configurare le impostazioni dell'app** fare clic su **"Mostra opzioni avanzate (facoltativo)"**, quindi digitare l'**URL di accesso** e fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_04.png)
  1. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<company name>.nyansa.com/`.
  2. Fare clic su **Avanti**.

      >[!NOTE]
      >È necessario aggiornare questi valori con l'URL di accesso, l'ID e l'URL di risposta effettivo. Per ottenere questi valori, contattare il [team di supporto di Voyance](emaiLto:support@nyansa.com).
      >

5. Nella pagina **Configura accesso Single Sign-On in Voyance** fare clic su **Download certificato** e quindi salvare il file nel computer:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_05.png) 

6. In un'altra finestra del browser Web accedere al tenant Voyance come amministratore.

7. Passare all'angolo superiore destro della barra di spostamento e fare clic nell'elenco a discesa che indica "**Acme University**".
    
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_001.png) 

8. Fare clic su "**Admin Settings**" (Impostazioni amministrazione).

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_002.png)

9. Fare clic sulla scheda "**User Access**" (Accesso utente).

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_003.png)

10. Fare clic sul pulsante "**SSO is disabled**" (SSO disabilitato) per configurare Azure AD come IdP tramite SAML 2.0.

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_004.png)

11. Passare alla sezione **SAML v2** e seguire questa procedura:

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_005.png)
 1. Selezionare **Enabled**.
 2. Nella casella di testo **IdP Login URL** (URL accesso IdP) inserire il valore di **URL SSO SAML** dalla configurazione guidata dell'applicazione di Azure AD.
 3. Aprire il certificato con codifica Base64 nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **IdP Cert** (Certificato IdP).
 4. Fare clic su **Save**.

12. Nel portale classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.

    ![Single Sign-On di Microsoft Azure AD][10]

13. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
  
    ![Single Sign-On di Microsoft Azure AD][11]


### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_09.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_04.png) 

5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_05.png) 
 1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
 2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
 3. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_06.png) 
 1. Nella casella di testo **Nome** digitare **Britta**.  
 2. Nella casella di testo **Cognome** digitare **Simon**.
 3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
 4. Nell'elenco **Ruolo** selezionare **Utente**.
 5. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_07.png) 

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_08.png) 
 1. Prendere nota del valore visualizzato in **Nuova password**.
 2. Fare clic su **Completa**.   

### <a name="create-a-voyance-test-user"></a>Creare un utente test di Voyance

Questa sezione descrive come creare un utente chiamato Britta Simon in Voyance. Voyance supporta il provisioning just-in-time, che è abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a Voyance verrà creato un nuovo utente, se non esiste già.

>[!NOTE]
>Per creare un utente manualmente, è necessario contattare il [team di supporto di Voyance](emaiLto:support@nyansa.com).
>

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Voyance.

![Assegna utente][200] 

**Per assegnare Britta Simon a Voyance, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Voyance**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_50.png) 

3. Scegliere **Utenti**dal menu in alto.

    ![Assegna utente][203] 

4. Nell'elenco di utenti selezionare **Britta Simon**.

5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
    
    ![Assegna utente][205]


### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Voyance nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Voyance.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_205.png

