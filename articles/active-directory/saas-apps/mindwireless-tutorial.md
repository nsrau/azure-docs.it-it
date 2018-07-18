---
title: 'Esercitazione: Integrazione di Azure Active Directory con mindWireless | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e mindWireless.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bd00a339-27c9-4904-b66f-a95bf597ac3c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: jeedes
ms.openlocfilehash: 293a8921e64bae03d036f847d2f9138d88ebef4a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210259"
---
# <a name="tutorial-azure-active-directory-integration-with-mindwireless"></a>Esercitazione: Integrazione di Azure Active Directory con mindWireless

Questa esercitazione descrive come integrare mindWireless con Azure Active Directory (Azure AD).

L'integrazione di mindWireless con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a mindWireless.
- È possibile abilitare gli utenti per l'accesso automatico a mindWireless (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con mindWireless, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di mindWireless abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di mindWireless dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-mindwireless-from-the-gallery"></a>Aggiunta di mindWireless dalla raccolta
Per configurare l'integrazione di mindWireless in Azure AD, è necessario aggiungere mindWireless dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere mindWireless dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **mindWireless**, selezionare **mindWireless** dal pannello dei risultati quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![mindWireless nell'elenco risultati](./media/mindwireless-tutorial/tutorial_mindwireless_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con mindWireless usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente controparte di mindWireless che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in mindWireless.

Per configurare e testare l'accesso Single Sign-On di Azure AD con mindWireless, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test per mindWireless](#create-a-mindwireless-test-user)**: per avere una controparte di Britta Simon in mindWireless collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione mindWireless.

**Per configurare l'accesso Single Sign-On di Azure AD con mindWireless, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **mindWireless** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Single Sign-On](./media/mindwireless-tutorial/tutorial_mindwireless_samlbase.png)

3. Nella sezione **URL e dominio mindWireless** seguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di mindWireless](./media/mindwireless-tutorial/tutorial_mindwireless_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<subdomain>.mwsmart.com/`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<subdomain>.mwsmart.com/SAML/AssertionConsumerService.aspx`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto di mindWireless](mailto:sdulloor@mindwireless.com).

4. L'applicazione mindWireless prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML.

5. La schermata seguente illustra un esempio relativo a questa operazione. Il nome dell'attestazione è sempre **ID dipendente**, con mapping del valore a user.employeeid, che contiene il valore EmployeeID dell'utente. In questo caso il mapping utente da Azure AD a mindWireless viene eseguito su EmployeeID, ma è possibile eseguirlo su un valore diverso anche in base alle impostazioni dell'applicazione. È possibile collaborare prima di tutto con il [team di supporto di mindWireless](mailto:sdulloor@mindwireless.com) per usare l'identificatore corretto di un utente ed eseguire il mapping di tale valore all'attestazione **ID dipendente**.

    ![Configure Single Sign-On](./media/mindwireless-tutorial/tutorial_attribute.png)

6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente ed eseguire i passaggi descritti di seguito:
    
    | Nome attributo | Valore attributo | Valore spazio dei nomi |
    | -------------- | --------------- | ----------------|
    | ID dipendente | user.employeeid | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/mindwireless-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/mindwireless-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Nella casella di testo **Spazio dei nomi**, digitare il valore dello spazio dei nomi indicato per la riga.
    
    e. Fare clic su **OK**.
    
7. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/mindwireless-tutorial/tutorial_mindwireless_certificate.png) 

8. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/mindwireless-tutorial/tutorial_general_400.png)

9. Nella sezione **Configurazione di mindWireless** fare clic su **Configura mindWireless** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di mindWireless](./media/mindwireless-tutorial/tutorial_mindwireless_configure.png) 

10. Per configurare l'accesso Single Sign-On sul lato **mindWireless**, è necessario inviare il **certificato (Base64) scaricato, l'URL del servizio Single Sign-On SAML** e l'**ID di entità SAML** al [team di supporto di mindWireless](mailto:sdulloor@mindwireless.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/mindwireless-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/mindwireless-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/mindwireless-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/mindwireless-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.

### <a name="create-a-mindwireless-test-user"></a>Creare un utente di test di mindWireless

In questa sezione viene creato un utente di nome Britta Simon in mindWireless. Collaborare con il [team di supporto di mindWireless](mailto:sdulloor@mindwireless.com) per aggiungere gli utenti nella piattaforma mindWireless. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a mindWireless.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a mindWireless, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **mindWireless**.

    ![Collegamento mindWireless nell'elenco delle applicazioni](./media/mindwireless-tutorial/tutorial_mindwireless_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro mindWireless nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione mindWireless.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/mindwireless-tutorial/tutorial_general_01.png
[2]: ./media/mindwireless-tutorial/tutorial_general_02.png
[3]: ./media/mindwireless-tutorial/tutorial_general_03.png
[4]: ./media/mindwireless-tutorial/tutorial_general_04.png

[100]: ./media/mindwireless-tutorial/tutorial_general_100.png

[200]: ./media/mindwireless-tutorial/tutorial_general_200.png
[201]: ./media/mindwireless-tutorial/tutorial_general_201.png
[202]: ./media/mindwireless-tutorial/tutorial_general_202.png
[203]: ./media/mindwireless-tutorial/tutorial_general_203.png

