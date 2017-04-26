---
title: 'Esercitazione: Integrazione di Azure Active Directory con Trello | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Trello.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: f3e607fbff1aa8c6a652dae452c75554a59828d9
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>Esercitazione: Integrazione di Azure Active Directory con Trello
Questa esercitazione descrive come integrare Trello con Azure Active Directory (Azure AD).

L'integrazione di Trello con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Trello
* È possibile abilitare gli utenti per l'accesso automatico con Single Sign-On (SSO) a Trello con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Trello, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di **Trello** abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Trello dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-trello-from-the-gallery"></a>Aggiungere Trello dalla raccolta
Per configurare l'integrazione di Trello in Azure AD, è necessario aggiungere Trello dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Trello dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]

6. Nella casella di ricerca digitare **Trello**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-trello-tutorial/tutorial_trello_01.png)

7. Nel riquadro dei risultati selezionare **Trello** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-trello-tutorial/tutorial_trello_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso Single Sign-On (SSO) di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Trello in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di Trello che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Trello.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Trello. Per configurare e testare l'accesso SSO di Azure AD con Trello, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Trello](#creating-a-the-funding-portal-test-user)** : per avere una controparte di Britta Simon in Trello collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD
Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione Trello.

L'applicazione Trello prevede che le asserzioni SAML contengano attributi specifici. Configurare gli attributi seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla scheda **"Attributes"** (Attributi) dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.

![Configura accesso Single Sign-On](./media/active-directory-saas-trello-tutorial/tutorial_trello_03.png) 

**Per configurare Single Sign-On di Azure AD con Trello, seguire questa procedura:**

1. Nel menu visualizzato nella parte superiore della pagina di integrazione dell'applicazione **Trello** del portale di Azure classico fare clic su **Attributi**.
   
    ![Configura accesso Single Sign-On][5]

2. Nella finestra di dialogo **Attributi token SAML** seguire questa procedura per ciascuna riga della tabella:

    | Nome attributo | Valore attributo |
    | --- | --- |    
    | User.Email | user.mail |
    | User.FirstName | user.givenname |
    | User.LastName | user.surname |

   1. Fare clic su **aggiungi attributo utente** per aprire la finestra di dialogo **Aggiungi attributo utente**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-trello-tutorial/tutorial_trello_05.png)
   2. Nella casella di testo **Nome attributo** , digitare il nome dell'attributo indicato per quella riga.
   3. Nell'elenco **Valore attributo** selezionare il valore indicato per quella riga.
   4. Fare clic su **Complete**. Fare quindi clic su **Salva le modifiche** nella parte inferiore della pagina.

1. Nel menu in alto fare clic su **Avvio rapido**.
   
    ![Configura accesso Single Sign-On][6]

2. Nella pagina di integrazione dell'applicazione **Trello** del portale di Azure classico, fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][7] 

3. Nella pagina **Stabilire come si desidera che gli utenti accedano a Trello** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-trello-tutorial/tutorial_trello_06.png)

4. Nella pagina della finestra di dialogo **Configurare le impostazioni dell'app**, se si desidera configurare l'applicazione in **modalità iniziata da IDP**,seguire la procedura seguente:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-trello-tutorial/tutorial_trello_07.png)

   1. Nella casella di testo URL di risposta digitare l'URL usando il modello seguente: `https://trello.com/auth/saml/consume/<enterprise>`
   2. Fare clic su **Avanti**.

    >[!NOTE]
    >È necessario ottenere il campo dati dinamico **\<enterprise\>** da Trello. Se non si dispone del valore per il campo dati dinamico, contattare il team di supporto di Trello all'indirizzo <mailto:support@trello.com> per ottenere il campo relativo all'organizzazione.
    > 
    > 

5. Se si desidera configurare l'applicazione in **SP initiated mode** (Modalità iniziata dal provider di servizi) nella finestra di dialogo **Configurare le impostazioni dell'app** fare clic su **"Mostra opzioni avanzate (facoltativo)"** e digitare l'**URL di accesso**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-trello-tutorial/tutorial_trello_08.png)
   
   1. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://trello.com/auth/saml/consume/<enterprise>`
   2. Fare clic su **Avanti**.

6. Nella pagina **Configura accesso Single Sign-On in Trello** fare clic su **Scarica certificato** e quindi salvare il file nel computer.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-trello-tutorial/tutorial_trello_09.png)

7. Per configurare l'SSO per l'applicazione, passare alla pagina [Configurazione Trello enterprise SSO](https://trello.com/sso-configuration) per inviare al team di Trello l'URL di accesso e allegare il certificato scaricato.
8. Nel portale classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]

9. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_09.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_04.png) 

5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_05.png) 
   
   1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   3. Fare clic su **Avanti**.

6. Nella pagina **Profilo utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_06.png) 
   
   1. Nella casella di testo **Nome** digitare **Britta**.  
   2. Nella casella di testo **Cognome** digitare **Simon**.
   3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   4. Nell'elenco **Ruolo** selezionare **Utente**.
   5. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_07.png) 

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_08.png) 
   
   1. Prendere nota del valore visualizzato in **Nuova password**.
   2. Fare clic su **Complete**.   

### <a name="create-a-trello-test-user"></a>Creare un utente di test di Trello
In questa sezione viene creato un utente di nome Britta Simon in Trello. In questa sezione viene creato un utente di nome Britta Simon in Trello. Trello supporta il provisioning JIT. Al primo accesso ad Azure AD, verrà creato un nuovo account.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Trello.

![Assegna utente][200] 

**Per assegnare Britta Simon a Trello, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione Directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Trello**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-trello-tutorial/tutorial_trello_10.png) 

3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 

4. Nell'elenco Tutti gli utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Trello nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Trello.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-trello-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trello-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trello-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trello-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-trello-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-trello-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-trello-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-trello-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-trello-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-trello-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trello-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trello-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-trello-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-trello-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-trello-tutorial/tutorial_general_205.png

